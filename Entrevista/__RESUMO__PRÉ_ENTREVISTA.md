# Exceptions

- Exceptions como parte do design da aplicação.
  - Primeiro separo exceptions de domínio de exceptions técnicas.  
    - `Exceptions de domínio`: representam situações semanticamente relevantes
    - `Exceptions técnicas` : representam falhas de infraestrutura.
      
  - Evito capturar exceptions indiscriminadamente em todas as camadas e deixo a propagação acontecer até um ponto que realmente tenha contexto para recuperar, traduzir ou responder à falha.

- Em aplicações Spring, centralizo a tradução para HTTP utilizando RestControllerAdvice.
  - O domínio não conhece status HTTP. A camada HTTP transforma exceptions em respostas coerentes, usando ProblemDetail e códigos de erro estáveis.

- Também diferencio validação, conflito, recurso inexistente e falha inesperada.
  - Para erros inesperados, considero logging estruturado, trace ID, métricas e observabilidade.
  - E evito expor stack traces ou detalhes internos ao cliente.
---
# Spring Core e JPA

- Spring core é baseado em `Inversion of Control IoC e Dependency Injection`, a aplicação declara o que precisa por meio de construtores.
  - `ApplicationContext` funciona como IoC Container, criando e gerenciando os Beans, resolvendo dependências, controlando scopes e lifecycle e permitindo que infraestrutura como AOP e proxies seja aplicada.
  - `Component scanning` encontra classes anotadas com estereótipos como Component, Service e Repository.
    - Podemos registrar Beans explicitamente através de métodos Bean, principalmente quando queremos controlar a construção ou integrar classes externas.
        
  - `Exceptions técnicas` : representam falhas de infraestrutura.
      
- JPA é uma especificação de ORM, enquanto Hibernate é uma de suas principais implementações.
  - O `EntityManager`: gerencia um Persistence Context. Dentro desse contexto, as entidades podem estar em estados como transient, managed, detached e removed.

- Do ponto de vista de performance, eu tomo cuidado principalmente com N mais um, carregamento Eager excessivo, associações Lazy utilizadas fora do fetch plan esperado, paginação combinada com fetch de coleções, crescimento do Persistence Context e operações em massa.

  - Não utilizaria Eager como solução automática para N mais um.

  - Escolheria a estratégia de consulta de acordo com o caso de uso, utilizando Join Fetch, Entity Graph, batch fetching.
 ---
# Resiliência

  - **Falha ou Lentidão** em chaamdas: defino timeouts explícitos e evito chamadas bloqueadas indefinidamente.
  - **Falhas transitórias**: posso utilizar `retry` com limite, quando a operação for segura para repetição ou possuir idempotência.
  - **Falhas Contínuas**: utilizo `circuit breaker` para evitar continuar 
pressionando um serviço degradado.
  - **Garantia de consistência** entre uma transação local e publicação de eventos, posso utilizar `Transactional Outbox`. 
  - **Impedir Consumo** de todos os recusos uso `bulkhead`
  - **Excesso de tráfego**: `Rate limiting` ajuda a proteger serviços .
  - **Reduzir acoplamento temporal**, considero `comunicação assíncrona` através de `filas ou eventos`.
    - Devo tratar duplicidade, idempotência, retry, Dead Letter Queue e eventual consistency.
#### Observabilidade
Eu monitoraria latência, taxa de erro, retries, circuit breakers, filas, pools de conexão e recursos da aplicação, utilizando logs estruturados, métricas e distributed tracing.
--- 

# Microsserviços 
- Cada serviço é **dono dos próprios dados**, evitando banco compartilhado.
  - Quando um serviço precisa de informações de outro, uso contratos bem definidos, normalmente APIs ou eventos.

- Na comunicação entre serviços:
  - **HTTP ou gRPC quando a resposta é necessária imediatamente**
  - **Comunicação assíncrona com Kafka ou filas**, quando quero desacoplamento temporal, absorção de picos ou processamento eventual.

- Evito cadeias síncronas longas, pois aumentam latência e reduzem a disponibilidade.
- Para resiliência, aplico mecanismos como **timeout, circuit breaker, bulkhead, rate limiting e retry com critério**.

- `@Transactional` local não resolve o problema distribuído. Nesse cenário, trabalho com **Saga, compensações, Transactional Outbox, idempotência e eventual consistency**.

- Em microsserviços uso **logs estruturados, métricas e distributed tracing**, com `traceId`, para conseguir acompanhar uma requisição atravessando vários serviços.
--- 

# 1. Como funciona uma Virtual Thread?
> Uma thread virtual é mais leve e roda sobre a plataform thread.
>
> Ao executar operações bloqueantes I/O bound, a JVM suspende a thread virtual e libera a plataform thread
>
> Permite suportar maior número de operações concorrentes
>
> Não tornam uma operação CPU-bound mais rápida e não eliminam limites externos, como pool de conexões 


# 2. Como o Kafka funciona internamente?

## Resposta em português

> É um log distribuído de eventos organizado em tópicos, e cada tópico é dividido em partições.
>
> O producer envia um evento para uma partição, normalmente com base em uma chave.
>
> O broker líder dessa partição grava o evento sequencialmente no log e o replica para outros brokers.
>
> Os consumers leem os eventos e controlam até qual offset processaram.
---

# Como funciona idempotência?

> Idempotencia é executar repetidamente com o mesmo identificador sem produzir efeitos adicionais.
>
> Produtor idempotente evita que retries gerem eventos duplicadas
>
> Consumidor idempotente causar o mesmo efeito ao consumir o evento uma ou mais vezes
>

# Como garantir consistência?

## Resposta em português

> Primeiro, eu definiria qual nível de consistência o negócio realmente exige.
>
> Dentro de um único banco de dados, utilizaria transações ACID, constraints e controle de concorrência. 
>
> Para fluxos distribuídos, utilizaria consistência eventual, eventos de negócio, consumidores idempotentes, Outbox Pattern e, quando necessário, Sagas com compensações.
>


---

# 6. Como funciona uma transação distribuída?

## Resposta em português

> Uma operação que passa por diferentes sistemas
>
> Uma abordagem clássica é o Two-Phase Commit. 
>
> Primeiro, um coordenador pergunta se todos os participantes estão preparados para confirmar. Depois, se todos responderem positivamente, ele envia o commit. Mas gera acoplamento, prefiro SAGA
>
> Na Saga, cada serviço executa sua própria transação local. Se uma etapa posterior falhar, são executadas ações compensatórias para desfazer logicamente as etapas anteriores.

---

# 7. Como projetar um microsserviço?

## Resposta em português

> Eu começaria pelo domínio e pela responsabilidade de negócio, não pela infraestrutura.
>
> O serviço deve possuir seus próprios dados e uma API ou contrato de eventos bem definido.
>
> Depois definiria requisitos não funcionais, como disponibilidade, volume, latência, segurança e consistência.
>
> Também projetaria tratamento de falhas, idempotência, observabilidade, versionamento de contrato, testes e estratégia de deploy desde o início.
>
> Antes de criar um novo microsserviço, eu validaria se a autonomia e a escalabilidade justificam o custo distribuído. Em vários cenários, um monólito modular é uma solução melhor.


# 3. Quando usar Redis?

## Resposta em português

> Eu usaria Redis quando precisasse de acesso de baixa latência a dados temporários ou frequentemente consultados.
>
> Casos comuns incluem cache, rate limiting, idempotency keys e dados com expiração.
>
> Antes de adotá-lo, eu avaliaria se os dados podem ser reconstruídos, qual política de expiração será usada, e qual comportamento a aplicação terá se o Redis ficar indisponível.
>

---

# 8. Como reduzir latência?

> Eu começaria medindo a latência ponta a ponta e identificando onde o tempo está sendo gasto.
>
> Utilizaria métricas p95 e p99, e tracing distribuído para decompor o tempo entre aplicação, banco, cache, mensageria e APIs externas.
>
> Depois atuaria no gargalo real. Poderia ser uma query sem índice, pool esgotado, lock, garbage collection 
>

---

# 9. Como fazer observabilidade?

## Resposta em português

> Eu implementaria observabilidade combinando métricas, logs estruturados e tracing distribuído.
>
> Para serviços, monitoraria taxa de requisições, erros e duração.
>
> Para infraestrutura, observaria utilização, saturação e erros.
>
> Os logs conteriam contexto, como serviço, versão, ambiente, trace ID e identificadores de negócio, sem dados sensíveis.
>
> Utilizaria OpenTelemetry para propagar traces entre APIs, bancos e mensageria. Também criaria dashboards, e alertas baseados no impacto ao usuário.
>

---

# Como investigar memória alta na JVM?

## Resposta em português

> Primeiro, eu confirmaria se o problema está no heap, metaspace, direct memory, thread stacks ou na memória nativa do processo.
>
> Analisaria métricas como heap utilizado, frequência e duração do garbage collector, taxa de alocação, quantidade de threads e comportamento após full GC.
>
> Também compararia o incidente com deploys recentes, aumento de tráfego e mudanças de configuração.
>

## Explicação

### Tipos de problema

| Sintoma                      | Possível causa                |
| ---------------------------- | ----------------------------- |
| Heap cresce continuamente    | Memory leak                   |
| Heap oscila em nível elevado | Carga ou heap pequeno         |
| Full GC frequente            | Pressão de memória            |
| Muitas threads               | Thread leak                   |
| RSS alto e heap normal       | Memória nativa/direct buffers |
| Metaspace crescente          | Classloader leak              |


# Como otimizar PostgreSQL?

## Resposta em português

> Eu começaria pelas queries realmente mais custosas, usando métricas e `pg_stat_statements`.
>
> Depois utilizaria `EXPLAIN ANALYZE` para comparar as estimativas do planner com a execução real, avaliando scans, joins, cardinalidade, ordenações, memória e leituras de disco.
>
> Com base nisso, poderia criar índices, reescrever consultas, remover N+1, particionar tabelas ou ajustar o modelo.
>
> Também avaliaria pool de conexões, locks, e parâmetros de memória.
>
> Toda alteração seria validada com dados representativos, porque um índice que melhora uma leitura pode aumentar o custo de escrita e armazenamento.


# 12. Como fazer deploy sem downtime?
> Eu utilizaria uma estratégia em que a nova versão fosse disponibilizada antes da remoção da versão anterior.
>
> Poderia usar rolling deployment, blue-green ou canary.
>
> Também garantiria graceful shutdown para que instâncias em encerramento terminassem requisições em andamento e parassem de receber novas chamadas.
>
> Depois do deploy, validaria métricas técnicas e de negócio antes de continuar a expansão.

## Explicação

### Rolling deployment

Substitui instâncias gradualmente.

```text
v1 v1 v1
v2 v1 v1
v2 v2 v1
v2 v2 v2
```

### Blue-green

```text
Blue: versão atual
Green: nova versão

Tráfego troca de Blue para Green
```

### Canary

```text
95% → versão antiga
5%  → versão nova
```

### Migração segura de banco

Errado:

```text
Renomear coluna imediatamente
→ versão antiga quebra
```

Correto:

```text
1. Adicionar nova coluna
2. Aplicação escreve nas duas
3. Migrar dados
4. Aplicação usa nova coluna
5. Remover coluna antiga posteriormente
```
---

# 14. Como implementar Circuit Breaker?

## Resposta em português

> Eu colocaria o Circuit Breaker ao redor de uma chamada remota sujeita a falhas, combinando-o com timeout.
>
> Inicialmente, o circuito fica fechado e permite chamadas. 
>
>Caso haja falhas dentro de uma janela, o circuito abre e rejeita novas tentativas.
>
> Depois de um período, ele passa para half-open e permite algumas chamadas de teste.
>
> Se tiver se recuperado, volta para closed. Caso contrário, abre novamente.
>
 
# Resumo para revisão

| Pergunta              | Conceito central                             |
| --------------------- | -------------------------------------------- |
| Virtual Threads       | Concorrência leve para I/O                   |
| Kafka                 | Log distribuído particionado                 |
| Redis                 | Baixa latência e dados temporários           |
| Duplicação            | Consumer idempotente                         |
| Consistência          | Invariantes e nível necessário               |
| Transação distribuída | Saga ou 2PC                                  |
| Microsserviço         | Capacidade de negócio coesa                  |
| Latência              | Medir antes de otimizar                      |
| Observabilidade       | Métricas, logs e traces                      |
| Memória JVM           | Identificar área e retenção                  |
| PostgreSQL            | Plano real com `EXPLAIN ANALYZE`             |
| Zero downtime         | Compatibilidade e implantação gradual        |
| Rollback              | Artefato versionado e banco compatível       |
| Circuit Breaker       | Interromper chamadas a dependência degradada |
| Retry                 | Falha transitória, backoff e jitter          |
| Idempotência          | Repetição sem efeito adicional               |

 

