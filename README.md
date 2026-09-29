# Respostas de Entrevista — Java, Spring, SQL, Arquitetura, Microserviços, Kafka, AWS & CI/CD

Documento consolidado contendo **somente respostas preparadas para entrevista** sobre Java, Spring, SQL, Arquitetura de Software, Microserviços, Kafka, AWS e CI/CD, organizado para consulta rápida.

<a id="sumario-geral"></a>

## Sumário Geral

### Java

- [JVM Memory & Garbage Collection](#java-jvm-memory-garbage-collection-sumario)
- [Concorrência em Java](#java-concorrencia-em-java-sumario)
- [Java Collections](#java-java-collections-sumario)
- [Programação Orientada a Objetos em Java](#java-programacao-orientada-a-objetos-em-java-sumario)

### Spring

- [Spring Core](#spring-spring-core-sumario)
- [Spring Boot](#spring-spring-boot-sumario)
- [Spring MVC](#spring-spring-mvc-sumario)
- [Spring Data](#spring-spring-data-sumario)
- [Spring Security](#spring-spring-security-sumario)
- [Spring Cloud](#spring-spring-cloud-sumario)
- [Ecossistema Spring — Visão Geral](#spring-ecossistema-spring-visao-geral-sumario)


### SQL

- [SQL & Bancos Relacionais](#sql-sql-bancos-relacionais-sumario)


### Arquitetura

- [Arquitetura de Software](#arquitetura-arquitetura-de-software-sumario)


### Microserviços

- [Microserviços](#microservicos-microservicos-sumario)

### Kafka

- [Kafka](#kafka-kafka-sumario)


### AWS

- [AWS](#aws-aws-sumario)


### CI/CD

- [CI/CD](#cicd-cicd-sumario)

> Cada tópico possui seu próprio sumário. Após cada resposta há atalhos para **↑ Sumário do tópico** e **↑ Sumário geral**.

---

<a id="java-jvm-memory-garbage-collection-sumario"></a>

# JVM Memory & Garbage Collection

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-jvm-memory-garbage-collection-visao-geral)
- [1 Heap](#java-jvm-memory-garbage-collection-1-heap)
- [2 Stack](#java-jvm-memory-garbage-collection-2-stack)
- [2 Regra de Alcançabilidade](#java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade)
- [1 Fluxo Geracional](#java-jvm-memory-garbage-collection-1-fluxo-geracional)
- [Garbage Collector](#java-jvm-memory-garbage-collection-garbage-collector)
- [3 Full GC](#java-jvm-memory-garbage-collection-3-full-gc)
- [2 ZGC](#java-jvm-memory-garbage-collection-2-zgc)
- [ZGC](#java-jvm-memory-garbage-collection-zgc)
- [Memória Fora da Heap](#java-jvm-memory-garbage-collection-memoria-fora-da-heap)
- [1 Heap Dump](#java-jvm-memory-garbage-collection-1-heap-dump)
- [2 Thread Dump](#java-jvm-memory-garbage-collection-2-thread-dump)
- [Resposta Completa de Entrevista](#java-jvm-memory-garbage-collection-resposta-completa-de-entrevista)

---

<a id="java-jvm-memory-garbage-collection-visao-geral"></a>

## Visão Geral

> A JVM possui diferentes áreas de memória. A Heap armazena objetos e é gerenciada pelo Garbage Collector, enquanto cada thread possui sua própria Stack para execução dos métodos. Em produção, começo investigando métricas e GC logs e, quando necessário, uso Heap Dump e Thread Dump para chegar à causa.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap"></a>

## 1 Heap

> A Heap é a área compartilhada entre as threads onde ficam objetos e arrays. Ela é gerenciada pelo Garbage Collector. Se não houver espaço suficiente para novas alocações e o GC não conseguir recuperar memória suficiente, pode ocorrer `OutOfMemoryError: Java heap space`.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-stack"></a>

## 2 Stack

> Cada thread possui sua própria Stack, que acompanha a execução dos métodos por meio de stack frames. Uma profundidade excessiva de chamadas, como em uma recursão infinita, pode provocar `StackOverflowError`.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade"></a>

## 2 Regra de Alcançabilidade

> O Garbage Collector determina a vida de um objeto através de reachability. Se existe um caminho de referências fortes partindo de algum GC Root até o objeto, ele continua vivo. Quando esse caminho deixa de existir, o objeto fica elegível para coleta.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-fluxo-geracional"></a>

## 1 Fluxo Geracional

> Coletores geracionais dividem logicamente a Heap conforme a idade dos objetos. Objetos normalmente começam na Young Generation e, quando sobrevivem a várias coletas, podem ser promovidos para a Old Generation. Essa estratégia é eficiente porque grande parte dos objetos de aplicações Java possui vida curta.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-garbage-collector"></a>

## Garbage Collector

> O Garbage Collector gerencia a memória da Heap. Ele identifica objetos que deixaram de ser alcançáveis a partir dos GC Roots e recupera essa memória. Coletores como G1 e ZGC utilizam estratégias diferentes para equilibrar throughput, latência e consumo de recursos.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-3-full-gc"></a>

## 3 Full GC

> O G1 divide a Heap em regiões e tenta controlar as pausas escolhendo quais regiões coletar. Ele possui Young Collections e Mixed Collections, que também podem recuperar regiões selecionadas da Old. Full GC é diferente: envolve a Heap inteira e tende a produzir uma pausa muito mais relevante.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-zgc"></a>

## 2 ZGC

> O G1 procura equilibrar throughput e metas de pausa. Já o ZGC prioriza baixa latência e realiza uma parcela maior do trabalho concorrentemente. O trade-off deve ser medido na carga real, observando CPU, memória, throughput e os SLOs de latência.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-zgc"></a>

## ZGC

> Aumentar a Heap pode reduzir pressão de GC quando a aplicação realmente precisa de mais espaço, mas não resolve retenção indevida. Da mesma forma, mudar para ZGC não deve ser uma decisão automática: eu escolheria o coletor com base em metas de latência, throughput e medições da carga real.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-memoria-fora-da-heap"></a>

## Memória Fora da Heap

> Eu não atribuiria automaticamente um aumento de memória do processo à Heap. Se a Heap permanece estável, investigaria Metaspace, Direct Buffers, stacks das threads, quantidade de threads e outras áreas nativas da JVM.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap-dump"></a>

## 1 Heap Dump

> Heap Dump mostra os objetos presentes na Heap e suas relações de referência. Eu o utilizaria para descobrir quais objetos estão retendo memória e qual caminho de referências até um GC Root impede que sejam coletados.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-thread-dump"></a>

## 2 Thread Dump

> Thread Dump mostra o estado das threads e suas stacks naquele instante. É útil para investigar bloqueios, deadlocks, contenção ou entender onde as threads estão gastando tempo.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> A JVM utiliza diferentes áreas de memória. A Heap é compartilhada entre as threads e armazena objetos e arrays, enquanto cada thread possui sua própria Stack para acompanhar a execução dos métodos.
>
> Na Heap, coletores geracionais trabalham com Young e Old Generation. Objetos normalmente começam na Young e, quando sobrevivem a várias coletas, podem ser promovidos para a Old.
>
> O Garbage Collector determina quais objetos podem ser coletados através de reachability a partir dos GC Roots. Se não existe mais um caminho de referências fortes até determinado objeto, ele fica elegível para coleta.
>
> Em relação aos coletores, o G1 busca equilibrar throughput e metas de pausa, enquanto o ZGC prioriza baixa latência realizando uma parcela maior do trabalho concorrentemente.
>
> Em produção, eu começo analisando métricas e GC logs. Se a ocupação da Heap após GC continua crescendo, parto para Heap Dump para investigar retenção e caminhos até GC Roots. Se a Heap está estável, mas há problema de latência, utilizo Thread Dumps ou JFR para investigar bloqueios, contenção ou saturação. Só depois considero tuning de Heap ou mudança de coletor.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-sumario"></a>

# Concorrência em Java

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-concorrencia-em-java-visao-geral)
- [1 Thread](#java-concorrencia-em-java-1-thread)
- [2 Runnable](#java-concorrencia-em-java-2-runnable)
- [3 Callable](#java-concorrencia-em-java-3-callable)
- [4 Future](#java-concorrencia-em-java-4-future)
- [Trade-off](#java-concorrencia-em-java-trade-off)
- [2 Happens-before](#java-concorrencia-em-java-2-happens-before)
- [Trade-off](#java-concorrencia-em-java-trade-off-2)
- [Bom para](#java-concorrencia-em-java-bom-para)
- [Trade-off](#java-concorrencia-em-java-trade-off-3)
- [Trade-off](#java-concorrencia-em-java-trade-off-4)
- [5 ConcurrentHashMap](#java-concorrencia-em-java-5-concurrenthashmap)
- [Java 24+](#java-concorrencia-em-java-java-24)
- [5 Trade-offs](#java-concorrencia-em-java-5-trade-offs)
- [Diagnóstico em Produção](#java-concorrencia-em-java-diagnostico-em-producao)
- [Resposta Completa de Entrevista](#java-concorrencia-em-java-resposta-completa-de-entrevista)

---

<a id="java-concorrencia-em-java-visao-geral"></a>

## Visão Geral

> Concorrência em Java é a execução coordenada de múltiplas tarefas durante o mesmo período. O Java fornece abstrações como Threads, ExecutorService, locks, atomics e coleções concorrentes. O principal desafio é coordenar acesso a estado compartilhado garantindo atomicidade, visibilidade e ordenação sem gerar contenção excessiva.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-1-thread"></a>

## 1 Thread

> Uma Thread representa um fluxo de execução. Platform Threads são relativamente caras porque dependem de threads do sistema operacional, enquanto Virtual Threads são muito mais leves e gerenciadas pela JVM.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-runnable"></a>

## 2 Runnable

> Runnable representa uma tarefa sem retorno. É adequada quando eu quero executar uma ação concorrentemente, mas não preciso receber um resultado da operação.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-3-callable"></a>

## 3 Callable

> Callable é semelhante ao Runnable, mas permite retornar um resultado e lançar exceptions. Quando submetido a um ExecutorService, normalmente recebo um Future representando o resultado futuro.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-4-future"></a>

## 4 Future

> Future representa o resultado de uma operação assíncrona. O principal cuidado é que `get()` pode bloquear até a operação terminar.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off"></a>

## Trade-off

> CompletableFuture permite compor operações assíncronas sem bloquear explicitamente a cada etapa. É útil para combinar tarefas independentes, mas fluxos excessivamente complexos podem prejudicar legibilidade e observabilidade.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-happens-before"></a>

## 2 Happens-before

> Happens-before define quando os efeitos de uma operação são garantidamente visíveis para outra. Por exemplo, liberar um monitor acontece antes de uma aquisição posterior do mesmo monitor, e uma escrita volatile acontece antes de uma leitura posterior da mesma variável.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-2"></a>

## Trade-off

> synchronized fornece exclusão mútua e garantia de visibilidade através do monitor. É simples para proteger regiões críticas, mas alta contenção sobre o mesmo monitor pode reduzir paralelismo e throughput.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-bom-para"></a>

## Bom para

> volatile garante visibilidade e ordenação de memória para uma variável, mas não transforma operações compostas em atômicas. Funciona bem para flags e estados simples, mas não substitui locks ou atomics em read-modify-write.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-3"></a>

## Trade-off

> ReentrantLock fornece exclusão mútua como synchronized, mas permite maior controle, como tryLock, timeout, interrupção e Conditions. O trade-off é maior complexidade e a obrigação de liberar explicitamente o lock.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-4"></a>

## Trade-off

> AtomicInteger é adequado para operações atômicas simples sobre um inteiro compartilhado. Evita locking explícito em muitos casos, mas não substitui locks quando preciso manter invariantes envolvendo múltiplos estados.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-concurrenthashmap"></a>

## 5 ConcurrentHashMap

> ConcurrentHashMap é um Map thread-safe otimizado para concorrência e fornece operações compostas atômicas como putIfAbsent, compute e merge.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-java-24"></a>

## Java 24+

> No Java 21, uma Virtual Thread podia ficar presa à carrier ao bloquear dentro de synchronized. A partir do JDK 24 esse caso foi resolvido. No Java 25, synchronized normalmente não causa mais esse pinning, embora ele ainda possa ocorrer em chamadas native ou foreign.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-trade-offs"></a>

## 5 Trade-offs

> Virtual Threads aumentam principalmente a escalabilidade de workloads I/O-bound. Elas não tornam CPU-bound mais rápido e não removem limites externos. Se meu banco possui 30 conexões, criar milhares de Virtual Threads não aumenta esse limite.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-diagnostico-em-producao"></a>

## Diagnóstico em Produção

> Em produção eu começo observando o sintoma e as métricas. Para suspeita de bloqueio ou contenção, utilizo Thread Dumps ou JFR para identificar estados das threads, locks e stacks. Também verifico pools e dependências externas antes de atribuir o problema simplesmente ao número de threads.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Concorrência em Java envolve executar e coordenar múltiplas tarefas durante o mesmo período. Para representar tarefas eu posso utilizar Runnable ou Callable e normalmente delego sua execução para abstrações como ExecutorService. Future representa um resultado assíncrono e CompletableFuture permite compor pipelines assíncronos.
>
> Quando existe estado compartilhado, os principais problemas são race condition, deadlock, livelock e contention. Para coordenar esse acesso posso usar synchronized, ReentrantLock, atomics ou estruturas como ConcurrentHashMap, escolhendo de acordo com a granularidade e as invariantes que preciso proteger.
>
> Também é importante entender o Java Memory Model. Happens-before estabelece garantias de visibilidade e ordenação entre threads, como ocorre na liberação e aquisição de um monitor ou em escrita e leitura de volatile.
>
> No Java moderno temos Virtual Threads, que permitem trabalhar com um número muito maior de tarefas concorrentes principalmente em workloads I/O-bound. Elas não tornam processamento CPU-bound mais rápido e não eliminam limites externos, como pools de conexão.
>
> Em produção eu observo métricas, Thread Dumps e JFR para identificar contention, bloqueios, deadlocks, saturação de executores ou dependências externas antes de decidir qualquer ajuste.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-sumario"></a>

# Java Collections

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-java-collections-visao-geral)
- [6 Map](#java-java-collections-6-map)
- [Complexidade típica](#java-java-collections-complexidade-tipica)
- [Trade-off](#java-java-collections-trade-off)
- [1 HashSet](#java-java-collections-1-hashset)
- [3 TreeSet](#java-java-collections-3-treeset)
- [1 HashMap](#java-java-collections-1-hashmap)
- [3 TreeMap](#java-java-collections-3-treemap)
- [Complexidade típica](#java-java-collections-complexidade-tipica-2)
- [2 ArrayDeque](#java-java-collections-2-arraydeque)
- [Problema clássico](#java-java-collections-problema-classico)
- [Detalhe de entrevista](#java-java-collections-detalhe-de-entrevista)
- [Comparable vs Comparator](#java-java-collections-comparable-vs-comparator)
- [3 Remoção segura](#java-java-collections-3-remocao-segura)
- [Armadilha clássica](#java-java-collections-armadilha-classica)
- [Uso comum](#java-java-collections-uso-comum)
- [3 BlockingQueue](#java-java-collections-3-blockingqueue)
- [Como escolher a Collection](#java-java-collections-como-escolher-a-collection)
- [Cenário 7 — Latência degradando com collection grande](#java-java-collections-cenario-7-latencia-degradando-com-collection-grande)
- [Resposta Completa de Entrevista](#java-java-collections-resposta-completa-de-entrevista)

---

<a id="java-java-collections-visao-geral"></a>

## Visão Geral

> O Java Collections Framework fornece estruturas de dados padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map. A escolha entre elas depende de requisitos como ordem, duplicidade, acesso por índice, busca por chave, ordenação e concorrência.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-6-map"></a>

## 6 Map

> List representa uma sequência e permite duplicados. Set representa elementos únicos. Queue e Deque são voltadas ao processamento ordenado de elementos. Map representa associações chave-valor e não herda de Collection.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica"></a>

## Complexidade típica

> ArrayList é baseada em array dinâmico. Possui acesso por índice em O(1) e inserção no final em O(1) amortizado. Inserções e remoções no meio são O(n) porque podem exigir deslocamento dos elementos.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-trade-off"></a>

## Trade-off

> LinkedList é uma lista duplamente encadeada. É eficiente para operações nas extremidades, mas acesso por índice é O(n). Na prática, ArrayList costuma ser preferível para listas gerais por ter melhor localidade de memória e menor overhead.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-1-hashset"></a>

## 1 HashSet

> HashSet é indicado quando preciso garantir unicidade e não preciso de ordenação. Em condições normais, add, contains e remove possuem custo próximo de O(1).

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-treeset"></a>

## 3 TreeSet

> TreeSet mantém os elementos ordenados e oferece operações em O(log n). Eu usaria quando preciso simultaneamente de unicidade e ordenação.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-1-hashmap"></a>

## 1 HashMap

> HashMap é uma estrutura chave-valor baseada em hashing. Em condições normais, get e put são O(1) em média. A qualidade de hashCode e o contrato com equals são fundamentais para funcionamento correto e desempenho.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-treemap"></a>

## 3 TreeMap

> TreeMap é indicado quando preciso de um mapa cujas chaves permaneçam ordenadas e quando operações de navegação por intervalo são importantes.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica-2"></a>

## Complexidade típica

> PriorityQueue é adequada quando preciso recuperar repetidamente o elemento de maior prioridade. Ela não representa uma lista totalmente ordenada para iteração.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-2-arraydeque"></a>

## 2 ArrayDeque

> ArrayDeque é uma estrutura eficiente para fila ou pilha. Para implementar uma stack moderna em Java, geralmente prefiro ArrayDeque em vez da classe legada Stack.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-problema-classico"></a>

## Problema clássico

> equals define igualdade lógica e hashCode determina a distribuição em estruturas baseadas em hashing. Se dois objetos são iguais segundo equals, obrigatoriamente devem produzir o mesmo hashCode.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-detalhe-de-entrevista"></a>

## Detalhe de entrevista

> HashMap distribui entradas em buckets utilizando hashCode. Quando existem colisões, equals é usado para distinguir as chaves. Em implementações modernas, buckets excessivamente congestionados podem ser convertidos em árvores para evitar degradação severa.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-comparable-vs-comparator"></a>

## Comparable vs Comparator

> Comparable define a ordenação natural do tipo. Comparator define estratégias externas e permite múltiplas formas de ordenação sem modificar a classe.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-remocao-segura"></a>

## 3 Remoção segura

> Muitas collections utilizam iteradores fail-fast. Modificar diretamente a estrutura durante uma iteração pode gerar ConcurrentModificationException. Para remoção durante iteração posso usar Iterator.remove ou operações apropriadas da própria Collection.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-armadilha-classica"></a>

## Armadilha clássica

> Collections.unmodifiableList cria uma visão não modificável da coleção original, enquanto List.of cria uma coleção estruturalmente imutável. Arrays.asList cria uma lista de tamanho fixo ligada ao array original.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-uso-comum"></a>

## Uso comum

> CopyOnWriteArrayList favorece workloads com muitas leituras e poucas escritas. Leituras são simples, mas cada modificação exige copiar a estrutura interna, então não é adequada para alta taxa de escrita.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-blockingqueue"></a>

## 3 BlockingQueue

> BlockingQueue é útil para coordenação entre produtores e consumidores, porque fornece operações que podem bloquear quando a fila está vazia ou cheia, ajudando também no controle de backpressure local.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-como-escolher-a-collection"></a>

## Como escolher a Collection

> Eu escolho a Collection a partir do padrão de acesso e das garantias necessárias. Para lookup por chave geralmente começo com HashMap, para sequência com ArrayList, para unicidade com HashSet, para ordenação com TreeMap ou TreeSet e para fila ou pilha com ArrayDeque. Depois considero concorrência e requisitos específicos.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-cenario-7-latencia-degradando-com-collection-grande"></a>

## Cenário 7 — Latência degradando com collection grande

> Em produção eu não escolho Collection apenas pelo tipo de dado, mas pelo padrão de acesso. Se há lookup frequente por chave, por exemplo, uma List pode gerar O(n) repetidamente enquanto um HashMap oferece O(1) médio. Também considero memória, ordenação, taxa de escrita, concorrência e volume.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> O Java Collections Framework fornece estruturas padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map, lembrando que Map faz parte do framework, mas não herda de Collection.
>
> Para listas, ArrayList normalmente é minha primeira opção por oferecer acesso por índice em O(1), boa localidade de memória e inserção no final em O(1) amortizado. LinkedList é uma lista duplamente encadeada e faz mais sentido quando preciso das características de Deque ou operações específicas nas extremidades.
>
> Para unicidade, uso Set. HashSet oferece operações O(1) em média sem garantir ordem, LinkedHashSet preserva ordem de inserção e TreeSet mantém os elementos ordenados em O(log n).
>
> Para chave-valor, HashMap é a escolha comum com get e put O(1) em média. LinkedHashMap adiciona ordem previsível e TreeMap mantém as chaves ordenadas com operações O(log n).
>
> Em estruturas baseadas em hash, equals e hashCode são fundamentais. Objetos iguais segundo equals precisam possuir o mesmo hashCode, e colisões são tratadas internamente pelo mapa.
>
> Também considero concorrência. HashMap não é thread-safe, então em cenários concorrentes posso utilizar ConcurrentHashMap e preferir operações atômicas como putIfAbsent ou compute.
>
> Em produção, a escolha depende do padrão de acesso. Eu avalio necessidade de lookup, ordenação, duplicidade, acesso por índice, concorrência, consumo de memória e complexidade das operações antes de escolher a estrutura.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-sumario"></a>

# Programação Orientada a Objetos em Java

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-programacao-orientada-a-objetos-em-java-visao-geral)
- [Exemplo](#java-programacao-orientada-a-objetos-em-java-exemplo)
- [Trade-off](#java-programacao-orientada-a-objetos-em-java-trade-off)
- [Regra prática](#java-programacao-orientada-a-objetos-em-java-regra-pratica)
- [Regra prática](#java-programacao-orientada-a-objetos-em-java-regra-pratica-2)
- [Overriding — Sobrescrita](#java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita)
- [3 Delegação](#java-programacao-orientada-a-objetos-em-java-3-delegacao)
- [Observação prática](#java-programacao-orientada-a-objetos-em-java-observacao-pratica)
- [Objetivo](#java-programacao-orientada-a-objetos-em-java-objetivo)
- [Cuidado](#java-programacao-orientada-a-objetos-em-java-cuidado)
- [3 Enums](#java-programacao-orientada-a-objetos-em-java-3-enums)
- [4 Encapsular invariantes](#java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes)
- [Correlação com concorrência](#java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia)
- [Resposta Completa de Entrevista](#java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista)
- [Resposta de 30 segundos](#java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos)

---

<a id="java-programacao-orientada-a-objetos-em-java-visao-geral"></a>

## Visão Geral

> Programação Orientada a Objetos é um paradigma que organiza o sistema em objetos com estado e comportamento. Em Java, os principais conceitos são encapsulamento, abstração, herança e polimorfismo. Na prática, eu uso POO para modelar responsabilidades, proteger invariantes e reduzir acoplamento, normalmente favorecendo abstrações e composição quando isso melhora a flexibilidade.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-exemplo"></a>

## Exemplo

> Uma classe define estrutura e comportamento; o objeto é uma instância concreta dessa classe. Eu procuro criar objetos já em estado válido e colocar comportamento próximo do estado que ele controla, evitando expor atributos sem necessidade.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-trade-off"></a>

## Trade-off

> Encapsulamento não é apenas colocar atributos como private. É controlar como o estado pode mudar e proteger invariantes. Eu prefiro expor operações de negócio em vez de setters genéricos, porque isso reduz estados inválidos e acoplamento com a implementação interna.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica"></a>

## Regra prática

> Abstração significa expor a capacidade essencial e esconder detalhes de implementação. Em Java, interfaces são ótimas para contratos e desacoplamento. Classes abstratas fazem sentido quando subclasses compartilham estado ou comportamento base e existe uma relação de especialização clara.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica-2"></a>

## Regra prática

> Herança modela uma relação IS-A. Eu uso quando existe uma especialização real e a subclasse pode substituir a superclasse preservando seu contrato. Evito usar herança apenas para reaproveitar código, porque ela aumenta o acoplamento entre pai e filhos.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita"></a>

## Overriding — Sobrescrita

> Polimorfismo permite tratar diferentes implementações através de um mesmo contrato. Em Java, quando uma referência da interface aponta para uma implementação concreta, o método sobrescrito é resolvido em runtime através de dynamic dispatch.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-delegacao"></a>

## 3 Delegação

> Composição representa relação HAS-A e permite montar comportamentos através de dependências. Eu normalmente prefiro composição quando quero reutilizar comportamento ou trocar implementações, porque ela reduz o acoplamento estrutural criado pela herança.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-observacao-pratica"></a>

## Observação prática

> Associação representa relacionamento geral entre objetos. Agregação é uma relação todo-parte mais fraca, em que a parte pode existir sozinha. Composição é mais forte e normalmente implica que a parte pertence ao ciclo de vida do todo.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-objetivo"></a>

## Objetivo

> Eu procuro baixa dependência entre componentes e alta coesão dentro de cada classe. Baixo acoplamento facilita substituição e evolução; alta coesão mantém responsabilidades relacionadas no mesmo lugar.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-cuidado"></a>

## Cuidado

> equals define igualdade lógica, hashCode precisa respeitar essa igualdade para estruturas hash e toString fornece uma representação textual útil para diagnóstico. Em entidades mutáveis, também tomo cuidado para não alterar campos usados em hashCode enquanto o objeto está dentro de HashMap ou HashSet.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-enums"></a>

## 3 Enums

> Em Java moderno, records são úteis para modelos orientados a dados com pouca cerimônia, sealed classes permitem controlar hierarquias e enums representam conjuntos fechados de valores com possibilidade de comportamento.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes"></a>

## 4 Encapsular invariantes

> Em modelagem OO eu procuro programar para abstrações, favorecer composição quando não existe uma relação IS-A legítima e manter invariantes dentro dos próprios objetos. Isso reduz acoplamento e evita que regras de negócio fiquem espalhadas.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia"></a>

## Correlação com concorrência

> Em produção eu uso POO para concentrar regras e responsabilidades, não apenas para criar hierarquias. Se encontro muitos condicionais por tipo, avalio polimorfismo. Se há setters permitindo estados inválidos, reforço encapsulamento. Se a hierarquia ficou profunda, normalmente considero composição e delegação.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Programação Orientada a Objetos organiza o sistema em objetos que possuem estado e comportamento. Os pilares clássicos são encapsulamento, abstração, herança e polimorfismo.
>
> Encapsulamento é controlar como o estado interno pode ser acessado e modificado, protegendo invariantes. Abstração permite expor contratos e esconder detalhes de implementação, normalmente através de interfaces ou classes abstratas.
>
> Herança representa uma relação IS-A e permite especialização, mas cria acoplamento entre superclasse e subclasses, então eu evito usá-la apenas para reaproveitar código. Quando quero combinar comportamentos ou trocar implementações, normalmente prefiro composição.
>
> Polimorfismo permite trabalhar com diferentes implementações através de um mesmo contrato. Por exemplo, um serviço pode depender de PagamentoGateway e receber implementações diferentes sem alterar sua lógica principal.
>
> Na prática, procuro manter alta coesão, baixo acoplamento, programar para abstrações e colocar regras próximas dos objetos responsáveis por elas. Em Java moderno, também utilizo records para objetos orientados a dados e sealed classes quando quero controlar uma hierarquia fechada.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos"></a>

## Resposta de 30 segundos

> POO em Java organiza o software em objetos com estado e comportamento. Os pilares principais são encapsulamento, abstração, herança e polimorfismo. Eu uso encapsulamento para proteger invariantes, abstrações para desacoplar implementações e polimorfismo para permitir comportamentos diferentes através do mesmo contrato. Herança eu reservo para relações IS-A reais; quando quero flexibilidade e reutilização de comportamento, geralmente prefiro composição.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-sumario"></a>

# Spring Core

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-core-visao-geral)
- [Regra prática](#spring-spring-core-regra-pratica)
- [5 Ciclo de Vida](#spring-spring-core-5-ciclo-de-vida)
- [Trade-off](#spring-spring-core-trade-off)
- [Trade-off](#spring-spring-core-trade-off-2)
- [Atenção](#spring-spring-core-atencao)
- [Resposta Completa de Entrevista](#spring-spring-core-resposta-completa-de-entrevista)

---

<a id="spring-spring-core-visao-geral"></a>

## Visão Geral

> Spring Core fornece o container de IoC e o mecanismo de Dependency Injection. Em vez de as classes criarem diretamente suas dependências, o container gerencia esses objetos como beans e injeta as dependências necessárias. Isso reduz acoplamento e facilita testes, configuração e substituição de implementações.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-regra-pratica"></a>

## Regra prática

> IoC é a inversão do controle de criação e gerenciamento dos objetos para o container. Dependency Injection é a forma como o container fornece essas dependências. Prefiro constructor injection porque deixa o contrato explícito e facilita testes.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-5-ciclo-de-vida"></a>

## 5 Ciclo de Vida

> Bean é um objeto gerenciado pelo Spring. O container cuida de criação, injeção, lifecycle e scope. Por padrão os beans são singleton por ApplicationContext, então evito guardar estado mutável de requisição em services.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-trade-off"></a>

## Trade-off

> Quando existem múltiplos beans do mesmo tipo, uso `@Primary` para definir o padrão ou `@Qualifier` para selecionar explicitamente. `@Lazy` posterga inicialização, mas deve ser usado com critério.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-trade-off-2"></a>

## Trade-off

> Spring usa proxies em recursos como transações, cache e segurança. O proxy intercepta chamadas ao bean e aplica comportamento adicional. Um cuidado clássico é self-invocation, porque chamadas internas podem não passar pelo proxy.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-atencao"></a>

## Atenção

> Spring permite externalizar configuração, ativar configurações por profiles e publicar eventos locais. Eu diferencio eventos dentro do processo de mensageria distribuída.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring Core é a base do ecossistema e fornece IoC, Dependency Injection e gerenciamento de beans. O container cria componentes, resolve dependências e controla lifecycle e scopes.
>
> Prefiro constructor injection para dependências obrigatórias porque deixa o contrato explícito, facilita testes e permite objetos consistentes. Beans são singleton por ApplicationContext por padrão, então evito estado mutável compartilhado.
>
> Também é importante entender proxies, porque vários recursos como `@Transactional`, cache e segurança dependem de interceptação. Isso explica problemas clássicos de self-invocation.
>
> Para configuração, uso propriedades externas e profiles com moderação, mantendo a aplicação previsível.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-sumario"></a>

# Spring Boot

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-boot-visao-geral)
- [2 Back-off](#spring-spring-boot-2-back-off)
- [3 Profiles](#spring-spring-boot-3-profiles)
- [Embedded Server](#spring-spring-boot-embedded-server)
- [3 Segurança dos Endpoints](#spring-spring-boot-3-seguranca-dos-endpoints)
- [Startup e Diagnóstico](#spring-spring-boot-startup-e-diagnostico)
- [Resposta Completa de Entrevista](#spring-spring-boot-resposta-completa-de-entrevista)

---

<a id="spring-spring-boot-visao-geral"></a>

## Visão Geral

> Spring Boot não substitui o Spring Framework. Ele reduz configuração manual através de auto-configuration, starters, servidor embarcado, configuração externa e ferramentas operacionais como Actuator.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-2-back-off"></a>

## 2 Back-off

> Auto-configuration é condicional. O Boot observa classpath, propriedades e beans existentes e registra defaults quando fazem sentido. Se eu forneço uma configuração própria, muitas auto-configurações fazem back-off.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-3-profiles"></a>

## 3 Profiles

> Para configuração externa, prefiro `@ConfigurationProperties` quando existe um grupo de propriedades. Isso melhora tipagem, validação e manutenção em comparação com espalhar `@Value`.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-embedded-server"></a>

## Embedded Server

> O servidor embarcado permite empacotar aplicação e servidor juntos, simplificando deploy e operação.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-3-seguranca-dos-endpoints"></a>

## 3 Segurança dos Endpoints

> Actuator fornece endpoints operacionais e integração com métricas. Em produção eu exponho somente o necessário, protejo endpoints sensíveis e uso health/readiness/liveness e métricas para observabilidade.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-startup-e-diagnostico"></a>

## Startup e Diagnóstico

> Para diagnosticar auto-configuration eu verifico condições, classpath, propriedades e beans existentes. O comportamento normalmente deriva de uma condition satisfeita ou não satisfeita.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring Boot simplifica aplicações Spring através de auto-configuration, starters, dependency management e convenções. A auto-configuração é condicional e leva em conta classpath, propriedades e beans existentes, fazendo back-off quando forneço configuração própria.
>
> Para propriedades estruturadas, prefiro `@ConfigurationProperties`. Em aplicações web, o Boot também simplifica o servidor embarcado.
>
> Em produção, Actuator e Micrometer são fundamentais para health, métricas e observabilidade. Também protejo endpoints sensíveis e configuro readiness e liveness corretamente.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-sumario"></a>

# Spring MVC

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-mvc-visao-geral)
- [4 HttpMessageConverter](#spring-spring-mvc-4-httpmessageconverter)
- [Regra prática](#spring-spring-mvc-regra-pratica)
- [Validação](#spring-spring-mvc-validacao)
- [2 @ControllerAdvice](#spring-spring-mvc-2-controlleradvice)
- [Interceptor](#spring-spring-mvc-interceptor)
- [Regra prática](#spring-spring-mvc-regra-pratica-2)
- [Resposta Completa de Entrevista](#spring-spring-mvc-resposta-completa-de-entrevista)

---

<a id="spring-spring-mvc-visao-geral"></a>

## Visão Geral

> Spring MVC é o framework web baseado em Servlet do Spring. Ele usa o DispatcherServlet como front controller, resolve o handler, executa o controller e converte request e response através de message converters.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-4-httpmessageconverter"></a>

## 4 HttpMessageConverter

> O DispatcherServlet recebe a requisição, usa HandlerMapping para encontrar o controller e HandlerAdapter para executá-lo. Os HttpMessageConverters fazem a conversão entre o corpo HTTP e objetos Java.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica"></a>

## Regra prática

> Controller deve lidar principalmente com rota, input, validação, status HTTP e output. Regra de negócio relevante fica em serviços ou no domínio.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-validacao"></a>

## Validação

> Uso Bean Validation para regras estruturais do input. Regras de negócio permanecem na camada responsável pelo domínio.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-2-controlleradvice"></a>

## 2 @ControllerAdvice

> Centralizo tratamento HTTP de exceptions com `@RestControllerAdvice`, transformando exceptions de aplicação em respostas consistentes sem vazar detalhes internos.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-interceptor"></a>

## Interceptor

> Filter atua no nível Servlet e é adequado para preocupações HTTP gerais. Interceptor atua dentro do Spring MVC e consegue trabalhar com o handler selecionado.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica-2"></a>

## Regra prática

> Escolho MVC ou WebFlux com base no stack inteiro. Se minhas dependências são bloqueantes, MVC costuma ser mais simples e coerente. WebFlux faz mais sentido quando o fluxo é realmente não bloqueante de ponta a ponta.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring MVC é o stack web baseado em Servlet. O DispatcherServlet funciona como front controller, resolve o handler, executa o controller e usa message converters para transformar JSON em objetos Java e vice-versa.
>
> Nos controllers, concentro preocupações HTTP e delego regra de negócio. Uso Bean Validation para validações estruturais e `@RestControllerAdvice` para padronizar erros.
>
> Também diferencio Filter, que atua no nível Servlet, de Interceptor, que atua no pipeline MVC. Para escolher MVC ou WebFlux, considero se o stack é bloqueante ou realmente reativo de ponta a ponta.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-sumario"></a>

# Spring Data

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-data-visao-geral)
- [Spring Data vs JPA vs Hibernate](#spring-spring-data-spring-data-vs-jpa-vs-hibernate)
- [4 @Query](#spring-spring-data-4-query)
- [3 Dirty Checking](#spring-spring-data-3-dirty-checking)
- [4 Rollback](#spring-spring-data-4-rollback)
- [Cuidado](#spring-spring-data-cuidado)
- [Slice](#spring-spring-data-slice)
- [2 Pessimistic Lock](#spring-spring-data-2-pessimistic-lock)
- [Auditing](#spring-spring-data-auditing)
- [Resposta Completa de Entrevista](#spring-spring-data-resposta-completa-de-entrevista)

---

<a id="spring-spring-data-visao-geral"></a>

## Visão Geral

> Spring Data JPA fornece abstrações de repository sobre JPA, reduzindo boilerplate. JPA é a especificação de persistência e Hibernate é uma implementação comum dessa especificação.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-spring-data-vs-jpa-vs-hibernate"></a>

## Spring Data vs JPA vs Hibernate

> JPA define o contrato de ORM, Hibernate implementa esse contrato e Spring Data JPA adiciona repositories e integração com Spring.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-4-query"></a>

## 4 @Query

> Repositories reduzem boilerplate. Derived queries funcionam bem para casos simples; para consultas mais complexas uso `@Query`, Specifications ou queries dedicadas.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-3-dirty-checking"></a>

## 3 Dirty Checking

> O persistence context gerencia entidades. Quando uma entidade está managed, o provider pode detectar alterações por dirty checking e sincronizá-las no flush ou commit.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-4-rollback"></a>

## 4 Rollback

> `@Transactional` define o boundary transacional e normalmente funciona via proxy. Também considero propagation, isolation e rollback. Evito `REQUIRES_NEW` sem necessidade porque cria transação independente e pode aumentar uso do pool.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-cuidado"></a>

## Cuidado

> N+1 ocorre quando uma query principal dispara várias queries adicionais para relacionamentos. Detecto por logs/APM e resolvo conforme o caso com fetch join, EntityGraph, projection ou query dedicada.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-slice"></a>

## Slice

> Uso `Page` quando preciso do total e `Slice` quando basta saber se existe próxima página, evitando count caro em alguns cenários.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-2-pessimistic-lock"></a>

## 2 Pessimistic Lock

> Optimistic locking detecta conflito por versão e funciona bem quando conflitos são raros. Pessimistic locking bloqueia no banco e pode ser necessário quando o custo do conflito é alto, mas aumenta contenção.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-auditing"></a>

## Auditing

> Projections reduzem dados carregados, Specifications ajudam em filtros dinâmicos e auditing automatiza metadados de criação e alteração.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring Data JPA reduz boilerplate de acesso a dados através de repositories. Ele fica sobre JPA, que é a especificação, enquanto Hibernate é uma implementação comum.
>
> Para trabalhar bem com JPA, é importante entender persistence context, estados da entidade, dirty checking e transações. Também presto atenção ao fetching, porque N+1 pode degradar fortemente a aplicação.
>
> Em concorrência de dados, escolho optimistic ou pessimistic locking conforme frequência e custo do conflito. Em produção, acompanho queries, índices, pool de conexões e tempo de transação, porque ORM não elimina a necessidade de entender o banco.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-sumario"></a>

# Spring Security

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-security-visao-geral)
- [Authentication vs Authorization](#spring-spring-security-authentication-vs-authorization)
- [Security Filter Chain](#spring-spring-security-security-filter-chain)
- [4 UserDetailsService](#spring-spring-security-4-userdetailsservice)
- [Passwords](#spring-spring-security-passwords)
- [2 CORS](#spring-spring-security-2-cors)
- [JWT e OAuth2 Resource Server](#spring-spring-security-jwt-e-oauth2-resource-server)
- [Resposta Completa de Entrevista](#spring-spring-security-resposta-completa-de-entrevista)

---

<a id="spring-spring-security-visao-geral"></a>

## Visão Geral

> Spring Security atua principalmente através de uma cadeia de filtros antes de a requisição chegar ao controller. Ele estabelece a identidade autenticada no SecurityContext e depois aplica regras de autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-authentication-vs-authorization"></a>

## Authentication vs Authorization

> Authentication valida identidade. Authorization decide se essa identidade pode acessar determinado recurso ou executar determinada operação.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-security-filter-chain"></a>

## Security Filter Chain

> Spring Security é filter-based no stack Servlet. A `SecurityFilterChain` define mecanismos e regras aplicados antes de a requisição alcançar o MVC.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-4-userdetailsservice"></a>

## 4 UserDetailsService

> O AuthenticationManager coordena o processo e delega a AuthenticationProviders. Em autenticação tradicional, o provider pode usar UserDetailsService para carregar o usuário e PasswordEncoder para validar a senha.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-passwords"></a>

## Passwords

> Senhas devem ser armazenadas com password hashing apropriado, nunca reversível ou em texto puro. Spring abstrai isso com PasswordEncoder.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-2-cors"></a>

## 2 CORS

> CSRF protege contra requisições forjadas quando credenciais são enviadas automaticamente pelo browser. CORS é uma política de browser para acesso entre origens e não substitui autenticação ou autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-jwt-e-oauth2-resource-server"></a>

## JWT e OAuth2 Resource Server

> JWT é um formato de token, enquanto OAuth2 define fluxos de autorização. Em um Resource Server, Spring Security valida o bearer token, cria a Authentication e usa claims ou scopes para autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring Security usa uma cadeia de filtros para autenticar e autorizar requisições antes de elas chegarem ao controller. Authentication identifica o usuário e Authorization decide o que ele pode fazer.
>
> Em autenticação tradicional, AuthenticationManager delega a AuthenticationProviders, que podem usar UserDetailsService e PasswordEncoder. Em OAuth2 Resource Server, o Spring valida bearer tokens e transforma claims/scopes em authorities.
>
> Também diferencio CSRF de CORS: CSRF protege contra requisições forjadas quando credenciais são enviadas automaticamente, enquanto CORS controla acesso entre origens no browser.
>
> Em produção, aplico least privilege, protejo segredos, não registro tokens em log e valido corretamente issuer, audience, expiração e permissões.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-sumario"></a>

# Spring Cloud

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Importante](#spring-spring-cloud-importante)
- [Configuração Distribuída](#spring-spring-cloud-configuracao-distribuida)
- [Service Discovery](#spring-spring-cloud-service-discovery)
- [Cuidado](#spring-spring-cloud-cuidado)
- [OpenFeign](#spring-spring-cloud-openfeign)
- [4 Bulkhead](#spring-spring-cloud-4-bulkhead)
- [Trade-off](#spring-spring-cloud-trade-off)
- [Observabilidade Distribuída](#spring-spring-cloud-observabilidade-distribuida)
- [Resposta Completa de Entrevista](#spring-spring-cloud-resposta-completa-de-entrevista)

---

<a id="spring-spring-cloud-importante"></a>

## Importante

> Spring Cloud complementa Spring Boot com padrões e integrações para sistemas distribuídos, como configuração centralizada, discovery, gateway, load balancing, clients declarativos, resiliência e mensageria.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-configuracao-distribuida"></a>

## Configuração Distribuída

> Config Server centraliza propriedades de múltiplos serviços. É útil para consistência e versionamento, mas passa a ser parte crítica da infraestrutura e precisa de alta disponibilidade e segurança.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-service-discovery"></a>

## Service Discovery

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, avalio primeiro os mecanismos nativos antes de adicionar registry dedicado.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-cuidado"></a>

## Cuidado

> Gateway centraliza preocupações de borda, como roteamento, filtros e políticas transversais. Evito colocar regra de negócio nele.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-openfeign"></a>

## OpenFeign

> OpenFeign simplifica o client HTTP, mas não remove propriedades de rede. Continuo configurando timeout, tratamento de erro, resiliência e observabilidade.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-4-bulkhead"></a>

## 4 Bulkhead

> Em chamadas remotas combino timeout, circuit breaker e, quando faz sentido, retry com backoff e jitter. Retry precisa respeitar idempotência. Bulkhead impede que uma dependência degradada consuma todos os recursos locais.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-trade-off"></a>

## Trade-off

> Spring Cloud Stream facilita integração com mensageria, mas não elimina a necessidade de conhecer o broker. Com Kafka, ainda preciso entender partições, consumer groups, offsets e semântica de entrega.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-observabilidade-distribuida"></a>

## Observabilidade Distribuída

> Em sistemas distribuídos, métricas isoladas não bastam. Correlaciono logs, métricas e traces para localizar onde latência ou erro foi introduzido na cadeia.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-resposta-completa-de-entrevista"></a>

## Resposta Completa de Entrevista

> Spring Cloud fornece integrações para problemas comuns de sistemas distribuídos. Eu destacaria configuração centralizada, service discovery, gateway, load balancing, clients declarativos, resiliência, mensageria e observabilidade.
>
> OpenFeign simplifica o client HTTP, mas uma chamada remota continua sujeita a timeout, erro e latência. Por isso configuro resiliência por dependência, usando timeout, circuit breaker e retry apenas quando a operação suporta reexecução.
>
> Também considero o ambiente. Em Kubernetes, discovery e load balancing podem ser fornecidos pela própria plataforma, então evito adicionar componentes redundantes.
>
> Em produção, acompanho traces, métricas e logs distribuídos para entender a cadeia inteira.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-ecossistema-spring-visao-geral-sumario"></a>

# Ecossistema Spring — Visão Geral

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Resposta de 60 segundos sobre o ecossistema](#spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema)

---

<a id="spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema"></a>

## Resposta de 60 segundos sobre o ecossistema

> Eu vejo o ecossistema Spring em camadas. Spring Core fornece IoC, Dependency Injection e gerenciamento de beans. Spring Boot automatiza a configuração e simplifica bootstrap e operação. Spring MVC cobre o stack HTTP baseado em Servlet, enquanto Spring Data abstrai parte do acesso a dados sobre tecnologias como JPA. Spring Security fornece a cadeia de autenticação e autorização. Quando a aplicação entra em um cenário distribuído, Spring Cloud adiciona integrações para configuração, gateway, clients, resiliência, mensageria e observabilidade.
>
> O ponto importante é entender que essas abstrações não eliminam os fundamentos. Mesmo usando Spring Data, eu preciso entender banco e transações; usando Feign, preciso entender rede, timeout e resiliência; usando Security, preciso entender autenticação e autorização.

[↑ Sumário do tópico](#spring-ecossistema-spring-visao-geral-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="sql-sql-bancos-relacionais-sumario"></a>

# SQL & Bancos Relacionais

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — SQL](#sql-visao-geral-sql)
- [INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN](#sql-inner-join-left-join-right-join-e-full-join)
- [WHERE vs HAVING](#sql-where-vs-having)
- [GROUP BY](#sql-group-by)
- [Primary Key, Unique e Foreign Key](#sql-primary-key-unique-e-foreign-key)
- [Índices](#sql-indices)
- [Índice Composto e Ordem das Colunas](#sql-indice-composto-e-ordem-das-colunas)
- [Seletividade e Cardinalidade](#sql-seletividade-e-cardinalidade)
- [Quando um índice pode não ser usado](#sql-quando-um-indice-pode-nao-ser-usado)
- [Sargabilidade](#sql-sargabilidade)
- [Execution Plan](#sql-execution-plan)
- [Otimização de Query em Produção](#sql-otimizacao-de-query-em-producao)
- [Normalização vs Desnormalização](#sql-normalizacao-vs-desnormalizacao)
- [ACID](#sql-acid)
- [Isolation Levels](#sql-isolation-levels)
- [Locks](#sql-locks)
- [Deadlock no Banco](#sql-deadlock-no-banco)
- [MVCC](#sql-mvcc)
- [Optimistic vs Pessimistic Concurrency](#sql-optimistic-vs-pessimistic-concurrency)
- [CTE vs Subquery](#sql-cte-vs-subquery)
- [Window Functions](#sql-window-functions)
- [EXISTS vs IN vs JOIN](#sql-exists-vs-in-vs-join)
- [UNION vs UNION ALL](#sql-union-vs-union-all)
- [NULL](#sql-null)
- [DELETE vs TRUNCATE vs DROP](#sql-delete-vs-truncate-vs-drop)
- [Paginação OFFSET vs Keyset](#sql-paginacao-offset-vs-keyset)
- [Prepared Statements e SQL Injection](#sql-prepared-statements-e-sql-injection)
- [Transação Longa](#sql-transacao-longa)
- [N+1 Queries](#sql-n-1-queries)
- [Resposta Completa de Entrevista — SQL](#sql-resposta-completa-de-entrevista-sql)

---

<a id="sql-visao-geral-sql"></a>

## Visão Geral — SQL

> SQL é a linguagem usada para consultar e manipular dados em bancos relacionais. Em uma entrevista, eu separo o tema em modelagem, consultas, índices, transações e concorrência. Para performance, não olho apenas a query: considero cardinalidade, seletividade, índices, volume de dados e o plano de execução.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-inner-join-left-join-right-join-e-full-join"></a>

## INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN

> `INNER JOIN` retorna somente linhas com correspondência entre os lados. `LEFT JOIN` preserva todas as linhas da tabela à esquerda e retorna `NULL` quando não existe correspondência à direita. `RIGHT JOIN` faz o inverso e `FULL JOIN` preserva linhas dos dois lados. Na prática, escolho o JOIN de acordo com a semântica do relacionamento, não por performance presumida.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-where-vs-having"></a>

## WHERE vs HAVING

> `WHERE` filtra linhas antes da agregação. `HAVING` filtra grupos depois do `GROUP BY`. Sempre que o filtro não depende de uma função agregadora, prefiro colocá-lo no `WHERE` para reduzir o conjunto de dados antes da agregação.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-group-by"></a>

## GROUP BY

> `GROUP BY` agrupa linhas para aplicar funções agregadoras como `COUNT`, `SUM`, `AVG`, `MIN` e `MAX`. Um cuidado importante é que colunas selecionadas fora de agregações precisam respeitar as regras de agrupamento do banco. Em produção também observo o custo de ordenar ou agregar grandes volumes.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-primary-key-unique-e-foreign-key"></a>

## Primary Key, Unique e Foreign Key

> Primary Key identifica unicamente cada linha e não permite `NULL`. Uma constraint `UNIQUE` também garante unicidade, mas sua semântica de `NULL` pode variar entre bancos. Foreign Key garante integridade referencial entre tabelas. Eu uso constraints no banco porque consistência não deve depender apenas da aplicação.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-indices"></a>

## Índices

> Índice é uma estrutura auxiliar que reduz o custo de localizar dados, normalmente evitando varrer toda a tabela. O trade-off é que índices ocupam espaço e aumentam o custo de `INSERT`, `UPDATE` e `DELETE`, porque também precisam ser mantidos. Portanto, não considero 'mais índices' automaticamente melhor.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-indice-composto-e-ordem-das-colunas"></a>

## Índice Composto e Ordem das Colunas

> Em um índice composto, a ordem das colunas é importante. Eu defino a ordem com base nos padrões reais de filtro, join e ordenação. Em índices B-tree, consultas tendem a aproveitar melhor os prefixos iniciais do índice; por isso um índice em `(cliente_id, status, data)` não equivale a um índice em `(status, cliente_id, data)`.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-seletividade-e-cardinalidade"></a>

## Seletividade e Cardinalidade

> Cardinalidade descreve quantos valores distintos existem em uma coluna e seletividade indica o quanto um predicado reduz o conjunto de linhas. Índices tendem a ser mais úteis quando a consulta elimina uma parcela relevante dos dados. Para colunas com poucos valores distintos, o otimizador pode preferir um scan dependendo da distribuição e do volume.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-quando-um-indice-pode-nao-ser-usado"></a>

## Quando um índice pode não ser usado

> Um índice pode não ser escolhido quando o filtro retorna grande parte da tabela, quando existe conversão ou função sobre a coluna indexada, quando a estatística está ruim ou quando outro plano é estimado como mais barato. Por isso eu confirmo com o plano de execução em vez de assumir que criar um índice garante seu uso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sargabilidade"></a>

## Sargabilidade

> Uma condição sargable permite que o banco use um índice de forma eficiente para localizar o intervalo de dados. Por exemplo, aplicar uma função diretamente sobre a coluna filtrada pode impedir ou reduzir o uso do índice. Eu tento escrever o predicado de forma que a coluna indexada permaneça pesquisável e valido o resultado no execution plan.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-execution-plan"></a>

## Execution Plan

> O execution plan mostra como o otimizador pretende executar uma consulta: scans, seeks, joins, ordenações, estimativas de linhas e custos relativos. Para investigar query lenta, eu comparo estimativa com quantidade real de linhas, verifico scans inesperados, joins caros, sorts, índices ausentes e estatísticas. O objetivo é encontrar a causa, não apenas 'forçar índice'.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-otimizacao-de-query-em-producao"></a>

## Otimização de Query em Produção

> Eu começo pela evidência: latência, frequência, volume retornado e execution plan. Depois verifico filtros, joins, índices, cardinalidade, estatísticas, quantidade de linhas lidas versus retornadas e possíveis operações O(n) desnecessárias. Só então considero reescrever a query, criar ou alterar índice, paginar ou remodelar o acesso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-normalizacao-vs-desnormalizacao"></a>

## Normalização vs Desnormalização

> Normalização reduz redundância e anomalias de atualização, normalmente separando entidades e relacionamentos em tabelas coerentes. Desnormalização duplica ou pré-calcula dados para reduzir joins ou acelerar leitura. Eu começo normalizado e desnormalizo apenas quando existe um requisito de performance comprovado, aceitando o custo adicional de consistência.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-acid"></a>

## ACID

> ACID significa Atomicidade, Consistência, Isolamento e Durabilidade. Atomicidade garante que a transação seja aplicada por inteiro ou revertida; consistência preserva regras e constraints; isolamento controla interferência entre transações concorrentes; durabilidade garante que um commit confirmado sobreviva a falhas conforme as garantias do banco.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-isolation-levels"></a>

## Isolation Levels

> Os níveis clássicos são `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ` e `SERIALIZABLE`. Quanto maior o isolamento, menor a possibilidade de fenômenos como dirty read, non-repeatable read e phantom read, mas potencialmente maior o custo de concorrência. O comportamento exato depende do banco e do uso de locks ou MVCC.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-locks"></a>

## Locks

> Locks coordenam acesso concorrente aos dados para preservar consistência. Podem existir locks de leitura e escrita em diferentes granularidades conforme o banco. O problema aparece quando transações seguram locks por tempo demais, aumentando espera, latência e risco de deadlock. Por isso mantenho transações curtas e acesso aos recursos em ordem previsível.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-deadlock-no-banco"></a>

## Deadlock no Banco

> Deadlock ocorre quando transações criam uma espera circular por locks. O banco normalmente detecta o ciclo e aborta uma das transações. Para reduzir a ocorrência, mantenho transações curtas, adquiro recursos em ordem consistente, garanto bons índices para evitar bloquear linhas desnecessárias e preparo a aplicação para retry da transação vítima quando apropriado.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-mvcc"></a>

## MVCC

> MVCC, Multi-Version Concurrency Control, mantém versões de linhas para permitir que leituras e escritas concorram com menos bloqueio em determinados níveis de isolamento. Isso melhora concorrência, mas não elimina conflitos, locks ou necessidade de entender o isolamento específico do banco.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-optimistic-vs-pessimistic-concurrency"></a>

## Optimistic vs Pessimistic Concurrency

> Concorrência otimista assume que conflitos são raros e detecta alteração concorrente, por exemplo usando uma coluna de versão. Concorrência pessimista bloqueia o recurso antes da alteração. Eu prefiro otimista quando conflitos são raros e pessimista quando o custo de conflito é alto e a contenção esperada justifica o bloqueio.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-cte-vs-subquery"></a>

## CTE vs Subquery

> CTE melhora legibilidade e permite estruturar consultas complexas, além de suportar recursão em bancos que oferecem CTE recursiva. Subquery pode ser suficiente para casos pequenos. Não assumo que CTE é automaticamente mais rápida ou mais lenta: o otimizador pode tratá-las de maneiras diferentes, então performance deve ser validada no plano.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-window-functions"></a>

## Window Functions

> Window Functions calculam valores sobre um conjunto relacionado de linhas sem colapsar o resultado como um `GROUP BY`. Exemplos são `ROW_NUMBER`, `RANK`, `LAG`, `LEAD` e agregações com `OVER`. São muito úteis para ranking, comparação com linha anterior e acumulados sem recorrer a várias subqueries.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-exists-vs-in-vs-join"></a>

## EXISTS vs IN vs JOIN

> `EXISTS`, `IN` e `JOIN` expressam necessidades diferentes e otimizadores modernos podem gerar planos equivalentes em alguns casos. Uso `EXISTS` quando quero testar existência, `JOIN` quando preciso combinar dados e `IN` quando a semântica de pertencimento é clara. Para performance, comparo os planos em vez de aplicar uma regra universal.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-union-vs-union-all"></a>

## UNION vs UNION ALL

> `UNION` combina resultados removendo duplicados; `UNION ALL` apenas concatena os conjuntos. Como remover duplicados exige trabalho adicional, prefiro `UNION ALL` quando duplicidade é aceitável ou impossível pela lógica do domínio.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-null"></a>

## NULL

> `NULL` representa ausência ou valor desconhecido e não deve ser comparado com `=`. Uso `IS NULL` e `IS NOT NULL`. Também lembro que lógica com `NULL` é ternária — true, false e unknown — e isso afeta filtros, joins e agregações.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-delete-vs-truncate-vs-drop"></a>

## DELETE vs TRUNCATE vs DROP

> `DELETE` remove linhas e permite filtro com `WHERE`. `TRUNCATE` remove os dados da tabela de forma mais ampla e costuma ter comportamento e logging diferentes conforme o banco. `DROP` remove o próprio objeto da estrutura. Detalhes de transação, identidade e rollback variam entre SGBDs, então evito generalizar além disso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-paginacao-offset-vs-keyset"></a>

## Paginação OFFSET vs Keyset

> Paginação com `OFFSET/LIMIT` é simples, mas offsets altos podem exigir que o banco percorra e descarte muitas linhas e podem gerar inconsistência visual quando os dados mudam entre páginas. Keyset pagination usa a última chave ordenada como cursor e costuma escalar melhor para grandes volumes, desde que exista ordenação estável e índice adequado.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-prepared-statements-e-sql-injection"></a>

## Prepared Statements e SQL Injection

> Para evitar SQL Injection, não concateno entrada do usuário na query. Uso parâmetros através de prepared statements ou APIs que façam binding corretamente. Isso separa dado de código SQL; validação de entrada continua importante, mas não substitui parametrização.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-transacao-longa"></a>

## Transação Longa

> Transações longas mantêm recursos, versões ou locks por mais tempo, aumentando contenção e consumo do banco. Em serviços backend, procuro manter o boundary transacional apenas ao redor do trabalho que realmente precisa ser atômico e evito chamadas remotas lentas dentro da transação sempre que possível.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-n-1-queries"></a>

## N+1 Queries

> N+1 acontece quando uma consulta inicial carrega uma coleção de registros e depois são executadas consultas adicionais para cada item. O efeito é aumento de round-trips e latência. Eu detecto por logs ou APM e resolvo alterando a estratégia de carregamento, usando joins, batch ou queries específicas conforme o caso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-resposta-completa-de-entrevista-sql"></a>

## Resposta Completa de Entrevista — SQL

> Em SQL, eu considero três dimensões principais: modelagem e integridade, consultas e performance, e transações e concorrência. Na modelagem, uso primary keys, foreign keys e constraints para manter consistência. Nas consultas, entendo joins, agregações, subqueries, CTEs e window functions, mas para performance o ponto principal é saber ler execution plan e escolher índices de acordo com o padrão de acesso.
>
> > Também considero os trade-offs dos índices: eles aceleram leitura, mas custam espaço e escrita. Em transações, entendo ACID, níveis de isolamento, locks, deadlocks e MVCC. Em produção, eu não otimizo por tentativa e erro; começo pelas métricas e pela query real, verifico plano de execução, cardinalidade, seletividade, índices e quantidade de linhas processadas antes de alterar a solução.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="arquitetura-arquitetura-de-software-sumario"></a>

# Arquitetura de Software

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Arquitetura de Software](#arquitetura-visao-geral-arquitetura-de-software)
- [Arquitetura em Camadas](#arquitetura-arquitetura-em-camadas)
- [Arquitetura Hexagonal](#arquitetura-arquitetura-hexagonal)
- [Clean Architecture](#arquitetura-clean-architecture)
- [Arquitetura Hexagonal vs Clean Architecture](#arquitetura-arquitetura-hexagonal-vs-clean-architecture)
- [Monólito](#arquitetura-monolito)
- [Monólito Modular](#arquitetura-monolito-modular)
- [Microserviços](#arquitetura-microservicos)
- [Monólito vs Microserviços](#arquitetura-monolito-vs-microservicos)
- [Quando extrair um Microserviço](#arquitetura-quando-extrair-um-microservico)
- [Bounded Context](#arquitetura-bounded-context)
- [DDD e Arquitetura](#arquitetura-ddd-e-arquitetura)
- [Entidade vs Value Object](#arquitetura-entidade-vs-value-object)
- [Agregado e Aggregate Root](#arquitetura-agregado-e-aggregate-root)
- [Arquitetura Orientada a Eventos](#arquitetura-arquitetura-orientada-a-eventos)
- [Evento vs Comando](#arquitetura-evento-vs-comando)
- [Síncrono vs Assíncrono](#arquitetura-sincrono-vs-assincrono)
- [Consistência Forte vs Eventual](#arquitetura-consistencia-forte-vs-eventual)
- [CAP Theorem](#arquitetura-cap-theorem)
- [CQRS](#arquitetura-cqrs)
- [Event Sourcing](#arquitetura-event-sourcing)
- [Saga](#arquitetura-saga)
- [Saga — Orquestração vs Coreografia](#arquitetura-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#arquitetura-outbox-pattern)
- [Idempotência](#arquitetura-idempotencia)
- [Retry, Timeout e Circuit Breaker na Arquitetura](#arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura)
- [Bulkhead](#arquitetura-bulkhead)
- [API Gateway](#arquitetura-api-gateway)
- [BFF — Backend for Frontend](#arquitetura-bff-backend-for-frontend)
- [Service Discovery](#arquitetura-service-discovery)
- [Load Balancing](#arquitetura-load-balancing)
- [Escalabilidade Vertical vs Horizontal](#arquitetura-escalabilidade-vertical-vs-horizontal)
- [Stateless vs Stateful](#arquitetura-stateless-vs-stateful)
- [Alta Disponibilidade](#arquitetura-alta-disponibilidade)
- [Tolerância a Falhas](#arquitetura-tolerancia-a-falhas)
- [Degradação Graciosa](#arquitetura-degradacao-graciosa)
- [Observabilidade](#arquitetura-observabilidade)
- [Logs, Métricas e Traces](#arquitetura-logs-metricas-e-traces)
- [SLO, SLI e SLA](#arquitetura-slo-sli-e-sla)
- [P95, P99 e Média](#arquitetura-p95-p99-e-media)
- [Backpressure](#arquitetura-backpressure)
- [Rate Limiting](#arquitetura-rate-limiting)
- [Cache](#arquitetura-cache)
- [Cache-Aside](#arquitetura-cache-aside)
- [Cache Stampede](#arquitetura-cache-stampede)
- [Banco por Serviço](#arquitetura-banco-por-servico)
- [Shared Database — Trade-off](#arquitetura-shared-database-trade-off)
- [Replicação e Read Replica](#arquitetura-replicacao-e-read-replica)
- [Sharding](#arquitetura-sharding)
- [CQRS vs Read Replica](#arquitetura-cqrs-vs-read-replica)
- [Arquitetura e Segurança](#arquitetura-arquitetura-e-seguranca)
- [12-Factor App](#arquitetura-12-factor-app)
- [Arquitetura Cloud-Native](#arquitetura-arquitetura-cloud-native)
- [Kubernetes na Arquitetura](#arquitetura-kubernetes-na-arquitetura)
- [Arquitetura Orientada a Mensagens](#arquitetura-arquitetura-orientada-a-mensagens)
- [At-most-once, At-least-once e Exactly-once](#arquitetura-at-most-once-at-least-once-e-exactly-once)
- [API REST vs Mensageria](#arquitetura-api-rest-vs-mensageria)
- [Versionamento de API](#arquitetura-versionamento-de-api)
- [Backward Compatibility](#arquitetura-backward-compatibility)
- [Strangler Fig Pattern](#arquitetura-strangler-fig-pattern)
- [Anti-Corruption Layer](#arquitetura-anti-corruption-layer)
- [Arquitetura Evolutiva](#arquitetura-arquitetura-evolutiva)
- [Trade-off Arquitetural](#arquitetura-trade-off-arquitetural)
- [Resposta Completa de Entrevista — Arquitetura](#arquitetura-resposta-completa-de-entrevista-arquitetura)

---

<a id="arquitetura-visao-geral-arquitetura-de-software"></a>

## Visão Geral — Arquitetura de Software

> Arquitetura de software define como responsabilidades, componentes, dados e integrações são organizados para atender requisitos funcionais e não funcionais. Em entrevista, eu procuro deixar claro que arquitetura não é escolher tecnologia, mas tomar decisões estruturais considerando trade-offs como escalabilidade, disponibilidade, consistência, complexidade, custo e capacidade de evolução.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-em-camadas"></a>

## Arquitetura em Camadas

> Arquitetura em camadas separa responsabilidades em níveis como apresentação, aplicação, domínio e infraestrutura. O principal benefício é organização e separação de responsabilidades. O trade-off é que, quando aplicada rigidamente, pode gerar acoplamento entre camadas e fluxos artificiais. Eu uso quando o domínio e a complexidade justificam essa separação, sem transformar a estrutura em burocracia.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal"></a>

## Arquitetura Hexagonal

> Arquitetura Hexagonal organiza o sistema em torno do domínio e usa portas e adaptadores para isolar infraestrutura. As portas representam contratos de entrada e saída; os adaptadores conectam HTTP, banco, mensageria ou APIs externas. O principal ganho é desacoplamento e testabilidade. O custo é mais abstração e mais classes, então aplico principalmente onde a independência de infraestrutura agrega valor.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-clean-architecture"></a>

## Clean Architecture

> Clean Architecture organiza dependências apontando para regras de negócio mais internas. Frameworks, banco e interface ficam nas bordas. O objetivo é manter o domínio independente de detalhes externos. O trade-off é aumento de abstrações e mapeamentos. Eu considero útil em sistemas com regra de negócio relevante e longa vida útil, mas evito aplicar de forma dogmática em CRUDs simples.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal-vs-clean-architecture"></a>

## Arquitetura Hexagonal vs Clean Architecture

> As duas buscam reduzir dependência de infraestrutura e proteger o domínio. Hexagonal enfatiza portas e adaptadores; Clean Architecture enfatiza círculos ou camadas com dependências apontando para dentro. Na prática, os princípios se sobrepõem bastante. Eu escolho a modelagem que deixa as dependências explícitas sem criar abstrações desnecessárias.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito"></a>

## Monólito

> Monólito é uma aplicação implantada como uma unidade única. Ele não é sinônimo de código ruim. Pode ser simples de desenvolver, testar, implantar e operar, especialmente no início. O problema aparece quando cresce sem modularização, gerando acoplamento e dificuldade de evolução. Eu começo simples e só distribuo quando existe uma razão concreta.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito-modular"></a>

## Monólito Modular

> Monólito modular mantém um único deploy, mas separa o sistema em módulos com fronteiras claras e dependências controladas. Ele preserva simplicidade operacional enquanto reduz acoplamento interno. Para muitos sistemas, é um excelente passo antes de microserviços, porque permite descobrir boundaries sem assumir de imediato o custo de distribuição.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-microservicos"></a>

## Microserviços

> Microserviços dividem o sistema em serviços implantáveis de forma independente, normalmente alinhados a capacidades de negócio. Os benefícios incluem autonomia de deploy, isolamento de falhas e escalabilidade independente. O custo é alto: rede, observabilidade distribuída, consistência eventual, versionamento, segurança, tracing, DevOps e operação. Eu uso microserviços quando esses benefícios compensam a complexidade distribuída.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito-vs-microservicos"></a>

## Monólito vs Microserviços

> Eu não trato microserviços como evolução obrigatória do monólito. Monólito reduz complexidade operacional e favorece consistência local. Microserviços ganham autonomia, escalabilidade e isolamento, mas introduzem falhas de rede e consistência distribuída. A decisão depende de escala organizacional, domínios independentes, frequência de deploy, requisitos de disponibilidade e capacidade operacional do time.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-quando-extrair-um-microservico"></a>

## Quando extrair um Microserviço

> Eu consideraria extrair um serviço quando existe uma fronteira de domínio clara e uma necessidade concreta, como escala independente, ritmo de deploy diferente, isolamento de falha, requisitos de segurança específicos ou ownership por outro time. Não extraio apenas porque uma classe ficou grande. Primeiro procuro um boundary estável e mensuro o custo operacional da separação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bounded-context"></a>

## Bounded Context

> Bounded Context, em DDD, define uma fronteira dentro da qual um modelo de domínio possui significado consistente. Em arquitetura de microserviços, ele ajuda a encontrar limites mais naturais para serviços. Um mesmo termo pode ter modelos diferentes em contextos distintos. Eu evito compartilhar o mesmo modelo de domínio entre serviços porque isso aumenta acoplamento.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-ddd-e-arquitetura"></a>

## DDD e Arquitetura

> Domain-Driven Design ajuda quando o problema de negócio é complexo. Eu destaco linguagem ubíqua, bounded contexts, entidades, value objects, agregados e serviços de domínio. DDD não significa obrigatoriamente microserviços. Pode ser aplicado em monólito modular e, na prática, ajuda mais a organizar o domínio do que a escolher infraestrutura.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-entidade-vs-value-object"></a>

## Entidade vs Value Object

> Entidade possui identidade própria ao longo do tempo. Value Object é definido por seus valores e normalmente é imutável. Por exemplo, um `Pedido` tende a ser entidade; `Dinheiro` pode ser um value object. Value Objects ajudam a encapsular regras e reduzir uso de tipos primitivos sem significado de negócio.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-agregado-e-aggregate-root"></a>

## Agregado e Aggregate Root

> Agregado é um conjunto de objetos de domínio tratado como uma unidade de consistência. A Aggregate Root é o ponto de entrada para alterações internas. Regras do agregado devem ser preservadas dentro dessa fronteira. Agregados muito grandes aumentam contenção e custo transacional, então tento mantê-los focados nas invariantes que realmente precisam de consistência forte.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-eventos"></a>

## Arquitetura Orientada a Eventos

> Arquitetura orientada a eventos comunica mudanças de estado através de eventos, reduzindo acoplamento temporal entre produtores e consumidores. Ela melhora extensibilidade e integração assíncrona, mas traz consistência eventual, duplicidade, ordenação, observabilidade e tratamento de falhas. Eu considero idempotência e semântica de entrega parte obrigatória do design.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-evento-vs-comando"></a>

## Evento vs Comando

> Comando expressa intenção: `CriarPedido`. Evento expressa um fato que já aconteceu: `PedidoCriado`. Um comando normalmente tem um destinatário esperado e pode falhar; um evento pode ter zero ou vários consumidores. Misturar esses conceitos costuma gerar contratos confusos em arquiteturas orientadas a mensagens.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-sincrono-vs-assincrono"></a>

## Síncrono vs Assíncrono

> Comunicação síncrona é mais simples quando o chamador precisa de resposta imediata, mas cria acoplamento temporal e propaga latência e falhas. Comunicação assíncrona reduz esse acoplamento e absorve picos, porém exige lidar com consistência eventual, idempotência e observabilidade. Eu escolho com base na necessidade de resposta e no comportamento esperado em falhas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-consistencia-forte-vs-eventual"></a>

## Consistência Forte vs Eventual

> Consistência forte garante que leitores observem o estado atualizado conforme a garantia transacional do sistema. Consistência eventual aceita um intervalo em que partes do sistema ainda convergem. Em sistemas distribuídos, consistência eventual permite maior desacoplamento e disponibilidade, mas exige que negócio e UX tolerem esse intervalo.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cap-theorem"></a>

## CAP Theorem

> CAP afirma que, diante de uma partição de rede, um sistema distribuído precisa priorizar consistência ou disponibilidade. Partição não é algo que normalmente escolhemos evitar em um sistema distribuído real; ela precisa ser considerada. Por isso, a discussão prática é como cada operação se comporta sob partição e qual garantia o domínio exige.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cqrs"></a>

## CQRS

> CQRS separa modelos ou caminhos de escrita e leitura. Pode variar desde handlers separados até armazenamentos distintos. O benefício é otimizar cada lado para necessidades diferentes e reduzir acoplamento entre modelos. O trade-off é maior complexidade, duplicação de modelos e possível consistência eventual. Eu não aplico CQRS apenas porque existem comandos e queries.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-event-sourcing"></a>

## Event Sourcing

> Event Sourcing persiste a sequência de eventos como fonte de verdade, em vez de armazenar apenas o estado atual. Isso oferece histórico completo e reconstrução de estado, mas aumenta bastante a complexidade de versionamento de eventos, replay, snapshots, debugging e evolução do modelo. É útil em domínios que realmente precisam desse histórico, não como padrão geral.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-saga"></a>

## Saga

> Saga coordena uma transação de negócio distribuída através de várias transações locais, usando ações compensatórias quando uma etapa falha. Pode ser por coreografia, via eventos, ou orquestração, com um coordenador explícito. Ela não fornece rollback ACID global; fornece uma estratégia de consistência de negócio entre serviços.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração, um componente central decide os próximos passos, o que facilita visualizar o fluxo, mas aumenta dependência no orquestrador. Na coreografia, serviços reagem a eventos, reduzindo controle central, porém o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade do processo e necessidade de governança do fluxo.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-outbox-pattern"></a>

## Outbox Pattern

> Outbox Pattern resolve o problema de atualizar o banco e publicar um evento sem depender de uma transação distribuída. A aplicação grava a mudança de negócio e um registro de outbox na mesma transação local. Depois, outro processo publica o evento. O trade-off é lidar com publicação assíncrona, deduplicação e limpeza da outbox.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-idempotencia"></a>

## Idempotência

> Uma operação idempotente pode ser repetida sem produzir efeitos adicionais indevidos. Em sistemas distribuídos isso é essencial porque retries e redelivery acontecem. Implemento com chave idempotente, unique constraint, registro de mensagens processadas ou lógica de negócio naturalmente idempotente, dependendo do caso.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura"></a>

## Retry, Timeout e Circuit Breaker na Arquitetura

> Timeout limita quanto tempo um serviço espera; retry tenta recuperar falhas transitórias; circuit breaker evita insistir em uma dependência degradada. Eles são complementares. Retry deve respeitar idempotência, usar backoff e jitter e caber dentro do budget de latência. Configuração errada pode amplificar uma falha em cascata.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que uma dependência degradada consuma toda a capacidade da aplicação. Posso aplicar com pools separados, semáforos ou limites de concorrência. O objetivo é conter falha e preservar capacidade para outras funções do sistema.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting e observabilidade. Ele pode simplificar clientes, mas não deve concentrar regras de negócio nem se tornar um novo monólito. Segurança e autorização internas ainda podem ser necessárias dependendo do trust boundary.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bff-backend-for-frontend"></a>

## BFF — Backend for Frontend

> BFF cria uma camada backend específica para as necessidades de um tipo de cliente, como web ou mobile. Ele reduz lógica de composição no frontend e permite contratos mais adequados, mas adiciona mais um serviço para operar. Eu uso quando os clientes possuem necessidades suficientemente diferentes para justificar essa camada.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-service-discovery"></a>

## Service Discovery

> Service Discovery permite localizar instâncias dinamicamente. Em plataformas como Kubernetes, esse recurso normalmente já existe através de Services e DNS. Eu evito adicionar um registry dedicado quando a plataforma já resolve o problema adequadamente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-load-balancing"></a>

## Load Balancing

> Load balancing distribui requisições entre múltiplas instâncias para melhorar capacidade e disponibilidade. Pode ocorrer no cliente, gateway, proxy ou infraestrutura. O algoritmo precisa considerar health e, em alguns casos, afinidade ou pesos. Load balancing não substitui autoscaling nem resiliência.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-escalabilidade-vertical-vs-horizontal"></a>

## Escalabilidade Vertical vs Horizontal

> Escala vertical aumenta recursos de uma instância; é simples, mas tem limites físicos e pode manter um ponto único de falha. Escala horizontal adiciona instâncias, aumentando capacidade e resiliência, porém exige que estado, sessão, cache e dados sejam projetados para distribuição. Eu prefiro aplicações stateless quando quero escalar horizontalmente com simplicidade.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-stateless-vs-stateful"></a>

## Stateless vs Stateful

> Serviço stateless não depende de estado de sessão local entre requisições, facilitando balanceamento e escala horizontal. Stateful pode ser necessário em alguns domínios, mas exige estratégia de replicação, afinidade ou armazenamento externo. Eu evito estado de sessão em memória local quando a aplicação precisa escalar horizontalmente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade busca manter o serviço operacional apesar de falhas. Normalmente envolve redundância, múltiplas instâncias, health checks, failover, replicação e eliminação de single points of failure. HA não é apenas subir duas instâncias; dependências como banco, fila e DNS também precisam ser consideradas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-tolerancia-a-falhas"></a>

## Tolerância a Falhas

> Tolerância a falhas significa continuar oferecendo comportamento aceitável mesmo quando componentes falham. Isso pode envolver redundância, retries controlados, circuit breaker, fallback, filas e degradação graciosa. O objetivo é evitar que uma falha local vire falha sistêmica.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais quando partes não críticas estão indisponíveis. Por exemplo, permitir checkout mesmo se recomendações estiverem fora. O fallback precisa ser explícito e observável; esconder falhas silenciosamente pode gerar dados incorretos ou mascarar incidentes.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-observabilidade"></a>

## Observabilidade

> Observabilidade permite entender o comportamento interno do sistema a partir de sinais externos. Eu considero três pilares principais: logs, métricas e traces. Em sistemas distribuídos, correlation ID, trace ID, p95/p99, taxa de erro e saturação são fundamentais para localizar gargalos e falhas entre serviços.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-logs-metricas-e-traces"></a>

## Logs, Métricas e Traces

> Logs ajudam a entender eventos específicos; métricas mostram comportamento agregado ao longo do tempo; traces mostram o caminho de uma requisição entre componentes. Eu uso os três de forma complementar. Um trace pode apontar o serviço lento, uma métrica mostrar frequência e um log explicar o erro daquela execução.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-slo-sli-e-sla"></a>

## SLO, SLI e SLA

> SLI é a métrica observada, como disponibilidade ou latência. SLO é o objetivo interno para essa métrica, por exemplo 99,9% de disponibilidade. SLA é o compromisso formal com consequência contratual. Arquitetura deve ser orientada pelos SLOs porque eles ajudam a justificar redundância, capacidade e investimento operacional.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-p95-p99-e-media"></a>

## P95, P99 e Média

> Média pode esconder caudas de latência. P95 e P99 mostram o comportamento das requisições mais lentas e são relevantes para experiência real do usuário e SLOs. Em arquitetura distribuída, a latência de cauda tende a se acumular ao longo de várias dependências.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-backpressure"></a>

## Backpressure

> Backpressure evita que produtores gerem trabalho mais rápido do que consumidores ou dependências conseguem processar. Pode ser implementado com filas limitadas, controle de concorrência, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular memória, threads ou conexões até falhar.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-rate-limiting"></a>

## Rate Limiting

> Rate limiting controla quantas operações são aceitas em determinado período. Ele protege capacidade, reduz abuso e evita sobrecarga de dependências. Pode ser por usuário, API key, IP ou tenant. É importante definir também resposta apropriada, normalmente `429`, e política de retry do cliente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache"></a>

## Cache

> Cache troca consistência e memória por menor latência e menor carga na origem. O principal desafio não é colocar dados no cache, mas invalidá-los corretamente. Eu defino estratégia de TTL, chave, tamanho, eviction e comportamento em miss ou indisponibilidade. Também observo risco de cache stampede.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache-aside"></a>

## Cache-Aside

> No padrão cache-aside, a aplicação consulta o cache; em miss busca a origem e popula o cache. É simples e comum, mas pode gerar stampede quando muitas requisições perdem a mesma chave. TTL, locking por chave ou refresh antecipado podem ajudar conforme o cenário.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave expira e muitas requisições consultam a origem simultaneamente. Isso pode derrubar a dependência. Estratégias incluem TTL com jitter, single-flight/locking por chave, refresh antecipado e stale-while-revalidate.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-banco-por-servico"></a>

## Banco por Serviço

> Em microserviços, cada serviço deve idealmente controlar seu próprio modelo e persistência. Compartilhar diretamente o mesmo schema cria acoplamento forte e dificulta deploy independente. Integração entre contextos deve acontecer por APIs ou eventos, embora migração gradual possa exigir compromissos temporários.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas aumenta acoplamento entre serviços e permite que um serviço dependa do schema interno de outro. Se a arquitetura exige autonomia real, eu procuro separar ownership dos dados e evitar acesso cruzado direto.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-replicacao-e-read-replica"></a>

## Replicação e Read Replica

> Read replicas podem aumentar capacidade de leitura e isolar carga analítica, mas introduzem possibilidade de replication lag. Uma leitura imediatamente após escrita pode retornar estado antigo. Portanto, eu só uso replica quando o caso de uso tolera essa consistência e faço roteamento de leitura conscientemente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-sharding"></a>

## Sharding

> Sharding divide dados entre múltiplas partições ou bancos usando uma shard key. Ele permite escalar volume e throughput, mas complica joins globais, transações, rebalanceamento e hotspots. A escolha da shard key é crítica. Eu só considero sharding quando uma única instância ou cluster já não atende com alternativas mais simples.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cqrs-vs-read-replica"></a>

## CQRS vs Read Replica

> Read replica é uma estratégia de infraestrutura para escalar leitura sobre um modelo semelhante. CQRS é uma decisão arquitetural que separa modelos ou caminhos de leitura e escrita. Eles podem coexistir, mas resolvem problemas diferentes.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-e-seguranca"></a>

## Arquitetura e Segurança

> Segurança deve ser tratada como requisito arquitetural. Eu considero autenticação, autorização, least privilege, secrets, criptografia em trânsito e em repouso, trust boundaries, auditoria e proteção de endpoints administrativos. Segurança apenas no gateway ou frontend não é suficiente para todos os cenários.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-12-factor-app"></a>

## 12-Factor App

> Os princípios 12-Factor são úteis para aplicações cloud-native: configuração externa, processos stateless, logs como fluxo de eventos, dependências explícitas e paridade entre ambientes. Eu não trato como regra absoluta, mas como um conjunto útil de práticas para facilitar deploy e operação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-cloud-native"></a>

## Arquitetura Cloud-Native

> Cloud-native não significa apenas hospedar na nuvem. Envolve projetar para automação, elasticidade, observabilidade, resiliência, configuração externa e infraestrutura dinâmica. Containers e Kubernetes podem fazer parte, mas a arquitetura precisa lidar corretamente com falhas, estado e operação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-kubernetes-na-arquitetura"></a>

## Kubernetes na Arquitetura

> Kubernetes resolve problemas de orquestração, como scheduling, service discovery, rollout, health checks e scaling. Ele não resolve regra de negócio, consistência de dados, idempotência ou arquitetura de domínio. Eu o trato como plataforma de execução, não como substituto de decisões arquiteturais.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-mensagens"></a>

## Arquitetura Orientada a Mensagens

> Mensageria desacopla produtores e consumidores no tempo e permite bufferizar carga. O trade-off é lidar com entrega duplicada, ordenação, retries, DLQ e observabilidade. Eu considero idempotência do consumidor e semântica de ack parte do contrato de produção.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-at-most-once-at-least-once-e-exactly-once"></a>

## At-most-once, At-least-once e Exactly-once

> At-most-once prioriza não duplicar, mas pode perder mensagem. At-least-once reduz risco de perda, mas permite duplicidade e exige idempotência. Exactly-once geralmente depende de escopo e garantias específicas da tecnologia; eu evito tratá-lo como garantia mágica end-to-end sem analisar banco, broker e efeitos externos.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-api-rest-vs-mensageria"></a>

## API REST vs Mensageria

> REST é adequado para interação síncrona em que o chamador precisa de resposta imediata. Mensageria é melhor quando quero desacoplar tempo, absorver picos ou propagar eventos. Muitas arquiteturas usam ambos: comando síncrono para uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contrato sem quebrar consumidores. Estratégias incluem URI, header ou negociação de conteúdo. Prefiro mudanças backward compatible quando possível. Em microserviços, também trato eventos como contratos versionados e evito mudanças destrutivas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility significa que consumidores antigos continuam funcionando após evolução do produtor. Em APIs e eventos, prefiro adicionar campos opcionais, evitar remover ou renomear campos abruptamente e coordenar mudanças em etapas. Isso reduz necessidade de deploy sincronizado.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-strangler-fig-pattern"></a>

## Strangler Fig Pattern

> Strangler Fig permite substituir um sistema legado gradualmente, desviando funcionalidades para novos componentes enquanto o legado continua operando. É útil para migração incremental porque reduz risco de big bang. O desafio é manter integração e consistência durante o período de coexistência.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-anti-corruption-layer"></a>

## Anti-Corruption Layer

> Anti-Corruption Layer protege um domínio contra modelos externos ou legados. Em vez de espalhar tipos e regras do sistema externo, crio uma camada que traduz contratos para o modelo interno. Isso reduz acoplamento e facilita substituição futura da integração.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-evolutiva"></a>

## Arquitetura Evolutiva

> Arquitetura evolutiva aceita que decisões mudam conforme o sistema cresce. Eu procuro decisões reversíveis, módulos bem definidos, métricas e fitness functions que ajudem a detectar degradação arquitetural. O objetivo não é prever tudo, mas manter capacidade de adaptação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-trade-off-arquitetural"></a>

## Trade-off Arquitetural

> Toda decisão arquitetural troca uma propriedade por outra. Mais consistência pode reduzir disponibilidade; mais abstração pode reduzir acoplamento, mas aumentar complexidade; mais serviços aumentam autonomia, mas também operação. Em entrevista, eu tento explicitar qual requisito estou priorizando e qual custo estou aceitando.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-resposta-completa-de-entrevista-arquitetura"></a>

## Resposta Completa de Entrevista — Arquitetura

> Arquitetura de software é o conjunto de decisões estruturais que define como responsabilidades, dados e integrações são organizados para atender requisitos funcionais e não funcionais. Eu começo entendendo domínio, volume, disponibilidade, latência, consistência, segurança e capacidade do time, antes de escolher um estilo.
>
> > Para sistemas simples, um monólito modular costuma oferecer ótima relação entre simplicidade e organização. Microserviços fazem sentido quando existem boundaries de domínio claros e necessidade real de autonomia de deploy, escala ou isolamento, porque trazem custos de rede, observabilidade e consistência distribuída.
>
> > Em sistemas distribuídos, eu considero timeout, retry, circuit breaker, idempotência, mensageria, consistência eventual e padrões como Saga e Outbox. Também trato observabilidade, segurança e SLOs como parte da arquitetura. O ponto central é explicitar trade-offs e evitar complexidade que não esteja resolvendo um problema real.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="microservicos-microservicos-sumario"></a>

# Microserviços

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Microserviços](#microservicos-visao-geral-microservicos)
- [Quando usar Microserviços](#microservicos-quando-usar-microservicos)
- [Quando não usar Microserviços](#microservicos-quando-nao-usar-microservicos)
- [Monólito Modular vs Microserviços](#microservicos-monolito-modular-vs-microservicos)
- [Boundaries de Serviço](#microservicos-boundaries-de-servico)
- [Banco por Serviço](#microservicos-banco-por-servico)
- [Shared Database — Trade-off](#microservicos-shared-database-trade-off)
- [Comunicação Síncrona](#microservicos-comunicacao-sincrona)
- [Comunicação Assíncrona](#microservicos-comunicacao-assincrona)
- [REST vs Mensageria](#microservicos-rest-vs-mensageria)
- [Consistência Distribuída](#microservicos-consistencia-distribuida)
- [Saga](#microservicos-saga)
- [Saga — Orquestração vs Coreografia](#microservicos-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#microservicos-outbox-pattern)
- [Idempotência em Microserviços](#microservicos-idempotencia-em-microservicos)
- [Timeout](#microservicos-timeout)
- [Retry](#microservicos-retry)
- [Circuit Breaker](#microservicos-circuit-breaker)
- [Bulkhead](#microservicos-bulkhead)
- [Fallback](#microservicos-fallback)
- [API Gateway](#microservicos-api-gateway)
- [Service Discovery](#microservicos-service-discovery)
- [Load Balancing](#microservicos-load-balancing)
- [Stateless](#microservicos-stateless)
- [Escalabilidade Horizontal](#microservicos-escalabilidade-horizontal)
- [Alta Disponibilidade](#microservicos-alta-disponibilidade)
- [Observabilidade em Microserviços](#microservicos-observabilidade-em-microservicos)
- [Tracing Distribuído](#microservicos-tracing-distribuido)
- [P95 e P99](#microservicos-p95-e-p99)
- [SLO e Error Budget](#microservicos-slo-e-error-budget)
- [Rate Limiting](#microservicos-rate-limiting)
- [Backpressure](#microservicos-backpressure)
- [Cache em Microserviços](#microservicos-cache-em-microservicos)
- [Cache Stampede](#microservicos-cache-stampede)
- [Versionamento de API](#microservicos-versionamento-de-api)
- [Backward Compatibility](#microservicos-backward-compatibility)
- [Deploy Independente](#microservicos-deploy-independente)
- [Falha em Cascata](#microservicos-falha-em-cascata)
- [Degradação Graciosa](#microservicos-degradacao-graciosa)
- [Configuração Distribuída](#microservicos-configuracao-distribuida)
- [Segurança entre Serviços](#microservicos-seguranca-entre-servicos)
- [Resposta Completa de Entrevista — Microserviços](#microservicos-resposta-completa-de-entrevista-microservicos)

---

<a id="microservicos-visao-geral-microservicos"></a>

## Visão Geral — Microserviços

> Microserviços dividem uma aplicação em serviços menores, implantáveis de forma independente e normalmente alinhados a capacidades de negócio. O principal ganho é autonomia de deploy, escala e ownership; o principal custo é a complexidade distribuída, como rede, observabilidade, consistência eventual, segurança, versionamento e operação.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-quando-usar-microservicos"></a>

## Quando usar Microserviços

> Eu usaria microserviços quando existem fronteiras de domínio claras e uma necessidade concreta de autonomia, como escalabilidade independente, ciclos de deploy diferentes, isolamento de falhas, requisitos de segurança distintos ou times com ownership separado. Não considero microserviços uma evolução obrigatória de todo monólito.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-quando-nao-usar-microservicos"></a>

## Quando não usar Microserviços

> Eu evitaria microserviços quando o domínio ainda não está claro, o time é pequeno, o volume não exige distribuição ou a capacidade operacional é baixa. Nesses casos, um monólito modular normalmente entrega mais velocidade com menos complexidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-monolito-modular-vs-microservicos"></a>

## Monólito Modular vs Microserviços

> Monólito modular mantém um único deploy, mas separa o código por módulos e boundaries claros. Microserviços levam essa separação para processos e deploys independentes. Eu costumo preferir monólito modular enquanto autonomia operacional e de escala ainda não justificarem a distribuição.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-boundaries-de-servico"></a>

## Boundaries de Serviço

> Eu defino boundaries com base em capacidade de negócio e ownership de dados, não por tabelas ou camadas técnicas. Um bom serviço tende a encapsular seu próprio modelo, regras e persistência, reduzindo necessidade de chamadas excessivas entre serviços.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-banco-por-servico"></a>

## Banco por Serviço

> Em microserviços, cada serviço deve idealmente possuir ownership dos próprios dados. Compartilhar diretamente o mesmo schema cria acoplamento forte e permite que um serviço dependa da estrutura interna de outro. A integração deve ocorrer por APIs ou eventos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas reduz autonomia e aumenta acoplamento entre serviços. Pode ser aceitável em migração gradual, mas eu trataria como compromisso temporário se o objetivo real é independência entre serviços.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-comunicacao-sincrona"></a>

## Comunicação Síncrona

> Comunicação síncrona é adequada quando o chamador precisa de resposta imediata. O custo é acoplamento temporal: se a dependência está lenta ou indisponível, o chamador sente diretamente. Por isso uso timeout, circuit breaker e observabilidade em chamadas remotas.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-comunicacao-assincrona"></a>

## Comunicação Assíncrona

> Comunicação assíncrona desacopla produtor e consumidor no tempo e ajuda a absorver picos. Em troca, introduz consistência eventual, duplicidade, ordenação, retry, DLQ e necessidade de idempotência. Eu uso quando o fluxo tolera resposta posterior ou quando desacoplamento temporal é importante.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-rest-vs-mensageria"></a>

## REST vs Mensageria

> REST é melhor quando preciso de resposta imediata e um contrato request-response claro. Mensageria é melhor para propagação de eventos, processamento assíncrono e absorção de picos. Em muitos sistemas uso os dois: comando síncrono para iniciar uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-consistencia-distribuida"></a>

## Consistência Distribuída

> Em microserviços eu evito tentar reproduzir uma transação ACID global entre serviços. Normalmente cada serviço mantém sua transação local e a consistência entre serviços é coordenada de forma eventual, usando eventos, Saga, Outbox e operações idempotentes.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-saga"></a>

## Saga

> Saga coordena uma operação de negócio distribuída por meio de várias transações locais. Se uma etapa falha, ações compensatórias podem desfazer efeitos de negócio anteriores. Pode ser implementada por orquestração ou coreografia.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração existe um coordenador explícito do fluxo, o que facilita entendimento e controle. Na coreografia os serviços reagem a eventos, reduzindo centralização, mas o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade e necessidade de governança do processo.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-outbox-pattern"></a>

## Outbox Pattern

> Outbox resolve o problema de atualizar o banco e publicar um evento de forma consistente sem transação distribuída. A aplicação grava a mudança de negócio e o registro da outbox na mesma transação local. Depois, outro processo publica a mensagem.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-idempotencia-em-microservicos"></a>

## Idempotência em Microserviços

> Idempotência garante que uma operação possa ser repetida sem gerar efeitos adicionais indevidos. Ela é importante em retries e mensageria. Eu implemento com chave idempotente, unique constraint, tabela de eventos processados ou lógica naturalmente idempotente.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-timeout"></a>

## Timeout

> Timeout limita quanto tempo um serviço espera por uma dependência. Sem timeout, threads, conexões e recursos podem ficar presos indefinidamente. O timeout deve respeitar o budget total da requisição e ser coerente com retries.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-retry"></a>

## Retry

> Retry deve ser usado apenas para falhas transitórias e quando a operação suporta reexecução. Eu limito tentativas, uso backoff e jitter e verifico idempotência. Retry mal configurado pode amplificar uma indisponibilidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-circuit-breaker"></a>

## Circuit Breaker

> Circuit Breaker interrompe temporariamente chamadas para uma dependência com alta taxa de falha, evitando desperdício de recursos e falha em cascata. Ele normalmente passa por estados fechado, aberto e semiaberto.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que a falha de uma dependência consuma toda a capacidade da aplicação. Pode ser implementado com pools separados, semáforos ou limites de concorrência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-fallback"></a>

## Fallback

> Fallback fornece uma resposta alternativa quando uma dependência falha. Eu só uso quando existe uma resposta de negócio válida. Fallback silencioso pode mascarar incidentes e retornar informação incorreta.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting, headers e observabilidade. Evito colocar regra de negócio no gateway para não criar um novo monólito central.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-service-discovery"></a>

## Service Discovery

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, normalmente uso os mecanismos nativos de Services e DNS antes de adicionar um registry dedicado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-load-balancing"></a>

## Load Balancing

> Load balancing distribui tráfego entre instâncias disponíveis. Pode existir no cliente, gateway, proxy ou infraestrutura. É complementar a health checks, autoscaling e resiliência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-stateless"></a>

## Stateless

> Serviços stateless não dependem de estado de sessão local entre requisições, o que facilita scale-out e failover. Estado durável deve ficar em sistemas apropriados, como banco, cache ou storage externo.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-escalabilidade-horizontal"></a>

## Escalabilidade Horizontal

> Escalabilidade horizontal adiciona instâncias para aumentar capacidade. Para funcionar bem, preciso evitar estado local, compartilhar apenas o necessário e garantir que banco, cache e mensageria também suportem o crescimento.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade exige eliminar single points of failure em toda a cadeia: aplicação, banco, broker, rede e infraestrutura. Subir duas instâncias da aplicação não é suficiente se uma dependência crítica continua única.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-observabilidade-em-microservicos"></a>

## Observabilidade em Microserviços

> Em microserviços eu considero logs, métricas e traces obrigatórios para investigação. Correlation ID e trace ID ajudam a seguir uma requisição entre serviços, enquanto p95, p99, taxa de erro e saturação mostram comportamento agregado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-tracing-distribuido"></a>

## Tracing Distribuído

> Tracing distribuído mostra o caminho de uma requisição por múltiplos serviços e o tempo gasto em cada etapa. É especialmente útil para descobrir qual dependência está elevando latência ou causando falha.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-p95-e-p99"></a>

## P95 e P99

> P95 e P99 mostram a cauda de latência e são mais úteis que a média para entender experiência real. Em sistemas distribuídos, atrasos em várias dependências podem se acumular, então acompanho esses percentis por serviço e por dependência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-slo-e-error-budget"></a>

## SLO e Error Budget

> SLO define um objetivo de confiabilidade ou latência. Error budget representa quanto de indisponibilidade ou erro ainda é aceitável dentro desse objetivo. Eu uso isso para equilibrar velocidade de entrega e investimento em confiabilidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-rate-limiting"></a>

## Rate Limiting

> Rate limiting protege o serviço e suas dependências contra excesso de requisições. Pode ser aplicado por usuário, token, tenant ou IP. Também preciso definir como clientes devem reagir, normalmente usando `429` e política de retry apropriada.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-backpressure"></a>

## Backpressure

> Backpressure controla o fluxo quando produtores geram trabalho mais rápido que consumidores conseguem processar. Pode ser implementado com filas limitadas, semáforos, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular trabalho até esgotar recursos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-cache-em-microservicos"></a>

## Cache em Microserviços

> Cache reduz latência e carga na origem, mas introduz risco de dados desatualizados e problemas de invalidação. Eu defino TTL, chave, política de eviction e comportamento em miss. Cache deve ser tratado como otimização, não como fonte de verdade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave muito acessada expira e várias requisições vão simultaneamente à origem. Estratégias comuns incluem TTL com jitter, locking por chave, single-flight e refresh antecipado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contratos sem quebrar consumidores. Prefiro mudanças backward compatible sempre que possível, como adicionar campos opcionais em vez de remover ou renomear campos de forma abrupta.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility permite que consumidores antigos continuem funcionando enquanto o produtor evolui. Isso reduz deploy coordenado entre serviços e é especialmente importante em APIs e eventos assíncronos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-deploy-independente"></a>

## Deploy Independente

> Deploy independente é um dos principais objetivos de microserviços, mas só existe de verdade quando contratos, banco, configuração e dependências também estão desacoplados. Se todo deploy exige coordenação entre serviços, a arquitetura ainda está fortemente acoplada.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-falha-em-cascata"></a>

## Falha em Cascata

> Falha em cascata acontece quando a degradação de um serviço consome recursos de serviços dependentes e propaga o problema. Timeout, circuit breaker, bulkhead, rate limiting e degradação graciosa ajudam a limitar esse efeito.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais mesmo quando componentes secundários falham. Um exemplo é continuar permitindo checkout se recomendações estiverem indisponíveis. O fallback precisa ser explícito e observável.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-configuracao-distribuida"></a>

## Configuração Distribuída

> Configuração distribuída centraliza propriedades comuns a vários serviços, mas cria uma dependência operacional adicional. Eu avalio disponibilidade, versionamento, segurança de secrets e comportamento de startup quando a fonte de configuração está indisponível.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-seguranca-entre-servicos"></a>

## Segurança entre Serviços

> Segurança entre serviços envolve autenticação, autorização, criptografia em trânsito, least privilege e proteção de secrets. Mesmo em rede interna, eu não assumo confiança irrestrita sem analisar o trust boundary.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-resposta-completa-de-entrevista-microservicos"></a>

## Resposta Completa de Entrevista — Microserviços

> Microserviços são uma forma de dividir o sistema em serviços implantáveis de forma independente, normalmente alinhados a capacidades de negócio. O benefício principal é autonomia de deploy, escala e ownership, mas o custo é a complexidade distribuída.
>
> > Eu considero boundaries de domínio, ownership de dados e necessidade real de autonomia antes de separar um serviço. Em comunicação síncrona, configuro timeout, circuit breaker e observabilidade. Em fluxos assíncronos, considero consistência eventual, idempotência, retry, DLQ e ordenação.
>
> > Para consistência entre serviços, prefiro transações locais combinadas com padrões como Saga e Outbox. Em produção, observo p95/p99, taxa de erro, saturação, traces distribuídos e dependências críticas. O objetivo é obter autonomia sem criar complexidade desnecessária.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="kafka-kafka-sumario"></a>

# Kafka

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Kafka](#kafka-visao-geral-kafka)
- [Broker](#kafka-broker)
- [Topic](#kafka-topic)
- [Partition](#kafka-partition)
- [Offset](#kafka-offset)
- [Partition Key](#kafka-partition-key)
- [Producer](#kafka-producer)
- [Consumer](#kafka-consumer)
- [Consumer Group](#kafka-consumer-group)
- [Mais Partições que Consumidores](#kafka-mais-particoes-que-consumidores)
- [Mais Consumidores que Partições](#kafka-mais-consumidores-que-particoes)
- [Ordenação](#kafka-ordenacao)
- [Leader e Replicas](#kafka-leader-e-replicas)
- [Replication Factor](#kafka-replication-factor)
- [ISR](#kafka-isr)
- [acks=0, 1 e all](#kafka-acks-0-1-e-all)
- [min.insync.replicas](#kafka-min-insync-replicas)
- [At-most-once](#kafka-at-most-once)
- [At-least-once](#kafka-at-least-once)
- [Exactly-once](#kafka-exactly-once)
- [Producer Idempotente](#kafka-producer-idempotente)
- [Consumer Idempotente](#kafka-consumer-idempotente)
- [Commit de Offset](#kafka-commit-de-offset)
- [Auto Commit vs Manual Commit](#kafka-auto-commit-vs-manual-commit)
- [Rebalance](#kafka-rebalance)
- [Cooperative Rebalancing](#kafka-cooperative-rebalancing)
- [Consumer Lag](#kafka-consumer-lag)
- [Lag em Produção](#kafka-lag-em-producao)
- [Retention](#kafka-retention)
- [Log Compaction](#kafka-log-compaction)
- [Replay](#kafka-replay)
- [DLQ](#kafka-dlq)
- [Retry com Kafka](#kafka-retry-com-kafka)
- [Poison Pill](#kafka-poison-pill)
- [Schema Evolution](#kafka-schema-evolution)
- [Schema Registry](#kafka-schema-registry)
- [Kafka vs RabbitMQ](#kafka-kafka-vs-rabbitmq)
- [Kafka vs SQS](#kafka-kafka-vs-sqs)
- [Kafka vs Banco de Dados](#kafka-kafka-vs-banco-de-dados)
- [Kafka e Outbox](#kafka-kafka-e-outbox)
- [Kafka e Spring](#kafka-kafka-e-spring)
- [@KafkaListener](#kafka-kafkalistener)
- [Concorrência no Spring Kafka](#kafka-concorrencia-no-spring-kafka)
- [Partições e Escalabilidade](#kafka-particoes-e-escalabilidade)
- [Hot Partition](#kafka-hot-partition)
- [Batching](#kafka-batching)
- [Backpressure no Consumidor](#kafka-backpressure-no-consumidor)
- [Observabilidade Kafka](#kafka-observabilidade-kafka)
- [Falha de Broker](#kafka-falha-de-broker)
- [Kafka e Ordenação por Entidade](#kafka-kafka-e-ordenacao-por-entidade)
- [Kafka e Transação com Banco](#kafka-kafka-e-transacao-com-banco)
- [Resposta Completa de Entrevista — Kafka](#kafka-resposta-completa-de-entrevista-kafka)

---

<a id="kafka-visao-geral-kafka"></a>

## Visão Geral — Kafka

> Apache Kafka é uma plataforma distribuída de streaming de eventos baseada em log append-only. Produtores publicam registros em tópicos, tópicos são divididos em partições e consumidores leem os eventos mantendo posição por offset. Kafka é muito forte em throughput, retenção e processamento distribuído de eventos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-broker"></a>

## Broker

> Broker é um servidor Kafka que armazena partições e atende produtores e consumidores. Um cluster é composto por múltiplos brokers para distribuir carga e fornecer redundância.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-topic"></a>

## Topic

> Topic é uma categoria lógica de eventos. Um tópico é dividido em partições, e cada registro pertence a uma dessas partições. A escolha do número de partições influencia paralelismo, throughput e ordenação.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-partition"></a>

## Partition

> Partition é um log ordenado e append-only dentro de um tópico. A ordem é garantida dentro de uma mesma partição, não globalmente entre todas as partições do tópico.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-offset"></a>

## Offset

> Offset identifica a posição de um registro dentro de uma partição. O consumidor usa offsets para saber até onde processou. O offset não é um identificador global do evento; ele só faz sentido dentro daquela partição.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-partition-key"></a>

## Partition Key

> A partition key influencia em qual partição um evento será gravado. Eventos com a mesma chave tendem a ir para a mesma partição, o que ajuda a preservar ordenação por entidade, como `orderId`. O trade-off é risco de hotspot se poucas chaves concentram muito volume.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-producer"></a>

## Producer

> Producer envia eventos para tópicos e escolhe partição com base na key, estratégia de particionamento ou distribuição padrão. Em produção, considero `acks`, retries, idempotência do producer, batching e compressão.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer"></a>

## Consumer

> Consumer lê registros das partições e controla o progresso através de offsets. O processamento deve considerar falhas, commit de offset, idempotência e tempo máximo de processamento.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-group"></a>

## Consumer Group

> Consumer group permite paralelizar consumo de um tópico. Dentro do mesmo grupo, uma partição é atribuída a no máximo um consumidor ativo por vez. Grupos diferentes recebem o mesmo fluxo de eventos de forma independente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-mais-particoes-que-consumidores"></a>

## Mais Partições que Consumidores

> Se existem mais partições do que consumidores no grupo, alguns consumidores processam várias partições. Isso aumenta o paralelismo potencial até o número de consumidores atingir o número de partições.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-mais-consumidores-que-particoes"></a>

## Mais Consumidores que Partições

> Se há mais consumidores que partições no mesmo grupo, consumidores excedentes ficam ociosos porque uma partição não é processada simultaneamente por dois consumidores do mesmo grupo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-ordenacao"></a>

## Ordenação

> Kafka garante ordenação apenas dentro de uma partição. Se preciso manter ordem por cliente ou pedido, uso uma key estável para que eventos relacionados caiam na mesma partição. Isso reduz paralelismo para aquela chave, mas preserva sequência.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-leader-e-replicas"></a>

## Leader e Replicas

> Cada partição possui um leader e pode possuir replicas. Produtores e consumidores trabalham principalmente com o leader. As replicas mantêm cópias para permitir failover quando o leader fica indisponível.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-replication-factor"></a>

## Replication Factor

> Replication factor define quantas cópias de cada partição existem no cluster. Um fator maior aumenta tolerância a falhas, mas também aumenta uso de disco e tráfego de replicação.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-isr"></a>

## ISR

> ISR, In-Sync Replicas, representa replicas suficientemente sincronizadas com o leader. Esse conjunto é importante para decisões de durabilidade e failover.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-acks-0-1-e-all"></a>

## acks=0, 1 e all

> `acks=0` prioriza throughput e não espera confirmação do broker. `acks=1` espera confirmação do leader. `acks=all` espera confirmação conforme as replicas em sincronia exigidas pela configuração. Quanto maior a garantia, maior tende a ser a latência e a durabilidade.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-min-insync-replicas"></a>

## min.insync.replicas

> `min.insync.replicas` define o número mínimo de replicas em sincronia necessário para aceitar gravações quando o producer usa `acks=all`. Essa combinação ajuda a evitar confirmar escrita com redundância insuficiente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-at-most-once"></a>

## At-most-once

> At-most-once prioriza evitar duplicidade, mas pode perder mensagens. Isso ocorre, por exemplo, se o offset é confirmado antes de o processamento ser concluído e a aplicação falha depois.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-at-least-once"></a>

## At-least-once

> At-least-once prioriza não perder mensagens, mas permite duplicidade. É comum confirmar o offset depois do processamento. Se a aplicação processa e falha antes do commit, o evento pode ser reentregue. Por isso idempotência é importante.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-exactly-once"></a>

## Exactly-once

> Exactly-once em Kafka depende do escopo. Kafka oferece mecanismos como producer idempotente e transações para fluxos Kafka-to-Kafka. Isso não significa automaticamente exactly-once end-to-end quando existem efeitos externos, como banco de dados ou chamadas HTTP.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-producer-idempotente"></a>

## Producer Idempotente

> Producer idempotente evita duplicatas causadas por retries do próprio producer dentro das garantias do Kafka. Ele não resolve sozinho duplicidade de negócio causada por reprocessamento no consumidor.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-idempotente"></a>

## Consumer Idempotente

> Consumer idempotente garante que reentrega do mesmo evento não produza efeito duplicado. Estratégias comuns são chave idempotente, unique constraint, tabela de eventos processados ou operação naturalmente idempotente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-commit-de-offset"></a>

## Commit de Offset

> Commit de offset registra o progresso do consumidor. Se eu commito antes de processar, posso perder evento em caso de falha. Se commito depois, posso receber duplicado. O desenho deve alinhar commit com a semântica de entrega desejada.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-auto-commit-vs-manual-commit"></a>

## Auto Commit vs Manual Commit

> Auto commit simplifica configuração, mas reduz controle sobre o ponto exato em que o progresso é confirmado. Manual commit permite alinhar offset ao processamento, porém exige mais cuidado com erros e rebalances.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-rebalance"></a>

## Rebalance

> Rebalance redistribui partições entre consumidores de um grupo quando consumidores entram, saem ou quando metadados mudam. Durante esse processo pode haver pausa no consumo. Rebalances frequentes prejudicam estabilidade e throughput.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-cooperative-rebalancing"></a>

## Cooperative Rebalancing

> Cooperative rebalancing reduz impacto ao mover partições de forma mais incremental, evitando parar todo o grupo quando possível. É útil para reduzir pausas em grupos maiores.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-lag"></a>

## Consumer Lag

> Consumer lag é a diferença entre o último offset disponível e o offset processado pelo consumidor. Lag crescente pode indicar consumidor lento, dependência externa degradada, partições insuficientes ou processamento pesado.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-lag-em-producao"></a>

## Lag em Produção

> Eu monitoro lag por partição e grupo, junto com throughput, tempo de processamento, erros e rebalances. Lag isolado não basta: preciso entender se está crescendo continuamente ou apenas absorvendo um pico temporário.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-retention"></a>

## Retention

> Kafka retém eventos por tempo ou tamanho, independentemente de terem sido consumidos. Isso diferencia Kafka de filas tradicionais e permite replay. O trade-off é uso de armazenamento.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-log-compaction"></a>

## Log Compaction

> Log compaction mantém, de forma geral, a versão mais recente por key ao longo do tempo. É útil para tópicos que representam estado atual por chave, mas não deve ser confundida com retenção temporal simples.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-replay"></a>

## Replay

> Replay significa reposicionar o consumidor em offsets anteriores e reprocessar eventos. Isso é útil para reconstrução de estado e correção de consumidores, mas exige processamento idempotente e cuidado com efeitos externos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-dlq"></a>

## DLQ

> DLQ armazena mensagens que não puderam ser processadas após a política de retry. Ela evita bloquear indefinidamente o consumo, mas exige estratégia de inspeção, correção e reprocessamento. DLQ não deve virar depósito permanente de erros ignorados.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-retry-com-kafka"></a>

## Retry com Kafka

> Retry pode ser imediato ou com atraso através de tópicos de retry, dependendo da arquitetura. Eu evito loop agressivo no mesmo evento porque isso bloqueia a partição e reduz throughput. Também considero idempotência e limite de tentativas.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-poison-pill"></a>

## Poison Pill

> Poison pill é uma mensagem que falha repetidamente, por exemplo por payload inválido. Sem tratamento, ela pode bloquear o progresso da partição. Estratégias incluem DLQ, tratamento específico de desserialização e limites de retry.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-schema-evolution"></a>

## Schema Evolution

> Eventos são contratos. Para evoluir schema, prefiro mudanças backward compatible, como adicionar campos opcionais. Formatos como Avro, Protobuf ou JSON Schema podem ser usados com registry para governar compatibilidade.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-schema-registry"></a>

## Schema Registry

> Schema Registry centraliza schemas e regras de compatibilidade. Ele ajuda produtores e consumidores a evoluírem sem quebrar contratos, mas adiciona mais um componente operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-rabbitmq"></a>

## Kafka vs RabbitMQ

> Kafka é orientado a log distribuído, retenção e alto throughput de eventos, com replay natural. RabbitMQ é tradicionalmente orientado a filas e roteamento de mensagens. A escolha depende do padrão: streaming e histórico favorecem Kafka; filas de trabalho e roteamento complexo podem favorecer RabbitMQ.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-sqs"></a>

## Kafka vs SQS

> Kafka oferece log particionado, replay e controle detalhado de consumo. SQS é uma fila gerenciada com modelo operacional mais simples. Eu escolho Kafka quando preciso de streaming, múltiplos consumidores independentes, replay e alto throughput; SQS quando quero desacoplamento por fila com menor custo operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-banco-de-dados"></a>

## Kafka vs Banco de Dados

> Kafka não substitui banco transacional. Ele é excelente como log de eventos e backbone de integração, enquanto banco relacional continua melhor para consultas transacionais e consistência de dados de negócio. Os dois frequentemente coexistem.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-outbox"></a>

## Kafka e Outbox

> Outbox é uma estratégia comum para publicar eventos no Kafka de forma consistente com uma transação de banco. A aplicação grava negócio e outbox na mesma transação e depois um publicador envia o evento ao Kafka.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-spring"></a>

## Kafka e Spring

> No ecossistema Spring, `spring-kafka` fornece producer, consumer, listeners, configuração de serializers, error handling e integração com transactions. Mesmo usando abstrações do Spring, eu continuo entendendo partitions, offsets, rebalances e delivery semantics.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafkalistener"></a>

## @KafkaListener

> `@KafkaListener` simplifica criação de consumers no Spring. Eu ainda configuro group id, concorrência, ack mode, error handler e desserialização de acordo com a necessidade do serviço.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-concorrencia-no-spring-kafka"></a>

## Concorrência no Spring Kafka

> A concorrência do listener permite múltiplos consumers dentro da mesma instância. O paralelismo efetivo continua limitado pelo número de partições. Aumentar threads acima do número de partições não aumenta throughput do grupo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-particoes-e-escalabilidade"></a>

## Partições e Escalabilidade

> Número de partições define o teto de paralelismo de consumo dentro de um consumer group. Mais partições aumentam paralelismo potencial, mas também aumentam metadados, arquivos, custo de rebalance e complexidade operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-hot-partition"></a>

## Hot Partition

> Hot partition ocorre quando uma ou poucas partições recebem volume muito maior, normalmente por distribuição desigual de keys. Isso limita throughput mesmo com muitos consumidores. Eu reviso estratégia de chave e distribuição de carga.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-batching"></a>

## Batching

> Producer batching agrupa registros para melhorar throughput e reduzir overhead de rede. O trade-off é introduzir pequena latência adicional enquanto o batch é formado. Compressão também pode reduzir rede ao custo de CPU.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-backpressure-no-consumidor"></a>

## Backpressure no Consumidor

> Se o processamento downstream é mais lento que o consumo, o lag cresce. Eu controlo concorrência, pausas de partição, capacidade de dependências e throughput de processamento em vez de simplesmente aumentar consumers sem analisar o gargalo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-observabilidade-kafka"></a>

## Observabilidade Kafka

> Em produção, monitoro lag, throughput, taxa de erro, rebalances, tempo de processamento, utilização de disco, estado de replicas e latência de produce/fetch. Para consumidores, lag por partição é uma das métricas mais importantes.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-falha-de-broker"></a>

## Falha de Broker

> Quando um broker falha, partições com replicas adequadas podem eleger novos leaders e continuar operando. A disponibilidade depende de replication factor, ISR e configurações de durabilidade.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-ordenacao-por-entidade"></a>

## Kafka e Ordenação por Entidade

> Para preservar ordem por entidade, uso a mesma partition key, como `customerId` ou `orderId`. Isso garante ordem dentro daquela partição, mas significa que todos os eventos da mesma chave serão processados sequencialmente naquele grupo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-transacao-com-banco"></a>

## Kafka e Transação com Banco

> Não existe uma transação ACID simples envolvendo Kafka e banco relacional de forma genérica. Para consistência entre os dois, eu normalmente uso Outbox ou desenho idempotente, em vez de depender de transação distribuída.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-resposta-completa-de-entrevista-kafka"></a>

## Resposta Completa de Entrevista — Kafka

> Kafka é uma plataforma distribuída de streaming baseada em log. Produtores publicam eventos em tópicos, tópicos são divididos em partições e consumidores leem mantendo posição por offset. A partição é a unidade de ordenação e também define o paralelismo do consumer group.
>
> > Em disponibilidade, cada partição possui leader e replicas. Configurações como replication factor, `acks=all` e `min.insync.replicas` aumentam durabilidade, com custo de latência e recursos.
>
> > No consumo, eu presto atenção à semântica de entrega. At-least-once é comum e exige idempotência porque mensagens podem ser reprocessadas. Também monitoro consumer lag, rebalances, retries e DLQ.
>
> > Em produção, escolho partition key com cuidado para preservar ordenação sem criar hot partitions e uso schema evolution compatível. Mesmo com Spring Kafka, continuo entendendo offsets, partitions, consumer groups e as garantias reais do broker.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="aws-aws-sumario"></a>

# AWS

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — AWS](#aws-visao-geral-aws)
- [Região e Availability Zone](#aws-regiao-e-availability-zone)
- [Alta Disponibilidade Multi-AZ](#aws-alta-disponibilidade-multi-az)
- [Multi-Region](#aws-multi-region)
- [Shared Responsibility Model](#aws-shared-responsibility-model)
- [IAM](#aws-iam)
- [IAM User vs Role](#aws-iam-user-vs-role)
- [IAM Policy](#aws-iam-policy)
- [Identity-based vs Resource-based Policy](#aws-identity-based-vs-resource-based-policy)
- [STS e AssumeRole](#aws-sts-e-assumerole)
- [VPC](#aws-vpc)
- [Subnet Pública vs Privada](#aws-subnet-publica-vs-privada)
- [Internet Gateway](#aws-internet-gateway)
- [NAT Gateway](#aws-nat-gateway)
- [Security Group vs NACL](#aws-security-group-vs-nacl)
- [Route Table](#aws-route-table)
- [VPC Endpoint](#aws-vpc-endpoint)
- [EC2](#aws-ec2)
- [Auto Scaling Group](#aws-auto-scaling-group)
- [ALB vs NLB](#aws-alb-vs-nlb)
- [EBS](#aws-ebs)
- [EFS](#aws-efs)
- [S3](#aws-s3)
- [S3 Versioning](#aws-s3-versioning)
- [S3 Lifecycle](#aws-s3-lifecycle)
- [S3 Presigned URL](#aws-s3-presigned-url)
- [CloudFront](#aws-cloudfront)
- [Route 53](#aws-route-53)
- [RDS](#aws-rds)
- [RDS Multi-AZ](#aws-rds-multi-az)
- [RDS Read Replica](#aws-rds-read-replica)
- [Aurora](#aws-aurora)
- [DynamoDB](#aws-dynamodb)
- [DynamoDB Partition Key](#aws-dynamodb-partition-key)
- [DynamoDB Sort Key](#aws-dynamodb-sort-key)
- [DynamoDB GSI e LSI](#aws-dynamodb-gsi-e-lsi)
- [DynamoDB Consistency](#aws-dynamodb-consistency)
- [DynamoDB On-Demand vs Provisioned](#aws-dynamodb-on-demand-vs-provisioned)
- [ElastiCache](#aws-elasticache)
- [Lambda](#aws-lambda)
- [Lambda Cold Start](#aws-lambda-cold-start)
- [Lambda Concurrency](#aws-lambda-concurrency)
- [Lambda Idempotência](#aws-lambda-idempotencia)
- [ECS](#aws-ecs)
- [ECS Service vs Task](#aws-ecs-service-vs-task)
- [Fargate](#aws-fargate)
- [EKS](#aws-eks)
- [ECS vs EKS](#aws-ecs-vs-eks)
- [ECS vs Lambda](#aws-ecs-vs-lambda)
- [API Gateway](#aws-api-gateway)
- [API Gateway vs ALB](#aws-api-gateway-vs-alb)
- [SQS](#aws-sqs)
- [SQS Standard vs FIFO](#aws-sqs-standard-vs-fifo)
- [SQS Visibility Timeout](#aws-sqs-visibility-timeout)
- [SQS DLQ](#aws-sqs-dlq)
- [SQS Long Polling](#aws-sqs-long-polling)
- [SNS](#aws-sns)
- [SNS + SQS](#aws-sns-sqs)
- [EventBridge](#aws-eventbridge)
- [SNS vs EventBridge](#aws-sns-vs-eventbridge)
- [SQS vs Kafka](#aws-sqs-vs-kafka)
- [CloudWatch](#aws-cloudwatch)
- [CloudTrail](#aws-cloudtrail)
- [KMS](#aws-kms)
- [Secrets Manager vs Parameter Store](#aws-secrets-manager-vs-parameter-store)
- [WAF](#aws-waf)
- [CloudFormation](#aws-cloudformation)
- [AWS CDK](#aws-aws-cdk)
- [Infrastructure as Code](#aws-infrastructure-as-code)
- [RTO e RPO](#aws-rto-e-rpo)
- [Disaster Recovery](#aws-disaster-recovery)
- [Well-Architected Framework](#aws-well-architected-framework)
- [Cost Optimization](#aws-cost-optimization)
- [NAT Gateway Cost](#aws-nat-gateway-cost)
- [Resposta Completa de Entrevista — AWS](#aws-resposta-completa-de-entrevista-aws)

---

<a id="aws-visao-geral-aws"></a>

## Visão Geral — AWS

> Em entrevistas eu organizo AWS em cinco blocos: compute, networking, storage/database, integração assíncrona e segurança/observabilidade. O ponto principal não é decorar serviços, mas saber escolher entre alternativas considerando disponibilidade, escalabilidade, segurança, custo e operação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-regiao-e-availability-zone"></a>

## Região e Availability Zone

> Uma Region é uma área geográfica da AWS composta por múltiplas Availability Zones. AZs são locais isolados dentro da mesma região, com conectividade de baixa latência entre si. Para alta disponibilidade regional, normalmente distribuo recursos por mais de uma AZ.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-alta-disponibilidade-multi-az"></a>

## Alta Disponibilidade Multi-AZ

> Multi-AZ reduz impacto da falha de uma zona. Em aplicações web, isso normalmente significa múltiplas instâncias distribuídas entre AZs, balanceador e dependências também redundantes. Não adianta colocar a aplicação em duas AZs se banco ou outro componente crítico continua como ponto único de falha.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-multi-region"></a>

## Multi-Region

> Multi-Region é usado quando requisitos de disponibilidade, disaster recovery, latência global ou residência de dados justificam operar em mais de uma região. O custo e a complexidade aumentam porque preciso tratar replicação, roteamento, consistência e failover entre regiões.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-shared-responsibility-model"></a>

## Shared Responsibility Model

> No modelo de responsabilidade compartilhada, a AWS é responsável pela segurança da nuvem, como datacenters, hardware e infraestrutura física; o cliente é responsável pela segurança na nuvem, como IAM, configuração de rede, dados, patches conforme o serviço e controles da aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam"></a>

## IAM

> IAM controla autenticação e autorização dentro da AWS. Eu trabalho com users, roles e policies, sempre seguindo least privilege: conceder apenas as permissões necessárias, pelo menor tempo e escopo possíveis.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam-user-vs-role"></a>

## IAM User vs Role

> IAM User representa uma identidade persistente. IAM Role é uma identidade assumível, normalmente temporária, usada por aplicações, serviços AWS ou usuários federados. Para workloads em EC2, ECS ou Lambda, prefiro roles em vez de access keys estáticas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam-policy"></a>

## IAM Policy

> IAM Policy define permissões com elementos como `Effect`, `Action`, `Resource` e condições. A autorização efetiva resulta da combinação de policies aplicáveis, lembrando que um `Deny` explícito prevalece sobre `Allow`.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-identity-based-vs-resource-based-policy"></a>

## Identity-based vs Resource-based Policy

> Identity-based policy é anexada a user, group ou role. Resource-based policy é anexada ao próprio recurso, como bucket S3 ou fila SQS. Resource policies são especialmente úteis em acesso cross-account.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sts-e-assumerole"></a>

## STS e AssumeRole

> AWS STS emite credenciais temporárias. `AssumeRole` permite que uma identidade assuma uma role e receba permissões temporárias. Isso é importante para acesso cross-account e para evitar distribuição de credenciais permanentes.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-vpc"></a>

## VPC

> VPC é a rede virtual isolada da AWS. Dentro dela defino CIDR, subnets, route tables, gateways e security groups. Eu normalmente separo recursos públicos e privados conforme a necessidade de exposição.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-subnet-publica-vs-privada"></a>

## Subnet Pública vs Privada

> Uma subnet é pública quando sua route table possui rota para um Internet Gateway e os recursos têm conectividade pública adequada. Subnet privada não possui rota direta para Internet Gateway. Servidores de aplicação e bancos normalmente ficam em subnets privadas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-internet-gateway"></a>

## Internet Gateway

> Internet Gateway conecta uma VPC à internet. Para uma instância ter acesso público, além do IGW é necessário roteamento adequado, endereço público ou equivalente e regras de segurança compatíveis.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-nat-gateway"></a>

## NAT Gateway

> NAT Gateway permite que recursos em subnets privadas iniciem conexões para a internet sem aceitar conexões de entrada iniciadas externamente. É comum para updates ou chamadas externas, mas possui custo relevante, principalmente por volume processado.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-security-group-vs-nacl"></a>

## Security Group vs NACL

> Security Group é stateful e atua no nível da interface de rede ou recurso. NACL é stateless e atua no nível da subnet. Em geral, Security Groups são o principal mecanismo de controle entre workloads; NACLs adicionam uma camada de controle mais ampla.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-route-table"></a>

## Route Table

> Route Table define para onde o tráfego de uma subnet deve ser encaminhado. Exemplos são rota local da VPC, Internet Gateway, NAT Gateway, Transit Gateway ou Peering. Problemas de conectividade frequentemente começam pela análise das rotas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-vpc-endpoint"></a>

## VPC Endpoint

> VPC Endpoint permite acessar determinados serviços AWS sem passar pela internet pública. Isso melhora a postura de segurança e pode reduzir dependência de NAT para alguns acessos. Existem endpoints do tipo Gateway e Interface, dependendo do serviço.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ec2"></a>

## EC2

> EC2 fornece máquinas virtuais sob demanda. Em entrevista, eu considero tipo de instância, AMI, storage, networking, autoscaling e estratégia de substituição. Para aplicações stateless, prefiro tratar instâncias como descartáveis e automatizar bootstrap e deploy.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-auto-scaling-group"></a>

## Auto Scaling Group

> Auto Scaling Group mantém uma quantidade desejada de instâncias e pode ajustar capacidade conforme métricas. Ele também substitui instâncias não saudáveis. Normalmente combino ASG com Load Balancer e health checks.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-alb-vs-nlb"></a>

## ALB vs NLB

> Application Load Balancer opera em camada 7 e entende HTTP/HTTPS, permitindo routing por host e path. Network Load Balancer opera em camada 4 e é indicado para TCP/UDP/TLS, alta performance e cenários que exigem características de rede mais baixas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ebs"></a>

## EBS

> EBS é block storage normalmente associado a EC2. É adequado para discos de sistema, banco autogerenciado e workloads que precisam de storage em bloco. Persistência e performance dependem do tipo de volume e da configuração.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-efs"></a>

## EFS

> EFS é um filesystem gerenciado acessível por múltiplas instâncias, útil quando várias máquinas precisam compartilhar arquivos. O modelo e a performance são diferentes de EBS, que é block storage.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3"></a>

## S3

> S3 é object storage altamente durável e escalável. Eu uso para arquivos, backups, artefatos, data lakes e conteúdo estático. Não é filesystem tradicional nem banco relacional.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-versioning"></a>

## S3 Versioning

> Versioning mantém múltiplas versões de um objeto e ajuda na recuperação de exclusões ou sobrescritas acidentais. O trade-off é maior consumo de storage, então normalmente combino com lifecycle policies.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-lifecycle"></a>

## S3 Lifecycle

> Lifecycle permite mover objetos para classes de storage mais econômicas ou expirá-los automaticamente conforme idade. É importante para reduzir custo em dados que envelhecem.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-presigned-url"></a>

## S3 Presigned URL

> Presigned URL fornece acesso temporário a um objeto S3 sem tornar o bucket público. É útil para upload ou download direto pelo cliente, reduzindo tráfego pela aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudfront"></a>

## CloudFront

> CloudFront é a CDN da AWS. Ele distribui conteúdo a partir de edge locations, reduzindo latência e carga na origem. Pode usar S3, ALB ou outras origens e integrar com controles como WAF.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-route-53"></a>

## Route 53

> Route 53 é o serviço DNS da AWS. Além de resolução DNS, oferece políticas de roteamento como weighted, latency-based e failover. É útil em arquiteturas multi-region e estratégias de recuperação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds"></a>

## RDS

> RDS é o serviço gerenciado para bancos relacionais. A AWS gerencia provisioning, backups e parte da operação conforme configuração. Eu ainda preciso cuidar de schema, índices, queries, capacidade e arquitetura de acesso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds-multi-az"></a>

## RDS Multi-AZ

> RDS Multi-AZ é voltado principalmente para alta disponibilidade e failover. Mantém redundância em outra AZ, mas não é usado principalmente para escalar leitura.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds-read-replica"></a>

## RDS Read Replica

> Read Replica é usada para escalar leitura e pode ter replicação assíncrona. Como pode existir lag, eu não assumo leitura imediatamente consistente após uma escrita. Multi-AZ e Read Replica resolvem problemas diferentes.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aurora"></a>

## Aurora

> Aurora é um banco relacional compatível com MySQL e PostgreSQL, com storage distribuído gerenciado pela AWS. Pode oferecer alta disponibilidade e escala de leitura, mas continua exigindo bom design de schema, índices e queries.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb"></a>

## DynamoDB

> DynamoDB é um banco NoSQL key-value/document totalmente gerenciado, projetado para baixa latência e escala horizontal. O design começa pelo padrão de acesso, porque partition key, sort key e índices determinam como os dados serão distribuídos e consultados.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-partition-key"></a>

## DynamoDB Partition Key

> Partition key influencia como os itens são distribuídos internamente. Uma chave com distribuição ruim pode criar hot partitions e limitar throughput. Por isso escolho uma chave com boa cardinalidade e distribuição.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-sort-key"></a>

## DynamoDB Sort Key

> Sort key permite armazenar múltiplos itens sob a mesma partition key e fazer consultas ordenadas ou por intervalo dentro dessa partição. É útil para séries temporais, eventos ou entidades relacionadas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-gsi-e-lsi"></a>

## DynamoDB GSI e LSI

> GSI cria uma nova chave de acesso com partition/sort key alternativas e é mais flexível. LSI compartilha a mesma partition key da tabela e define outra sort key, com restrições maiores. Eu escolho de acordo com os padrões de acesso necessários.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-consistency"></a>

## DynamoDB Consistency

> DynamoDB suporta leituras eventualmente consistentes por padrão e, em contextos suportados, leituras fortemente consistentes. Strong consistency custa mais capacidade, então escolho conforme a necessidade do caso de uso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-on-demand-vs-provisioned"></a>

## DynamoDB On-Demand vs Provisioned

> On-Demand simplifica workloads imprevisíveis porque a capacidade é ajustada pelo serviço. Provisioned permite definir capacidade e pode ser mais econômico em carga previsível. A decisão depende de perfil de tráfego e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-elasticache"></a>

## ElastiCache

> ElastiCache fornece Redis ou Memcached gerenciados. Eu uso principalmente para cache, sessões distribuídas, rate limiting e dados temporários. Cache melhora latência, mas exige TTL, invalidação e estratégia para falhas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda"></a>

## Lambda

> Lambda executa funções sob demanda sem gerenciar servidores. É adequada para processamento orientado a eventos, automações e APIs de carga compatível. O trade-off envolve cold start, limites de execução, concorrência e observabilidade.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-cold-start"></a>

## Lambda Cold Start

> Cold start é o tempo adicional para inicializar um novo ambiente de execução. Em Java pode ser mais perceptível dependendo do framework e do tamanho da aplicação. Eu reduzo dependências e trabalho no startup e avalio provisioned concurrency quando o requisito justificar.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-concurrency"></a>

## Lambda Concurrency

> Concurrency representa quantas execuções podem ocorrer simultaneamente. É importante para capacidade e também para proteger dependências downstream. Aumentar concorrência do Lambda não aumenta automaticamente a capacidade do banco ou API chamada.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-idempotencia"></a>

## Lambda Idempotência

> Como eventos podem ser reentregues ou retries acontecerem, funções Lambda devem ser idempotentes quando produzem efeitos externos. Uso chave idempotente, conditional writes ou registro de processamento conforme o cenário.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs"></a>

## ECS

> ECS é o serviço de orquestração de containers da AWS. Permite executar tasks e services e integrar com Load Balancer, autoscaling e IAM. É mais simples operacionalmente que Kubernetes para muitos workloads AWS-native.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-service-vs-task"></a>

## ECS Service vs Task

> Task é uma execução de uma task definition. Service mantém uma quantidade desejada de tasks em execução e integra health check, deployment e autoscaling.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-fargate"></a>

## Fargate

> Fargate permite executar containers em ECS ou EKS sem gerenciar diretamente instâncias EC2. Simplifica operação e isolamento de capacidade, com trade-off de custo e menor controle sobre a infraestrutura subjacente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-eks"></a>

## EKS

> EKS é o Kubernetes gerenciado da AWS. A AWS gerencia o control plane, enquanto o usuário continua responsável por workloads, networking, policies, observabilidade e nós quando não usa Fargate.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-vs-eks"></a>

## ECS vs EKS

> ECS é mais simples e integrado ao ecossistema AWS. EKS oferece o ecossistema e a portabilidade do Kubernetes, mas aumenta complexidade operacional. Eu escolheria EKS quando existe necessidade real de Kubernetes; caso contrário, ECS pode ser suficiente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-vs-lambda"></a>

## ECS vs Lambda

> ECS é mais apropriado para serviços long-running e workloads containerizados com maior controle do runtime. Lambda é adequada para execução orientada a eventos e curta duração. A escolha depende do modelo de execução, duração, dependências e operação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-api-gateway"></a>

## API Gateway

> API Gateway fornece gerenciamento de APIs com roteamento, autenticação, throttling e integração com Lambda ou backends HTTP. É especialmente comum em arquiteturas serverless. Para serviços containerizados simples, um ALB pode ser mais direto.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-api-gateway-vs-alb"></a>

## API Gateway vs ALB

> API Gateway oferece API management, throttling, authorization e integração serverless. ALB é um load balancer HTTP de camada 7 e costuma ser mais simples para rotear tráfego a ECS ou EC2. A escolha depende das funcionalidades necessárias.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs"></a>

## SQS

> SQS é uma fila gerenciada para desacoplamento assíncrono. Produtores enviam mensagens e consumidores processam independentemente. É útil para absorver picos, retry e reduzir acoplamento temporal entre serviços.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-standard-vs-fifo"></a>

## SQS Standard vs FIFO

> Standard oferece alto throughput com entrega at-least-once e ordenação best-effort. FIFO oferece ordenação e mecanismos de deduplicação dentro das garantias do serviço. Eu escolho FIFO quando ordem é requisito real.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-visibility-timeout"></a>

## SQS Visibility Timeout

> Visibility Timeout esconde temporariamente uma mensagem após ela ser recebida. Se o consumidor não a remover antes do timeout, ela pode ficar disponível novamente. O valor precisa considerar o tempo esperado de processamento.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-dlq"></a>

## SQS DLQ

> Dead Letter Queue recebe mensagens que falharam repetidamente após o limite configurado. Ela evita retry infinito e facilita inspeção, mas precisa de processo de análise e redrive.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-long-polling"></a>

## SQS Long Polling

> Long polling espera por mensagens durante um intervalo em vez de retornar imediatamente vazio. Isso reduz requests desnecessários e custo quando a fila tem baixo volume.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns"></a>

## SNS

> SNS é um serviço pub/sub. Um publisher envia uma mensagem para um tópico e múltiplos subscribers podem recebê-la. É adequado para fan-out.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns-sqs"></a>

## SNS + SQS

> Combinar SNS com SQS permite fan-out com isolamento por consumidor. Cada serviço recebe sua própria fila, podendo processar em ritmo diferente e aplicar retry e DLQ independentemente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-eventbridge"></a>

## EventBridge

> EventBridge é um event bus gerenciado com regras de roteamento baseadas no conteúdo do evento e integração ampla com serviços AWS e SaaS. É útil quando o roteamento é mais rico que um simples fan-out.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns-vs-eventbridge"></a>

## SNS vs EventBridge

> SNS é excelente para pub/sub e fan-out simples. EventBridge oferece regras de roteamento, event buses e integração ampla. Eu escolho conforme necessidade de filtragem, roteamento e governança.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-vs-kafka"></a>

## SQS vs Kafka

> SQS é uma fila gerenciada simples, ótima para desacoplamento com baixa operação. Kafka oferece log particionado, replay, consumer groups e streaming de alto throughput. Escolho Kafka quando preciso de histórico, replay e múltiplos consumidores independentes; SQS quando uma fila de trabalho é suficiente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudwatch"></a>

## CloudWatch

> CloudWatch centraliza métricas, logs, alarms e dashboards de serviços AWS. Em produção, monitoro latência, taxa de erro, saturação, throttling, filas, CPU, memória quando disponível e métricas de negócio.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudtrail"></a>

## CloudTrail

> CloudTrail registra eventos de API e atividades na conta AWS. É fundamental para auditoria, investigação de mudanças e segurança. Não substitui logs de aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-kms"></a>

## KMS

> KMS gerencia chaves criptográficas e integra com diversos serviços AWS. Eu uso para criptografia de dados e secrets, controlando quem pode usar cada chave através de IAM e key policies.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-secrets-manager-vs-parameter-store"></a>

## Secrets Manager vs Parameter Store

> Secrets Manager é focado em secrets e oferece recursos como rotação integrada em vários cenários. Parameter Store é útil para configuração e também pode armazenar valores seguros. Eu escolho considerando rotação, integração e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-waf"></a>

## WAF

> AWS WAF aplica regras de camada 7 para proteger aplicações web contra padrões maliciosos, bots e ataques comuns. Pode ser associado a serviços como CloudFront e ALB. Ele complementa, mas não substitui segurança da aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudformation"></a>

## CloudFormation

> CloudFormation permite declarar infraestrutura como código usando templates. Isso melhora repetibilidade, versionamento e automação. O trade-off é lidar com ciclo de vida e dependências dos stacks.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-cdk"></a>

## AWS CDK

> AWS CDK permite definir infraestrutura usando linguagens de programação e sintetiza templates de CloudFormation. Ele melhora abstração e reutilização, mas continua baseado no modelo de recursos e deployment do CloudFormation.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-infrastructure-as-code"></a>

## Infrastructure as Code

> IaC torna infraestrutura versionável, reproduzível e auditável. Eu evito alterações manuais em produção porque geram drift. Seja com Terraform, CloudFormation ou CDK, o objetivo é manter infraestrutura dentro do processo de entrega.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rto-e-rpo"></a>

## RTO e RPO

> RTO é quanto tempo o sistema pode ficar indisponível até recuperação. RPO é quanto de dados pode ser perdido em termos de tempo. Esses requisitos determinam estratégia de backup, replicação, failover e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-disaster-recovery"></a>

## Disaster Recovery

> Estratégias de DR variam de backup/restore até warm standby e active-active. Quanto menores RTO e RPO, maior o custo e a complexidade. Eu começo pelos requisitos de negócio antes de escolher a estratégia.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-well-architected-framework"></a>

## Well-Architected Framework

> O AWS Well-Architected Framework organiza boas práticas em pilares como excelência operacional, segurança, confiabilidade, eficiência de performance, otimização de custos e sustentabilidade. Eu o uso como estrutura para justificar decisões, não como checklist decorado.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cost-optimization"></a>

## Cost Optimization

> Otimização de custo envolve right sizing, autoscaling, desligar recursos ociosos, storage adequado, compromissos de uso quando a carga é previsível e atenção a data transfer. A arquitetura mais barata é a que atende requisitos sem excesso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-nat-gateway-cost"></a>

## NAT Gateway Cost

> NAT Gateway possui custo por hora e por volume processado. Em arquiteturas com muito acesso a serviços AWS, VPC Endpoints podem reduzir tráfego pelo NAT e melhorar segurança, dependendo do padrão de uso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-resposta-completa-de-entrevista-aws"></a>

## Resposta Completa de Entrevista — AWS

> Em AWS eu organizo o raciocínio por compute, networking, dados, integração, segurança e operação. Para compute posso escolher EC2, ECS, EKS ou Lambda conforme modelo de execução e complexidade operacional. Em rede, entendo VPC, subnets, Security Groups, NACLs e Load Balancers.
>
> > Para dados, diferencio S3, RDS/Aurora, DynamoDB e ElastiCache de acordo com o tipo de persistência e padrão de acesso. Em integração assíncrona, uso SQS para filas, SNS para fan-out e EventBridge para roteamento de eventos.
>
> > Segurança passa por IAM, roles, least privilege, KMS e gerenciamento de secrets. Em produção, uso CloudWatch, CloudTrail e Infrastructure as Code, além de desenhar para Multi-AZ quando existe requisito de alta disponibilidade.
>
> > O ponto principal é justificar cada serviço por requisito e trade-off. AWS fornece componentes gerenciados, mas ainda preciso cuidar de arquitetura, segurança, observabilidade, custo e limites das dependências.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

---

<a id="cicd-cicd-sumario"></a>

# CI/CD

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — CI/CD](#cicd-visao-geral-ci-cd)
- [Continuous Integration](#cicd-continuous-integration)
- [Continuous Delivery vs Continuous Deployment](#cicd-continuous-delivery-vs-continuous-deployment)
- [Pipeline CI/CD](#cicd-pipeline-ci-cd)
- [Fail Fast](#cicd-fail-fast)
- [Maven em CI/CD](#cicd-maven-em-ci-cd)
- [Gradle em CI/CD](#cicd-gradle-em-ci-cd)
- [Maven Wrapper e Gradle Wrapper](#cicd-maven-wrapper-e-gradle-wrapper)
- [Build Reprodutível](#cicd-build-reprodutivel)
- [Artefato Imutável](#cicd-artefato-imutavel)
- [Nexus e Artifactory](#cicd-nexus-e-artifactory)
- [Versionamento de Artefato](#cicd-versionamento-de-artefato)
- [Snapshot vs Release](#cicd-snapshot-vs-release)
- [Git Strategy](#cicd-git-strategy)
- [Trunk-Based Development](#cicd-trunk-based-development)
- [Pull Request e Code Review](#cicd-pull-request-e-code-review)
- [Branch Protection](#cicd-branch-protection)
- [Testes Unitários na Pipeline](#cicd-testes-unitarios-na-pipeline)
- [Testes de Integração](#cicd-testes-de-integracao)
- [Testcontainers](#cicd-testcontainers)
- [Contract Testing](#cicd-contract-testing)
- [End-to-End Tests](#cicd-end-to-end-tests)
- [Test Pyramid](#cicd-test-pyramid)
- [Flaky Tests](#cicd-flaky-tests)
- [Quality Gate](#cicd-quality-gate)
- [SonarQube](#cicd-sonarqube)
- [Cobertura de Testes](#cicd-cobertura-de-testes)
- [SAST](#cicd-sast)
- [SCA](#cicd-sca)
- [DAST](#cicd-dast)
- [SBOM](#cicd-sbom)
- [Supply Chain Security](#cicd-supply-chain-security)
- [Secrets no Pipeline](#cicd-secrets-no-pipeline)
- [Docker Build em CI](#cicd-docker-build-em-ci)
- [Multi-stage Docker Build](#cicd-multi-stage-docker-build)
- [Imagem Docker Imutável](#cicd-imagem-docker-imutavel)
- [Image Scanning](#cicd-image-scanning)
- [Registry](#cicd-registry)
- [Environment Promotion](#cicd-environment-promotion)
- [Configuração por Ambiente](#cicd-configuracao-por-ambiente)
- [Feature Flag](#cicd-feature-flag)
- [Rolling Deployment](#cicd-rolling-deployment)
- [Blue-Green Deployment](#cicd-blue-green-deployment)
- [Canary Deployment](#cicd-canary-deployment)
- [Rolling vs Blue-Green vs Canary](#cicd-rolling-vs-blue-green-vs-canary)
- [Rollback](#cicd-rollback)
- [Roll Forward](#cicd-roll-forward)
- [Database Migration](#cicd-database-migration)
- [Flyway e Liquibase](#cicd-flyway-e-liquibase)
- [Zero-Downtime Database Migration](#cicd-zero-downtime-database-migration)
- [Expand and Contract](#cicd-expand-and-contract)
- [Backward Compatibility no Deploy](#cicd-backward-compatibility-no-deploy)
- [Health Check Pós-Deploy](#cicd-health-check-pos-deploy)
- [Smoke Test](#cicd-smoke-test)
- [Observabilidade Pós-Deploy](#cicd-observabilidade-pos-deploy)
- [Automated Rollback](#cicd-automated-rollback)
- [Approval Gate](#cicd-approval-gate)
- [Pipeline as Code](#cicd-pipeline-as-code)
- [Jenkins](#cicd-jenkins)
- [GitHub Actions](#cicd-github-actions)
- [GitLab CI](#cicd-gitlab-ci)
- [Runner Self-hosted vs Hosted](#cicd-runner-self-hosted-vs-hosted)
- [Cache na Pipeline](#cicd-cache-na-pipeline)
- [Paralelismo na Pipeline](#cicd-paralelismo-na-pipeline)
- [Artifacts entre Jobs](#cicd-artifacts-entre-jobs)
- [Idempotência do Deploy](#cicd-idempotencia-do-deploy)
- [Infraestrutura como Código no CI/CD](#cicd-infraestrutura-como-codigo-no-ci-cd)
- [GitOps](#cicd-gitops)
- [CI/CD em Kubernetes](#cicd-ci-cd-em-kubernetes)
- [CI/CD em ECS](#cicd-ci-cd-em-ecs)
- [Java e Container Memory](#cicd-java-e-container-memory)
- [Graceful Shutdown](#cicd-graceful-shutdown)
- [Readiness e Liveness](#cicd-readiness-e-liveness)
- [Deploy de Java com Migração de Banco](#cicd-deploy-de-java-com-migracao-de-banco)
- [Dependency Management](#cicd-dependency-management)
- [Pipeline para Microserviços](#cicd-pipeline-para-microservicos)
- [Pipeline Monorepo](#cicd-pipeline-monorepo)
- [Pipeline Failure — Como Investigar](#cicd-pipeline-failure-como-investigar)
- [Lead Time](#cicd-lead-time)
- [Deployment Frequency](#cicd-deployment-frequency)
- [Change Failure Rate](#cicd-change-failure-rate)
- [MTTR](#cicd-mttr)
- [DORA Metrics](#cicd-dora-metrics)
- [CI/CD e Segurança](#cicd-ci-cd-e-seguranca)
- [CI/CD e Compliance](#cicd-ci-cd-e-compliance)
- [Resposta Completa de Entrevista — CI/CD](#cicd-resposta-completa-de-entrevista-ci-cd)

---

<a id="cicd-visao-geral-ci-cd"></a>

## Visão Geral — CI/CD

> CI/CD automatiza integração, validação, empacotamento e entrega de software. Para Java, eu penso no fluxo completo: checkout, build Maven ou Gradle, testes, análise estática, geração do artefato, criação da imagem, publicação em registry e deployment. O objetivo é reduzir risco, feedback time e trabalho manual, mantendo releases reproduzíveis e auditáveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-continuous-integration"></a>

## Continuous Integration

> Continuous Integration significa integrar mudanças pequenas e frequentes em uma branch compartilhada, executando build e validações automaticamente. O foco é detectar regressões cedo. Para Java, normalmente incluo compilação, testes unitários, análise estática e, quando necessário, testes de integração.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-continuous-delivery-vs-continuous-deployment"></a>

## Continuous Delivery vs Continuous Deployment

> Continuous Delivery mantém o software sempre em estado implantável, mas o deploy em produção pode depender de aprovação. Continuous Deployment leva isso adiante e promove automaticamente para produção quando todos os gates passam. A diferença principal é o último passo de promoção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-ci-cd"></a>

## Pipeline CI/CD

> Uma pipeline típica para Java tem estágios como checkout, build, testes unitários, análise estática, testes de integração, empacotamento, publicação do artefato ou imagem, deployment e validação pós-deploy. Eu separo stages para falhar cedo e economizar tempo e recursos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-fail-fast"></a>

## Fail Fast

> Fail fast significa executar primeiro as validações mais rápidas e baratas. Por exemplo, compilar e rodar testes unitários antes de testes de integração ou deploy. Isso reduz feedback time e custo de pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-maven-em-ci-cd"></a>

## Maven em CI/CD

> Em projetos Maven, eu costumo usar comandos reprodutíveis como `mvn clean verify` para compilar, testar e executar verificações configuradas. Também uso Maven Wrapper quando disponível para garantir a versão esperada da ferramenta no ambiente de CI.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gradle-em-ci-cd"></a>

## Gradle em CI/CD

> Com Gradle, prefiro usar o Gradle Wrapper, como `./gradlew build`, para garantir versão consistente entre desenvolvedor e CI. Também observo cache, paralelismo e tasks de teste separadas quando a pipeline cresce.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-maven-wrapper-e-gradle-wrapper"></a>

## Maven Wrapper e Gradle Wrapper

> Wrappers evitam depender da versão instalada globalmente no runner. Isso melhora reprodutibilidade porque a própria aplicação define qual versão do Maven ou Gradle deve ser utilizada.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-build-reprodutivel"></a>

## Build Reprodutível

> Um build reproduzível deve produzir o mesmo resultado a partir da mesma revisão e configuração. Para isso, fixo versões, uso wrappers, evito dependências dinâmicas, controlo plugins e não dependo de estado local do runner.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-artefato-imutavel"></a>

## Artefato Imutável

> Eu prefiro construir o artefato uma única vez e promover o mesmo binário ou imagem entre ambientes. Rebuildar em cada ambiente pode gerar diferenças de dependência ou configuração e quebra a garantia de que o que foi testado é exatamente o que chegou à produção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-nexus-e-artifactory"></a>

## Nexus e Artifactory

> Nexus e Artifactory são repositórios de artefatos. Em Java, armazenam JARs, BOMs e outros pacotes, além de poderem atuar como proxy de dependências externas. Eles ajudam em versionamento, disponibilidade, auditoria e controle da supply chain.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-versionamento-de-artefato"></a>

## Versionamento de Artefato

> Eu evito sobrescrever versões publicadas. Normalmente uso versão única por build, associada a commit ou release. Isso facilita rollback, rastreabilidade e reprodução de incidentes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-snapshot-vs-release"></a>

## Snapshot vs Release

> Em Maven, `SNAPSHOT` representa uma versão em desenvolvimento que pode mudar ao longo do tempo, enquanto uma release deve ser imutável. Para produção, uso versões imutáveis e rastreáveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-git-strategy"></a>

## Git Strategy

> A estratégia de Git deve reduzir branches longas e integração tardia. Trunk-based development favorece branches curtas e integração frequente. Git Flow pode funcionar em alguns contextos, mas adiciona mais branches e coordenação. Eu escolho de acordo com frequência de release e maturidade do time.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-trunk-based-development"></a>

## Trunk-Based Development

> Trunk-based development incentiva branches pequenas e curtas, integradas rapidamente ao trunk. Isso reduz divergência, conflitos e tempo até integração. Feature flags ajudam quando funcionalidades ainda não estão prontas para exposição.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pull-request-e-code-review"></a>

## Pull Request e Code Review

> Pull Request serve como gate técnico e de colaboração. Eu combino review com validações automáticas para evitar depender apenas de revisão humana. Branch protection deve impedir merge quando build, testes ou checks obrigatórios falham.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-branch-protection"></a>

## Branch Protection

> Branch protection normalmente exige review, checks verdes e bloqueia push direto em branches críticas. Isso reduz mudanças não auditadas e garante que a política de qualidade seja aplicada de forma consistente.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testes-unitarios-na-pipeline"></a>

## Testes Unitários na Pipeline

> Testes unitários devem rodar cedo porque são rápidos e dão feedback imediato. Eles validam comportamento isolado e ajudam a impedir regressões antes de etapas mais caras.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testes-de-integracao"></a>

## Testes de Integração

> Testes de integração validam interação entre componentes reais, como banco, fila ou serviços. São mais caros e lentos, então normalmente ficam em estágio posterior ao unit test, mas antes de promover o artefato.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testcontainers"></a>

## Testcontainers

> Testcontainers permite subir dependências reais, como PostgreSQL, Kafka ou Redis, durante testes de integração. Isso reduz diferenças entre mock e ambiente real, embora aumente tempo e consumo de recursos da pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-contract-testing"></a>

## Contract Testing

> Contract tests verificam compatibilidade entre produtor e consumidor sem exigir um ambiente end-to-end completo. São úteis em microserviços para detectar breaking changes cedo e reduzir dependência de testes integrados frágeis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-end-to-end-tests"></a>

## End-to-End Tests

> Testes end-to-end validam o fluxo completo, mas são lentos e mais suscetíveis a flakiness. Eu mantenho poucos E2E focados em jornadas críticas e deixo a maior parte da cobertura em testes unitários, integração e contrato.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-test-pyramid"></a>

## Test Pyramid

> A test pyramid prioriza muitos testes unitários, uma quantidade menor de integração e poucos end-to-end. O objetivo é equilibrar velocidade, confiança e custo de manutenção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-flaky-tests"></a>

## Flaky Tests

> Teste flaky falha de forma não determinística e reduz confiança na pipeline. Eu trato flakiness como defeito: investigo concorrência, timing, dependência externa ou estado compartilhado em vez de simplesmente adicionar retry indefinido.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-quality-gate"></a>

## Quality Gate

> Quality gate define condições mínimas para promoção, como testes aprovados, análise estática aceitável e ausência de vulnerabilidades críticas. O gate deve bloquear problemas reais, não virar uma coleção de métricas arbitrárias.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sonarqube"></a>

## SonarQube

> SonarQube analisa qualidade e segurança do código, como bugs potenciais, code smells e vulnerabilidades. Eu uso como sinal automatizado dentro da pipeline, mas não substituo revisão técnica nem testes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cobertura-de-testes"></a>

## Cobertura de Testes

> Cobertura mede código executado pelos testes, não qualidade dos testes. Eu uso coverage para identificar áreas sem validação, mas evito metas cegas que incentivem testes sem valor.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sast"></a>

## SAST

> SAST analisa código-fonte ou bytecode procurando vulnerabilidades sem executar a aplicação. É útil cedo na pipeline para detectar padrões inseguros.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sca"></a>

## SCA

> Software Composition Analysis identifica vulnerabilidades conhecidas em dependências e componentes de terceiros. Em Java é especialmente relevante por causa do grande ecossistema Maven e Gradle.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dast"></a>

## DAST

> DAST testa a aplicação em execução, simulando ataques ou comportamentos maliciosos. Normalmente roda em ambiente implantado e complementa SAST e SCA.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sbom"></a>

## SBOM

> SBOM é uma lista estruturada dos componentes e dependências que formam o software. Ajuda a responder rapidamente se uma aplicação usa uma biblioteca vulnerável e melhora rastreabilidade da supply chain.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-supply-chain-security"></a>

## Supply Chain Security

> Segurança da supply chain inclui dependências, plugins, imagens, runners, registry e processo de build. Eu evito binários não verificados, mantenho dependências controladas, aplico scanning e protejo credenciais do pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-secrets-no-pipeline"></a>

## Secrets no Pipeline

> Secrets nunca devem ficar hardcoded no repositório ou nos logs. Uso secret stores da plataforma de CI/CD ou serviços como Secrets Manager e Vault e limito acesso por ambiente e job.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-docker-build-em-ci"></a>

## Docker Build em CI

> Na pipeline, construo a imagem Docker depois que código e testes passam. A imagem deve ser versionada de forma imutável, por exemplo com tag de commit, e publicada em registry.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-multi-stage-docker-build"></a>

## Multi-stage Docker Build

> Multi-stage build permite separar a etapa de compilação da imagem final. Em Java, posso compilar com Maven ou Gradle em uma etapa e copiar apenas o JAR e runtime necessário para a imagem final, reduzindo tamanho e superfície de ataque.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-imagem-docker-imutavel"></a>

## Imagem Docker Imutável

> Eu não reutilizo a mesma tag mutável para representar builds diferentes em produção. Prefiro tags imutáveis por commit ou versão e, quando possível, referência por digest.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-image-scanning"></a>

## Image Scanning

> Image scanning identifica vulnerabilidades conhecidas no sistema operacional e nas dependências da imagem. Eu incluo esse check antes da promoção e também reavalio imagens já publicadas porque novas CVEs surgem depois do build.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-registry"></a>

## Registry

> Registry armazena imagens versionadas, como ECR ou outro registry. Além do armazenamento, considero políticas de retenção, scanning, permissões e imutabilidade de tags.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-environment-promotion"></a>

## Environment Promotion

> Eu promovo o mesmo artefato entre dev, staging e produção, mudando apenas configuração externa. Isso mantém rastreabilidade e reduz diferenças entre ambientes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-configuracao-por-ambiente"></a>

## Configuração por Ambiente

> Configuração específica de ambiente deve ser externa ao artefato, por exemplo variáveis, ConfigMap, Secrets ou serviço de configuração. O código e a imagem devem permanecer iguais.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-feature-flag"></a>

## Feature Flag

> Feature flag desacopla deploy de release. Posso colocar código em produção desabilitado e ativá-lo gradualmente. O cuidado é evitar flags permanentes e combinações demais, que aumentam complexidade.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rolling-deployment"></a>

## Rolling Deployment

> Rolling deployment substitui instâncias gradualmente. Tem baixo custo de infraestrutura e é simples, mas versões antiga e nova coexistem durante o rollout, então compatibilidade entre versões é importante.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-blue-green-deployment"></a>

## Blue-Green Deployment

> Blue-Green mantém dois ambientes: o atual e o novo. Após validação, o tráfego é trocado para o novo ambiente. O rollback é rápido, mas exige capacidade duplicada durante a transição.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-canary-deployment"></a>

## Canary Deployment

> Canary envia uma pequena parcela do tráfego para a nova versão e aumenta gradualmente se as métricas estiverem saudáveis. Reduz blast radius, mas exige bom roteamento e observabilidade.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rolling-vs-blue-green-vs-canary"></a>

## Rolling vs Blue-Green vs Canary

> Rolling é simples e econômico, mas mistura versões. Blue-Green facilita rollback rápido com custo de capacidade duplicada. Canary reduz risco expondo progressivamente, porém exige automação e métricas maduras. Eu escolho conforme criticidade, custo e capacidade operacional.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rollback"></a>

## Rollback

> Rollback retorna rapidamente para uma versão conhecida. Para funcionar, o artefato anterior deve estar disponível e mudanças de banco precisam ser compatíveis. Nem todo incidente permite rollback simples, principalmente quando dados já foram transformados.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-roll-forward"></a>

## Roll Forward

> Roll forward corrige o problema com uma nova versão em vez de voltar. É útil quando rollback de schema ou dados é arriscado. Em sistemas modernos, muitas equipes preferem roll forward para mudanças irreversíveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-database-migration"></a>

## Database Migration

> Migração de banco deve fazer parte do processo de entrega, usando ferramentas como Flyway ou Liquibase. Eu versiono scripts, executo de forma controlada e evito mudanças destrutivas incompatíveis com versões ainda em execução.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-flyway-e-liquibase"></a>

## Flyway e Liquibase

> Flyway e Liquibase versionam mudanças de schema e permitem aplicar migrations de forma automatizada. O objetivo é tratar banco como parte versionada da aplicação e evitar alterações manuais em produção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-zero-downtime-database-migration"></a>

## Zero-Downtime Database Migration

> Para zero downtime, faço mudanças backward compatible em etapas. Primeiro adiciono coluna ou estrutura nova sem remover a antiga, depois atualizo aplicação para usar o novo formato, migro dados e só por último removo o legado quando nenhuma versão depende dele.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-expand-and-contract"></a>

## Expand and Contract

> Expand and Contract é uma estratégia de evolução de schema em duas fases. Expand adiciona estruturas compatíveis; Contract remove estruturas antigas somente depois que todos os consumidores migraram. É essencial em rolling deployments.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-backward-compatibility-no-deploy"></a>

## Backward Compatibility no Deploy

> Durante rollout, versões antiga e nova podem rodar juntas. Por isso APIs, eventos e schema precisam permanecer compatíveis durante a transição. Breaking changes exigem estratégia em múltiplas etapas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-health-check-pos-deploy"></a>

## Health Check Pós-Deploy

> Depois do deployment, verifico readiness, liveness e health checks relevantes. O objetivo é confirmar que a nova versão está apta a receber tráfego e que dependências críticas estão funcionando.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-smoke-test"></a>

## Smoke Test

> Smoke test é um conjunto pequeno de testes após o deploy para validar fluxos essenciais. Ele detecta rapidamente problemas de configuração, rota, banco ou dependência antes de liberar tráfego completo.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-observabilidade-pos-deploy"></a>

## Observabilidade Pós-Deploy

> Deployment não termina quando o container sobe. Eu observo erro, latência, p95/p99, CPU, memória, GC, pool de conexões e métricas de negócio para confirmar que a nova versão está saudável.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-automated-rollback"></a>

## Automated Rollback

> Rollback automático pode ser disparado quando métricas ou health checks ultrapassam limites durante canary ou rollout. Eu uso com critérios bem definidos para evitar rollback por ruído.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-approval-gate"></a>

## Approval Gate

> Approval gate adiciona aprovação manual antes de promover para ambiente crítico. Faz sentido em organizações reguladas ou quando o risco exige controle adicional. Em fluxos maduros e bem automatizados, o objetivo pode ser reduzir gates manuais desnecessários.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-as-code"></a>

## Pipeline as Code

> Pipeline as Code mantém a definição da pipeline versionada junto do código ou em repositório controlado. Isso facilita review, histórico, reprodução e evolução do processo de entrega.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-jenkins"></a>

## Jenkins

> Jenkins é um servidor de automação flexível e extensível. Permite pipelines complexas via Jenkinsfile, mas exige operação, atualização de plugins, segurança e manutenção de agentes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-github-actions"></a>

## GitHub Actions

> GitHub Actions integra workflows diretamente ao repositório. É conveniente para CI/CD e automação, mas eu considero segurança de actions de terceiros, permissões do token, secrets e custo dos runners.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gitlab-ci"></a>

## GitLab CI

> GitLab CI define pipelines em arquivo versionado e integra build, test e deploy à plataforma. O conceito é semelhante a outras ferramentas: stages, jobs, runners, artifacts e environments.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-runner-self-hosted-vs-hosted"></a>

## Runner Self-hosted vs Hosted

> Runner hospedado simplifica operação. Self-hosted oferece mais controle de rede, performance e ferramentas, mas aumenta responsabilidade de patching, isolamento e segurança.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cache-na-pipeline"></a>

## Cache na Pipeline

> Cache acelera pipelines reaproveitando dependências ou outputs intermediários. Em Java, cache de Maven ou Gradle pode reduzir bastante o tempo. O cuidado é evitar cache incorreto ou não invalidado, que pode mascarar problemas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-paralelismo-na-pipeline"></a>

## Paralelismo na Pipeline

> Stages independentes podem rodar em paralelo, como testes unitários, análise estática e scanning. Isso reduz lead time, desde que não aumente excessivamente custo ou competição por recursos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-artifacts-entre-jobs"></a>

## Artifacts entre Jobs

> Artifacts permitem transportar outputs de um estágio para outro, como JAR, relatórios ou pacotes. Prefiro passar o artefato construído em vez de reconstruí-lo em cada job.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-idempotencia-do-deploy"></a>

## Idempotência do Deploy

> Deploy idempotente significa que executar o mesmo processo novamente leva ao mesmo estado desejado sem efeitos colaterais indevidos. Isso é importante para retries automáticos e recuperação de falhas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-infraestrutura-como-codigo-no-ci-cd"></a>

## Infraestrutura como Código no CI/CD

> IaC integra mudanças de infraestrutura ao mesmo processo de review e automação. Eu executo validação e plan antes de aplicar mudanças, mantendo auditabilidade e reduzindo drift manual.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gitops"></a>

## GitOps

> GitOps usa Git como fonte declarativa do estado desejado e um controlador aplica mudanças no ambiente. É comum em Kubernetes. O benefício é auditabilidade e reconciliação contínua; o trade-off é adicionar outro modelo operacional.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-em-kubernetes"></a>

## CI/CD em Kubernetes

> Em Kubernetes, a pipeline normalmente constrói e publica a imagem e depois atualiza manifests, Helm chart ou configuração GitOps. O rollout deve considerar readiness, strategy, recursos, autoscaling e rollback.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-em-ecs"></a>

## CI/CD em ECS

> Em ECS, a pipeline publica a imagem no registry, cria uma nova task definition e atualiza o service. Eu acompanho health checks e deployment status e mantenho revisão anterior disponível para rollback.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-java-e-container-memory"></a>

## Java e Container Memory

> Ao implantar Java em containers, considero o limite total de memória, heap, metaspace, stacks e memória nativa. `-Xmx` não deve consumir todo o limite do container porque a JVM precisa de memória fora da heap.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-graceful-shutdown"></a>

## Graceful Shutdown

> Graceful shutdown permite que a instância pare de aceitar novas requisições e termine trabalho em andamento antes de encerrar. É importante em rolling deploys para evitar requests interrompidas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-readiness-e-liveness"></a>

## Readiness e Liveness

> Readiness indica se a instância está pronta para receber tráfego. Liveness indica se o processo está saudável o suficiente para continuar vivo. Confundir os dois pode causar reinícios desnecessários ou tráfego para instâncias não prontas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-deploy-de-java-com-migracao-de-banco"></a>

## Deploy de Java com Migração de Banco

> Eu separo mudança de schema destrutiva da mudança de aplicação. Primeiro aplico schema compatível, depois implanto código novo, valido a execução e só removo estruturas antigas em uma release posterior.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dependency-management"></a>

## Dependency Management

> Em Java, mantenho versões controladas por BOM ou dependency management, evito ranges dinâmicos e monitoro vulnerabilidades. Reprodutibilidade depende de saber exatamente quais versões entraram no build.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-para-microservicos"></a>

## Pipeline para Microserviços

> Cada microserviço deve ter pipeline independente sempre que possível. Um deploy de um serviço não deveria exigir rebuild de todos os outros. Contratos, testes e versionamento devem permitir autonomia.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-monorepo"></a>

## Pipeline Monorepo

> Em monorepo, evito rebuildar tudo quando apenas um módulo mudou. Posso detectar paths impactados e executar pipelines seletivas, mantendo também uma validação global quando necessário.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-failure-como-investigar"></a>

## Pipeline Failure — Como Investigar

> Eu identifico primeiro o estágio que falhou: build, teste, análise, publicação ou deploy. Depois separo erro de código de erro de infraestrutura, verifico logs, artefatos, versão das ferramentas, credenciais e diferenças de ambiente. Evito simplesmente reexecutar até passar sem entender a causa.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-lead-time"></a>

## Lead Time

> Lead time mede quanto tempo uma mudança leva desde o commit até produção. Pipelines lentas aumentam batch size e atrasam feedback. Eu reduzo lead time com fail fast, paralelismo, cache e testes bem distribuídos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-deployment-frequency"></a>

## Deployment Frequency

> Deployment frequency mostra com que frequência o time consegue colocar mudanças em produção. Frequência alta normalmente depende de automação, testes confiáveis e mudanças pequenas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-change-failure-rate"></a>

## Change Failure Rate

> Change failure rate mede a proporção de deploys que causam incidente, rollback ou correção urgente. É um indicador importante da qualidade do processo de entrega.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-mttr"></a>

## MTTR

> Mean Time to Recovery mede quanto tempo o time leva para restaurar o serviço após uma falha. Rollback rápido, observabilidade e automação reduzem MTTR.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dora-metrics"></a>

## DORA Metrics

> As métricas DORA mais conhecidas incluem deployment frequency, lead time for changes, change failure rate e time to restore service. Eu uso essas métricas para avaliar fluxo de entrega e confiabilidade, não para medir produtividade individual.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-e-seguranca"></a>

## CI/CD e Segurança

> Pipeline possui acesso a código, artefatos, ambientes e credenciais, então é parte da superfície de ataque. Eu aplico least privilege, runners isolados, actions ou plugins confiáveis, secrets protegidos, branch protection e auditoria.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-e-compliance"></a>

## CI/CD e Compliance

> Em ambientes regulados, a pipeline pode registrar aprovações, artefatos, evidências de testes, versão implantada e histórico de mudanças. Automação melhora auditabilidade quando cada passo deixa evidência rastreável.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-resposta-completa-de-entrevista-ci-cd"></a>

## Resposta Completa de Entrevista — CI/CD

> CI/CD é o processo de automatizar integração, validação e entrega do software. Em um projeto Java, minha pipeline normalmente começa com checkout, build via Maven ou Gradle, testes unitários, análise estática e segurança, depois testes de integração, empacotamento do JAR ou imagem Docker, publicação em registry e deployment.
>
> Eu tento construir o artefato uma única vez e promover o mesmo binário entre ambientes, mantendo configuração externa. Para deployment, escolho Rolling, Blue-Green ou Canary de acordo com risco, custo e observabilidade. Também considero migrations de banco com Flyway ou Liquibase e uso expand-and-contract para evitar breaking changes durante rollout.
>
> Depois do deploy, valido readiness, smoke tests e métricas como erro, latência e p95/p99. Se necessário, faço rollback ou roll forward. O objetivo é reduzir lead time sem aumentar change failure rate, mantendo segurança, rastreabilidade e capacidade de recuperação.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

