Lucas, segue uma tabela enxuta com os **conceitos mais importantes de concorrência em Java**, focando em decisão prática e produção.

| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Concorrência** | Múltiplas tarefas fazendo progresso no mesmo intervalo de tempo. Não implica execução simultânea. | Aumenta throughput, mas também complexidade, risco de race condition, deadlock e inconsistência. | APIs atendendo várias requisições simultâneas, consumers Kafka, processamento assíncrono. |
| **Paralelismo** | Execução simultânea de tarefas em múltiplos núcleos de CPU. | Limitado pela quantidade de CPU disponível. Mais threads não significam mais performance. | Processamento CPU-bound, cálculos, transformação de grandes volumes de dados. |
| **Thread** | Unidade de execução dentro de um processo/JVM. | Platform Threads possuem custo de memória e escalabilidade limitada em grande quantidade. | Requisições HTTP, jobs, consumers, processamento em background. |
| **Race Condition** | O resultado depende da ordem em que múltiplas threads acessam/modificam o mesmo estado. | Pode gerar bugs intermitentes, difíceis de reproduzir e diagnosticar. | Atualização simultânea de saldo, estoque, contador, cache ou estado em memória. |
| **Critical Section** | Trecho que acessa estado compartilhado e precisa ser protegido contra execução concorrente inadequada. | Quanto maior a região crítica, maior a contenção e menor o throughput. | Atualização de estruturas compartilhadas ou invariantes que envolvem várias operações. |
| **Atomicidade** | Uma operação é observada como indivisível: ocorre completamente ou não ocorre. | Operações atômicas mais complexas normalmente exigem sincronização ou mecanismos específicos. | Incrementos, compare-and-set, atualização de saldo, check-and-update. |
| **Visibilidade** | Garantia de que uma thread veja alterações realizadas por outra. | Garantir visibilidade não implica garantir atomicidade. | Flags de shutdown, estados compartilhados, configuração dinâmica entre threads. |
| **Java Memory Model — JMM** | Modelo que define visibilidade, ordenação e interação de memória entre threads Java. | É conceitualmente complexo, mas fundamental para compreender `volatile` e `synchronized`. | Base das garantias de segurança de memória em código concorrente. |
| **happens-before** | Relação do JMM que garante que efeitos de uma operação sejam visíveis para outra. | Precisa existir uma relação válida; não deve ser presumida apenas porque uma operação ocorreu antes cronologicamente. | `synchronized`, `volatile`, `Thread.start()`, `Thread.join()`, estruturas de `java.util.concurrent`. |
| **`synchronized`** | Usa o monitor de um objeto para garantir exclusão mútua e visibilidade. | Pode causar contenção, bloqueio e reduzir throughput se usado de forma excessiva. | Proteção de invariantes e operações compostas dentro da mesma JVM. |
| **`volatile`** | Garante visibilidade e ordenação de leituras/escritas de uma variável. | Não fornece exclusão mútua e não torna operações compostas atômicas. | Flags como `running`, `shutdownRequested`, estados simples lidos por várias threads. |
| **`AtomicInteger` / `AtomicLong`** | Tipos que oferecem operações atômicas, normalmente usando CAS. | Adequados para operações simples; não resolvem facilmente invariantes envolvendo vários campos. | Contadores, métricas, sequências, estatísticas concorrentes. |
| **CAS — Compare-And-Set** | Atualiza um valor apenas se ele ainda possuir o valor esperado. | Sob alta contenção pode gerar várias tentativas/retries. | Implementação de classes `Atomic*`, estruturas lock-free. |
| **`ReentrantLock`** | Lock explícito com mais controle que `synchronized`. | Mais flexível, porém mais fácil de usar incorretamente; `unlock()` precisa ser garantido. | `tryLock`, timeout, lock interruptível, múltiplas `Condition`. |
| **Deadlock** | Duas ou mais threads ficam esperando indefinidamente recursos umas das outras. | Pode paralisar completamente uma parte da aplicação. | Dois recursos sendo bloqueados em ordens diferentes. |
| **Starvation** | Uma thread não consegue executar porque outras monopolizam recursos. | Pode causar latência extrema sem necessariamente travar toda a aplicação. | Locks altamente disputados, pools mal dimensionados. |
| **Livelock** | Threads continuam executando, mas ficam reagindo umas às outras sem progresso útil. | Consome recursos sem realizar trabalho real. | Algoritmos concorrentes com retries ou resolução de conflitos mal projetada. |
| **Imutabilidade** | Estado do objeto não pode mudar após sua criação. | Pode gerar mais objetos/alocações, mas reduz enormemente problemas de sincronização. | DTOs, records, eventos Kafka, objetos compartilhados entre threads. |
| **`ExecutorService`** | Abstração para executar tarefas sem gerenciar threads manualmente. | Pool mal dimensionado pode gerar filas, starvation ou excesso de threads. | Background jobs, processamento paralelo, integração com serviços externos. |
| **Thread Pool** | Conjunto limitado de threads reutilizadas para executar várias tarefas. | Pool pequeno gera fila; pool grande pode gerar excesso de memória/context switching. | `ThreadPoolTaskExecutor`, executores de aplicações Spring, workers. |
| **`Runnable`** | Representa uma tarefa sem retorno. | Não retorna resultado e não declara checked exceptions diretamente. | Jobs assíncronos, tarefas simples. |
| **`Callable`** | Tarefa que retorna resultado e pode lançar exception. | Normalmente precisa ser combinada com `Future` ou executor. | Consultas paralelas, cálculos, processamento que retorna valor. |
| **`Future`** | Representa o resultado futuro de uma operação assíncrona. | `get()` é bloqueante e composição é limitada. | Esperar resultado de tarefas submetidas ao `ExecutorService`. |
| **`CompletableFuture`** | Permite executar e compor pipelines assíncronos. | Pode ficar complexo rapidamente; uso inadequado pode gerar bloqueios e uso incorreto do common pool. | Agregação de múltiplas APIs, chamadas independentes em paralelo. |
| **`ForkJoinPool`** | Executor especializado em dividir tarefas CPU-bound em subtarefas menores. | Não é ideal para I/O bloqueante prolongado. | Algoritmos divide-and-conquer, processamento paralelo CPU-bound. |
| **`ConcurrentHashMap`** | Implementação de `Map` preparada para acesso concorrente. | Operações individuais são thread-safe, mas sequências de operações podem não ser atômicas. | Cache em memória, registros concorrentes, lookup compartilhado. |
| **Operação composta** | Combinação de várias operações que precisam ser tratadas como uma única unidade lógica. | Mesmo collections concorrentes não garantem automaticamente atomicidade entre chamadas separadas. | `containsKey + put`, `get + update`, validar saldo + debitar. |
| **`Semaphore`** | Limita quantas threads podem acessar um recurso simultaneamente. | Introduz espera e precisa ser corretamente liberado. | Limitar chamadas para API externa, banco ou serviço legado. |
| **`CountDownLatch`** | Permite que uma ou mais threads aguardem outras terminarem determinadas tarefas. | Uso único; depois de chegar a zero não pode ser resetado. | Inicialização paralela, testes concorrentes, aguardar múltiplos workers. |
| **`CyclicBarrier`** | Faz múltiplas threads aguardarem umas pelas outras em um ponto comum. | Todas dependem das demais chegarem à barreira. | Algoritmos paralelos executados em fases. |
| **Virtual Threads** | Threads leves gerenciadas pela JVM, adequadas para alta concorrência com I/O bloqueante. | Não aceleram CPU-bound e não eliminam race conditions ou problemas de sincronização. | APIs Spring com muitas chamadas HTTP/DB bloqueantes, alta quantidade de requisições simultâneas. |
| **Platform Threads** | Threads tradicionais associadas a threads nativas do sistema operacional. | Possuem maior custo de memória e sistema operacional. | Pools tradicionais, workloads CPU-bound, aplicações Java anteriores às Virtual Threads. |
| **I/O-bound** | Workload cujo tempo é gasto principalmente esperando rede, disco ou banco. | Threads ficam grande parte do tempo bloqueadas. | REST clients, JDBC, chamadas de APIs, filesystem. |
| **CPU-bound** | Workload cujo gargalo é processamento de CPU. | Criar mais threads que núcleos úteis geralmente não melhora desempenho e pode piorar. | Criptografia, compressão, cálculos matemáticos. |
| **Backpressure / limitação de concorrência** | Controlar quanto trabalho pode entrar simultaneamente no sistema. | Reduz throughput máximo em troca de estabilidade. | `Semaphore`, filas, rate limit, bulkhead, limite de consumers. |
| **Concorrência distribuída** | Concorrência envolvendo múltiplas JVMs, pods ou serviços. | Locks locais como `synchronized` deixam de ser suficientes. | Aplicações Kubernetes com múltiplas réplicas acessando o mesmo banco. |
| **Optimistic Locking** | Detecta conflito usando versão do registro, normalmente `@Version`. | Conflitos exigem retry; piora quando há muita contenção. | Atualização concorrente de entidade JPA com baixa/moderada disputa. |
| **Pessimistic Locking** | Bloqueia o registro para impedir alterações concorrentes. | Pode reduzir throughput e provocar waits/deadlocks. | Estoque, saldo ou operações críticas com alta chance de conflito. |
| **Atomic UPDATE no banco** | Faz validação e atualização em uma única instrução SQL atômica. | Pode ser menos expressivo no domínio, mas é extremamente eficiente. | `UPDATE product SET stock = stock - ? WHERE stock >= ?`. |
| **Idempotência** | Executar a mesma operação várias vezes produz o mesmo efeito lógico de uma execução. | Exige chave idempotente e armazenamento/verificação adicional. | Kafka, retries, pagamentos, processamento distribuído. |
| **Kafka Partitioning** | Eventos com a mesma key podem ser direcionados para a mesma partição, preservando ordem dentro dela. | A ordem é garantida apenas por partição; keys mal distribuídas podem criar hot partitions. | Serializar eventos do mesmo `orderId`, `customerId` ou agregado. |

## Os que você precisa dominar primeiro

Para entrevista Java/Spring, eu reduziria inicialmente para esta sequência:

```text
Race Condition
    ↓
Atomicidade
    ↓
Visibilidade
    ↓
Java Memory Model / happens-before
    ↓
synchronized
    ↓
volatile
    ↓
Atomic*
    ↓
ReentrantLock
    ↓
ExecutorService
    ↓
CompletableFuture
    ↓
ConcurrentHashMap
    ↓
Deadlock
    ↓
Virtual Threads
    ↓
Concorrência distribuída
    ↓
Optimistic / Pessimistic Locking
```

A separação mais importante para arquitetura é esta:

```text
Dentro da JVM
────────────────────────
synchronized
volatile
Atomic*
Lock
ConcurrentHashMap


Entre JVMs / Pods
────────────────────────
Banco de dados
Optimistic Locking
Pessimistic Locking
Atomic UPDATE
Kafka partitioning
Idempotência
Distributed Lock
```

Esse último ponto evita um erro comum: tentar resolver um problema **distribuído** usando uma ferramenta de concorrência **local da JVM**.
