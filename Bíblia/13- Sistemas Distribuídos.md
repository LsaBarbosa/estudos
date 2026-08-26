# FASE 7 — Sistemas Distribuídos

Lucas, aqui muda bastante o nível da discussão. O foco deixa de ser apenas **“como dois microsserviços se comunicam”** e passa a ser:

> **O que acontece quando a rede falha, a resposta não chega, mensagens duplicam, serviços discordam sobre o estado e não existe uma única transação envolvendo tudo?**

Esse é o raciocínio necessário para Senior avançado, Tech Lead e Staff.

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **CAP** | Durante uma partição de rede, um sistema distribuído não consegue garantir simultaneamente consistência forte e disponibilidade. Na prática, mantendo tolerância a partição, precisa escolher como sacrificar C ou A naquele cenário.  | Priorizar consistência pode rejeitar operações; priorizar disponibilidade pode aceitar dados temporariamente divergentes. | Bancos distribuídos, replicação entre regiões. |
| **Consistency** | Define quais garantias existem sobre o valor observado por diferentes clientes/nós. | Quanto mais forte a consistência, geralmente maior a necessidade de coordenação. | Saldo financeiro versus feed social. |
| **Availability** | No contexto de CAP, cada requisição a um nó não falho deve obter uma resposta em vez de ser recusada por impossibilidade de garantir consistência.  | Manter disponibilidade durante partition pode significar trabalhar com dados desatualizados. | Catálogos, feeds, sistemas tolerantes a dados temporariamente stale. |
| **Partition Tolerance** | Sistema continua operando mesmo quando partes da rede deixam de trocar mensagens corretamente. | Depois que existe uma partition, aparece o conflito C versus A. | Comunicação entre datacenters, regiões ou réplicas. |
| **PACELC** | Estende CAP: se houver Partition, escolha entre Availability e Consistency; Else, mesmo sem partition, existe trade-off entre Latency e Consistency.  | Consistência síncrona pode aumentar latência; baixa latência pode exigir consistência mais relaxada. | Escolha de estratégia de replicação. |
| **Strong Consistency** | Depois que uma escrita é confirmada, leituras subsequentes observam o estado mais recente segundo o modelo forte adotado. | Exige mais coordenação e pode aumentar latência ou reduzir disponibilidade. | Saldo, limite financeiro, controle crítico de recursos. |
| **Eventual Consistency** | Réplicas/serviços podem divergir temporariamente, mas convergem se não houver novas atualizações. | O negócio precisa aceitar estados intermediários e leituras stale. | Catálogo, analytics, projeções, processamento por eventos. |
| **Read-your-writes** | Um cliente que acabou de escrever determinado dado consegue enxergar sua própria alteração nas leituras seguintes. | Pode exigir sticky routing, sessão ou coordenação adicional. | Usuário atualiza perfil e imediatamente consulta o perfil. |
| **Monotonic Reads** | Depois que um cliente observou uma versão nova, suas leituras posteriores não devem voltar para uma versão mais antiga. | Pode restringir escolha de réplica ou exigir controle de versão. | Sistemas replicados geograficamente. |
| **Network Partition** | Parte dos nós continua ativa, mas não consegue se comunicar corretamente com outra parte. | É impossível simplesmente assumir que o outro lado morreu. | Falha entre regiões ou segmentos de rede. |
| **Partial Failure** | Parte do sistema falha enquanto o restante continua funcionando. | O sistema pode ficar em estado ambíguo: algumas operações terminaram, outras não. | Order funciona, Payment está indisponível. |
| **Timeout** | Limite máximo que o chamador espera por uma operação remota. | Timeout curto gera falsos erros; longo demais prende threads, conexões e recursos.  | HTTP entre microsserviços, banco, broker. |
| **Clock Skew** | Relógios de máquinas diferentes não estão perfeitamente sincronizados. | Não se deve depender ingenuamente de timestamps físicos para ordenação global. | Eventos distribuídos, leases, expiração. |
| **Duplicate Message** | A mesma mensagem pode chegar mais de uma vez, especialmente com delivery at-least-once. | Consumidor precisa ser idempotente.  | Kafka, RabbitMQ, filas. |
| **Lost Message** | Mensagem pode deixar de ser publicada, entregue ou confirmada devido a falhas. | É preciso combinar persistência, acknowledgement, retries e padrões como Outbox. | Eventos críticos de negócio. |
| **Out-of-order Message** | Eventos podem ser observados em ordem diferente da ordem lógica esperada. | Pode exigir chave de particionamento, versionamento ou rejeição de eventos antigos. | `OrderPaid` chegando antes de uma atualização anterior. |
| **Split Brain** | Dois subconjuntos do cluster acreditam que podem continuar atuando como autoridade. | Pode gerar decisões conflitantes e corrupção lógica. | Clusters sem quorum adequado. |
| **Idempotency** | Executar a mesma operação várias vezes produz o mesmo efeito lógico que executá-la uma vez. | Requer armazenamento de chave/estado e definição clara do resultado repetido. | Pagamentos, criação de pedidos, consumers. |
| **Idempotency-Key** | Identificador enviado com uma operação para detectar retries da mesma intenção. | Precisa de persistência, escopo e política de expiração. | `POST /payments`. |
| **UNIQUE Constraint** | Restrição de banco pode impedir processamento concorrente duplicado da mesma chave. | Precisa tratar conflito e decidir qual resposta retornar. | `UNIQUE(idempotency_key)`. |
| **Saga** | Transação distribuída transformada em uma sequência de transações locais, com compensações quando necessário.  | Não fornece rollback ACID global; exige modelar estados e compensações. | Order + Payment + Inventory + Shipping. |
| **Saga Orchestration** | Um coordenador central determina os passos da Saga. | Fluxo fica explícito, mas o orchestrator concentra conhecimento e pode crescer bastante. | Checkout complexo com muitas etapas. |
| **Saga Choreography** | Serviços reagem a eventos sem coordenador central explícito. | Menor centralização, mas fluxo pode ficar difícil de visualizar e depurar.  | Fluxos relativamente simples orientados a eventos. |
| **Compensation** | Operação de negócio que semanticamente desfaz ou neutraliza uma etapa anterior. | Nem tudo pode ser literalmente desfeito. | Reserva de estoque → liberar estoque. |
| **Transactional Outbox** | Alteração de negócio e evento são persistidos na mesma transação local; um relay publica o evento posteriormente.  | Adiciona tabela/relay e ainda pode publicar duplicatas. | Persistir Order e garantir publicação de `OrderCreated`. |
| **Inbox** | Consumidor registra IDs das mensagens processadas para impedir efeitos duplicados. | Exige storage, limpeza e atomicidade com a operação de negócio. | Consumers idempotentes. |
| **`@Transactional` local** | Protege atomicidade dos recursos participantes daquela transação local; não cria automaticamente uma transação ACID entre microsserviços independentes. | Confundir transação local com distribuída gera inconsistência. | Atualizar `orders` + `outbox` no mesmo banco. |

---

# 2. CAP — o conceito correto

A explicação simplificada:

> “CAP significa escolha dois de três”

é útil para memorizar, mas pode induzir erro.

O raciocínio realmente importante é:

```text
Network Partition aconteceu
          ↓

Não consigo comunicar
todos os nós
          ↓

Preciso escolher

     ┌───────────────┐
     │               │
Consistency      Availability
     │               │
recuso operação    respondo mesmo
se não consigo     sem garantir
garantir estado    estado mais novo
```

Em sistemas distribuídos reais, **Partition Tolerance não costuma ser simplesmente uma opção descartável**, porque a rede pode falhar. O trade-off relevante do CAP aparece justamente quando a partition acontece. 

---

# 3. CAP — exemplo prático

Imagine duas réplicas:

```text
Node A  ←── rede ──→  Node B
```

Estado inicial:

```text
saldo = 100
```

A rede quebra:

```text
Node A       X       Node B
```

Um usuário altera em A:

```text
saldo = 50
```

Agora outro cliente consulta B.

B não sabe que A possui:

```text
saldo = 50
```

Tem duas opções.

### Priorizar consistência

```text
Node B

"Não consigo garantir
que meu dado é atual."

        ↓
erro / indisponibilidade
```

### Priorizar disponibilidade

```text
Node B

responde saldo = 100
```

Mas isso pode ser stale.

Esse é o coração do CAP.

---

# 4. PACELC

PACELC melhora o raciocínio porque diz que o problema não existe apenas durante falhas.

Memorize:

```text
PACELC

IF Partition

P
↓
A ou C


ELSE

E
↓
Latency ou Consistency
```

Ou:

> **Se houver uma Partition, qual é meu trade-off entre Availability e Consistency? Se não houver partition, qual é meu trade-off entre Latency e Consistency?** 

Imagine replicação síncrona:

```text
Write
  ↓
Node A
  ↓
espera Node B
  ↓
espera Node C
  ↓
ACK
```

Ganha consistência mais forte.

Mas paga em:

```text
latência
```

Com replicação assíncrona:

```text
Write
  ↓
Node A
  ↓
ACK imediato

Node B ← replica depois
Node C ← replica depois
```

Menor latência.

Mas pode existir um período de divergência.

---

# 5. Modelos de consistência

Não existe apenas:

```text
Strong
versus
Eventual
```

Existem garantias intermediárias.

### Strong consistency

O cliente espera enxergar um estado consistente com as escritas confirmadas.

### Eventual consistency

Pode existir:

```text
Node A = versão 10

Node B = versão 9
```

e posteriormente:

```text
Node A = versão 10

Node B = versão 10
```

### Read-your-writes

Se eu alterei:

```text
name = "Lucas"
```

minha próxima leitura não deveria responder:

```text
name = "João"
```

### Monotonic reads

Se já observei:

```text
version = 10
```

uma leitura posterior não deveria retornar:

```text
version = 8
```

O ponto para entrevista é:

> **consistência não é simplesmente ligada ou desligada; existem diferentes garantias que podem ser escolhidas pelo negócio.**

---

# 6. Partial Failure

Esse é um dos conceitos mais importantes.

Em um monólito:

```text
methodA()
   ↓
methodB()
```

se `methodB` retorna, normalmente sabemos que ela executou.

Em sistemas distribuídos:

```text
Order Service
      ↓ HTTP
Payment Service
```

podemos ter:

```text
Payment processou ✓

Resposta voltou ✗
```

Do ponto de vista do Order:

```text
timeout
```

Mas timeout não significa:

```text
operação não aconteceu
```

Pode significar:

> **Eu não sei o que aconteceu.**

Essa ambiguidade é fundamental em sistemas distribuídos. A AWS destaca justamente que falhas remotas podem ser parciais ou transitórias e que timeout/retry precisam ser projetados levando em conta efeitos colaterais. 

---

# 7. Timeout + Retry pode duplicar efeito

Imagine:

```text
Order
  ↓
POST /payments
  ↓
Payment processa
  ↓
cobra cartão
  ↓
resposta se perde
  ↓
TIMEOUT
```

Order pensa:

```text
falhou
```

e executa:

```text
RETRY
```

Resultado possível:

```text
Cobrança 1
Cobrança 2
```

Esse é o motivo pelo qual:

> **Retry e idempotência precisam ser estudados juntos.**

A AWS recomenda que operações remotas com side effects sejam retryable apenas quando projetadas para serem idempotentes. 

---

# 8. Idempotência

Uma operação idempotente possui o mesmo efeito lógico mesmo se for processada novamente.

Exemplo:

```text
POST /payments

Idempotency-Key:
payment-order-123
```

Primeira requisição:

```text
key não existe
      ↓
processa pagamento
      ↓
salva resultado
```

Retry:

```text
mesma key
    ↓
já processada
    ↓
não cobra novamente
    ↓
retorna resultado anterior
```

---

# 9. Idempotency-Key + UNIQUE

Um padrão robusto é:

```text
Request
   ↓
Idempotency-Key
   ↓
Database
   ↓
UNIQUE constraint
```

Por exemplo:

```text
payments

id
order_id
idempotency_key UNIQUE
status
result
```

Duas threads recebem simultaneamente:

```text
payment-order-123
```

Uma consegue inserir.

A outra recebe conflito da constraint.

Isso é melhor do que apenas:

```java
if (!repository.exists(key)) {
    process();
}
```

porque existe uma race condition entre:

```text
exists
```

e:

```text
insert
```

A constraint no banco fornece a proteção final contra concorrência duplicada.

---

# 10. Mensagens duplicadas

Em messaging, duplicatas não devem ser tratadas como evento impossível.

Imagine:

```text
Kafka
  ↓
Consumer
  ↓
atualiza banco
  ↓
consumer crash
  ↓
antes do acknowledgement
```

O broker pode entregar novamente:

```text
mesma mensagem
```

Se o consumidor executa:

```text
saldo = saldo - 100
```

duas vezes, temos erro.

Por isso consumers precisam frequentemente ser **idempotentes**. 

---

# 11. Inbox Pattern

Uma abordagem é manter:

```text
processed_messages

consumer
message_id
```

com:

```text
UNIQUE(consumer, message_id)
```

Fluxo:

```text
Message
   ↓
BEGIN
   ↓
INSERT inbox/message_id
   ↓
já existe?
   ├── sim → ignora
   │
   └── não
        ↓
   executa negócio
        ↓
      COMMIT
```

Assim, registro da mensagem e alteração de negócio podem ocorrer atomicamente no mesmo banco.

Esse é o princípio do **Idempotent Consumer / Inbox**. 

---

# 12. `@Transactional` não resolve transação distribuída

Imagine:

```java
@Transactional
public void checkout() {

    orderRepository.save(order);

    paymentClient.pay();

    inventoryClient.reserve();
}
```

Pode parecer:

```text
@Transactional

Order
Payment
Inventory
```

Mas não é assim.

Normalmente a transação Spring protege algo como:

```text
Order Service
     ↓
Order Database
```

A chamada:

```text
Payment Service
```

possui:

```text
outra aplicação
outro processo
outra conexão
outra transação
outro banco
```

Então podemos ter:

```text
Order DB commit ✓

Payment ✓

Inventory ✗
```

Não existe um rollback mágico capaz de desfazer tudo.

---

# 13. Saga

Saga resolve esse problema modelando a operação distribuída como:

```text
sequência de transações locais
```

Por exemplo:

```text
Create Order
     ↓
Reserve Inventory
     ↓
Authorize Payment
     ↓
Create Shipment
```

Cada serviço faz:

```text
BEGIN
operação local
COMMIT
```

Se algo falha:

```text
compensation
```

pode ser necessária. 

---

# 14. Compensation não é rollback

Esse detalhe é importante.

Rollback:

```text
UPDATE ainda não confirmado
        ↓
ROLLBACK
        ↓
como se não tivesse acontecido
```

Compensation:

```text
operação já aconteceu
       ↓
outra operação tenta
neutralizar seu efeito
```

Por exemplo:

```text
Inventory Reserved
      ↓

Payment Failed
      ↓

Release Inventory
```

`Release Inventory` é uma nova operação de negócio.

Não é um rollback do banco anterior.

---

# 15. Saga por Orchestration

Existe um coordenador explícito.

```text
             Saga Orchestrator
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     Payment    Inventory    Shipping
```

Exemplo:

```text
Orchestrator
    ↓
Reserve Inventory
    ↓
Authorize Payment
    ↓
Create Shipping
```

Em falha:

```text
Payment failed
     ↓
Orchestrator
     ↓
Release Inventory
```

### Vantagens

O fluxo fica explícito.

É mais fácil saber:

```text
qual etapa estamos
qual falhou
qual compensação executar
```

### Riscos

O orchestrator pode ficar:

```text
grande
complexo
centralizador de regras
```

se responsabilidades não forem bem separadas.

---

# 16. Saga por Choreography

Não existe coordenador central explícito.

```text
OrderCreated
      ↓
Payment Service
      ↓
PaymentApproved
      ↓
Inventory Service
      ↓
InventoryReserved
      ↓
Shipping Service
```

Cada serviço:

```text
consome um evento
     ↓
executa
     ↓
publica outro evento
```

### Vantagens

Menor coordenação central.

Boa independência entre participantes.

### Riscos

Com muitos passos:

```text
A → B → C → D → E → F
```

fica difícil responder:

> Quem controla esse processo?

O fluxo pode ficar espalhado por muitos serviços e difícil de visualizar, testar e diagnosticar. Choreography e orchestration são as duas abordagens típicas de Saga e possuem trade-offs distintos de coordenação. 

---

# 17. Orchestration x Choreography

Para entrevista, memorize:

| Orchestration | Choreography |
|---|---|
| Coordenador explícito | Sem coordenador central |
| Fluxo fácil de visualizar | Fluxo distribuído |
| Melhor para processos complexos | Boa para fluxos menores |
| Centraliza coordenação | Maior desacoplamento |
| Orchestrator pode crescer demais | Pode virar uma cadeia difícil de entender |

Minha heurística seria:

```text
Saga simples
     ↓
Choreography pode funcionar bem


Saga longa e complexa
     ↓
Orchestration geralmente facilita
coordenação e observabilidade
```

Não é regra absoluta.

---

# 18. O problema DB + Kafka

Agora chegamos a um dos problemas mais importantes.

Imagine:

```java
@Transactional
public void createOrder() {

    repository.save(order);

}
```

Commit acontece:

```text
Order DB ✓
```

Depois:

```java
kafka.send(new OrderCreated());
```

Mas a aplicação cai exatamente entre os dois.

Resultado:

```text
Order DB
   ✓

Kafka
   ✗
```

Então temos:

```text
pedido existe

mas ninguém recebeu
OrderCreated
```

Esse é o problema de **dual write**.

---

# 19. Publicar Kafka antes do commit também não resolve

Talvez alguém pense:

```text
primeiro Kafka
depois banco
```

Agora pode acontecer:

```text
Kafka publish ✓

DB commit ✗
```

Consumidores receberam:

```text
OrderCreated
```

mas:

```text
Order não existe
```

Portanto:

```text
DB primeiro
```

não resolve.

E:

```text
Kafka primeiro
```

também não resolve.

O problema é:

> **como tornar alteração do banco e intenção de publicar atomicamente conectadas sem depender de uma transação distribuída entre banco e broker?**

---

# 20. Transactional Outbox

A solução é salvar:

```text
Order
+
Outbox Event
```

na **mesma transação local**.

```text
BEGIN

INSERT Order

INSERT Outbox(
    OrderCreated
)

COMMIT
```

Agora:

```text
Database Transaction

Order ✓
Outbox ✓
```

ou:

```text
Order ✗
Outbox ✗
```

Nunca:

```text
Order ✓
Outbox ✗
```

Depois:

```text
Outbox
   ↓
Publisher / CDC
   ↓
Kafka
```

Esse é o Transactional Outbox Pattern. 

---

# 21. Outbox não significa exactly-once

Esse detalhe é muito importante.

Imagine:

```text
Publisher
   ↓
Kafka publish ✓
   ↓
CRASH
   ↓
antes de marcar evento
como publicado
```

Quando voltar:

```text
Publisher
   ↓
publica novamente
```

Agora Kafka pode receber:

```text
OrderCreated
OrderCreated
```

Portanto:

> **Outbox resolve atomicidade entre mudança local e intenção de publicação, mas consumidores ainda precisam tolerar duplicatas.**

A própria descrição do padrão destaca que o relay pode publicar uma mensagem mais de uma vez. 

É por isso que os padrões se complementam:

```text
Outbox
   +
Inbox
   +
Idempotency
```

---

# 22. O trio importante

Para memorizar:

```text
OUTBOX
    ↓
garante que mudança local
e evento sejam persistidos juntos


INBOX
    ↓
registra mensagens recebidas


IDEMPOTENCY
    ↓
garante que retry/duplicata
não repita o efeito de negócio
```

Esse trio aparece constantemente em sistemas distribuídos confiáveis.

---

# 23. Lost e Out-of-order Messages

Não basta pensar em duplicidade.

Também pode existir:

```text
mensagem não entregue ainda
```

ou:

```text
ordem lógica diferente
```

Imagine:

```text
OrderCreated
OrderCancelled
```

mas o consumidor observa:

```text
OrderCancelled
OrderCreated
```

Se ordem importa, precisamos pensar em:

```text
partition key
aggregate ID
sequence number
version
ordering guarantees
```

Isso será ainda mais importante quando entrar profundamente em Kafka.

---

# 24. Split Brain

Imagine um cluster:

```text
       Cluster

Node A ── Node B ── Node C
```

A rede quebra:

```text
Node A        Node B ── Node C
   X
```

Se ambos os lados acreditarem:

```text
"Eu sou o líder"
```

podemos ter:

```text
duas autoridades
```

aceitando operações conflitantes.

Isso é **split brain**.

Por isso sistemas distribuídos utilizam mecanismos como:

```text
quorum
consensus
leader election
fencing
```

para impedir duas partes do sistema de agirem simultaneamente como autoridade legítima.

---

# 25. O mapa mental mais importante

Quando você fizer uma operação remota, pense:

```text
Request
   ↓
pode atrasar
   ↓
pode falhar
   ↓
pode ter processado
sem responder
   ↓
retry pode duplicar
   ↓
mensagem pode duplicar
   ↓
eventos podem chegar fora de ordem
```

Então pergunte:

```text
Tenho timeout?

Retry é seguro?

A operação é idempotente?

Tenho uma chave única?

Quem é source of truth?

Qual consistência o negócio precisa?

O que acontece em partial failure?

Existe compensação?

Como publico o evento com segurança?

Como trato duplicatas?
```

Essa mentalidade é o verdadeiro conteúdo de sistemas distribuídos.

---

# Resposta objetiva para entrevista

> Em sistemas distribuídos, eu parto da premissa de que rede e componentes podem falhar parcialmente. Um timeout, por exemplo, não significa necessariamente que a operação falhou; ela pode ter sido executada e apenas a resposta não ter chegado. Por isso retries precisam ser combinados com idempotência. 
>
> Em consistência, considero os trade-offs de CAP e PACELC. Durante uma network partition, normalmente existe uma escolha entre preservar consistência forte ou continuar disponível. Mesmo sem falha, PACELC lembra que pode existir trade-off entre consistência e latência. 
>
> Para idempotência em operações críticas, utilizo uma `Idempotency-Key` persistida e protegida por uma constraint única, evitando que retries concorrentes produzam efeitos duplicados. Em consumers aplico o mesmo princípio através de Inbox ou registro de IDs processados. 
>
> Também separo transação local de transação distribuída. `@Transactional` normalmente protege o banco local daquele serviço; ele não faz rollback automaticamente em outros microsserviços. Quando uma operação atravessa vários serviços, posso utilizar Saga, dividindo o processo em transações locais e compensações. A Saga pode ser orquestrada, com um coordenador explícito, ou coreografada através de eventos. 
>
> Para o problema de atualizar o banco e publicar um evento sem correr o risco de `DB commit` funcionar e `Kafka publish` falhar, utilizo Transactional Outbox. A alteração de negócio e o evento são persistidos na mesma transação local e um publisher publica posteriormente. Como esse publisher pode entregar novamente uma mensagem após uma falha, os consumers continuam precisando ser idempotentes. 
>
> Então, para mim, projetar sistemas distribuídos significa assumir **falhas parciais, mensagens duplicadas, estados temporariamente inconsistentes e ausência de uma transação ACID global**, e construir mecanismos explícitos de idempotência, compensação, mensageria confiável e observabilidade para lidar com isso.
