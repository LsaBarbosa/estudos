# FASE 9 — Resiliência

Lucas, resiliência significa projetar a aplicação assumindo que **dependências remotas vão ficar lentas, indisponíveis ou responder com erro em algum momento**.

Os seis padrões formam um conjunto:

```text
Timeout
   ↓
Retry
   ↓
Circuit Breaker
   ↓
Bulkhead
   ↓
Rate Limit
   ↓
Fallback
```

Eles não resolvem o mesmo problema.

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Timeout** | Define quanto tempo uma operação pode esperar antes de desistir. | Curto demais gera falsos erros; longo demais prende threads, conexões e recursos. | Chamadas HTTP, banco, Kafka, APIs externas. |
| **Connection Timeout** | Tempo máximo para estabelecer conexão com o destino. | Muito pequeno pode falhar em redes lentas; muito grande prende recursos quando o destino está inacessível. | Abrir conexão TCP/TLS com Payment API. |
| **Read Timeout** | Tempo máximo esperando dados após a conexão ter sido estabelecida. | Um serviço lento pode causar timeout mesmo estando disponível. | API conectou, mas demora para devolver resposta. |
| **Request Timeout** | Limite para a duração total ou lógica da requisição, dependendo do cliente/framework utilizado. | Precisa ser coerente com timeouts internos para não criar operações órfãs. | Limitar chamada inteira a um serviço remoto. |
| **Retry** | Repete automaticamente uma operação após uma falha considerada transitória. | Multiplica carga e pode piorar uma indisponibilidade. Operações não idempotentes podem duplicar efeitos. | Timeout transitório, conexão recusada, alguns `5xx`. |
| **Exponential Backoff** | Aumenta progressivamente o intervalo entre retries. | Aumenta o tempo total até a resposta final. | Dependência temporariamente indisponível. |
| **Jitter** | Adiciona aleatoriedade ao intervalo de retry para evitar vários clientes retentando simultaneamente. | Torna os tempos menos determinísticos. | Muitos pods chamando a mesma dependência. |
| **Circuit Breaker** | Interrompe temporariamente chamadas para uma dependência que apresenta taxa elevada de falhas ou lentidão. | Configuração agressiva pode bloquear um serviço que já se recuperou; permissiva demais demora a reagir. | Payment Service indisponível. |
| **CLOSED** | Circuito normal; chamadas são permitidas e monitoradas. | Ainda existe custo da chamada remota. | Estado saudável. |
| **OPEN** | Chamadas são rejeitadas imediatamente, sem atingir a dependência. | Operações legítimas também ficam bloqueadas durante o período aberto. | Serviço remoto claramente degradado. |
| **HALF_OPEN** | Algumas chamadas de teste são liberadas para verificar recuperação. | Poucas chamadas podem não representar perfeitamente a saúde real. | Testar recuperação antes de fechar o circuito. |
| **Bulkhead** | Limita quantas operações podem utilizar determinada dependência simultaneamente. | Limites baixos reduzem throughput; altos demais não oferecem isolamento suficiente. | Evitar que Payment consuma todas as threads/conexões. |
| **Semaphore Bulkhead** | Limita concorrência através de permits de um semáforo. | Chamadas acima do limite são rejeitadas ou precisam esperar conforme configuração. | HTTP/JDBC e modelos modernos de concorrência. |
| **Thread Pool Bulkhead** | Isola chamadas utilizando pool e fila próprios. | Mais threads, memória, filas e context switching. | Workloads que realmente precisam de isolamento por pool. |
| **Rate Limit** | Controla quantas operações podem ocorrer em determinado intervalo. | Rejeita ou posterga tráfego acima do limite. | Proteger API, banco ou serviço externo. |
| **Fallback** | Fornece uma resposta alternativa quando a operação principal não pode ser concluída. | Pode esconder indisponibilidades se usado de forma indiscriminada; nem toda operação admite resposta degradada. | Cache, dado antigo, resposta parcial. |
| **Resilience4j** | Biblioteca Java modular para padrões como Circuit Breaker, Retry, RateLimiter, Bulkhead e TimeLimiter. | Mais configuração e observabilidade necessárias; padrões mal combinados podem agravar problemas. | Aplicações Java/Spring Boot distribuídas. |

A documentação atual do Resilience4j continua oferecendo módulos independentes para `CircuitBreaker`, `Retry`, `RateLimiter`, `Bulkhead` e `TimeLimiter`; a versão 2 da biblioteca requer Java 17 ou superior. 

---

# 2. Timeout — primeira defesa

Toda chamada remota precisa responder à pergunta:

> **Quanto tempo estou disposto a esperar?**

Sem timeout:

```text
Order Service
     ↓
Payment Service
     ↓
fica travado
     ↓
thread ocupada
connection ocupada
request pendente
```

Se isso acontecer várias vezes:

```text
50 requests
   ↓
50 chamadas bloqueadas
   ↓
pool esgota
   ↓
Order Service também começa a falhar
```

Uma falha no Payment pode provocar uma **falha em cascata**.

Por isso timeout é uma das primeiras barreiras de resiliência.

---

# 3. Connection, Read e Request Timeout

É importante diferenciar.

### Connection Timeout

Pergunta:

> Quanto tempo espero para conseguir conectar?

```text
Client
  ↓
TCP/TLS connection
  ↓
Payment Service
```

Se nem a conexão consegue ser estabelecida dentro do limite:

```text
Connection Timeout
```

### Read Timeout

A conexão já existe:

```text
Connection ✓
```

mas:

```text
enviei request
      ↓
esperando resposta...
```

Se o backend demora demais:

```text
Read Timeout
```

### Request Timeout

É um limite mais amplo sobre o tempo permitido para aquela operação/request, dependendo da biblioteca utilizada.

Uma boa arquitetura precisa alinhar esses limites.

---

# 4. Timeout precisa respeitar o orçamento de latência

Imagine que sua API promete:

```text
p99 < 2 segundos
```

Mas você configura:

```text
Payment timeout = 10s
```

Isso não faz sentido para aquela cadeia síncrona.

Pense em um **latency budget**:

```text
Request total
    2 segundos

├── aplicação
├── banco
├── Payment API
└── margem de erro
```

Os timeouts internos precisam caber no orçamento total.

---

# 5. Retry

Retry significa tentar novamente depois de uma falha.

Mas a pergunta importante não é:

> "Posso usar retry?"

É:

> **Essa falha é transitória e essa operação pode ser repetida com segurança?**

Exemplos de possíveis falhas transitórias:

```text
timeout
connection reset
connection refused temporário
alguns 5xx
429 em determinados cenários
```

Normalmente não faz sentido retry automático para:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
business validation
```

porque repetir exatamente a mesma chamada provavelmente produzirá o mesmo resultado.

O Resilience4j permite configurar quais exceções ou resultados devem disparar retry e quais devem ser ignorados. 

---

# 6. Retry precisa de idempotência

Imagine:

```text
POST /payments
      ↓
pagamento executado ✓
      ↓
resposta se perde
      ↓
timeout
```

O cliente executa retry:

```text
POST /payments novamente
```

Sem idempotência:

```text
cobrança 1
+
cobrança 2
```

Portanto:

```text
Retry
  +
side effect
  ↓
Idempotência
```

é essencial.

Isso conecta diretamente resiliência ao tema de sistemas distribuídos.

---

# 7. O problema do retry imediato

Uma dependência fica indisponível.

Imagine 100 instâncias:

```text
100 clientes
     ↓
erro
     ↓
retry imediato
     ↓
erro
     ↓
retry imediato
```

Você cria uma tempestade de retries.

O serviço que já estava sobrecarregado passa a receber ainda mais tráfego.

Por isso usamos:

```text
Exponential Backoff
+
Jitter
```

---

# 8. Exponential Backoff

Em vez de:

```text
Retry 1 → 100 ms
Retry 2 → 100 ms
Retry 3 → 100 ms
```

podemos ter:

```text
Retry 1 → 100 ms

Retry 2 → 200 ms

Retry 3 → 400 ms

Retry 4 → 800 ms
```

Isso dá tempo para a dependência se recuperar.

O Resilience4j oferece suporte a funções de intervalo customizadas e exponential backoff para Retry. 

---

# 9. Jitter

Existe outro problema.

Imagine:

```text
100 pods
```

falhando no mesmo instante.

Todos calculam:

```text
retry em 1 segundo
```

Então, exatamente um segundo depois:

```text
100 pods
   ↓
Payment Service
```

Isso cria o chamado efeito de sincronização ou **thundering herd**.

Com jitter:

```text
Pod A → 870 ms
Pod B → 1.120 ms
Pod C → 940 ms
Pod D → 1.270 ms
```

As tentativas ficam distribuídas.

A AWS recomenda backoff com jitter justamente para reduzir picos sincronizados de retries. 

---

# 10. Circuit Breaker

Retry tenta novamente.

Circuit Breaker responde a outra pergunta:

> **Ainda faz sentido continuar chamando essa dependência?**

Imagine:

```text
Payment Service
90% de erro
```

Sem Circuit Breaker:

```text
Request
 ↓
Payment
 ↓
timeout

Request
 ↓
Payment
 ↓
timeout

Request
 ↓
Payment
 ↓
timeout
```

Estamos desperdiçando:

```text
threads
connections
CPU
tempo
```

O Circuit Breaker interrompe temporariamente essas chamadas.

---

# 11. Estados do Circuit Breaker

Memorize:

```text
CLOSED
   ↓
OPEN
   ↓
HALF_OPEN
   ↓
CLOSED
```

### CLOSED

Funcionamento normal.

```text
requests
   ↓
backend
```

O Circuit Breaker monitora:

```text
failure rate
slow calls
```

### OPEN

Quando o limite configurado é ultrapassado:

```text
Circuit OPEN
```

As chamadas deixam de atingir o backend.

Elas são rejeitadas rapidamente.

### HALF_OPEN

Depois de determinado período:

```text
OPEN
 ↓
HALF_OPEN
```

Uma quantidade limitada de chamadas é liberada.

Se funcionarem:

```text
HALF_OPEN
    ↓
CLOSED
```

Se continuarem falhando:

```text
HALF_OPEN
    ↓
OPEN
```

É exatamente o modelo principal utilizado pelo CircuitBreaker do Resilience4j. 

---

# 12. Circuit Breaker não substitui Timeout

Esse erro é comum.

Circuit Breaker:

```text
identifica dependência degradada
e para de chamá-la temporariamente
```

Timeout:

```text
limita quanto uma chamada
individual pode esperar
```

Você normalmente precisa dos dois.

```text
Request
   ↓
Circuit Breaker
   ↓
Timeout
   ↓
Remote Service
```

Se não existe timeout, uma chamada pode permanecer presa antes que o Circuit Breaker consiga registrar adequadamente o resultado.

---

# 13. Bulkhead

Bulkhead vem da ideia dos compartimentos de um navio.

Se um compartimento alaga:

```text
não queremos que
todo o navio afunde
```

Em software:

```text
Payment lento
```

não deveria consumir:

```text
todas as threads
todas as conexões
todos os recursos
```

da aplicação.

Então:

```text
Payment
   ↓
máximo 20 chamadas concorrentes
```

Enquanto:

```text
Customer
Inventory
Shipping
```

continuam funcionando.

---

# 14. Semaphore Bulkhead x Thread Pool Bulkhead

Resilience4j oferece duas implementações principais de Bulkhead: uma baseada em `Semaphore` e outra baseada em pool fixo com fila limitada. 

### Semaphore Bulkhead

```text
100 chamadas
    ↓
Semaphore
20 permits
    ↓
máximo 20 simultâneas
```

É simples e costuma funcionar bem em vários modelos de execução.

### Thread Pool Bulkhead

```text
requests
   ↓
queue limitada
   ↓
thread pool dedicado
   ↓
backend
```

Cria isolamento físico maior entre workloads.

Mas aumenta:

```text
threads
filas
memória
context switching
```

No Java moderno, especialmente com Virtual Threads, não escolha Thread Pool Bulkhead automaticamente. Muitas vezes o isolamento de concorrência pode ser feito diretamente com limites/semaphore.

---

# 15. Rate Limit

Bulkhead e Rate Limit parecem semelhantes, mas resolvem problemas diferentes.

### Bulkhead

Limita:

> **quantas operações podem estar executando simultaneamente?**

### Rate Limit

Limita:

> **quantas operações podem acontecer em determinado período?**

Exemplo:

```text
Rate Limit

100 requests / segundo
```

Enquanto:

```text
Bulkhead

20 requests concorrentes
```

São dimensões diferentes.

---

# 16. Para que usar Rate Limit

Imagine uma API externa que permite:

```text
1.000 requests/minuto
```

Seu sistema não deveria ultrapassar isso.

Ou sua própria API suporta:

```text
5.000 requests/s
```

e você quer proteger o sistema contra picos.

O Rate Limiter controla esse fluxo.

No Resilience4j, o RateLimiter trabalha com permissões disponibilizadas por períodos configuráveis. 

---

# 17. Fallback

Fallback responde:

> **Se a operação principal falhar, existe uma resposta alternativa útil?**

Exemplo:

```text
Recommendation Service
        ↓
falhou
        ↓
Fallback
        ↓
produtos populares
```

Outro:

```text
Exchange Rate API
      ↓
falhou
      ↓
último valor em cache
```

Ou:

```text
Customer Profile
      ↓
serviço secundário falhou
      ↓
resposta parcial
```

---

# 18. Fallback não deve mentir

Esse é o maior cuidado.

Imagine:

```text
Payment Service falhou
```

Fallback:

```text
"Pagamento aprovado"
```

Isso seria incorreto.

Algumas funcionalidades admitem degradação:

```text
recomendação
avatar
analytics
dados não críticos
```

Outras não:

```text
pagamento
transferência
autorização
estoque crítico
```

Fallback precisa preservar a **semântica do negócio**.

---

# 19. Como os padrões se combinam

Uma arquitetura pode ter:

```text
Request
   ↓
Rate Limit
   ↓
Bulkhead
   ↓
Circuit Breaker
   ↓
Retry
   ↓
Timeout
   ↓
Payment Service
```

Em falha final:

```text
Fallback
```

Mas a ordem exata e a composição dependem do caso.

O Resilience4j permite combinar decorators, justamente para utilizar vários desses mecanismos sobre a mesma operação. 

O ponto não é decorar uma ordem universal.

É entender **qual problema cada camada resolve**.

---

# 20. Cuidado com Retry + Circuit Breaker

Imagine:

```text
1 request
```

com:

```text
3 attempts de Retry
```

Para o backend isso pode significar:

```text
1 request lógico
=
3 chamadas físicas
```

Agora imagine mil requests:

```text
1.000 requests
×
3 tentativas
=
até 3.000 chamadas
```

Por isso retry precisa ser:

```text
limitado
seletivo
observável
com backoff
com jitter
```

e deve ser considerado ao configurar os thresholds do Circuit Breaker.

---

# 21. Retry em várias camadas

Outro antipadrão:

```text
API Gateway
  Retry 3x
     ↓
Order Service
  Retry 3x
     ↓
Payment Client
  Retry 3x
```

No pior caso:

```text
3 × 3 × 3
=
27 tentativas
```

para uma única operação original.

Isso pode destruir uma dependência degradada.

Portanto:

> **defina conscientemente em qual camada o retry pertence.**

---

# 22. Observabilidade

Padrões de resiliência sem métricas são perigosos.

Você precisa observar:

```text
timeouts
retries
retry attempts
circuit breaker state
failure rate
slow call rate
bulkhead rejected calls
rate-limit rejections
fallbacks
```

Resilience4j possui integração com Micrometer e expõe métricas para Circuit Breaker, Retry, Bulkhead e RateLimiter. 

Imagine descobrir que:

```text
99% das requests
estão usando fallback
```

A aplicação pode aparentar estar:

```text
UP
```

mas a funcionalidade principal pode estar praticamente quebrada.

---

# 23. Mapa mental dos seis padrões

Memorize assim:

```text
TIMEOUT
   ↓
Quanto tempo posso esperar?


RETRY
   ↓
Vale a pena tentar novamente?


CIRCUIT BREAKER
   ↓
Ainda vale a pena chamar?


BULKHEAD
   ↓
Quanto desse recurso pode
ser consumido simultaneamente?


RATE LIMIT
   ↓
Quanto tráfego permito
por período?


FALLBACK
   ↓
O que faço quando
a operação não funciona?
```

Essa é uma excelente forma de responder em entrevista.

---

# 24. Resilience4j

No ecossistema Java, Resilience4j é uma biblioteca importante para implementar esses mecanismos.

Ela possui módulos como:

```text
resilience4j-circuitbreaker
resilience4j-retry
resilience4j-bulkhead
resilience4j-ratelimiter
resilience4j-timelimiter
```

e pode ser integrada com aplicações Spring. 

Mas uma resposta Senior não deveria ser:

> "Eu uso `@Retry` e `@CircuitBreaker`."

Deveria ser:

> "Eu entendo por que estou usando cada mecanismo e quais falhas ele resolve."

---

# Resposta objetiva para entrevista

> Em sistemas distribuídos, parto da premissa de que qualquer dependência remota pode ficar lenta ou indisponível. Por isso, toda chamada precisa ter uma estratégia de timeout para evitar consumir recursos indefinidamente.
>
> Retry eu utilizo apenas para falhas potencialmente transitórias e, principalmente quando existe side effect, garanto que a operação seja idempotente. Também limito o número de tentativas e utilizo exponential backoff com jitter para evitar retry storms. 
>
> Circuit Breaker utilizo para parar temporariamente de chamar uma dependência que apresenta taxa elevada de falhas ou lentidão. O fluxo principal é `CLOSED`, `OPEN` e `HALF_OPEN`, permitindo testar posteriormente se o backend se recuperou. 
>
> Bulkhead serve para isolar recursos e impedir que uma dependência degradada consuma toda a capacidade da aplicação. Rate Limit controla a quantidade de tráfego permitida em determinado período. 
>
> Fallback só utilizo quando existe uma alternativa semanticamente válida, como cache ou resposta parcial. Não uso fallback para mascarar operações críticas que realmente falharam.
>
> No Java, Resilience4j oferece esses mecanismos de forma modular e permite combiná-los. Mas o principal para mim é configurar tudo com observabilidade e entender que **resiliência não elimina falhas; ela impede que uma falha localizada se transforme em falha sistêmica**. 
