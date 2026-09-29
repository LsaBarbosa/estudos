# Respostas de Entrevista — Java & Spring

Documento consolidado contendo **somente as respostas preparadas para entrevista**, organizado para consulta rápida.

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
