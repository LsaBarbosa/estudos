
# 1. Como funciona uma Virtual Thread?

## Resposta em português

> Virtual Threads são threads leves gerenciadas pela JVM.
>
> Quando uma Virtual Thread executa uma operação bloqueante de I/O, como consulta ao banco, a JVM pode suspender essa Virtual Thread e liberar a platform thread subjacente para executar outra tarefa.
>
> Isso permite trabalhar com um modelo simples de uma thread por requisição, mas suportando uma quantidade muito maior de operações concorrentes.
>
> Elas são especialmente adequadas para aplicações I/O-bound. 
>
>Não tornam uma operação CPU-bound mais rápida e não eliminam limites externos, como pool de conexões, banco de dados ou APIs remotas.
o.

## Answer in English

> Virtual Threads are lightweight threads managed primarily by the JVM.
>
> When a Virtual Thread performs a blocking I/O operation, such as database call, the JVM can suspend it and release the underlying platform thread to execute another task.
>
> This allows us to keep the simple thread-per-request programming model while supporting a much larger number of concurrent operations

---

# 2. Como o Kafka funciona internamente?

## Resposta em português

> Kafka é um log distribuído de eventos organizado em tópicos, e cada tópico é dividido em partições.
>
> O producer envia um evento para uma partição, normalmente com base em uma chave.
>
> O broker líder dessa partição grava o evento sequencialmente no log e o replica para outros brokers.
>
> Cada evento recebe um offset, que identifica sua posição dentro da partição.
>
> Os consumers leem os eventos e controlam até qual offset processaram.
>
> Dentro de um consumer group, cada partição é atribuída a apenas um consumer por vez.
>
> Isso permite processamento paralelo mantendo a ordenação dentro de cada partição.
>
> Kafka não remove o evento assim que ele é consumido.
>
> A retenção é definida por tempo ou tamanho, o que permite replay e reprocessamento.

## Answer in English

> Kafka is a distributed event log organized into topics, and each topic is divided into partitions.
>
> A producer sends an event to a partition, usually based on a record key. 
>
> Each event receives an offset representing its position within the partition.
>
> Consumers read events and track the offsets they have processed.
>
> Within a consumer group, each partition is assigned to only one consumer at a time. This enables parallel processing while preserving ordering within each partition.
>
> Kafka does not delete an event immediately after consumption. Events are retained according to time- or size-based policies, enabling replay and reprocessing.

---
# 16. Como funciona idempotência?

## Resposta em português

> Uma operação idempotente pode ser executada repetidamente com o mesmo identificador sem produzir efeitos adicionais.
>
> Em uma API de pagamento, o cliente envia uma idempotency key. O servidor registra essa chave com o resultado da primeira execução.
>
> Se a mesma requisição chegar novamente, o servidor não realiza uma nova cobrança. Ele retorna o resultado previamente armazenado.
>
> A validação da chave e a alteração de negócio devem ser atômicas. Também é necessário decidir o escopo, o tempo de retenção e como tratar uma chave reutilizada com payload diferente.


## Answer in English

> An idempotent operation can be executed repeatedly with the same identifier without producing additional side effects.
>
> In a payment API, for example, the client sends an idempotency key. The server records that key together with the result of the first execution.
>
> If the same request arrives again, the server does not perform another charge. It returns the previously stored result.
>
> The key validation and business state change must be atomic. It is also necessary to define the key scope, retention period, and how to handle the same key being reused with a different payload.


---

# 4. Como evitar mensagens duplicadas?

## Resposta em português

> Eu assumiria que mensagens duplicadas podem ocorrer, especialmente em modelos at-least-once.
>
> Para lidar com isso, cada evento teria um identificador único e o consumer seria idempotente.
>
> Antes de aplicar o efeito, ele verificaria se aquele identificador já foi processado.
>
> A verificação e a alteração de negócio devem ocorrer de forma atômica, normalmente na mesma transação do banco.
>
> No producer Kafka, eu habilitaria idempotência para impedir duplicações provocadas por retries durante a produção.
>
> Entretanto, isso não substitui a idempotência do consumer, porque duplicações podem ocorrer em outras etapas do fluxo.


## Answer in English

> I would assume that duplicate messages can occur, especially with at-least-once delivery.
>
> Each event would have a unique identifier, and the consumer would be designed to be idempotent. 
>
> The deduplication check and the business state change should be performed atomically, usually within the same database transaction.
>
> On the Kafka producer side, I would enable idempotence to prevent duplicates caused by producer retries. However, this does not replace consumer idempotency because duplicates may still appear at other points in the workflow.

---
# 15. Como funciona Retry?

## Resposta em português

> Retry consiste em executar novamente uma operação que falhou, mas deve ser usado apenas para falhas transitórias.
>
> Eu definiria um número limitado de tentativas, timeout por tentativa e backoff exponencial com jitter.
>
> Não faria retry para erros determinísticos, como validação inválida, autenticação negada ou recurso inexistente.
>
> A operação também precisa ser idempotente, porque a primeira tentativa pode ter sido executada com sucesso mesmo que a resposta não tenha chegado.
>
> Em mensageria, depois do limite de tentativas, eu enviaria a mensagem para uma fila de retry ou dead-letter queue, preservando contexto para investigação.


## Answer in English

> Retry means executing a failed operation again, but it should only be used for transient failures.
>
> I would define a limited number of attempts, a timeout for each attempt, and exponential backoff with jitter.
>
> I would not retry deterministic failures such as invalid input, authentication failures, authorization failures, or missing resources.
>
> The operation should also be idempotent because the first attempt may have completed successfully even if the response was lost.
>
> In messaging systems, after the retry limit is reached, I would send the message to a retry topic or dead-letter queue while preserving enough context for investigation.

---



# 5. Como garantir consistência?

## Resposta em português

> Primeiro, eu definiria qual nível de consistência o negócio realmente exige.
>
> Dentro de um único banco de dados, utilizaria transações ACID, constraints e controle de concorrência. 
>
> Para fluxos distribuídos, utilizaria consistência eventual, eventos de negócio, consumidores idempotentes, Outbox Pattern e, quando necessário, Sagas com compensações.
>

## Answer in English

> First, I would define the consistency level that the business actually requires.
>
> Within a single database, I would use ACID transactions, constraints, and concurrency control. 
>
> For distributed workflows, I would use eventual consistency, business events, idempotent consumers, the Transactional Outbox Pattern, and, when necessary, Sagas with compensating actions.
>

---

# 6. Como funciona uma transação distribuída?

## Resposta em português

> Uma transação distribuída envolve alterações em mais de um recurso independente, como dois bancos ou um banco e outro sistema.
>
> Uma abordagem clássica é o Two-Phase Commit. Primeiro, um coordenador pergunta se todos os participantes estão preparados para confirmar. Depois, se todos responderem positivamente, ele envia o commit.
>
> O problema é que isso gera forte acoplamento, bloqueios e dependência do coordenador. Por isso, em microsserviços, geralmente prefiro uma Saga.
>
> Na Saga, cada serviço executa sua própria transação local. Se uma etapa posterior falhar, são executadas ações compensatórias para desfazer logicamente as etapas anteriores.


## Answer in English

> A distributed transaction involves changes across more than one independent resource, such as two databases or multiple services.
>
> A traditional approach is Two-Phase Commit. First, a coordinator asks all participants whether they are prepared to commit. If every participant agrees, the coordinator sends the final commit instruction.
>
> The drawbacks are strong coupling, resource locking, and dependence on the coordinator. For that reason, in microservice architectures I would usually prefer a Saga.
>
> In a Saga, each service performs its own local transaction. If a later step fails, compensating actions are executed to logically reverse the previous steps.

---

# 7. Como projetar um microsserviço?

## Resposta em português

> Eu começaria pelo domínio e pela responsabilidade de negócio, não pela infraestrutura.
>
> O serviço deve possuir uma capacidade de negócio coesa, seus próprios dados e uma API ou contrato de eventos bem definido.
>
> Depois definiria requisitos não funcionais, como disponibilidade, volume, latência, segurança e consistência.
>
> Também projetaria tratamento de falhas, idempotência, observabilidade, versionamento de contrato, testes e estratégia de deploy desde o início.
>
> Antes de criar um novo microsserviço, eu validaria se a autonomia e a escalabilidade justificam o custo distribuído. Em vários cenários, um monólito modular é uma solução melhor.


## Answer in English

> I would start with the business domain and business capability, not with infrastructure.
>
> The service should own a cohesive business responsibility, its own data, and a well-defined API or event contract.
>
> I would then define non-functional requirements such as availability, traffic volume, latency, security, and consistency.
>
> Failure handling, idempotency, observability, contract versioning, testing, and deployment strategy should also be designed from the beginning.
>
> Before creating a new microservice, I would validate whether the required autonomy and scalability justify the distributed-system complexity. In many scenarios, a modular monolith is the better choice.


# 3. Quando usar Redis?

## Resposta em português

> Eu usaria Redis quando precisasse de acesso de baixa latência a dados temporários ou frequentemente consultados.
>
> Casos comuns incluem cache, rate limiting, idempotency keys e dados com expiração.
>
> Antes de adotá-lo, eu avaliaria se os dados podem ser reconstruídos, qual política de expiração será usada, e qual comportamento a aplicação terá se o Redis ficar indisponível.
>

## Answer in English

> I would use Redis when I need low-latency access to temporary or frequently accessed data.
>
> Common use cases include caching, rate limiting, idempotency keys, and data with expiration.
>
> Before adopting it, I would evaluate whether the data can be rebuilt, which expiration policy should be used, and how the application should behave if Redis becomes unavailable.
>
> 


---

# 8. Como reduzir latência?

## Resposta em português

> Eu começaria medindo a latência ponta a ponta e identificando onde o tempo está sendo gasto.
>
> Utilizaria métricas por percentis, principalmente p95 e p99, e tracing distribuído para decompor o tempo entre aplicação, banco, cache, mensageria e APIs externas.
>
> Depois atuaria no gargalo real. Poderia ser uma query sem índice, excesso de chamadas remotas, serialização, pool esgotado, lock, garbage collection ou processamento sequencial.
>
> Eu evitaria aplicar cache ou paralelismo sem evidência, porque isso pode apenas esconder o problema ou aumentar a complexidade.

## Answer in English

> I would start by measuring end-to-end latency and identifying where the time is being spent.
>
> I would use percentile metrics, especially p95 and p99, together with distributed tracing to break down the time across the application, database, cache, message broker, and external APIs.
>
> I would then address the actual bottleneck. It could be a missing index, excessive remote calls, serialization overhead, an exhausted connection pool, lock contention, garbage collection, or sequential processing.
>
> I would avoid introducing caching or parallelism without evidence, because those approaches may only hide the problem or add unnecessary complexity.

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
> Utilizaria OpenTelemetry para propagar traces entre APIs, bancos e mensageria. Também criaria dashboards, SLIs, SLOs e alertas baseados no impacto ao usuário.
>
> O objetivo não é apenas detectar que algo falhou, mas conseguir investigar por que falhou.


## Answer in English

> I would implement observability by combining metrics, structured logs, and distributed tracing.
>
> For services, I would monitor request rate, errors, and duration. For infrastructure, I would monitor utilization, saturation, and errors.
>
> Logs would include contextual information such as service name, application version, environment, trace ID, and business identifiers, while excluding sensitive data.
>
> I would use OpenTelemetry to propagate traces across APIs, databases, and messaging systems. I would also create dashboards, SLIs, SLOs, and alerts based on user impact.
>
> The goal is not only to detect that something failed, but to understand why it failed.

---

# 10. Como investigar memória alta na JVM?

## Resposta em português

> Primeiro, eu confirmaria se o problema está no heap, metaspace, direct memory, thread stacks ou na memória nativa do processo.
>
> Analisaria métricas como heap utilizado, frequência e duração do garbage collector, taxa de alocação, quantidade de threads e comportamento após full GC.
>
> Se o heap continuar crescendo e não retornar após a coleta, suspeitaria de retenção indevida. Nesse caso, coletaria um heap dump e analisaria dominator tree, retained size e caminhos até GC roots.
>
> Também compararia o incidente com deploys recentes, aumento de tráfego e mudanças de configuração.
>
> Eu evitaria aumentar o heap antes de encontrar a causa, porque isso pode apenas adiar uma falha de memória.

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

## Answer in English

> First, I would determine whether the problem is related to heap memory, metaspace, direct memory, thread stacks, or native process memory.
>
> I would analyze metrics such as heap utilization, garbage collection frequency and duration, allocation rate, thread count, and memory behavior after a full GC.
>
> If the heap continued to grow and did not decrease after garbage collection, I would suspect unintended object retention. I would collect a heap dump and analyze the dominator tree, retained size, and paths to GC roots.
>
> I would also correlate the issue with recent deployments, traffic changes, and configuration changes.
>
> I would avoid increasing the heap before identifying the cause because that may only delay an out-of-memory failure.

---

# 11. Como otimizar PostgreSQL?

## Resposta em português

> Eu começaria pelas queries realmente mais custosas, usando métricas e `pg_stat_statements`.
>
> Depois utilizaria `EXPLAIN ANALYZE` para comparar as estimativas do planner com a execução real, avaliando scans, joins, cardinalidade, ordenações, memória e leituras de disco.
>
> Com base nisso, poderia criar índices, reescrever consultas, corrigir estatísticas, remover N+1, particionar tabelas ou ajustar o modelo.
>
> Também avaliaria pool de conexões, autovacuum, locks, bloat, frequência de checkpoints e parâmetros de memória.
>
> Toda alteração seria validada com dados representativos, porque um índice que melhora uma leitura pode aumentar o custo de escrita e armazenamento.

## Explicação

`EXPLAIN ANALYZE` executa a consulta e mostra tempos e quantidade real de linhas por etapa, permitindo comparar as estimativas do planejador com a execução. ([PostgreSQL][7])

### Investigar

```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT ...
```

### Observar

* Sequential scans inesperados.
* Índices não usados.
* Estimativa de linhas incorreta.
* Nested loops muito caros.
* Sort em disco.
* Muitas leituras de buffer.
* Funções em colunas indexadas.
* Filtros pouco seletivos.
* Joins desnecessários.

### Índice não é sempre a resposta

Índices:

* Aceleram certas leituras.
* Ocupam espaço.
* Aumentam custo de `INSERT`, `UPDATE` e `DELETE`.
* Precisam corresponder ao padrão real de consulta.

## Answer in English

> I would start with the queries that are actually consuming the most resources, using monitoring data and `pg_stat_statements`.
>
> Then I would use `EXPLAIN ANALYZE` to compare the query planner's estimates with actual execution, evaluating scans, joins, cardinality, sorting, memory usage, and disk reads.
>
> Based on the evidence, I might create indexes, rewrite queries, update statistics, eliminate N+1 queries, partition tables, or adjust the data model.
>
> I would also evaluate connection pooling, autovacuum, locks, table bloat, checkpoint behavior, and memory settings.
>
> Every change would be tested with representative data because an index that improves reads can increase write and storage costs.

---

# 12. Como fazer deploy sem downtime?

## Resposta em português

> Eu utilizaria uma estratégia em que a nova versão fosse disponibilizada antes da remoção da versão anterior.
>
> Poderia usar rolling deployment, blue-green ou canary, dependendo do risco. A aplicação precisaria ter readiness probes para receber tráfego somente quando estivesse pronta.
>
> As mudanças de banco deveriam ser retrocompatíveis. Eu aplicaria o padrão expand-and-contract: primeiro adicionar a nova estrutura, depois atualizar a aplicação e somente em outra etapa remover a estrutura antiga.
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

## Answer in English

> I would use a deployment strategy where the new version becomes available before the old version is removed.
>
> Depending on the risk, I could use rolling deployment, blue-green deployment, or a canary release. The application would need readiness probes so that it only receives traffic after it is fully ready.
>
> Database changes must be backward compatible. I would use the expand-and-contract pattern: first add the new structure, then update the application, and only remove the old structure in a later deployment.
>
> I would also implement graceful shutdown so that terminating instances can finish in-flight requests and stop accepting new traffic.
>
> After deployment, I would validate both technical and business metrics before increasing traffic to the new version.

---

# 13. Como faria rollback?

## Resposta em português

> Eu trataria rollback como parte do planejamento do deploy, não como uma decisão improvisada depois da falha.
>
> A aplicação deveria possuir artefatos versionados e imutáveis, permitindo retornar rapidamente à versão anterior.
>
> Em blue-green, o rollback poderia ser a troca do tráfego para o ambiente anterior. Em rolling ou canary, eu interromperia a expansão e restauraria a versão estável.
>
> O maior cuidado seria com mudanças de banco. Por isso, as migrations deveriam ser retrocompatíveis e destrutivas apenas em etapas posteriores.
>
> Também avaliaria se rollback é realmente seguro. Se a nova versão já tiver produzido dados em um formato incompatível, pode ser necessário roll forward com uma correção.


## Answer in English

> I would treat rollback as part of deployment planning rather than as an improvised reaction after a failure.
>
> Application artifacts should be immutable and versioned so that the system can quickly return to the previous version.
>
> In a blue-green deployment, rollback may simply mean switching traffic back to the previous environment. In a rolling or canary deployment, I would stop the rollout and restore the stable version.
>
> Database changes require the most caution. Migrations should therefore remain backward compatible, with destructive changes postponed to later stages.
>
> I would also verify whether rollback is actually safe. If the new version has already produced data in an incompatible format, a roll-forward fix may be safer than reverting the application.

---

# 14. Como implementar Circuit Breaker?

## Resposta em português

> Eu colocaria o Circuit Breaker ao redor de uma chamada remota sujeita a falhas, combinando-o com timeout.
>
> Inicialmente, o circuito fica fechado e permite chamadas. Se a taxa de falhas ou chamadas lentas ultrapassar um limite dentro de uma janela, o circuito abre e rejeita novas tentativas rapidamente.
>
> Depois de um período, ele passa para half-open e permite algumas chamadas de teste. Se a dependência tiver se recuperado, volta para closed. Caso contrário, abre novamente.
>
> Eu configuraria os limites com base em métricas reais e definiria um fallback apenas quando houver uma resposta funcionalmente válida.

## Explicação

### Estados

```text
CLOSED
Chamadas normais
   ↓ falhas acima do limite

OPEN
Falha rápida
   ↓ após intervalo

HALF-OPEN
Chamadas de teste
   ↓ sucesso → CLOSED
   ↓ falha   → OPEN
```

### Exemplo com Resilience4j

```java
@CircuitBreaker(
    name = "paymentProvider",
    fallbackMethod = "paymentFallback"
)
public PaymentResponse processPayment(PaymentRequest request) {
    return providerClient.process(request);
}
```

### Cuidados

* Circuit Breaker não substitui timeout.
* Fallback não deve retornar sucesso falso.
* Não abrir o circuito por erro de validação.
* Monitorar transições de estado.
* Separar circuitos por dependência relevante.

## Answer in English

> I would place a Circuit Breaker around a remote call that is subject to failure, and I would combine it with a timeout.
>
> Initially, the circuit remains closed and allows calls. If the failure rate or slow-call rate exceeds a configured threshold within a given window, the circuit opens and rejects new calls immediately.
>
> After a waiting period, it moves to the half-open state and allows a limited number of test calls. If the dependency has recovered, the circuit returns to closed. Otherwise, it opens again.
>
> I would configure thresholds based on production metrics and only implement a fallback when there is a functionally valid degraded response.

---



# Estrutura curta para respostas em inglês

Quando precisar formular uma resposta sem decorar o conteúdo inteiro:

```text
First, I would understand...
Then, I would evaluate...
The main trade-off is...
For example...
Finally, I would validate...
```

Exemplo:

> First, I would understand the consistency requirements. Then, I would evaluate whether the operation can remain within a local transaction or requires a distributed workflow. The main trade-off is between strong consistency, availability, and operational complexity. For example, I might use an Outbox Pattern and a Saga across microservices. Finally, I would validate the design through failure and recovery scenarios.

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

[1]: https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html?utm_source=chatgpt.com "Virtual Threads - Java"
[2]: https://kafka.apache.org/documentation/?utm_source=chatgpt.com "Introduction | Apache Kafka"
[3]: https://redis.io/docs/latest/develop/data-types/?utm_source=chatgpt.com "Redis data types | Docs"
[4]: https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/?utm_source=chatgpt.com "Redis persistence | Docs"
[5]: https://kafka.apache.org/43/configuration/producer-configs/?utm_source=chatgpt.com "Producer Configs | Apache Kafka"
[6]: https://docs.oracle.com/en/java/javase/21/?utm_source=chatgpt.com "JDK 21 Documentation - Home"
[7]: https://www.postgresql.org/docs/current/using-explain.html?utm_source=chatgpt.com "Documentation: 18: 14.1. Using EXPLAIN"
