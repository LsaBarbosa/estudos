# Guia de revisão — Java Sênior (Java 17/21 + Spring)

> **Objetivo:** revisar os conceitos das três vagas com foco em decisões técnicas, trade-offs e respostas de entrevista. Use como checklist: se consegue explicar o tópico e citar um caso real, avance.

**Prioridade:** **P0** = dominar; **P1** = saber aplicar e discutir; **P2** = diferencial.

| Prioridade | Blocos de estudo |
| --- | --- |
| **P0** | Java, Spring Boot, REST/Security, SQL/JDBC, testes, SOLID, arquitetura, concorrência |
| **P1** | Redis, MongoDB, mensageria, microsserviços, resiliência, Docker, CI/CD, observabilidade |
| **P2** | Azure específica, Kubernetes avançado, React, Gitflow detalhado |

---

## 1. Java 17/21 e fundamentos — P0

- **POO:** encapsulamento protege invariantes; abstração expõe contratos; herança modela relação *é um*; polimorfismo permite trocar implementações. Prefira **composição** quando herança cria acoplamento.
- **`equals()` e `hashCode()`:** objetos iguais devem ter o mesmo hash; fundamentais para `HashMap` e `HashSet`. Cuidado com chaves mutáveis.
- **Records:** classes concisas para dados, com estado superficialmente imutável; ótimos para DTOs. Não garantem imutabilidade profunda de listas/objetos internos.
- **Sealed classes:** restringem as subclasses permitidas; úteis para hierarquias de domínio controladas.
- **Java 17:** `record`, `sealed`, *switch expressions*, *text blocks* e `instanceof` com *pattern matching*.
- **Java 21:** *virtual threads* (melhor escalabilidade em tarefas bloqueantes de I/O, não aceleração de CPU); *sequenced collections*; *record patterns* e *pattern matching* para `switch`.
- **Collections:** `List` mantém sequência e aceita duplicatas; `Set` evita duplicatas; `Map` associa chave/valor; `Queue` representa fila; `Deque` atende filas e pilhas.

| Estrutura | Característica e custo típico |
| --- | --- |
| `ArrayList` | Acesso por índice **O(1)**; inserção no meio **O(n)** |
| `LinkedList` | Acesso por índice **O(n)**; inserção **O(1)** quando o nó/posição já é conhecido |
| `HashMap` / `HashSet` | Busca média **O(1)**; sem garantia de ordenação |
| `TreeMap` / `TreeSet` | Elementos ordenados; busca/inserção **O(log n)** |
| `ArrayDeque` | Pilha/fila eficiente; prefira a `Stack` na maioria dos usos |
| Árvore / heap | Árvore organiza busca hierárquica; heap sustenta filas de prioridade |

- **Generics:** segurança de tipos em compilação; *type erasure* remove boa parte da informação genérica em runtime. **PECS:** `? extends T` para produzir/ler; `? super T` para consumir/escrever.
- **Stream API:** operações intermediárias (`filter`, `map`) são *lazy*; terminal (`collect`, `toList`, `reduce`) dispara execução. Evite efeitos colaterais; `parallelStream()` não é ganho automático.
- **Concorrência:** *race condition* = acesso simultâneo incorreto; *deadlock* = espera circular; `volatile` dá visibilidade, **não** torna `count++` atômico; `synchronized`/`Lock` protegem regiões críticas; `Atomic*` oferece operações atômicas; `ConcurrentHashMap` facilita acesso concorrente.
- **Execução assíncrona:** `ExecutorService` gerencia tarefas; `CompletableFuture` compõe resultados assíncronos. Virtual threads são úteis para muitas operações bloqueantes; cuide de pools de conexões e limites de serviços externos.
- **Exceções:** *checked* exigem tratamento/declaração; *unchecked* representam muitas falhas de programação/negócio. Não engula exceções nem exponha stack traces pela API.
- **Design Patterns:** **Strategy** troca algoritmos; **Factory** encapsula criação; **Adapter** integra contratos; **Decorator** adiciona comportamento; **Observer** desacopla notificações.

**Pergunta-chave:** quando usar `HashMap`, `ConcurrentHashMap`, `synchronized` ou banco de dados para garantir consistência? A resposta depende do escopo (thread, instância ou sistema distribuído).

## 2. Spring Boot, Beans e APIs REST — P0

- **IoC / DI:** o Spring gerencia criação e injeção de objetos. Prefira **injeção por construtor**: dependências explícitas e testes simples.
- **Beans:** `@Component`, `@Service`, `@Repository` registram componentes por varredura; `@Bean` declara objetos em classes `@Configuration`; `@Qualifier` e `@Primary` resolvem múltiplas implementações. Escopo padrão: **singleton por contexto**, não singleton global da JVM.
- **Spring Boot:** *starters* agrupam dependências; *auto-configuration* configura componentes condicionalmente; *profiles* e propriedades externalizam configuração; Actuator expõe sinais operacionais.
- **Spring MVC:** `@RestController` recebe HTTP, `@Service` concentra casos de uso e `@Repository` acessa persistência. Separe DTOs HTTP de entidades de banco/domínio.
- **REST:** recursos com URIs, métodos HTTP e semântica: `GET` lê, `POST` cria/processa, `PUT` substitui, `PATCH` altera parcialmente, `DELETE` remove. `GET`, `PUT` e `DELETE` têm semântica idempotente; o contrato da implementação precisa respeitá-la.
- **Status HTTP:** `200` OK, `201` criado, `204` sem corpo, `400` entrada inválida, `401` sem autenticação válida, `403` sem permissão, `404` ausente, `409` conflito, `422` regra/semântica inválida quando adotado, `500` falha interna.
- **Validação:** Jakarta Bean Validation (`@Valid`, `@NotNull`, `@Size`); validação sintática na borda, regras de negócio no domínio. Centralize erros com `@RestControllerAdvice` e estrutura consistente (ex.: *Problem Details*).
- **Segurança:** **autenticação** verifica identidade; **autorização** verifica permissões. Spring Security aplica filtros e políticas; JWT é um formato de token, **OAuth 2.0** é um framework de autorização e **OpenID Connect** adiciona identidade. Valide assinatura, expiração, emissor e público conforme o cenário; nunca confie apenas no conteúdo decodificado.
- **Transações:** `@Transactional` delimita unidade de trabalho; *rollback* padrão ocorre em exceções unchecked. A anotação é normalmente aplicada por **proxy**, portanto chamadas internas no mesmo objeto (*self-invocation*) podem ignorar a interceptação.

**Pergunta-chave:** o que acontece do `POST /pedidos` até a gravação? Explique autenticação, validação, controller, caso de uso, transação, persistência e resposta.

## 3. SQL, JDBC, Oracle, MongoDB e performance — P0/P1

- **JDBC:** API para conexões, `PreparedStatement`, parâmetros e `ResultSet`. Use parâmetros vinculados contra SQL injection, `try-with-resources` e *connection pool* (ex.: HikariCP); evite abrir conexão física por requisição.
- **Modelo relacional:** chaves primárias/estrangeiras, normalização, `JOIN`, índices, constraints e integridade referencial. `INNER JOIN` requer correspondência; `LEFT JOIN` preserva a tabela da esquerda.
- **ACID:** atomicidade, consistência, isolamento, durabilidade. Isolamento é um compromisso entre concorrência e anomalias (*dirty read*, *non-repeatable read*, *phantom read*); conheça o nível padrão e o comportamento **do banco usado**.
- **Locks e concorrência:** *optimistic locking* com coluna de versão; *pessimistic locking* bloqueia registros. Deadlocks exigem transações curtas, ordem consistente de atualização e estratégia de retry quando apropriada.
- **Índices:** aceleram filtros, junções e ordenações, mas custam escrita e espaço. Índice composto exige atenção à ordem das colunas e ao padrão de consulta. Inspecione planos com `EXPLAIN` / ferramentas do SGBD.
- **Otimização:** observe latência **p95/p99**, tamanho de páginas, consultas repetidas, `SELECT *`, N+1 do ORM, índices ausentes e número de conexões. Meça antes de otimizar.
- **Oracle:** revise `SEQUENCE`/identidades, `EXPLAIN PLAN`, plano de execução, paginação (`OFFSET ... FETCH`), controle de transação e bloqueios.
- **MongoDB:** documentos BSON, coleções, índices e *aggregation pipeline*. **Embed** dados lidos/alterados juntos; **referencie** quando houver alto crescimento, compartilhamento ou relacionamentos complexos. Modelagem nasce dos padrões de acesso.
- **SQL × NoSQL:** SQL favorece integridade relacional e junções; documento favorece agregados flexíveis. Não escolha só pela promessa genérica de performance ou escala.

**Pergunta-chave:** uma consulta passou de 50 ms para 2 s. Mostre como identificaria a causa usando métricas, plano de execução, cardinalidade, índices, N+1 e pool de conexões.

## 4. Redis e caching — P1

- **Cache-aside:** aplicação consulta o Redis; em *miss*, lê banco e popula cache. Simples, mas exige estratégia de invalidação.
- **TTL:** expiração evita dados eternamente desatualizados; não substitui invalidação ao modificar o dado.
- **Write-through / write-behind:** escrita no cache junto ao armazenamento / escrita posterior assíncrona; aumentam complexidade e exigem estratégia de falhas.
- **Riscos:** *cache stampede* (muitos misses simultâneos), *hot keys*, dados inconsistentes, limites de memória e política de eviction. Possíveis soluções: TTL com variação, coalescência/lock, métricas e invalidação por evento.
- **Redis como estrutura de dados:** strings, hashes, lists, sets, sorted sets. **Cache não deve ser a fonte de verdade** por padrão.

**Pergunta-chave:** como impedir leitura de preço antigo após atualização? Explique consistência desejada, invalidação, janela de stale data e falhas do Redis.

## 5. Clean Code, SOLID e arquiteturas — P0

- **Clean Code:** nomes que comunicam intenção, funções coesas, baixa complexidade, tratamento de erros explícito, testes e ausência de duplicação acidental. Evite abstrações antes de haver necessidade.
- **SOLID:** **S** responsabilidade única (um motivo para mudar); **O** aberto à extensão/fechado à modificação; **L** subtipo preserva contrato; **I** interfaces específicas; **D** módulos de alto nível dependem de abstrações.
- **MVC:** controller recebe interação, model representa estado/regras, view apresenta; no Spring REST é comum usar controller-service-repository, que não é toda a arquitetura do sistema.
- **MVVM:** *view* se vincula ao estado/comportamento de um *view model*; mais comum em interfaces cliente.
- **DDD:** linguagem ubíqua, *bounded contexts*, entidades (identidade), *value objects* (valor), agregados (fronteiras de consistência), repositórios (abstrações de persistência) e serviços de domínio. DDD não é só criar pastas `domain`.
- **Clean Architecture:** dependências de código apontam para regras centrais; detalhes de banco/framework são externos. **Hexagonal:** casos de uso/domínio expõem **ports**; adaptadores implementam HTTP, banco, mensageria etc. Ambas reduzem acoplamento ao framework.
- **Microsserviços:** serviços autônomos alinhados a capacidades de negócio, com dados sob sua responsabilidade. Trazem deploy e escala independentes, porém adicionam latência, observabilidade, consistência distribuída e custo operacional. **Monólito modular** costuma ser alternativa válida.
- **Integração:** síncrona (REST) para resposta imediata; assíncrona (eventos/mensagens) para desacoplamento temporal. Considere governança de contratos, versionamento, idempotência e rastreabilidade.

**Pergunta-chave:** onde colocar uma regra de crédito? No domínio/caso de uso, evitando dependência direta de controller, JPA ou infraestrutura.

## 6. Kafka, Azure Service Bus/Event Hubs e sistemas distribuídos — P1

- **Event-driven:** produtores publicam mudanças/eventos; consumidores reagem. Diferencie **comando** (pedido de ação) de **evento** (fato ocorrido).
- **Kafka:** *topic* particionado, *offset*, *consumer group* e retenção do log. A ordem é garantida **dentro da partição**, não globalmente; use chave estável por entidade quando precisar da ordem relativa.
- **Azure Service Bus:** filas e tópicos/subscrições para **mensageria empresarial**; recursos como *dead-letter queue*, sessões e transações, conforme configuração.
- **Azure Event Hubs:** ingestão/streaming em alto volume, partições, grupos de consumidores, checkpoints e capacidade de releitura dentro da retenção. É mais próximo do padrão de log de eventos do Kafka.
- **Entrega:** assuma que duplicatas e reprocessamentos podem ocorrer; implemente **idempotência** com ID de mensagem/operação, deduplicação e restrições persistentes. “Exactly once” exige especificar o **escopo** da garantia e seus efeitos externos.
- **Falhas:** retry com *exponential backoff* + *jitter* para falhas transitórias; DLQ para mensagens problemáticas; timeout, circuit breaker e bulkhead para proteger recursos. Nunca faça retries ilimitados de operações não idempotentes.
- **Transactional Outbox:** salve estado e evento na **mesma transação local**; um publicador envia o evento posteriormente, evitando perder mensagens entre commit e publicação. Consumidores continuam precisando ser idempotentes.
- **Saga:** coordena transações locais com eventos/comandos e **ações compensatórias**; não equivale a uma transação ACID global.
- **Consistência eventual:** consumidores podem observar estados diferentes por algum tempo; defina o que o negócio tolera, monitore atraso e planeje reconciliação.
- **APIs externas:** limite de tempo, retries seletivos, autenticação, tratamento de 429/5xx, rate limiting, *fallback* seguro e propagação de *correlation ID*.

**Pergunta-chave:** o pedido foi gravado no banco, mas o evento não foi publicado. Explique Outbox, retries, idempotência e monitoração do backlog.

## 7. Testes, qualidade e Git — P0/P1

- **Teste unitário:** rápido e isolado, cobrindo regra e comportamento observável; **JUnit 5** organiza testes e **Mockito** substitui dependências externas. Evite verificar detalhes internos sem valor de negócio.
- **Mocks:** `mock` simula colaborador; `stub` fornece respostas pré-definidas; `verify` verifica interações relevantes. Não faça mock de entidade/DTO simples nem da classe realmente testada.
- **Integração:** **Testcontainers** sobe serviços reais descartáveis (PostgreSQL, Redis, Kafka etc.) para testar queries, migrations e integrações. Use dados de teste independentes.
- **Spring tests:** `@WebMvcTest` foca MVC, `@DataJpaTest` foca persistência JPA, `@SpringBootTest` carrega contexto mais amplo. Use o menor escopo que valide o comportamento.
- **Qualidade:** pirâmide de testes, casos de erro, testes de contrato entre serviços, análise estática e revisão de código. Cobertura alta **não prova** qualidade.
- **SonarQube:** acompanha bugs, vulnerabilidades, *code smells*, duplicação e cobertura; *quality gates* impõem critérios ao pipeline, especialmente em código novo.
- **Git:** `rebase` reescreve histórico; `merge` preserva ponto de integração; `cherry-pick` aplica commit específico. Em branches compartilhadas, evite reescrever histórico inadvertidamente.
- **Gitflow:** branches `main`, `develop`, `feature`, `release` e `hotfix`; útil para ciclos de release, mas pode criar atrito com deploy contínuo. Compare com **trunk-based development**.

**Pergunta-chave:** o que testar com Mockito e o que testar com Testcontainers em um repositório JDBC/JPA?

## 8. Cloud, containers, Kubernetes, CI/CD e observabilidade — P1/P2

- **Azure:** **Service Bus** (filas/tópicos), **Event Hubs** (streaming), **Blob Storage** (objetos/arquivos), **Key Vault** (segredos/chaves/certificados). Prefira identidades gerenciadas e acesso mínimo necessário.
- **AWS/GCP:** domine os conceitos transferíveis: compute, storage, IAM, redes, secrets, filas/streams, bancos gerenciados, métricas e escalabilidade.
- **Docker:** **imagem** imutável de camadas; **container** executa processo isolado. Use multi-stage build, imagem-base enxuta, usuário não root, variáveis externas e *health checks*.
- **Kubernetes:** `Pod` executa containers; `Deployment` gerencia réplicas/rollouts; `Service` oferece acesso estável; `ConfigMap` configura; `Secret` guarda dados sensíveis (exige proteção adequada); `Ingress`/Gateway recebe tráfego. **Readiness** controla recebimento de tráfego; **liveness** orienta reinício. Recursos/limites e HPA afetam estabilidade e custo.
- **CI/CD:** pipeline típico: compilar → testes → análise de segurança/qualidade → gerar imagem → publicar → deploy → smoke tests/monitoramento. Versione artefatos; tenha rollback, segregação de ambientes e migrations compatíveis.
- **Datadog / observabilidade:** **logs** contam eventos, **métricas** quantificam, **traces** acompanham requisições entre serviços. Use `traceId`/`correlationId`, dashboards e alertas em sinais de experiência do usuário. Diferencie SLI (medida) e SLO (meta).
- **Performance:** observe throughput, latência p95/p99, taxa de erro, saturação de CPU/memória, pool JDBC, threads, pausas GC e lag de consumidores. Otimize após medir o gargalo.
- **React (diferencial):** componentes, props, state, hooks (`useState`, `useEffect`), ciclo de renderização, chamadas HTTP, tratamento de estados de loading/erro e noções de autenticação no front-end.

**Pergunta-chave:** a latência aumentou após o deploy. Explique como correlacionar versão, logs, traces, métricas, banco, limites do cluster e como decidir rollback.

---

## 9. Respostas rápidas para entrevista sênior

1. **Hexagonal × MVC?** MVC organiza a camada de interação; Hexagonal isola o núcleo por portas e adaptadores. Podem coexistir.
2. **Quando NÃO usar microsserviços?** Domínio pouco definido, equipe pequena e custo operacional alto: comece por monólito modular bem delimitado.
3. **`@Transactional` resolve consistência entre serviços?** Não. Vale para o gerenciador/transação configurado; use padrões como Outbox e Saga para fluxos distribuídos.
4. **Redis garante dado atualizado?** Não sozinho. É preciso política de TTL/invalidação coerente com a consistência exigida.
5. **Kafka garante execução única?** Não de modo geral para efeitos externos; prepare consumidores para duplicatas e reprocessamento.
6. **`volatile` resolve `contador++`?** Não; visibilidade não significa atomicidade. Use `AtomicInteger` ou sincronização.
7. **Mockito × Testcontainers?** Mockito isola regras e colaboradores; Testcontainers valida integração real com infraestrutura.
8. **JWT × OAuth2?** JWT é formato de token; OAuth 2.0 define mecanismos de autorização. Um pode ser usado sem implicar o outro.
9. **`401` × `403`?** `401`: autenticação ausente/inválida; `403`: autenticado, mas sem acesso (ou acesso negado pela política).
10. **Qual o maior desafio de microsserviços?** Coordenar dados, falhas, contratos e operação distribuída — além de escrever endpoints.

## 10. Revisão ativa em 7 dias (30–45 min/dia)

| Dia | Revisar | Praticar em voz alta |
| --- | --- | --- |
| **1** | Java 17/21, collections, streams, concorrência | `HashMap` vs `ConcurrentHashMap`; virtual threads |
| **2** | Spring Beans, REST, Security, validação | Fluxo de requisição; `@Transactional`; 401 vs 403 |
| **3** | SQL/JDBC, índices, transações, Mongo | Diagnóstico de query lenta; lock otimista |
| **4** | SOLID, DDD, Hexagonal, microsserviços | Desenhar serviço de pedidos e fronteiras |
| **5** | Redis, Kafka, Service Bus/Event Hubs | Duplicatas, Outbox, DLQ e cache inconsistente |
| **6** | JUnit/Mockito/Testcontainers, Git, Sonar | Estratégia de testes e quality gate |
| **7** | Docker/K8s, Azure, CI/CD, Datadog | Incidente em produção e plano de mitigação |

### Checklist final

- [ ] Explico cada conceito em **30–60 segundos**, com exemplo real e trade-off.
- [ ] Desenho uma API de pedidos com **autenticação, validação, banco e evento**.
- [ ] Justifico escolhas de **transação, cache, fila/stream, consistência e retries**.
- [ ] Descrevo **testes unitários, integração, contrato e pipeline**.
- [ ] Relato um incidente real usando **situação → diagnóstico → decisão → resultado**.

### Fontes oficiais para aprofundar somente onde houver lacuna

- [Oracle — novidades de Java 21](https://docs.oracle.com/en/java/javase/21/language/java-language-changes-release.html)
- [Oracle — virtual threads](https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html)
- [Spring Boot — testes](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html)
- [Spring Boot — Testcontainers](https://docs.spring.io/spring-boot/reference/testing/testcontainers.html)
- [Testcontainers — JUnit 5](https://java.testcontainers.org/test_framework_integration/junit_5/)
- [Microsoft Learn — Service Bus × Event Hubs](https://learn.microsoft.com/en-us/azure/service-bus-messaging/compare-messaging-services)

> **Regra de ouro para nível sênior:** não basta definir a tecnologia. Diga **quando usar, quando evitar, quais falhas ela introduz, como testar e como observar em produção**.
