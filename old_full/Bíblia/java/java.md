# Guia de Revisão Java — Entrevistas

Material consolidado para revisão objetiva dos quatro tópicos:

- JVM Memory & Garbage Collection
- Concorrência
- Collections
- Programação Orientada a Objetos

<a id="sumario-geral"></a>

## Sumário Geral

1. [JVM Memory & Garbage Collection → abrir sumário do tópico](#memory-indice)
2. [Concorrência em Java → abrir sumário do tópico](#concorrencia-indice)
3. [Java Collections → abrir sumário do tópico](#collections-indice)
4. [Programação Orientada a Objetos em Java → abrir sumário do tópico](#poo-indice)

> Cada tópico mantém seu próprio índice clicável. Ao longo do documento existem atalhos para retornar tanto ao sumário do tópico quanto ao sumário geral.

---

# JVM Memory & Garbage Collection

<a id="memory-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#memory-visao-geral)
- [2. Heap vs Stack](#memory-heap-vs-stack)
  - [2.1 Heap](#memory-heap)
  - [2.2 Stack](#memory-stack)
- [3. Reachability e GC Roots](#memory-reachability-gc-roots)
  - [3.1 GC Roots](#memory-gc-roots)
  - [3.2 Regra de Alcançabilidade](#memory-regra-alcancabilidade)
- [4. Young e Old Generation](#memory-young-old)
  - [4.1 Fluxo Geracional](#memory-fluxo-geracional)
- [5. Garbage Collector](#memory-garbage-collector)
- [6. G1 GC](#memory-g1-gc)
  - [6.1 Young Collection](#memory-young-collection)
  - [6.2 Mixed Collection](#memory-mixed-collection)
  - [6.3 Full GC](#memory-full-gc)
- [7. G1 vs ZGC](#memory-g1-vs-zgc)
  - [7.1 G1](#memory-g1)
  - [7.2 ZGC](#memory-zgc)
- [8. Trade-offs de Heap e GC](#memory-trade-offs)
  - [8.1 Xmx](#memory-xmx)
  - [8.2 Escolha do Coletor](#memory-escolha-coletor)
- [9. Memória Fora da Heap](#memory-memoria-processo)
- [10. Heap Dump vs Thread Dump](#memory-heap-vs-thread-dump)
  - [10.1 Heap Dump](#memory-heap-dump)
  - [10.2 Thread Dump](#memory-thread-dump)
- [11. Investigação em Produção](#memory-producao)
  - [11.1 Métricas e GC Logs](#memory-metricas-gc)
  - [11.2 Heap após GC continua crescendo](#memory-heap-crescendo)
  - [11.3 Processo cresce, mas Heap estável](#memory-processo-cresce)
  - [11.4 Heap estável, mas latência aumenta](#memory-latencia-aumenta)
  - [11.5 Tuning](#memory-tuning)
- [12. Resposta Completa de Entrevista](#memory-resposta-completa)
- [13. Mapa Mental](#memory-mapa-mental)

---

<a id="memory-visao-geral"></a>
# 1. Visão Geral

- **Heap** → onde ficam principalmente objetos e arrays.
- **Stack** → acompanha a execução de cada thread.
- **Young / Old Generation** → organização geracional da Heap para otimizar a coleta.
- **GC** → identifica objetos não alcançáveis e recupera memória.
- **G1 / ZGC** → coletores com objetivos e trade-offs diferentes.
- **Heap Dump** → investigação de objetos, retenção e caminhos até GC Roots.
- **Thread Dump** → investigação de execução, espera, bloqueios e contenção.

### Resposta de entrevista

> A JVM possui diferentes áreas de memória. A Heap armazena objetos e é gerenciada pelo Garbage Collector, enquanto cada thread possui sua própria Stack para execução dos métodos. Em produção, começo investigando métricas e GC logs e, quando necessário, uso Heap Dump e Thread Dump para chegar à causa.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-heap-vs-stack"></a>
# 2. Heap vs Stack

<a id="memory-heap"></a>
## 2.1 Heap

- Área de memória **compartilhada entre as threads**.
- Armazena principalmente objetos e arrays.
- É gerenciada pelo Garbage Collector.
- Pode ocorrer:

```text
OutOfMemoryError: Java heap space
```

quando a JVM não consegue atender novas alocações dentro da Heap disponível.

### Resposta de entrevista

> A Heap é a área compartilhada entre as threads onde ficam objetos e arrays. Ela é gerenciada pelo Garbage Collector. Se não houver espaço suficiente para novas alocações e o GC não conseguir recuperar memória suficiente, pode ocorrer `OutOfMemoryError: Java heap space`.

<a id="memory-stack"></a>
## 2.2 Stack

- Cada thread possui sua própria Stack.
- Contém os stack frames das chamadas de métodos.
- Está relacionada a variáveis locais, parâmetros e referências locais.
- Os frames são removidos conforme os métodos retornam.
- Pode ocorrer:

```text
StackOverflowError
```

normalmente por profundidade excessiva de chamadas, como recursão infinita.

### Resposta de entrevista

> Cada thread possui sua própria Stack, que acompanha a execução dos métodos por meio de stack frames. Uma profundidade excessiva de chamadas, como em uma recursão infinita, pode provocar `StackOverflowError`.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-reachability-gc-roots"></a>
# 3. Reachability e GC Roots

O GC determina a vida de um objeto pela **alcançabilidade**.

<a id="memory-gc-roots"></a>
## 3.1 GC Roots

Exemplos de raízes incluem referências originadas de:

- threads ativas e seus frames;
- referências estáticas;
- estruturas internas da JVM;
- JNI/native roots.

<a id="memory-regra-alcancabilidade"></a>
## 3.2 Regra de Alcançabilidade

```text
GC Root
   │
   ▼
Objeto A
   │
   ▼
Objeto B
```

Se existe um caminho de referências fortes:

```text
GC Root → A → B
```

os objetos continuam alcançáveis.

Se esse caminho deixa de existir:

```text
GC Roots ──X──> Objeto
```

o objeto fica **elegível para coleta**.

### Resposta de entrevista

> O Garbage Collector determina a vida de um objeto através de reachability. Se existe um caminho de referências fortes partindo de algum GC Root até o objeto, ele continua vivo. Quando esse caminho deixa de existir, o objeto fica elegível para coleta.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-young-old"></a>
# 4. Young e Old Generation

Coletores geracionais exploram a hipótese de que muitos objetos possuem vida curta.

<a id="memory-fluxo-geracional"></a>
## 4.1 Fluxo Geracional

```text
Alocação
   ↓
Eden
   ↓
Young GC
   ↓
Survivor
   ↓
sobrevive a várias coletas
   ↓
Old Generation
```

Fluxo simplificado:

1. O objeto normalmente é alocado na Eden, dentro da Young Generation.
2. Durante uma coleta da Young, objetos não alcançáveis podem ser recuperados.
3. Objetos sobreviventes podem ir para áreas Survivor.
4. Após sobreviverem a coletas suficientes, podem ser promovidos para a Old Generation.

> O fluxo é uma simplificação conceitual; detalhes variam conforme o coletor.

### Resposta de entrevista

> Coletores geracionais dividem logicamente a Heap conforme a idade dos objetos. Objetos normalmente começam na Young Generation e, quando sobrevivem a várias coletas, podem ser promovidos para a Old Generation. Essa estratégia é eficiente porque grande parte dos objetos de aplicações Java possui vida curta.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-garbage-collector"></a>
# 5. Garbage Collector

O Garbage Collector gerencia automaticamente a memória da Heap e recupera espaço ocupado por objetos que deixaram de ser alcançáveis.

Pontos importantes:

- GC não é um único algoritmo.
- A JVM pode usar diferentes coletores.
- O objeto não é necessariamente coletado imediatamente ao deixar de ser usado.
- Diferentes coletores equilibram latência, throughput e consumo de recursos de formas diferentes.

### Resposta de entrevista

> O Garbage Collector gerencia a memória da Heap. Ele identifica objetos que deixaram de ser alcançáveis a partir dos GC Roots e recupera essa memória. Coletores como G1 e ZGC utilizam estratégias diferentes para equilibrar throughput, latência e consumo de recursos.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-g1-gc"></a>
# 6. G1 GC

O G1 — Garbage First — organiza a Heap em **regiões** e procura equilibrar throughput e metas de pausa.

<a id="memory-young-collection"></a>
## 6.1 Young Collection

Coleta regiões da Young Generation.

```text
Eden + Survivor
      ↓
Young GC
```

<a id="memory-mixed-collection"></a>
## 6.2 Mixed Collection

Coleta:

```text
Young
  +
regiões selecionadas da Old
```

Isso permite recuperar parte da Old sem necessariamente executar um Full GC.

> **Mixed Collection não é a mesma coisa que Full GC.**

<a id="memory-full-gc"></a>
## 6.3 Full GC

É um mecanismo de fallback que envolve a Heap inteira e pode gerar uma pausa Stop-The-World significativamente maior.

### Resposta de entrevista

> O G1 divide a Heap em regiões e tenta controlar as pausas escolhendo quais regiões coletar. Ele possui Young Collections e Mixed Collections, que também podem recuperar regiões selecionadas da Old. Full GC é diferente: envolve a Heap inteira e tende a produzir uma pausa muito mais relevante.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-g1-vs-zgc"></a>
# 7. G1 vs ZGC

<a id="memory-g1"></a>
## 7.1 G1

Objetivo:

```text
equilíbrio
Throughput ←→ Latência
```

Características:

- Heap organizada em regiões;
- parte do trabalho ocorre concorrentemente;
- possui fases Stop-The-World;
- trabalha com metas de pausa;
- boa relação entre throughput e latência para muitos workloads.

<a id="memory-zgc"></a>
## 7.2 ZGC

Objetivo:

```text
baixa latência
```

Características:

- executa grande parte do trabalho concorrentemente;
- busca manter pausas muito pequenas;
- no Java moderno é geracional;
- pode trocar parte do throughput ou recursos por menor latência.

### Resposta de entrevista

> O G1 procura equilibrar throughput e metas de pausa. Já o ZGC prioriza baixa latência e realiza uma parcela maior do trabalho concorrentemente. O trade-off deve ser medido na carga real, observando CPU, memória, throughput e os SLOs de latência.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-trade-offs"></a>
# 8. Trade-offs de Heap e GC

<a id="memory-xmx"></a>
## 8.1 Xmx

`-Xmx` define o limite máximo da Java Heap.

### Aumentar Xmx

Vantagens:

- mais espaço para objetos vivos;
- mais espaço para alocação entre coletas;
- pode reduzir pressão de GC.

Cuidados:

- aumenta o limite de memória do processo;
- não resolve retenção indevida;
- pode apenas postergar um memory leak.

### Diminuir Xmx

Vantagem:

- restringe o consumo da Heap.

Trade-offs:

- aumenta a pressão sobre o GC;
- pode elevar a frequência das coletas;
- aumenta o risco de falta de memória.

<a id="memory-escolha-coletor"></a>
## 8.2 Escolha do Coletor

### G1

Boa opção quando se busca equilíbrio entre throughput e pausas.

### ZGC

Faz sentido quando há requisito rigoroso de latência, mas deve-se medir:

- CPU;
- memória;
- throughput;
- comportamento real da aplicação.

### Resposta de entrevista

> Aumentar a Heap pode reduzir pressão de GC quando a aplicação realmente precisa de mais espaço, mas não resolve retenção indevida. Da mesma forma, mudar para ZGC não deve ser uma decisão automática: eu escolheria o coletor com base em metas de latência, throughput e medições da carga real.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-memoria-processo"></a>
# 9. Memória Fora da Heap

A Heap não representa toda a memória consumida pelo processo JVM.

```text
Memória do processo JVM
│
├── Heap
├── Metaspace
├── Thread Stacks
├── Code Cache
├── Direct / Native Buffers
└── outras estruturas nativas
```

Portanto:

```text
processo usando muita RAM
          ≠
Heap necessariamente grande
```

### Resposta de entrevista

> Eu não atribuiria automaticamente um aumento de memória do processo à Heap. Se a Heap permanece estável, investigaria Metaspace, Direct Buffers, stacks das threads, quantidade de threads e outras áreas nativas da JVM.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-heap-vs-thread-dump"></a>
# 10. Heap Dump vs Thread Dump

| Ferramenta | Mostra | Principal pergunta |
|---|---|---|
| Heap Dump | Objetos e relações de referência | O que ocupa memória e quem mantém objetos vivos? |
| Thread Dump | Threads, estados e stacks | Onde as threads estão executando, esperando ou bloqueadas? |

<a id="memory-heap-dump"></a>
## 10.1 Heap Dump

Útil para investigar:

- uso excessivo de Heap;
- retenção de objetos;
- memory leaks;
- retained size;
- caminhos até GC Roots.

Perguntas:

```text
Quais objetos consomem mais memória?
Quem os mantém vivos?
Qual caminho até o GC Root?
```

### Resposta de entrevista

> Heap Dump mostra os objetos presentes na Heap e suas relações de referência. Eu o utilizaria para descobrir quais objetos estão retendo memória e qual caminho de referências até um GC Root impede que sejam coletados.

<a id="memory-thread-dump"></a>
## 10.2 Thread Dump

Mostra:

- threads existentes;
- estados;
- stack traces;
- bloqueios;
- espera;
- possíveis deadlocks;
- pontos de contenção.

### Resposta de entrevista

> Thread Dump mostra o estado das threads e suas stacks naquele instante. É útil para investigar bloqueios, deadlocks, contenção ou entender onde as threads estão gastando tempo.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-producao"></a>
# 11. Investigação em Produção

Ordem geral:

```text
Métricas / GC Logs
        ↓
Identificar padrão
        ↓
Heap Dump / Thread Dump
        ↓
Encontrar causa
        ↓
Ajustar JVM/GC somente com evidência
```

<a id="memory-metricas-gc"></a>
## 11.1 Métricas e GC Logs

Observo:

- frequência de coletas;
- duração das pausas;
- Heap utilizada;
- Heap utilizada após GC;
- taxa de alocação;
- CPU;
- comportamento da Old Generation;
- p95 / p99.

<a id="memory-heap-crescendo"></a>
## 11.2 Heap após GC continua crescendo

Padrão:

```text
GC → Heap cai pouco
GC → baseline cresce
GC → baseline continua crescendo
```

Isso sugere retenção crescente.

Ação:

> Coletar Heap Dump e investigar retained size, dominators e caminhos até GC Roots.

<a id="memory-processo-cresce"></a>
## 11.3 Processo cresce, mas Heap estável

Investigo antes de culpar o GC:

- Metaspace;
- Direct Buffers;
- memória nativa;
- quantidade de threads;
- tamanho das stacks;
- bibliotecas nativas.

<a id="memory-latencia-aumenta"></a>
## 11.4 Heap estável, mas latência aumenta

Se:

```text
Heap OK
GC OK
p99 ↑
```

investigo execução das threads com Thread Dumps/JFR procurando:

- bloqueios;
- contenção;
- I/O;
- saturação de pools;
- deadlocks.

<a id="memory-tuning"></a>
## 11.5 Tuning

Somente depois do diagnóstico considero:

- `Xmx`;
- `Xms`;
- G1;
- ZGC;
- metas de pausa;
- arquitetura da aplicação.

> Tuning deve ser consequência do diagnóstico, não substituto para ele.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-resposta-completa"></a>
# 12. Resposta Completa de Entrevista

> A JVM utiliza diferentes áreas de memória. A Heap é compartilhada entre as threads e armazena objetos e arrays, enquanto cada thread possui sua própria Stack para acompanhar a execução dos métodos.
>
> Na Heap, coletores geracionais trabalham com Young e Old Generation. Objetos normalmente começam na Young e, quando sobrevivem a várias coletas, podem ser promovidos para a Old.
>
> O Garbage Collector determina quais objetos podem ser coletados através de reachability a partir dos GC Roots. Se não existe mais um caminho de referências fortes até determinado objeto, ele fica elegível para coleta.
>
> Em relação aos coletores, o G1 busca equilibrar throughput e metas de pausa, enquanto o ZGC prioriza baixa latência realizando uma parcela maior do trabalho concorrentemente.
>
> Em produção, eu começo analisando métricas e GC logs. Se a ocupação da Heap após GC continua crescendo, parto para Heap Dump para investigar retenção e caminhos até GC Roots. Se a Heap está estável, mas há problema de latência, utilizo Thread Dumps ou JFR para investigar bloqueios, contenção ou saturação. Só depois considero tuning de Heap ou mudança de coletor.

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="memory-mapa-mental"></a>
# 13. Mapa Mental

```text
JVM MEMORY & GC
│
├── Heap
│   ├── objetos
│   ├── arrays
│   └── compartilhada
│
├── Stack
│   ├── uma por thread
│   └── stack frames
│
├── Reachability
│   ├── GC Roots
│   └── objeto não alcançável → elegível para GC
│
├── Generational
│   ├── Eden
│   ├── Survivor
│   └── Old
│
├── G1
│   ├── Young Collection
│   ├── Mixed Collection
│   └── Full GC
│
├── ZGC
│   └── foco em baixa latência
│
├── Diagnóstico
│   ├── GC logs
│   ├── Heap Dump
│   ├── Thread Dump
│   └── JFR
│
└── Tuning
    ├── Xmx / Xms
    └── coletor
```

[↑ Sumário do tópico](#memory-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Concorrência em Java

<a id="concorrencia-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#concorrencia-visao-geral)
- [2. Conceitos Fundamentais](#concorrencia-conceitos-fundamentais)
  - [2.1 Thread](#concorrencia-thread)
  - [2.2 Runnable](#concorrencia-runnable)
  - [2.3 Callable](#concorrencia-callable)
  - [2.4 Future](#concorrencia-future)
  - [2.5 CompletableFuture](#concorrencia-completablefuture)
- [3. Problemas de Concorrência](#concorrencia-problemas-concorrencia)
  - [3.1 Race Condition](#concorrencia-race-condition)
  - [3.2 Deadlock](#concorrencia-deadlock)
  - [3.3 Livelock](#concorrencia-livelock)
  - [3.4 Contention](#concorrencia-contention)
- [4. Java Memory Model](#concorrencia-jmm)
  - [4.1 Visibilidade](#concorrencia-visibilidade)
  - [4.2 Happens-before](#concorrencia-happens-before)
- [5. Sincronização e Coordenação](#concorrencia-sincronizacao)
  - [5.1 synchronized](#concorrencia-synchronized)
  - [5.2 volatile](#concorrencia-volatile)
  - [5.3 ReentrantLock](#concorrencia-reentrantlock)
  - [5.4 AtomicInteger](#concorrencia-atomicinteger)
  - [5.5 ConcurrentHashMap](#concorrencia-concurrenthashmap)
- [6. Virtual Threads](#concorrencia-virtual-threads)
  - [6.1 Funcionamento](#concorrencia-virtual-funcionamento)
  - [6.2 CPU-bound vs I/O-bound](#concorrencia-cpu-io)
  - [6.3 Carrier Thread](#concorrencia-carrier)
  - [6.4 Pinning: Java 21 vs Java 25](#concorrencia-pinning)
  - [6.5 Trade-offs](#concorrencia-virtual-tradeoffs)
- [7. Diagnóstico em Produção](#concorrencia-producao)
- [8. Resposta Completa de Entrevista](#concorrencia-resposta-entrevista)
- [9. Mapa Mental](#concorrencia-mapa-mental)

---

<a id="concorrencia-visao-geral"></a>
# 1. Visão Geral

Concorrência é a capacidade de uma aplicação lidar com múltiplas tarefas progredindo durante o mesmo período.

```text
Concorrência
    └── múltiplas tarefas em progresso

Paralelismo
    └── múltiplas tarefas executando simultaneamente
        em diferentes CPUs/cores
```

Principais peças no Java:

```text
Threads
├── Platform Threads
└── Virtual Threads

Tasks
├── Runnable
└── Callable

Execução
└── ExecutorService

Resultado assíncrono
├── Future
└── CompletableFuture

Sincronização
├── synchronized
├── Lock
├── volatile
├── Atomic*
└── Concurrent Collections
```

### Resposta de entrevista

> Concorrência em Java é a execução coordenada de múltiplas tarefas durante o mesmo período. O Java fornece abstrações como Threads, ExecutorService, locks, atomics e coleções concorrentes. O principal desafio é coordenar acesso a estado compartilhado garantindo atomicidade, visibilidade e ordenação sem gerar contenção excessiva.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-conceitos-fundamentais"></a>
# 2. Conceitos Fundamentais

<a id="concorrencia-thread"></a>
## 2.1 Thread

Uma `Thread` representa um fluxo de execução.

```text
Platform Thread
      ↓
associada a uma OS Thread

Virtual Thread
      ↓
gerenciada pela JVM
executada sobre carrier threads
```

Platform Threads possuem custo relevante de stack, memória, escalonamento pelo SO e context switching.

### Resposta de entrevista

> Uma Thread representa um fluxo de execução. Platform Threads são relativamente caras porque dependem de threads do sistema operacional, enquanto Virtual Threads são muito mais leves e gerenciadas pela JVM.

<a id="concorrencia-runnable"></a>
## 2.2 Runnable

Representa uma tarefa que executa código e não retorna resultado.

```java
Runnable task = () -> {
    System.out.println("Processando");
};
```

Assinatura:

```java
void run();
```

Não permite declarar checked exceptions diretamente.

### Resposta de entrevista

> Runnable representa uma tarefa sem retorno. É adequada quando eu quero executar uma ação concorrentemente, mas não preciso receber um resultado da operação.

<a id="concorrencia-callable"></a>
## 2.3 Callable

Representa uma tarefa que retorna resultado e pode lançar exceptions.

```java
Callable<Integer> task = () -> 10;
```

Assinatura:

```java
V call() throws Exception;
```

Quando submetido a `ExecutorService`, normalmente retorna `Future<V>`.

### Resposta de entrevista

> Callable é semelhante ao Runnable, mas permite retornar um resultado e lançar exceptions. Quando submetido a um ExecutorService, normalmente recebo um Future representando o resultado futuro.

<a id="concorrencia-future"></a>
## 2.4 Future

Representa o resultado de uma computação assíncrona.

```java
Future<Integer> future = executor.submit(task);
Integer resultado = future.get();
```

`get()` pode bloquear a thread atual até o resultado estar disponível.

Outras operações:

```java
future.isDone();
future.cancel(true);
```

### Resposta de entrevista

> Future representa o resultado de uma operação assíncrona. O principal cuidado é que `get()` pode bloquear até a operação terminar.

<a id="concorrencia-completablefuture"></a>
## 2.5 CompletableFuture

Permite compor pipelines assíncronos.

```java
CompletableFuture
    .supplyAsync(() -> buscarCliente())
    .thenApply(cliente -> calcularLimite(cliente))
    .thenAccept(System.out::println);
```

Operações comuns:

```text
thenApply       → transforma resultado
thenAccept      → consome resultado
thenCompose     → encadeia operação assíncrona
thenCombine     → combina operações independentes
exceptionally   → tratamento de falha
```

### Trade-off

Pipelines muito extensos podem ficar difíceis de ler, depurar e observar.

### Resposta de entrevista

> CompletableFuture permite compor operações assíncronas sem bloquear explicitamente a cada etapa. É útil para combinar tarefas independentes, mas fluxos excessivamente complexos podem prejudicar legibilidade e observabilidade.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-problemas-concorrencia"></a>
# 3. Problemas de Concorrência

<a id="concorrencia-race-condition"></a>
## 3.1 Race Condition

O resultado depende da ordem imprevisível em que múltiplas threads acessam estado compartilhado.

Exemplo:

```java
saldo = saldo - valor;
```

Conceitualmente:

```text
ler saldo
   ↓
calcular novo saldo
   ↓
escrever saldo
```

Duas threads podem intercalar essas etapas.

### Soluções

- `synchronized`;
- `ReentrantLock`;
- atomics;
- estruturas concorrentes;
- imutabilidade;
- evitar estado compartilhado.

### Regra mental

> **Race Condition → proteger ou eliminar estado compartilhado.**

<a id="concorrencia-deadlock"></a>
## 3.2 Deadlock

Threads ficam bloqueadas esperando indefinidamente por recursos umas das outras.

```text
Thread A: segura Lock 1 → espera Lock 2
Thread B: segura Lock 2 → espera Lock 1
```

### Prevenção

- adquirir locks sempre na mesma ordem;
- reduzir locks aninhados;
- diminuir tempo segurando locks;
- usar `tryLock()`/timeout quando apropriado.

### Regra mental

> **Deadlock → ordenar aquisição dos locks.**

<a id="concorrencia-livelock"></a>
## 3.3 Livelock

As threads continuam executando e reagindo umas às outras, mas nenhuma progride.

```text
Thread A → cede
Thread B → cede
Thread A → cede
...
```

### Soluções

- backoff;
- jitter;
- retries limitados;
- evitar políticas que fazem ambas reagirem para sempre.

### Regra mental

> **Deadlock:** ninguém executa.  
> **Livelock:** todos executam, mas ninguém progride.

<a id="concorrencia-contention"></a>
## 3.4 Contention

Várias threads disputam o mesmo recurso.

```text
Thread 1 ─┐
Thread 2 ─┤
Thread 3 ─┼──► LOCK ───► execução serial
Thread 4 ─┤
Thread 5 ─┘
```

Consequências:

- espera;
- queda de throughput;
- aumento de latência;
- redução de escalabilidade.

### Soluções

- reduzir região crítica;
- reduzir tempo segurando lock;
- diminuir compartilhamento;
- locks mais granulares;
- estruturas concorrentes adequadas.

### Regra mental

> **Contention → reduzir disputa pelo recurso.**

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-jmm"></a>
# 4. Java Memory Model

<a id="concorrencia-visibilidade"></a>
## 4.1 Visibilidade

Uma thread modificar uma variável não significa, por si só, que outra thread observará imediatamente a alteração.

O JMM define as garantias de comunicação segura entre threads.

<a id="concorrencia-happens-before"></a>
## 4.2 Happens-before

`happens-before` é uma relação do Java Memory Model que fornece garantias de visibilidade e ordenação.

Exemplos:

```text
volatile write
      ↓ happens-before
volatile read posterior da mesma variável
```

```text
unlock de monitor
      ↓ happens-before
lock posterior do mesmo monitor
```

Também há garantias importantes com:

```java
Thread.start()
Thread.join()
```

### Resposta de entrevista

> Happens-before define quando os efeitos de uma operação são garantidamente visíveis para outra. Por exemplo, liberar um monitor acontece antes de uma aquisição posterior do mesmo monitor, e uma escrita volatile acontece antes de uma leitura posterior da mesma variável.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-sincronizacao"></a>
# 5. Sincronização e Coordenação

<a id="concorrencia-synchronized"></a>
## 5.1 synchronized

`synchronized` utiliza um monitor e fornece:

### Exclusão mútua

Somente uma thread por vez executa a região protegida pelo mesmo monitor.

### Visibilidade

Existe happens-before entre a liberação do monitor e a aquisição posterior do mesmo monitor.

### Trade-off

Alta contenção reduz paralelismo e throughput.

### Resposta de entrevista

> synchronized fornece exclusão mútua e garantia de visibilidade através do monitor. É simples para proteger regiões críticas, mas alta contenção sobre o mesmo monitor pode reduzir paralelismo e throughput.

<a id="concorrencia-volatile"></a>
## 5.2 volatile

Fornece principalmente visibilidade e garantias de ordenação.

```java
private volatile boolean running = true;
```

### Não garante atomicidade composta

```java
volatile int contador;
contador++;
```

continua sendo uma operação composta.

### Bom para

- flags;
- estados simples;
- publicação de valores.

### Resposta de entrevista

> volatile garante visibilidade e ordenação de memória para uma variável, mas não transforma operações compostas em atômicas. Funciona bem para flags e estados simples, mas não substitui locks ou atomics em read-modify-write.

<a id="concorrencia-reentrantlock"></a>
## 5.3 ReentrantLock

Fornece exclusão mútua com maior controle.

```java
lock.lock();
try {
    alterarEstado();
} finally {
    lock.unlock();
}
```

Recursos:

```java
tryLock()
tryLock(timeout)
lockInterruptibly()
Condition
```

### Trade-off

Mais flexível, mas aumenta complexidade e exige liberar o lock explicitamente.

### Resposta de entrevista

> ReentrantLock fornece exclusão mútua como synchronized, mas permite maior controle, como tryLock, timeout, interrupção e Conditions. O trade-off é maior complexidade e a obrigação de liberar explicitamente o lock.

<a id="concorrencia-atomicinteger"></a>
## 5.4 AtomicInteger

Fornece operações atômicas sobre um `int` compartilhado.

```java
AtomicInteger contador = new AtomicInteger();
contador.incrementAndGet();
```

Operações:

```java
incrementAndGet()
compareAndSet()
getAndIncrement()
```

### Trade-off

Ótimo para estado simples, mas não substitui locks quando é preciso manter invariantes envolvendo múltiplas variáveis.

### Resposta de entrevista

> AtomicInteger é adequado para operações atômicas simples sobre um inteiro compartilhado. Evita locking explícito em muitos casos, mas não substitui locks quando preciso manter invariantes envolvendo múltiplos estados.

<a id="concorrencia-concurrenthashmap"></a>
## 5.5 ConcurrentHashMap

`ConcurrentHashMap` é um `Map` thread-safe otimizado para concorrência.

Operações atômicas importantes:

```java
putIfAbsent()
compute()
computeIfAbsent()
merge()
```

Evite:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Prefira:

```java
map.putIfAbsent(key, value);
```

### Resposta de entrevista

> ConcurrentHashMap é um Map thread-safe otimizado para concorrência e fornece operações compostas atômicas como putIfAbsent, compute e merge.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-virtual-threads"></a>
# 6. Virtual Threads

Virtual Threads são threads leves gerenciadas pela JVM, adequadas para grande quantidade de tarefas concorrentes principalmente em workloads I/O-bound.

<a id="concorrencia-virtual-funcionamento"></a>
## 6.1 Funcionamento

```text
Virtual Thread
      ↓ mount
Carrier Thread
      ↓
CPU
```

Quando realiza uma operação bloqueante suportada:

```text
Virtual Thread
      ↓
bloqueia em I/O
      ↓
unmount
      ↓
Carrier fica disponível
```

Isso permite manter muitas tarefas concorrentes com um número muito menor de Platform Threads.

<a id="concorrencia-cpu-io"></a>
## 6.2 CPU-bound vs I/O-bound

Virtual Threads ajudam principalmente em:

- HTTP;
- banco de dados;
- arquivos;
- APIs externas;
- outras operações bloqueantes de I/O.

Não tornam tarefas CPU-bound mais rápidas.

```text
100.000 Virtual Threads
         ≠
100.000 operações CPU-bound simultâneas
```

O paralelismo de CPU continua limitado pelos cores disponíveis.

<a id="concorrencia-carrier"></a>
## 6.3 Carrier Thread

Carrier Thread é uma Platform Thread usada pela JVM para executar Virtual Threads.

```text
Virtual Thread
      ↓
Carrier Thread
      ↓
OS Thread
```

A aplicação não deve depender de qual carrier executa determinada Virtual Thread.

<a id="concorrencia-pinning"></a>
## 6.4 Pinning: Java 21 vs Java 25

### Java 21

Bloquear dentro de:

```java
synchronized (lock) {
    chamadaBloqueante();
}
```

podia manter uma Virtual Thread presa à carrier.

### Java 24+

O runtime foi alterado para que bloqueios em `synchronized` normalmente não causem mais esse tipo de pinning.

No Java 25, pinning ainda pode ocorrer principalmente em cenários envolvendo código `native` ou Foreign Function.

### Resposta de entrevista

> No Java 21, uma Virtual Thread podia ficar presa à carrier ao bloquear dentro de synchronized. A partir do JDK 24 esse caso foi resolvido. No Java 25, synchronized normalmente não causa mais esse pinning, embora ele ainda possa ocorrer em chamadas native ou foreign.

<a id="concorrencia-virtual-tradeoffs"></a>
## 6.5 Trade-offs

Virtual Threads tornam barato ter muitas tarefas concorrentes, mas não removem limites externos.

```text
100.000 Virtual Threads
         ↓
pool JDBC com 30 conexões
         ↓
~30 operações simultâneas no banco
```

O recurso externo continua sendo o gargalo.

Ferramentas de controle:

- Semaphore;
- Rate Limiter;
- pool de conexões;
- backpressure.

### Resposta de entrevista

> Virtual Threads aumentam principalmente a escalabilidade de workloads I/O-bound. Elas não tornam CPU-bound mais rápido e não removem limites externos. Se meu banco possui 30 conexões, criar milhares de Virtual Threads não aumenta esse limite.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-producao"></a>
# 7. Diagnóstico em Produção

Comece pelo sintoma.

```text
CPU alta
     ↓
threads executando / loops / contention

Latência alta
     ↓
Thread Dump / JFR / pools / locks

Threads BLOCKED
     ↓
lock contention

Aplicação travada
     ↓
deadlock

Fila de Executor crescendo
     ↓
executor saturado ou dependência lenta

Muitas Virtual Threads esperando
     ↓
DB / HTTP / semaphore / rate limit
```

Ferramentas relevantes:

- Thread Dump;
- JFR;
- `jcmd`;
- métricas da aplicação;
- métricas de pools;
- CPU;
- p95 / p99.

### Resposta de entrevista

> Em produção eu começo observando o sintoma e as métricas. Para suspeita de bloqueio ou contenção, utilizo Thread Dumps ou JFR para identificar estados das threads, locks e stacks. Também verifico pools e dependências externas antes de atribuir o problema simplesmente ao número de threads.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-resposta-entrevista"></a>
# 8. Resposta Completa de Entrevista

> Concorrência em Java envolve executar e coordenar múltiplas tarefas durante o mesmo período. Para representar tarefas eu posso utilizar Runnable ou Callable e normalmente delego sua execução para abstrações como ExecutorService. Future representa um resultado assíncrono e CompletableFuture permite compor pipelines assíncronos.
>
> Quando existe estado compartilhado, os principais problemas são race condition, deadlock, livelock e contention. Para coordenar esse acesso posso usar synchronized, ReentrantLock, atomics ou estruturas como ConcurrentHashMap, escolhendo de acordo com a granularidade e as invariantes que preciso proteger.
>
> Também é importante entender o Java Memory Model. Happens-before estabelece garantias de visibilidade e ordenação entre threads, como ocorre na liberação e aquisição de um monitor ou em escrita e leitura de volatile.
>
> No Java moderno temos Virtual Threads, que permitem trabalhar com um número muito maior de tarefas concorrentes principalmente em workloads I/O-bound. Elas não tornam processamento CPU-bound mais rápido e não eliminam limites externos, como pools de conexão.
>
> Em produção eu observo métricas, Thread Dumps e JFR para identificar contention, bloqueios, deadlocks, saturação de executores ou dependências externas antes de decidir qualquer ajuste.

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="concorrencia-mapa-mental"></a>
# 9. Mapa Mental

```text
CONCORRÊNCIA
│
├── Execução
│   ├── Thread
│   ├── Runnable
│   ├── Callable
│   ├── Future
│   └── CompletableFuture
│
├── Problemas
│   ├── Race Condition → proteger estado
│   ├── Deadlock       → ordenar locks
│   ├── Livelock       → backoff / jitter
│   └── Contention     → reduzir disputa
│
├── JMM
│   ├── Visibilidade
│   └── Happens-before
│
├── Sincronização
│   ├── synchronized
│   ├── volatile
│   ├── ReentrantLock
│   ├── AtomicInteger
│   └── ConcurrentHashMap
│
└── Virtual Threads
    ├── leves
    ├── I/O-bound
    ├── mount / unmount
    ├── carrier thread
    ├── não aceleram CPU-bound
    └── não removem limites externos
```

[↑ Sumário do tópico](#concorrencia-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Java Collections

<a id="collections-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#collections-visao-geral)
- [2. Hierarquia Principal](#collections-hierarquia)
  - [2.1 Collection](#collections-collection)
  - [2.2 List](#collections-list)
  - [2.3 Set](#collections-set)
  - [2.4 Queue](#collections-queue)
  - [2.5 Deque](#collections-deque)
  - [2.6 Map](#collections-map)
- [3. List](#collections-list-detalhes)
  - [3.1 ArrayList](#collections-arraylist)
  - [3.2 LinkedList](#collections-linkedlist)
  - [3.3 ArrayList vs LinkedList](#collections-arraylist-vs-linkedlist)
- [4. Set](#collections-set-detalhes)
  - [4.1 HashSet](#collections-hashset)
  - [4.2 LinkedHashSet](#collections-linkedhashset)
  - [4.3 TreeSet](#collections-treeset)
  - [4.4 HashSet vs LinkedHashSet vs TreeSet](#collections-comparacao-set)
- [5. Map](#collections-map-detalhes)
  - [5.1 HashMap](#collections-hashmap)
  - [5.2 LinkedHashMap](#collections-linkedhashmap)
  - [5.3 TreeMap](#collections-treemap)
  - [5.4 HashMap vs LinkedHashMap vs TreeMap](#collections-comparacao-map)
- [6. Queue e Deque](#collections-queue-deque)
  - [6.1 PriorityQueue](#collections-priorityqueue)
  - [6.2 ArrayDeque](#collections-arraydeque)
- [7. Hashing: equals e hashCode](#collections-equals-hashcode)
  - [7.1 Contrato de equals](#collections-equals)
  - [7.2 Contrato de hashCode](#collections-hashcode)
  - [7.3 Colisões](#collections-colisoes)
- [8. Internals do HashMap](#collections-hashmap-internals)
  - [8.1 Buckets](#collections-buckets)
  - [8.2 Load Factor](#collections-load-factor)
  - [8.3 Resize](#collections-resize)
  - [8.4 Treeification](#collections-treeification)
- [9. Ordenação](#collections-ordenacao)
  - [9.1 Comparable](#collections-comparable)
  - [9.2 Comparator](#collections-comparator)
- [10. Iteração](#collections-iteracao)
  - [10.1 Iterator](#collections-iterator)
  - [10.2 Fail-fast](#collections-fail-fast)
  - [10.3 Remoção segura](#collections-remocao-segura)
- [11. Imutabilidade e Views](#collections-imutabilidade)
  - [11.1 Collections.unmodifiableList](#collections-unmodifiable)
  - [11.2 List.of / Set.of / Map.of](#collections-factory-methods)
  - [11.3 Arrays.asList](#collections-arrays-as-list)
- [12. Collections Concorrentes](#collections-collections-concorrentes)
  - [12.1 ConcurrentHashMap](#collections-concurrenthashmap)
  - [12.2 CopyOnWriteArrayList](#collections-copyonwritearraylist)
  - [12.3 BlockingQueue](#collections-blockingqueue)
- [13. Complexidade das Principais Estruturas](#collections-complexidade)
- [14. Como escolher a Collection](#collections-escolha)
- [15. Armadilhas comuns de entrevista](#collections-armadilhas)
- [16. Cenários de Produção](#collections-producao)
- [17. Resposta Completa de Entrevista](#collections-resposta-completa)
- [18. Mapa Mental](#collections-mapa-mental)

---

<a id="collections-visao-geral"></a>
# 1. Visão Geral

O **Java Collections Framework** fornece interfaces e implementações para armazenar, organizar e manipular grupos de objetos.

Os principais grupos são:

```text
Collection
│
├── List
├── Set
└── Queue
    └── Deque

Map
```

> **Importante:** `Map` faz parte do Collections Framework, mas **não herda de `Collection`**.

A escolha da estrutura deve considerar principalmente:

- necessidade de ordem;
- duplicidade;
- busca por chave;
- custo de inserção e remoção;
- acesso por índice;
- ordenação;
- concorrência;
- volume de dados.

### Resposta de entrevista

> O Java Collections Framework fornece estruturas de dados padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map. A escolha entre elas depende de requisitos como ordem, duplicidade, acesso por índice, busca por chave, ordenação e concorrência.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-hierarquia"></a>
# 2. Hierarquia Principal

```text
Iterable
   │
Collection
   │
   ├── List
   │    ├── ArrayList
   │    └── LinkedList
   │
   ├── Set
   │    ├── HashSet
   │    ├── LinkedHashSet
   │    └── SortedSet
   │         └── NavigableSet
   │              └── TreeSet
   │
   └── Queue
        ├── PriorityQueue
        └── Deque
             ├── ArrayDeque
             └── LinkedList

Map
├── HashMap
├── LinkedHashMap
└── SortedMap
     └── NavigableMap
          └── TreeMap
```

<a id="collections-collection"></a>
## 2.1 Collection

`Collection<E>` representa um grupo de elementos.

Operações comuns:

```java
add()
remove()
contains()
size()
isEmpty()
clear()
iterator()
```

---

<a id="collections-list"></a>
## 2.2 List

Características:

- mantém sequência;
- permite elementos duplicados;
- permite acesso por índice;
- normalmente preserva ordem de inserção.

Exemplos:

```java
ArrayList
LinkedList
```

---

<a id="collections-set"></a>
## 2.3 Set

Características:

- não permite elementos duplicados segundo `equals`;
- não fornece acesso por índice.

Exemplos:

```java
HashSet
LinkedHashSet
TreeSet
```

---

<a id="collections-queue"></a>
## 2.4 Queue

Representa estruturas orientadas ao processamento de elementos.

Operações importantes:

```java
offer()
poll()
peek()
```

Em geral, é preferível usar essas operações às alternativas que lançam exceção:

| Retorna valor especial | Lança exceção |
|---|---|
| `offer()` | `add()` |
| `poll()` | `remove()` |
| `peek()` | `element()` |

---

<a id="collections-deque"></a>
## 2.5 Deque

`Deque` significa **Double Ended Queue**.

Permite inserir e remover dos dois lados:

```java
addFirst()
addLast()
removeFirst()
removeLast()
peekFirst()
peekLast()
```

Pode funcionar como:

```text
Queue
ou
Stack
```

---

<a id="collections-map"></a>
## 2.6 Map

Armazena pares:

```text
chave → valor
```

Exemplo:

```java
Map<Long, Cliente> clientes;
```

A chave é utilizada para localizar o valor.

Características:

- chaves são únicas;
- valores podem ser repetidos;
- cada implementação possui garantias diferentes de ordem e complexidade.

### Resposta de entrevista

> List representa uma sequência e permite duplicados. Set representa elementos únicos. Queue e Deque são voltadas ao processamento ordenado de elementos. Map representa associações chave-valor e não herda de Collection.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-list-detalhes"></a>
# 3. List

<a id="collections-arraylist"></a>
## 3.1 ArrayList

`ArrayList` utiliza internamente um **array redimensionável**.

```java
List<String> nomes = new ArrayList<>();
```

### Vantagens

- acesso por índice rápido;
- boa localidade de memória;
- iteração eficiente;
- normalmente é a implementação padrão de `List`.

### Complexidade típica

```text
get(index)          → O(1)
add(final)          → O(1) amortizado
contains            → O(n)
remove(final)       → O(1)
insert/remover meio → O(n)
```

Inserções ou remoções no meio exigem deslocamento de elementos.

### Resposta de entrevista

> ArrayList é baseada em array dinâmico. Possui acesso por índice em O(1) e inserção no final em O(1) amortizado. Inserções e remoções no meio são O(n) porque podem exigir deslocamento dos elementos.

---

<a id="collections-linkedlist"></a>
## 3.2 LinkedList

`LinkedList` é uma **lista duplamente encadeada**.

Cada nó mantém referências para:

```text
anterior ← nó → próximo
```

Também implementa `Deque`.

### Complexidade típica

```text
acesso por índice → O(n)
inserção nas pontas → O(1)
remoção nas pontas  → O(1)
```

### Trade-off

Cada elemento possui overhead adicional devido às referências entre os nós.

Além disso, possui pior localidade de memória que um `ArrayList`.

### Resposta de entrevista

> LinkedList é uma lista duplamente encadeada. É eficiente para operações nas extremidades, mas acesso por índice é O(n). Na prática, ArrayList costuma ser preferível para listas gerais por ter melhor localidade de memória e menor overhead.

---

<a id="collections-arraylist-vs-linkedlist"></a>
## 3.3 ArrayList vs LinkedList

| Característica | ArrayList | LinkedList |
|---|---|---|
| Estrutura | Array dinâmico | Lista duplamente encadeada |
| `get(index)` | O(1) | O(n) |
| Inserir no final | O(1) amortizado | O(1) |
| Inserir no início | O(n) | O(1) |
| Memória | Menor overhead | Maior overhead |
| Cache locality | Melhor | Pior |
| Implementa Deque | Não | Sim |

### Regra prática

> Para uma `List` comum, comece considerando `ArrayList`. Use `LinkedList` quando as características de deque/lista encadeada realmente forem relevantes.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-set-detalhes"></a>
# 4. Set

<a id="collections-hashset"></a>
## 4.1 HashSet

`HashSet` armazena elementos únicos utilizando hashing.

Internamente é baseado em uma estrutura equivalente a um `HashMap`.

Características:

- não garante ordem de iteração;
- busca, inserção e remoção normalmente O(1);
- depende corretamente de `equals()` e `hashCode()`.

```java
Set<String> ids = new HashSet<>();
```

### Resposta de entrevista

> HashSet é indicado quando preciso garantir unicidade e não preciso de ordenação. Em condições normais, add, contains e remove possuem custo próximo de O(1).

---

<a id="collections-linkedhashset"></a>
## 4.2 LinkedHashSet

Mantém a unicidade do `Set`, mas também preserva a **ordem de inserção**.

```java
Set<String> nomes = new LinkedHashSet<>();
```

### Trade-off

Possui overhead adicional para manter a ordem.

---

<a id="collections-treeset"></a>
## 4.3 TreeSet

Mantém os elementos **ordenados**.

Baseia-se em uma árvore balanceada.

Complexidade típica:

```text
add      → O(log n)
remove   → O(log n)
contains → O(log n)
```

A ordenação pode vir de:

```java
Comparable
```

ou:

```java
Comparator
```

### Resposta de entrevista

> TreeSet mantém os elementos ordenados e oferece operações em O(log n). Eu usaria quando preciso simultaneamente de unicidade e ordenação.

---

<a id="collections-comparacao-set"></a>
## 4.4 HashSet vs LinkedHashSet vs TreeSet

| Estrutura | Duplicados | Ordem | Complexidade típica |
|---|---:|---|---|
| HashSet | Não | Não garantida | O(1) |
| LinkedHashSet | Não | Inserção | O(1) |
| TreeSet | Não | Ordenada | O(log n) |

### Regra mental

```text
unicidade apenas        → HashSet
unicidade + inserção    → LinkedHashSet
unicidade + ordenação   → TreeSet
```

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-map-detalhes"></a>
# 5. Map

<a id="collections-hashmap"></a>
## 5.1 HashMap

`HashMap` armazena associações:

```text
chave → valor
```

```java
Map<Long, Cliente> clientes = new HashMap<>();
```

Características:

- não garante ordem;
- permite uma chave `null`;
- permite valores `null`;
- busca média por chave próxima de O(1);
- depende do contrato de `equals()` e `hashCode()`.

### Resposta de entrevista

> HashMap é uma estrutura chave-valor baseada em hashing. Em condições normais, get e put são O(1) em média. A qualidade de hashCode e o contrato com equals são fundamentais para funcionamento correto e desempenho.

---

<a id="collections-linkedhashmap"></a>
## 5.2 LinkedHashMap

Mantém as características de `HashMap`, adicionando uma estrutura ligada para controlar ordem.

Pode preservar:

- ordem de inserção;
- ordem de acesso, se configurado.

```java
Map<K, V> map = new LinkedHashMap<>();
```

Também pode ser útil como base para estratégias simples de **LRU cache**.

### Trade-off

Consome mais memória que `HashMap` para manter a ordem.

---

<a id="collections-treemap"></a>
## 5.3 TreeMap

Mantém as chaves ordenadas.

Complexidade:

```text
put    → O(log n)
get    → O(log n)
remove → O(log n)
```

Permite operações navegacionais:

```java
firstKey()
lastKey()
floorKey()
ceilingKey()
higherKey()
lowerKey()
```

### Resposta de entrevista

> TreeMap é indicado quando preciso de um mapa cujas chaves permaneçam ordenadas e quando operações de navegação por intervalo são importantes.

---

<a id="collections-comparacao-map"></a>
## 5.4 HashMap vs LinkedHashMap vs TreeMap

| Estrutura | Ordem | Busca típica | Uso comum |
|---|---|---:|---|
| HashMap | Não garantida | O(1) | lookup geral |
| LinkedHashMap | Inserção/acesso | O(1) | ordem previsível / LRU |
| TreeMap | Ordenada | O(log n) | intervalos e navegação |

### Regra mental

```text
lookup rápido            → HashMap
lookup + ordem           → LinkedHashMap
lookup + ordenação       → TreeMap
```

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-queue-deque"></a>
# 6. Queue e Deque

<a id="collections-priorityqueue"></a>
## 6.1 PriorityQueue

`PriorityQueue` organiza elementos por prioridade.

Normalmente utiliza um **heap binário**.

```java
Queue<Integer> fila = new PriorityQueue<>();
```

O elemento retornado por:

```java
peek()
poll()
```

é o de maior prioridade segundo sua ordenação.

### Importante

Iterar sobre uma `PriorityQueue` **não significa receber todos os elementos em ordem de prioridade**.

### Complexidade típica

```text
peek   → O(1)
offer  → O(log n)
poll   → O(log n)
```

### Resposta de entrevista

> PriorityQueue é adequada quando preciso recuperar repetidamente o elemento de maior prioridade. Ela não representa uma lista totalmente ordenada para iteração.

---

<a id="collections-arraydeque"></a>
## 6.2 ArrayDeque

Implementação eficiente de `Deque`.

É normalmente preferível a `Stack` para implementar uma pilha moderna.

Como stack:

```java
Deque<String> stack = new ArrayDeque<>();

stack.push("A");
stack.pop();
stack.peek();
```

Como fila:

```java
queue.offerLast(valor);
queue.pollFirst();
```

### Resposta de entrevista

> ArrayDeque é uma estrutura eficiente para fila ou pilha. Para implementar uma stack moderna em Java, geralmente prefiro ArrayDeque em vez da classe legada Stack.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-equals-hashcode"></a>
# 7. Hashing: equals e hashCode

Esse é um dos pontos mais importantes de Collections em entrevistas.

<a id="collections-equals"></a>
## 7.1 Contrato de equals

`equals()` define **igualdade lógica**.

O contrato inclui:

- reflexivo;
- simétrico;
- transitivo;
- consistente;
- `x.equals(null)` deve retornar `false`.

Exemplo:

```java
cliente1.equals(cliente2)
```

pode representar que dois objetos diferentes em memória representam logicamente o mesmo cliente.

---

<a id="collections-hashcode"></a>
## 7.2 Contrato de hashCode

Regra fundamental:

```text
se a.equals(b) == true
então
a.hashCode() == b.hashCode()
```

O inverso não precisa ser verdadeiro:

```text
mesmo hashCode
≠
necessariamente iguais
```

### Problema clássico

Se sobrescrever:

```java
equals()
```

normalmente também deve sobrescrever:

```java
hashCode()
```

Caso contrário, estruturas como:

```java
HashMap
HashSet
```

podem se comportar incorretamente.

### Resposta de entrevista

> equals define igualdade lógica e hashCode determina a distribuição em estruturas baseadas em hashing. Se dois objetos são iguais segundo equals, obrigatoriamente devem produzir o mesmo hashCode.

---

<a id="collections-colisoes"></a>
## 7.3 Colisões

Colisão ocorre quando objetos diferentes produzem hashes que levam ao mesmo bucket.

```text
Objeto A ─┐
          ├── bucket X
Objeto B ─┘
```

Isso é esperado em estruturas hash.

O `HashMap` precisa então distinguir as chaves utilizando também `equals()`.

### Boa implementação de hashCode

Busca distribuir os objetos adequadamente entre buckets.

Um hashCode ruim pode gerar:

```text
muitas colisões
      ↓
mais comparação
      ↓
queda de desempenho
```

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-hashmap-internals"></a>
# 8. Internals do HashMap

<a id="collections-buckets"></a>
## 8.1 Buckets

Conceitualmente:

```text
hashCode da chave
       ↓
transformação do hash
       ↓
índice do bucket
       ↓
bucket
```

Um bucket pode conter mais de uma entrada devido a colisões.

---

<a id="collections-load-factor"></a>
## 8.2 Load Factor

O `HashMap` possui um **load factor**, cujo valor padrão normalmente é:

```text
0.75
```

Ele define quando a estrutura deve expandir sua capacidade.

Conceitualmente:

```text
threshold = capacity × loadFactor
```

Quando o número de elementos ultrapassa o threshold, pode ocorrer resize.

### Trade-off

Load factor maior:

- menos memória;
- potencialmente mais colisões.

Load factor menor:

- mais memória;
- potencialmente menos colisões.

---

<a id="collections-resize"></a>
## 8.3 Resize

Quando a capacidade não é mais suficiente, o `HashMap` aumenta sua tabela interna.

Resize pode ter custo relevante porque a estrutura precisa reorganizar suas entradas.

### Implicação prática

Se o volume esperado é conhecido, informar uma capacidade inicial adequada pode reduzir resizes desnecessários.

Mas:

> não faça tuning de capacidade sem necessidade ou medição.

---

<a id="collections-treeification"></a>
## 8.4 Treeification

Em implementações modernas do `HashMap`, buckets com muitas colisões podem ser convertidos de estrutura encadeada para uma árvore balanceada.

Conceitualmente:

```text
poucas colisões
     ↓
lista de entradas

muitas colisões
     ↓
árvore balanceada
```

Isso evita degradação extrema da busca.

### Detalhe de entrevista

Os thresholds clássicos do `HashMap` moderno incluem:

```text
TREEIFY_THRESHOLD = 8
UNTREEIFY_THRESHOLD = 6
MIN_TREEIFY_CAPACITY = 64
```

Não é necessário memorizar esses números para usar Collections corretamente, mas eles aparecem em entrevistas mais profundas.

### Resposta de entrevista

> HashMap distribui entradas em buckets utilizando hashCode. Quando existem colisões, equals é usado para distinguir as chaves. Em implementações modernas, buckets excessivamente congestionados podem ser convertidos em árvores para evitar degradação severa.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-ordenacao"></a>
# 9. Ordenação

<a id="collections-comparable"></a>
## 9.1 Comparable

Define a **ordem natural** da própria classe.

```java
class Cliente implements Comparable<Cliente> {

    @Override
    public int compareTo(Cliente outro) {
        return this.nome.compareTo(outro.nome);
    }
}
```

Utilizado quando existe uma ordenação considerada natural para o tipo.

---

<a id="collections-comparator"></a>
## 9.2 Comparator

Define ordenação **externamente** ao objeto.

```java
Comparator<Cliente> porIdade =
        Comparator.comparingInt(Cliente::getIdade);
```

Permite múltiplas estratégias:

```java
Comparator.comparing(Cliente::getNome)
          .thenComparing(Cliente::getIdade);
```

### Comparable vs Comparator

| Comparable | Comparator |
|---|---|
| Implementado pela própria classe | Externo à classe |
| `compareTo()` | `compare()` |
| Ordem natural | Estratégias múltiplas |

### Resposta de entrevista

> Comparable define a ordenação natural do tipo. Comparator define estratégias externas e permite múltiplas formas de ordenação sem modificar a classe.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-iteracao"></a>
# 10. Iteração

<a id="collections-iterator"></a>
## 10.1 Iterator

`Iterator` fornece navegação sequencial:

```java
Iterator<String> iterator = lista.iterator();

while (iterator.hasNext()) {
    String valor = iterator.next();
}
```

---

<a id="collections-fail-fast"></a>
## 10.2 Fail-fast

Muitas collections tradicionais detectam modificações estruturais inesperadas durante iteração.

Exemplo problemático:

```java
for (String item : lista) {
    lista.remove(item);
}
```

Pode ocorrer:

```text
ConcurrentModificationException
```

### Importante

`ConcurrentModificationException` **não significa necessariamente acesso por múltiplas threads**.

Também pode acontecer em código single-thread quando a collection é modificada de forma incompatível durante a iteração.

---

<a id="collections-remocao-segura"></a>
## 10.3 Remoção segura

Com `Iterator`:

```java
Iterator<String> iterator = lista.iterator();

while (iterator.hasNext()) {
    String item = iterator.next();

    if (deveRemover(item)) {
        iterator.remove();
    }
}
```

Ou utilizando:

```java
lista.removeIf(this::deveRemover);
```

### Resposta de entrevista

> Muitas collections utilizam iteradores fail-fast. Modificar diretamente a estrutura durante uma iteração pode gerar ConcurrentModificationException. Para remoção durante iteração posso usar Iterator.remove ou operações apropriadas da própria Collection.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-imutabilidade"></a>
# 11. Imutabilidade e Views

<a id="collections-unmodifiable"></a>
## 11.1 Collections.unmodifiableList

```java
List<String> original = new ArrayList<>();
List<String> view = Collections.unmodifiableList(original);
```

A `view` não permite:

```java
view.add(...)
```

Mas se `original` mudar:

```java
original.add("A");
```

a mudança pode aparecer na `view`.

Portanto:

> `unmodifiable` não significa necessariamente objeto profundamente imutável.

---

<a id="collections-factory-methods"></a>
## 11.2 List.of / Set.of / Map.of

Exemplo:

```java
List<String> nomes = List.of("Ana", "João");
```

A coleção retornada não permite modificações estruturais:

```java
nomes.add("Lucas"); // UnsupportedOperationException
```

Essas factories são úteis para representar coleções fixas.

### Atenção

Elas também possuem restrições específicas, como não aceitar `null`.

---

<a id="collections-arrays-as-list"></a>
## 11.3 Arrays.asList

```java
List<String> lista = Arrays.asList("A", "B", "C");
```

O tamanho fica vinculado ao array.

É possível:

```java
lista.set(0, "X");
```

Mas normalmente não:

```java
lista.add("D");
lista.remove("A");
```

Essas operações geram:

```text
UnsupportedOperationException
```

### Armadilha clássica

```java
Arrays.asList(...)
```

não cria um `ArrayList` redimensionável comum.

### Resposta de entrevista

> Collections.unmodifiableList cria uma visão não modificável da coleção original, enquanto List.of cria uma coleção estruturalmente imutável. Arrays.asList cria uma lista de tamanho fixo ligada ao array original.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-collections-concorrentes"></a>
# 12. Collections Concorrentes

<a id="collections-concurrenthashmap"></a>
## 12.1 ConcurrentHashMap

Mapa thread-safe otimizado para concorrência.

```java
ConcurrentHashMap<String, Cliente> cache =
        new ConcurrentHashMap<>();
```

Operações importantes:

```java
putIfAbsent()
compute()
computeIfAbsent()
merge()
```

Evita serializar todo acesso em um único lock global.

### Importante

Prefira operações atômicas da própria API.

Evite:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Prefira:

```java
map.putIfAbsent(key, value);
```

---

<a id="collections-copyonwritearraylist"></a>
## 12.2 CopyOnWriteArrayList

Em cada modificação relevante, cria uma nova cópia do array interno.

Excelente quando:

```text
muitas leituras
pouquíssimas escritas
```

Ruim quando:

```text
muitas escritas
```

porque copiar o array possui custo relevante.

### Uso comum

- listas de listeners;
- configurações que mudam raramente;
- estruturas read-mostly.

### Resposta de entrevista

> CopyOnWriteArrayList favorece workloads com muitas leituras e poucas escritas. Leituras são simples, mas cada modificação exige copiar a estrutura interna, então não é adequada para alta taxa de escrita.

---

<a id="collections-blockingqueue"></a>
## 12.3 BlockingQueue

Representa uma fila capaz de bloquear produtores ou consumidores.

Exemplo:

```java
BlockingQueue<Evento> queue =
        new ArrayBlockingQueue<>(100);
```

Consumidor:

```java
Evento evento = queue.take();
```

Se estiver vazia:

```text
consumidor espera
```

Produtor:

```java
queue.put(evento);
```

Se estiver cheia:

```text
produtor espera
```

Muito útil em:

```text
Producer / Consumer
```

### Resposta de entrevista

> BlockingQueue é útil para coordenação entre produtores e consumidores, porque fornece operações que podem bloquear quando a fila está vazia ou cheia, ajudando também no controle de backpressure local.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-complexidade"></a>
# 13. Complexidade das Principais Estruturas

| Estrutura | Busca | Inserção | Remoção | Ordenada |
|---|---:|---:|---:|---|
| ArrayList | O(n) | O(1) amortizado no final | O(n) no meio | Não |
| LinkedList | O(n) | O(1) nas pontas | O(1) nas pontas | Não |
| HashSet | O(1) médio | O(1) médio | O(1) médio | Não |
| LinkedHashSet | O(1) médio | O(1) médio | O(1) médio | Inserção |
| TreeSet | O(log n) | O(log n) | O(log n) | Sim |
| HashMap | O(1) médio | O(1) médio | O(1) médio | Não |
| LinkedHashMap | O(1) médio | O(1) médio | O(1) médio | Inserção/acesso |
| TreeMap | O(log n) | O(log n) | O(log n) | Sim |
| PriorityQueue | O(n)* | O(log n) | O(log n)** | Prioridade |

\* Busca arbitrária.  
\** `poll()` para remover o elemento prioritário.

### Observação importante

Big-O não é tudo.

Na prática também importam:

```text
cache locality
alocações
overhead por objeto
concorrência
volume de dados
padrão de acesso
```

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-escolha"></a>
# 14. Como escolher a Collection

Use esta sequência mental:

```text
Preciso chave → valor?
        │
        ├── Sim → Map
        │          │
        │          ├── sem ordem → HashMap
        │          ├── ordem inserção → LinkedHashMap
        │          └── ordenado → TreeMap
        │
        └── Não
            │
            ├── Preciso unicidade?
            │      │
            │      ├── Sim
            │      │    ├── sem ordem → HashSet
            │      │    ├── inserção → LinkedHashSet
            │      │    └── ordenado → TreeSet
            │      │
            │      └── Não
            │           │
            │           ├── acesso por índice → ArrayList
            │           ├── fila/pilha → ArrayDeque
            │           └── prioridade → PriorityQueue
```

### Resposta de entrevista

> Eu escolho a Collection a partir do padrão de acesso e das garantias necessárias. Para lookup por chave geralmente começo com HashMap, para sequência com ArrayList, para unicidade com HashSet, para ordenação com TreeMap ou TreeSet e para fila ou pilha com ArrayDeque. Depois considero concorrência e requisitos específicos.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-armadilhas"></a>
# 15. Armadilhas comuns de entrevista

## `HashMap` é thread-safe?

```text
Não.
```

Para uso concorrente, considere:

```java
ConcurrentHashMap
```

---

## `Map` herda de `Collection`?

```text
Não.
```

Ambos fazem parte do Collections Framework, mas possuem hierarquias diferentes.

---

## `HashSet` usa `HashMap` internamente?

Conceitualmente e na implementação padrão moderna:

```text
Sim.
```

Os elementos do Set são armazenados como chaves do mapa.

---

## `ArrayList` é sempre melhor que `LinkedList`?

Não.

Mas para listas gerais, `ArrayList` costuma ser melhor devido a:

- acesso aleatório;
- menor overhead;
- melhor localidade de memória.

---

## `HashMap` mantém ordem?

```text
Não há garantia de ordem.
```

Se ordem de inserção for necessária:

```java
LinkedHashMap
```

---

## `TreeMap` é mais rápido que `HashMap`?

Não para lookup geral.

```text
HashMap → O(1) médio
TreeMap → O(log n)
```

Use `TreeMap` quando a **ordenação ou navegação por chave** for necessária.

---

## Se dois objetos têm mesmo hashCode, eles são iguais?

```text
Não.
```

Podem ser apenas uma colisão.

Mas:

```text
equals == true
→ hashCode obrigatoriamente igual
```

---

## `ConcurrentModificationException` significa múltiplas threads?

```text
Não necessariamente.
```

Pode ocorrer até em uma única thread por modificação estrutural inadequada durante iteração.

---

## `Collections.unmodifiableList` é imutável?

Não necessariamente.

Ela impede alterações através daquela referência, mas pode refletir mudanças feitas na coleção original.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-producao"></a>
# 16. Cenários de Produção

## Cenário 1 — Lookup frequente por ID

```java
List<Cliente>
```

com busca frequente:

```java
clientes.stream()
        .filter(c -> c.getId().equals(id))
```

Custo aproximado:

```text
O(n)
```

Se o lookup por ID domina o workload:

```java
Map<Long, Cliente>
```

pode ser mais adequado:

```text
O(1) médio
```

---

## Cenário 2 — Remover duplicados

Em vez de:

```java
List
+
contains repetidamente
```

considere:

```java
HashSet
```

se ordem não for requisito.

---

## Cenário 3 — Cache simples com ordem de acesso

`LinkedHashMap` configurado com access order pode ser usado como base para um LRU simples.

Mas em produção:

> para caching real, normalmente prefira bibliotecas especializadas com política de expiração, limite, métricas e concorrência apropriadas.

---

## Cenário 4 — Múltiplas threads acessando HashMap

Não faça simplesmente:

```java
Map<K, V> map = new HashMap<>();
```

se existir mutação concorrente sem coordenação.

Considere:

```java
ConcurrentHashMap
```

e operações atômicas como:

```java
computeIfAbsent()
putIfAbsent()
merge()
```

---

## Cenário 5 — Lista muito lida e raramente alterada

Considere:

```java
CopyOnWriteArrayList
```

Mas somente quando a proporção for claramente:

```text
leituras >>> escritas
```

---

## Cenário 6 — Producer / Consumer

Utilize:

```java
BlockingQueue
```

para coordenação local entre produtores e consumidores.

Isso evita criar loops manuais do tipo:

```java
while (queue.isEmpty()) {
}
```

que desperdiçam CPU.

---

## Cenário 7 — Latência degradando com collection grande

Investigue:

- escolha incorreta da estrutura;
- hashCode ruim;
- excesso de colisões;
- resize excessivo;
- cópias desnecessárias;
- complexidade O(n) escondida;
- ordenação desnecessária;
- contenção concorrente.

### Resposta de entrevista

> Em produção eu não escolho Collection apenas pelo tipo de dado, mas pelo padrão de acesso. Se há lookup frequente por chave, por exemplo, uma List pode gerar O(n) repetidamente enquanto um HashMap oferece O(1) médio. Também considero memória, ordenação, taxa de escrita, concorrência e volume.

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-resposta-completa"></a>
# 17. Resposta Completa de Entrevista

Se perguntarem:

**“O que você entende sobre Collections em Java?”**

Uma resposta objetiva pode ser:

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

[↑ Sumário do tópico](#collections-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="collections-mapa-mental"></a>
# 18. Mapa Mental

```text
JAVA COLLECTIONS
│
├── List
│   ├── ArrayList
│   │    ├── array dinâmico
│   │    ├── get O(1)
│   │    └── inserção meio O(n)
│   │
│   └── LinkedList
│        ├── lista duplamente encadeada
│        ├── acesso O(n)
│        └── também é Deque
│
├── Set
│   ├── HashSet
│   │    └── unicidade + O(1) médio
│   ├── LinkedHashSet
│   │    └── unicidade + ordem inserção
│   └── TreeSet
│        └── unicidade + ordenação O(log n)
│
├── Map
│   ├── HashMap
│   │    └── chave/valor + O(1) médio
│   ├── LinkedHashMap
│   │    └── chave/valor + ordem
│   └── TreeMap
│        └── chave/valor + ordenação O(log n)
│
├── Queue / Deque
│   ├── PriorityQueue
│   │    └── prioridade
│   └── ArrayDeque
│        └── fila ou pilha
│
├── Hashing
│   ├── equals
│   ├── hashCode
│   ├── buckets
│   ├── colisões
│   ├── load factor
│   └── resize
│
├── Ordenação
│   ├── Comparable
│   └── Comparator
│
├── Iteração
│   ├── Iterator
│   ├── fail-fast
│   └── ConcurrentModificationException
│
└── Concorrência
    ├── ConcurrentHashMap
    ├── CopyOnWriteArrayList
    └── BlockingQueue
```

---

# Resumo rápido para revisão

| Conceito | O que lembrar |
|---|---|
| ArrayList | Array dinâmico, acesso O(1), inserção no meio O(n) |
| LinkedList | Lista encadeada, acesso O(n), boa nas extremidades |
| HashSet | Unicidade, O(1) médio |
| LinkedHashSet | Unicidade + ordem de inserção |
| TreeSet | Unicidade + ordenação, O(log n) |
| HashMap | Chave-valor, O(1) médio |
| LinkedHashMap | HashMap + ordem |
| TreeMap | Chaves ordenadas, O(log n) |
| PriorityQueue | Recuperação por prioridade |
| ArrayDeque | Fila ou pilha eficiente |
| equals/hashCode | Fundamentais para HashMap/HashSet |
| Comparable | Ordem natural |
| Comparator | Estratégia externa de ordenação |
| Iterator | Percorre coleção |
| Fail-fast | Detecta algumas modificações estruturais indevidas |
| ConcurrentHashMap | Map thread-safe |
| CopyOnWriteArrayList | Muitas leituras, poucas escritas |
| BlockingQueue | Producer/Consumer |



---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Programação Orientada a Objetos em Java

<a id="poo-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#poo-visao-geral)
- [2. Classe, Objeto e Instância](#poo-classe-objeto-instancia)
  - [2.1 Classe](#poo-classe)
  - [2.2 Objeto](#poo-objeto)
  - [2.3 Estado e Comportamento](#poo-estado-comportamento)
  - [2.4 Construtores](#poo-construtores)
- [3. Encapsulamento](#poo-encapsulamento)
  - [3.1 Modificadores de Acesso](#poo-modificadores-acesso)
  - [3.2 Getters e Setters](#poo-getters-setters)
  - [3.3 Invariantes](#poo-invariantes)
  - [3.4 Imutabilidade](#poo-imutabilidade)
- [4. Abstração](#poo-abstracao)
  - [4.1 Classe Abstrata](#poo-classe-abstrata)
  - [4.2 Interface](#poo-interface)
  - [4.3 Classe Abstrata vs Interface](#poo-classe-abstrata-vs-interface)
- [5. Herança](#poo-heranca)
  - [5.1 Relação IS-A](#poo-is-a)
  - [5.2 Sobrescrita de Métodos](#poo-override)
  - [5.3 super](#poo-super)
  - [5.4 final em Herança](#poo-final-heranca)
  - [5.5 Trade-offs da Herança](#poo-tradeoffs-heranca)
- [6. Polimorfismo](#poo-polimorfismo)
  - [6.1 Polimorfismo de Subtipo](#poo-polimorfismo-subtipo)
  - [6.2 Dynamic Dispatch](#poo-dynamic-dispatch)
  - [6.3 Upcasting e Downcasting](#poo-casting)
  - [6.4 Sobrecarga vs Sobrescrita](#poo-overload-vs-override)
- [7. Composição](#poo-composicao)
  - [7.1 Relação HAS-A](#poo-has-a)
  - [7.2 Composição vs Herança](#poo-composicao-vs-heranca)
  - [7.3 Delegação](#poo-delegacao)
- [8. Associação, Agregação e Composição](#poo-relacionamentos)
  - [8.1 Associação](#poo-associacao)
  - [8.2 Agregação](#poo-agregacao)
  - [8.3 Composição](#poo-composicao-forte)
- [9. Acoplamento e Coesão](#poo-acoplamento-coesao)
  - [9.1 Acoplamento](#poo-acoplamento)
  - [9.2 Coesão](#poo-coesao)
- [10. Contratos entre Objetos](#poo-contratos)
  - [10.1 equals](#poo-equals)
  - [10.2 hashCode](#poo-hashcode)
  - [10.3 toString](#poo-tostring)
- [11. static, final e this](#poo-static-final-this)
  - [11.1 static](#poo-static)
  - [11.2 final](#poo-final)
  - [11.3 this](#poo-this)
- [12. Java Moderno e Modelagem OO](#poo-java-moderno)
  - [12.1 Records](#poo-records)
  - [12.2 Sealed Classes](#poo-sealed-classes)
  - [12.3 Enums](#poo-enums)
- [13. Princípios de Design relacionados a POO](#poo-principios-design)
  - [13.1 Programar para abstrações](#poo-programar-abstracoes)
  - [13.2 Favor composition over inheritance](#poo-favor-composition)
  - [13.3 Tell, Don't Ask](#poo-tell-dont-ask)
  - [13.4 Encapsular invariantes](#poo-encapsular-invariantes)
- [14. Armadilhas comuns de entrevista](#poo-armadilhas)
- [15. Cenários de Produção](#poo-producao)
- [16. Resposta Completa de Entrevista](#poo-resposta-completa)
- [17. Mapa Mental](#poo-mapa-mental)
- [18. Resumo Rápido](#poo-resumo-rapido)

---

<a id="poo-visao-geral"></a>
# 1. Visão Geral

Programação Orientada a Objetos — **POO** — organiza o software em torno de **objetos que possuem estado e comportamento**.

Os quatro pilares clássicos são:

```text
POO
│
├── Encapsulamento
├── Abstração
├── Herança
└── Polimorfismo
```

Em Java, esses conceitos aparecem através de:

```text
classes
interfaces
objetos
métodos
modificadores de acesso
herança
composição
polimorfismo
```

O objetivo não é apenas "usar classes", mas criar modelos em que:

- responsabilidades estejam bem definidas;
- invariantes sejam protegidas;
- dependências sejam controladas;
- implementação possa evoluir sem quebrar consumidores;
- comportamento esteja próximo dos dados que governa.

### Resposta de entrevista

> Programação Orientada a Objetos é um paradigma que organiza o sistema em objetos com estado e comportamento. Em Java, os principais conceitos são encapsulamento, abstração, herança e polimorfismo. Na prática, eu uso POO para modelar responsabilidades, proteger invariantes e reduzir acoplamento, normalmente favorecendo abstrações e composição quando isso melhora a flexibilidade.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-classe-objeto-instancia"></a>
# 2. Classe, Objeto e Instância

<a id="poo-classe"></a>
## 2.1 Classe

Uma **classe** define estrutura e comportamento para objetos.

```java
public class Conta {

    private BigDecimal saldo;

    public void depositar(BigDecimal valor) {
        saldo = saldo.add(valor);
    }
}
```

A classe define:

- atributos;
- métodos;
- construtores;
- regras de acesso;
- invariantes.

### Regra mental

```text
Classe → modelo
Objeto → instância concreta desse modelo
```

---

<a id="poo-objeto"></a>
## 2.2 Objeto

Um objeto possui:

```text
identidade
estado
comportamento
```

Exemplo:

```java
Conta conta = new Conta();
```

`conta` referencia uma instância da classe `Conta`.

---

<a id="poo-estado-comportamento"></a>
## 2.3 Estado e Comportamento

Estado:

```java
private BigDecimal saldo;
```

Comportamento:

```java
public void sacar(BigDecimal valor) {
    ...
}
```

Um bom modelo OO evita expor estado de forma indiscriminada.

Em vez de:

```java
conta.setSaldo(conta.getSaldo().subtract(valor));
```

prefira comportamento que represente a regra do domínio:

```java
conta.sacar(valor);
```

Isso mantém a regra dentro do objeto responsável por ela.

---

<a id="poo-construtores"></a>
## 2.4 Construtores

Construtores inicializam objetos.

```java
public Conta(String numero, BigDecimal saldoInicial) {
    this.numero = numero;
    this.saldo = saldoInicial;
}
```

Podem garantir que o objeto já seja criado em um estado válido.

### Exemplo

Ruim:

```java
Conta conta = new Conta();
conta.setNumero(null);
conta.setSaldo(new BigDecimal("-100"));
```

Melhor:

```java
Conta conta = new Conta(numero, saldoInicial);
```

com validação no construtor.

### Resposta de entrevista

> Uma classe define estrutura e comportamento; o objeto é uma instância concreta dessa classe. Eu procuro criar objetos já em estado válido e colocar comportamento próximo do estado que ele controla, evitando expor atributos sem necessidade.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-encapsulamento"></a>
# 3. Encapsulamento

Encapsulamento consiste em **controlar acesso ao estado interno e expor uma API coerente para manipulação desse estado**.

Não significa apenas:

```java
private + getter + setter
```

O objetivo real é:

```text
proteger invariantes
esconder detalhes internos
controlar mudanças de estado
reduzir acoplamento
```

Exemplo:

```java
public class Conta {

    private BigDecimal saldo = BigDecimal.ZERO;

    public void sacar(BigDecimal valor) {
        if (valor.signum() <= 0) {
            throw new IllegalArgumentException("Valor inválido");
        }

        if (saldo.compareTo(valor) < 0) {
            throw new IllegalStateException("Saldo insuficiente");
        }

        saldo = saldo.subtract(valor);
    }

    public BigDecimal getSaldo() {
        return saldo;
    }
}
```

O consumidor não altera diretamente:

```java
saldo
```

Ele solicita uma operação:

```java
conta.sacar(valor);
```

---

<a id="poo-modificadores-acesso"></a>
## 3.1 Modificadores de Acesso

| Modificador | Mesma classe | Mesmo package | Subclasse | Qualquer classe |
|---|---:|---:|---:|---:|
| `private` | Sim | Não | Não | Não |
| package-private | Sim | Sim | Depende do package | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

### Regra prática

> Use a menor visibilidade necessária.

Isso reduz a superfície pública da classe.

---

<a id="poo-getters-setters"></a>
## 3.2 Getters e Setters

Ter campos `private` com getters e setters para tudo **não garante bom encapsulamento**.

Exemplo fraco:

```java
public void setStatus(Status status) {
    this.status = status;
}
```

Isso permite transições inválidas.

Melhor:

```java
public void aprovar() {
    if (status != Status.PENDENTE) {
        throw new IllegalStateException();
    }

    status = Status.APROVADO;
}
```

Agora o objeto controla a própria regra.

---

<a id="poo-invariantes"></a>
## 3.3 Invariantes

Invariante é uma condição que deve permanecer verdadeira para o objeto ser válido.

Exemplo:

```text
saldo nunca pode ficar negativo
```

ou:

```text
pedido CANCELADO não pode ser APROVADO
```

Boa POO mantém essa regra dentro da abstração responsável.

---

<a id="poo-imutabilidade"></a>
## 3.4 Imutabilidade

Objeto imutável não altera seu estado após criação.

Exemplo:

```java
public final class Dinheiro {

    private final BigDecimal valor;

    public Dinheiro(BigDecimal valor) {
        if (valor == null) {
            throw new IllegalArgumentException();
        }

        this.valor = valor;
    }

    public BigDecimal valor() {
        return valor;
    }
}
```

### Vantagens

- mais simples de raciocinar;
- facilita concorrência;
- evita estado compartilhado mutável;
- reduz efeitos colaterais.

### Trade-off

Pode gerar novos objetos em vez de atualizar o existente.

### Resposta de entrevista

> Encapsulamento não é apenas colocar atributos como private. É controlar como o estado pode mudar e proteger invariantes. Eu prefiro expor operações de negócio em vez de setters genéricos, porque isso reduz estados inválidos e acoplamento com a implementação interna.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-abstracao"></a>
# 4. Abstração

Abstração expõe **o que um componente faz**, escondendo detalhes de **como ele faz**.

Exemplo:

```java
public interface PagamentoGateway {

    ResultadoPagamento pagar(Pagamento pagamento);
}
```

O consumidor depende do contrato:

```java
PagamentoGateway
```

e não precisa conhecer:

```text
HTTP
SDK externo
autenticação
timeout
serialização
```

---

<a id="poo-classe-abstrata"></a>
## 4.1 Classe Abstrata

Uma classe abstrata pode possuir:

- estado;
- construtores;
- métodos concretos;
- métodos abstratos;
- visibilidade `protected`;
- lógica compartilhada.

Exemplo:

```java
public abstract class Funcionario {

    private final String nome;

    protected Funcionario(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }

    public abstract BigDecimal calcularBonus();
}
```

Não pode ser instanciada diretamente.

---

<a id="poo-interface"></a>
## 4.2 Interface

Interface define um contrato.

```java
public interface Notificador {

    void enviar(Mensagem mensagem);
}
```

Implementações:

```java
public class NotificadorEmail implements Notificador {
    ...
}

public class NotificadorSms implements Notificador {
    ...
}
```

Interfaces modernas também podem possuir:

```java
default
static
private
```

methods.

### Uso típico

- definir capacidades;
- criar contratos;
- desacoplar consumidores de implementações;
- permitir múltiplas implementações.

---

<a id="poo-classe-abstrata-vs-interface"></a>
## 4.3 Classe Abstrata vs Interface

| Classe Abstrata | Interface |
|---|---|
| Pode manter estado de instância | Normalmente representa contrato/capacidade |
| Pode ter construtor | Não possui construtor de instância |
| Herança única | Uma classe pode implementar várias interfaces |
| Pode compartilhar implementação e estado | Boa para desacoplamento |
| Relação de especialização mais forte | Contrato mais flexível |

### Regra prática

Use **interface** quando o principal objetivo é definir um contrato.

Use **classe abstrata** quando existe uma relação forte de especialização e comportamento/estado comum relevante.

### Resposta de entrevista

> Abstração significa expor a capacidade essencial e esconder detalhes de implementação. Em Java, interfaces são ótimas para contratos e desacoplamento. Classes abstratas fazem sentido quando subclasses compartilham estado ou comportamento base e existe uma relação de especialização clara.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-heranca"></a>
# 5. Herança

Herança permite que uma classe derive comportamento e estrutura de outra.

```java
public class Gerente extends Funcionario {
    ...
}
```

---

<a id="poo-is-a"></a>
## 5.1 Relação IS-A

Herança deve representar uma relação válida:

```text
Gerente IS-A Funcionario
```

Não use herança apenas para reaproveitar código.

Exemplo ruim:

```text
Pedido extends ArrayList<Item>
```

apenas porque Pedido contém itens.

O correto normalmente é:

```text
Pedido HAS-A List<Item>
```

---

<a id="poo-override"></a>
## 5.2 Sobrescrita de Métodos

Override ocorre quando uma subclasse redefine um método herdado.

```java
@Override
public BigDecimal calcularBonus() {
    return salario.multiply(new BigDecimal("0.20"));
}
```

A assinatura deve ser compatível com o contrato do método pai.

### Use sempre

```java
@Override
```

porque o compilador valida a intenção.

---

<a id="poo-super"></a>
## 5.3 super

`super` acessa membros da superclasse.

Exemplo:

```java
public Gerente(String nome) {
    super(nome);
}
```

Também pode chamar implementação do método pai:

```java
super.processar();
```

---

<a id="poo-final-heranca"></a>
## 5.4 final em Herança

Classe `final`:

```java
public final class Dinheiro {
}
```

não pode ser herdada.

Método `final`:

```java
public final void validar() {
}
```

não pode ser sobrescrito.

---

<a id="poo-tradeoffs-heranca"></a>
## 5.5 Trade-offs da Herança

### Vantagens

- reutilização de comportamento;
- polimorfismo;
- modelagem de especializações reais.

### Custos

- forte acoplamento com superclasse;
- hierarquias difíceis de evoluir;
- subclasses podem depender de detalhes do pai;
- alterações no pai podem impactar toda a árvore.

### Regra prática

> Herança deve representar substituição válida, não apenas compartilhamento de código.

### Resposta de entrevista

> Herança modela uma relação IS-A. Eu uso quando existe uma especialização real e a subclasse pode substituir a superclasse preservando seu contrato. Evito usar herança apenas para reaproveitar código, porque ela aumenta o acoplamento entre pai e filhos.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-polimorfismo"></a>
# 6. Polimorfismo

Polimorfismo permite usar diferentes implementações através de uma mesma abstração.

```java
Notificador notificador = new NotificadorEmail();
```

Depois:

```java
notificador.enviar(mensagem);
```

O consumidor trabalha com:

```java
Notificador
```

e não com a implementação específica.

---

<a id="poo-polimorfismo-subtipo"></a>
## 6.1 Polimorfismo de Subtipo

Exemplo:

```java
List<Notificador> notificadores = List.of(
    new NotificadorEmail(),
    new NotificadorSms()
);

for (Notificador notificador : notificadores) {
    notificador.enviar(mensagem);
}
```

Cada implementação possui comportamento próprio.

---

<a id="poo-dynamic-dispatch"></a>
## 6.2 Dynamic Dispatch

Quando:

```java
Notificador n = new NotificadorEmail();
n.enviar(mensagem);
```

o método executado é determinado pelo **tipo real do objeto em runtime**.

```text
tipo da referência → Notificador
tipo do objeto     → NotificadorEmail
método executado   → NotificadorEmail.enviar()
```

---

<a id="poo-casting"></a>
## 6.3 Upcasting e Downcasting

### Upcasting

```java
Notificador n = new NotificadorEmail();
```

É natural e seguro.

### Downcasting

```java
NotificadorEmail email = (NotificadorEmail) n;
```

É mais perigoso.

Pode gerar:

```text
ClassCastException
```

se o objeto não for daquele tipo.

Java moderno permite pattern matching:

```java
if (n instanceof NotificadorEmail email) {
    email.enviarComTemplate();
}
```

### Regra prática

> Muitos downcasts podem indicar uma abstração mal desenhada.

---

<a id="poo-overload-vs-override"></a>
## 6.4 Sobrecarga vs Sobrescrita

### Overloading — Sobrecarga

Mesmo nome, parâmetros diferentes.

```java
enviar(String mensagem)

enviar(String mensagem, Prioridade prioridade)
```

Resolvido principalmente em **compile time**.

### Overriding — Sobrescrita

Subclasse redefine comportamento herdado.

```java
@Override
public void enviar(Mensagem mensagem) {
    ...
}
```

A implementação concreta é escolhida em **runtime**.

| Overload | Override |
|---|---|
| Mesmo nome | Mesmo contrato herdado |
| Parâmetros diferentes | Mesma assinatura compatível |
| Compile time | Runtime |
| Não exige herança | Exige relação de herança/implementação |

### Resposta de entrevista

> Polimorfismo permite tratar diferentes implementações através de um mesmo contrato. Em Java, quando uma referência da interface aponta para uma implementação concreta, o método sobrescrito é resolvido em runtime através de dynamic dispatch.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-composicao"></a>
# 7. Composição

Composição constrói objetos a partir de outros objetos.

```java
public class Pedido {

    private final CalculadoraFrete calculadoraFrete;
    private final PagamentoGateway pagamentoGateway;
}
```

---

<a id="poo-has-a"></a>
## 7.1 Relação HAS-A

```text
Pedido HAS-A PagamentoGateway
```

Diferente da herança:

```text
Gerente IS-A Funcionario
```

---

<a id="poo-composicao-vs-heranca"></a>
## 7.2 Composição vs Herança

Com composição:

```java
public class CheckoutService {

    private final PagamentoGateway gateway;

    public CheckoutService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

A implementação pode ser trocada:

```java
new CheckoutService(new StripeGateway());
new CheckoutService(new PixGateway());
```

Sem mudar a classe principal.

### Vantagens

- menor acoplamento;
- maior flexibilidade;
- facilita testes;
- troca de implementação;
- evita hierarquias profundas.

---

<a id="poo-delegacao"></a>
## 7.3 Delegação

Composição normalmente usa delegação.

```java
public Resultado pagar(Pagamento pagamento) {
    return gateway.pagar(pagamento);
}
```

O objeto delega uma responsabilidade a outro objeto especializado.

### Resposta de entrevista

> Composição representa relação HAS-A e permite montar comportamentos através de dependências. Eu normalmente prefiro composição quando quero reutilizar comportamento ou trocar implementações, porque ela reduz o acoplamento estrutural criado pela herança.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-relacionamentos"></a>
# 8. Associação, Agregação e Composição

<a id="poo-associacao"></a>
## 8.1 Associação

Um objeto conhece ou utiliza outro.

```text
Pedido ───── Cliente
```

Não implica necessariamente propriedade de ciclo de vida.

---

<a id="poo-agregacao"></a>
## 8.2 Agregação

Representa relação todo-parte em que a parte pode existir independentemente.

Exemplo conceitual:

```text
Time ◇──── Jogador
```

O jogador pode continuar existindo sem o time.

---

<a id="poo-composicao-forte"></a>
## 8.3 Composição

Relação todo-parte mais forte.

```text
Pedido ◆──── ItemPedido
```

`ItemPedido` normalmente só faz sentido dentro do `Pedido`.

### Observação prática

Em código Java, a diferença entre agregação e composição nem sempre aparece sintaticamente.

Ela é principalmente uma decisão de **modelagem e ciclo de vida**.

### Resposta de entrevista

> Associação representa relacionamento geral entre objetos. Agregação é uma relação todo-parte mais fraca, em que a parte pode existir sozinha. Composição é mais forte e normalmente implica que a parte pertence ao ciclo de vida do todo.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-acoplamento-coesao"></a>
# 9. Acoplamento e Coesão

<a id="poo-acoplamento"></a>
## 9.1 Acoplamento

Acoplamento mede quanto uma unidade depende de detalhes de outra.

Exemplo fortemente acoplado:

```java
public class PedidoService {

    private final StripeClient client = new StripeClient();
}
```

Agora `PedidoService` depende diretamente da implementação.

Melhor:

```java
public class PedidoService {

    private final PagamentoGateway gateway;

    public PedidoService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

---

<a id="poo-coesao"></a>
## 9.2 Coesão

Coesão mede quão relacionadas são as responsabilidades dentro de uma classe.

Classe pouco coesa:

```text
ClienteService
├── cadastrar cliente
├── gerar PDF
├── enviar e-mail
├── calcular imposto
└── acessar FTP
```

Classe mais coesa:

```text
ClienteService
└── regras de cliente
```

### Objetivo

```text
baixo acoplamento
+
alta coesão
```

### Resposta de entrevista

> Eu procuro baixa dependência entre componentes e alta coesão dentro de cada classe. Baixo acoplamento facilita substituição e evolução; alta coesão mantém responsabilidades relacionadas no mesmo lugar.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-contratos"></a>
# 10. Contratos entre Objetos

Todo objeto Java herda de:

```java
java.lang.Object
```

Métodos importantes:

```java
equals()
hashCode()
toString()
```

---

<a id="poo-equals"></a>
## 10.1 equals

Define igualdade lógica.

Exemplo:

```java
cliente1.equals(cliente2)
```

pode significar que representam a mesma identidade de negócio.

Contrato:

- reflexivo;
- simétrico;
- transitivo;
- consistente;
- comparação com `null` retorna `false`.

---

<a id="poo-hashcode"></a>
## 10.2 hashCode

Regra fundamental:

```text
se a.equals(b) == true
então
a.hashCode() == b.hashCode()
```

Importante para:

```java
HashMap
HashSet
```

### Atenção

Objetos usados como chave de `HashMap` devem evitar alterar campos usados por:

```java
equals()
hashCode()
```

enquanto estiverem armazenados no mapa.

---

<a id="poo-tostring"></a>
## 10.3 toString

Fornece representação textual do objeto.

```java
@Override
public String toString() {
    return "Cliente{id=" + id + ", nome='" + nome + "'}";
}
```

Útil para:

- debug;
- logs;
- diagnóstico.

### Cuidado

Evite incluir:

- senhas;
- tokens;
- dados pessoais sensíveis;
- segredos.

### Resposta de entrevista

> equals define igualdade lógica, hashCode precisa respeitar essa igualdade para estruturas hash e toString fornece uma representação textual útil para diagnóstico. Em entidades mutáveis, também tomo cuidado para não alterar campos usados em hashCode enquanto o objeto está dentro de HashMap ou HashSet.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-static-final-this"></a>
# 11. static, final e this

<a id="poo-static"></a>
## 11.1 static

Membro `static` pertence à classe, não a uma instância específica.

```java
public static final int MAX_RETRIES = 3;
```

Método estático:

```java
public static Cliente criar(...) {
    ...
}
```

### Atenção

Estado `static` mutável é compartilhado por todas as instâncias e pode gerar:

- acoplamento global;
- problemas de concorrência;
- dificuldade de testes.

---

<a id="poo-final"></a>
## 11.2 final

Pode ser aplicado a:

### Variável

```java
final Cliente cliente;
```

A referência não pode ser reatribuída.

Isso **não torna o objeto imutável**.

### Método

```java
public final void executar() {
}
```

não pode ser sobrescrito.

### Classe

```java
public final class Dinheiro {
}
```

não pode ser herdada.

---

<a id="poo-this"></a>
## 11.3 this

`this` referencia a instância atual.

```java
public Cliente(String nome) {
    this.nome = nome;
}
```

Também permite delegar entre construtores:

```java
public Cliente(String nome) {
    this(nome, Status.ATIVO);
}
```

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-java-moderno"></a>
# 12. Java Moderno e Modelagem OO

<a id="poo-records"></a>
## 12.1 Records

Records são adequados para representar **dados imutáveis por referência de componentes**, com pouca cerimônia.

```java
public record ClienteResponse(
    Long id,
    String nome
) {}
```

O compilador gera automaticamente:

- construtor canônico;
- acessores;
- `equals`;
- `hashCode`;
- `toString`.

### Bom uso

- DTOs;
- value carriers;
- mensagens;
- respostas de API;
- objetos de transporte.

### Não significa imutabilidade profunda

```java
record Pedido(List<Item> itens) {}
```

A referência `itens` é final, mas a lista pode continuar mutável.

---

<a id="poo-sealed-classes"></a>
## 12.2 Sealed Classes

Controlam quais tipos podem herdar ou implementar uma abstração.

```java
public sealed interface Pagamento
        permits Pix, Cartao, Boleto {
}
```

Implementações:

```java
public final class Pix implements Pagamento {
}
```

Útil para hierarquias fechadas e bem conhecidas.

Combina bem com pattern matching.

---

<a id="poo-enums"></a>
## 12.3 Enums

Enum representa um conjunto fechado de constantes.

```java
public enum StatusPedido {
    CRIADO,
    PAGO,
    CANCELADO
}
```

Enums também podem possuir:

- atributos;
- métodos;
- construtores;
- comportamento específico por constante.

Exemplo:

```java
public enum Operacao {

    SOMA {
        @Override
        public int executar(int a, int b) {
            return a + b;
        }
    };

    public abstract int executar(int a, int b);
}
```

### Resposta de entrevista

> Em Java moderno, records são úteis para modelos orientados a dados com pouca cerimônia, sealed classes permitem controlar hierarquias e enums representam conjuntos fechados de valores com possibilidade de comportamento.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-principios-design"></a>
# 13. Princípios de Design relacionados a POO

<a id="poo-programar-abstracoes"></a>
## 13.1 Programar para abstrações

Evite depender diretamente de implementação.

Menos flexível:

```java
private StripeGateway gateway;
```

Mais flexível:

```java
private PagamentoGateway gateway;
```

Isso permite:

- substituição;
- testes;
- múltiplas implementações.

---

<a id="poo-favor-composition"></a>
## 13.2 Favor composition over inheritance

Herança cria acoplamento estrutural.

Composição cria dependência substituível.

```text
Herança
Classe A
   ↑
Classe B
   ↑
Classe C
```

versus:

```text
CheckoutService
   │
   ├── PagamentoGateway
   ├── FraudChecker
   └── CalculadoraFrete
```

Prefira composição quando a relação não é claramente IS-A.

---

<a id="poo-tell-dont-ask"></a>
## 13.3 Tell, Don't Ask

Em vez de buscar dados e decidir tudo fora do objeto:

```java
if (pedido.getStatus() == PENDENTE) {
    pedido.setStatus(APROVADO);
}
```

prefira:

```java
pedido.aprovar();
```

A regra fica encapsulada.

---

<a id="poo-encapsular-invariantes"></a>
## 13.4 Encapsular invariantes

Ruim:

```java
conta.setSaldo(new BigDecimal("-500"));
```

Melhor:

```java
conta.sacar(valor);
```

e a própria classe valida se a operação é permitida.

### Resposta de entrevista

> Em modelagem OO eu procuro programar para abstrações, favorecer composição quando não existe uma relação IS-A legítima e manter invariantes dentro dos próprios objetos. Isso reduz acoplamento e evita que regras de negócio fiquem espalhadas.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-armadilhas"></a>
# 14. Armadilhas comuns de entrevista

## Encapsulamento é apenas usar `private`?

```text
Não.
```

`private` ajuda a restringir acesso, mas encapsulamento envolve controlar mudanças de estado e proteger invariantes.

---

## Herança é sempre reutilização de código?

```text
Não.
```

Herança deve representar uma relação de substituição válida.

Para reutilização de comportamento, composição muitas vezes é melhor.

---

## Java suporta herança múltipla de classes?

```text
Não.
```

Uma classe só pode:

```java
extends UmaClasse
```

Mas pode implementar múltiplas interfaces:

```java
implements A, B, C
```

---

## Interface pode ter implementação?

```text
Sim.
```

Pode ter métodos:

```java
default
static
private
```

---

## Classe abstrata pode ter construtor?

```text
Sim.
```

O construtor é utilizado pelas subclasses durante inicialização.

---

## Método `static` é polimórfico por override?

```text
Não.
```

Métodos estáticos pertencem à classe.

Eles podem sofrer **method hiding**, não overriding polimórfico tradicional.

---

## `final` torna objeto imutável?

```text
Não.
```

Isto:

```java
final List<String> nomes = new ArrayList<>();
```

impede:

```java
nomes = outraLista;
```

mas permite:

```java
nomes.add("Lucas");
```

---

## Overload e Override são iguais?

```text
Não.
```

```text
Overload  → parâmetros diferentes → compile time
Override  → comportamento redefinido → runtime
```

---

## Composição e agregação são diferenciadas pelo Java?

Não diretamente.

A diferença é principalmente semântica e relacionada ao ciclo de vida dos objetos.

---

## Polimorfismo exige classe abstrata?

```text
Não.
```

Pode ocorrer via:

- interfaces;
- superclasses concretas;
- classes abstratas.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-producao"></a>
# 15. Cenários de Produção

## Cenário 1 — Muitos `if/else` por tipo

Exemplo:

```java
if (tipo.equals("PIX")) {
    ...
} else if (tipo.equals("CARTAO")) {
    ...
} else if (tipo.equals("BOLETO")) {
    ...
}
```

Pode indicar oportunidade para:

```java
PagamentoProcessor
```

com implementações:

```text
PixProcessor
CartaoProcessor
BoletoProcessor
```

Uso:

```java
processor.processar(pagamento);
```

### Conceito relacionado

```text
abstração + polimorfismo
```

---

## Cenário 2 — Classe com dezenas de setters

```text
Pedido
├── setStatus()
├── setTotal()
├── setDesconto()
├── setPagamento()
└── ...
```

Risco:

```text
objeto entrar em estado inválido
```

Melhor:

```java
pedido.aplicarDesconto(...)
pedido.confirmarPagamento(...)
pedido.cancelar(...)
```

### Conceito relacionado

```text
encapsulamento
```

---

## Cenário 3 — Hierarquia muito profunda

```text
BaseService
   ↑
AbstractCrudService
   ↑
AbstractAuditableService
   ↑
ClienteService
```

Problemas possíveis:

- comportamento implícito;
- dificuldade de rastrear origem dos métodos;
- forte acoplamento;
- efeito cascata em mudanças.

Considere:

```text
composição
+
delegação
```

---

## Cenário 4 — Dependência direta de infraestrutura

Ruim:

```java
public class PedidoService {

    private final StripeSdk stripe = new StripeSdk();
}
```

Melhor:

```java
public class PedidoService {

    private final PagamentoGateway gateway;

    public PedidoService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

Implementação de infraestrutura:

```java
public class StripePagamentoGateway
        implements PagamentoGateway {
}
```

### Ganhos

- teste unitário;
- substituição;
- menor acoplamento.

---

## Cenário 5 — Objeto anêmico

Modelo:

```java
class Conta {
    private BigDecimal saldo;

    public BigDecimal getSaldo() { ... }
    public void setSaldo(...) { ... }
}
```

Toda regra fica em:

```java
ContaService
```

Isso pode resultar em:

```text
dados separados do comportamento
```

Em domínios ricos, considere mover invariantes e comportamento relevantes para a entidade.

### Atenção

Nem todo DTO precisa ter comportamento.

Um DTO pode ser apenas transporte de dados.

---

## Cenário 6 — Testabilidade

Com dependência concreta:

```java
class PedidoService {
    private StripeClient client;
}
```

Teste fica acoplado ao cliente real.

Com abstração:

```java
class PedidoService {
    private PagamentoGateway gateway;
}
```

No teste:

```java
PagamentoGateway fake = ...
```

### Conceito relacionado

```text
abstração
+
composição
+
injeção de dependência
```

---

## Cenário 7 — Estado compartilhado mutável

Objetos mutáveis compartilhados entre threads podem exigir:

- sincronização;
- locks;
- atomics;
- controle de visibilidade.

Objetos imutáveis reduzem esse problema.

### Correlação com concorrência

```text
imutabilidade
      ↓
menos estado compartilhado mutável
      ↓
menos necessidade de sincronização
```

### Resposta de entrevista

> Em produção eu uso POO para concentrar regras e responsabilidades, não apenas para criar hierarquias. Se encontro muitos condicionais por tipo, avalio polimorfismo. Se há setters permitindo estados inválidos, reforço encapsulamento. Se a hierarquia ficou profunda, normalmente considero composição e delegação.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-resposta-completa"></a>
# 16. Resposta Completa de Entrevista

Se perguntarem:

**"O que você entende sobre Programação Orientada a Objetos em Java?"**

Uma resposta objetiva pode ser:

> Programação Orientada a Objetos organiza o sistema em objetos que possuem estado e comportamento. Os pilares clássicos são encapsulamento, abstração, herança e polimorfismo.
>
> Encapsulamento é controlar como o estado interno pode ser acessado e modificado, protegendo invariantes. Abstração permite expor contratos e esconder detalhes de implementação, normalmente através de interfaces ou classes abstratas.
>
> Herança representa uma relação IS-A e permite especialização, mas cria acoplamento entre superclasse e subclasses, então eu evito usá-la apenas para reaproveitar código. Quando quero combinar comportamentos ou trocar implementações, normalmente prefiro composição.
>
> Polimorfismo permite trabalhar com diferentes implementações através de um mesmo contrato. Por exemplo, um serviço pode depender de PagamentoGateway e receber implementações diferentes sem alterar sua lógica principal.
>
> Na prática, procuro manter alta coesão, baixo acoplamento, programar para abstrações e colocar regras próximas dos objetos responsáveis por elas. Em Java moderno, também utilizo records para objetos orientados a dados e sealed classes quando quero controlar uma hierarquia fechada.

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-mapa-mental"></a>
# 17. Mapa Mental

```text
POO EM JAVA
│
├── Classe / Objeto
│   ├── estado
│   ├── comportamento
│   └── construtor
│
├── Encapsulamento
│   ├── private
│   ├── invariantes
│   ├── comportamento
│   └── imutabilidade
│
├── Abstração
│   ├── interface
│   └── classe abstrata
│
├── Herança
│   ├── IS-A
│   ├── extends
│   ├── override
│   ├── super
│   └── acoplamento
│
├── Polimorfismo
│   ├── interface → implementação
│   ├── dynamic dispatch
│   ├── upcasting
│   └── override
│
├── Composição
│   ├── HAS-A
│   ├── delegação
│   └── flexibilidade
│
├── Relacionamentos
│   ├── associação
│   ├── agregação
│   └── composição
│
├── Design
│   ├── baixo acoplamento
│   ├── alta coesão
│   ├── programar para abstrações
│   └── composição > herança quando aplicável
│
├── Object
│   ├── equals
│   ├── hashCode
│   └── toString
│
└── Java Moderno
    ├── records
    ├── sealed classes
    └── enums
```

[↑ Sumário do tópico](#poo-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="poo-resumo-rapido"></a>
# 18. Resumo Rápido

| Conceito | O que lembrar | Trade-off / cuidado |
|---|---|---|
| Classe | Modelo de estado e comportamento | Evitar classes com responsabilidades demais |
| Objeto | Instância concreta | Pode ser mutável ou imutável |
| Encapsulamento | Controla acesso e protege invariantes | Não é apenas getter/setter |
| Abstração | Expõe contrato e esconde detalhes | Abstração excessiva aumenta complexidade |
| Interface | Contrato flexível | Evitar interfaces sem necessidade |
| Classe abstrata | Estado/comportamento base compartilhado | Herança aumenta acoplamento |
| Herança | Relação IS-A | Não usar só para reaproveitar código |
| Polimorfismo | Mesma abstração, comportamentos diferentes | Evitar downcasts frequentes |
| Composição | Relação HAS-A | Geralmente mais flexível que herança |
| Override | Redefine comportamento herdado | Deve preservar contrato |
| Overload | Mesmo nome, parâmetros diferentes | Resolvido em compile time |
| Imutabilidade | Estado não muda após criação | Pode criar mais objetos |
| Acoplamento | Dependência entre componentes | Preferir baixo acoplamento |
| Coesão | Responsabilidades relacionadas | Preferir alta coesão |
| equals/hashCode | Igualdade lógica e hashing | Contrato deve ser consistente |
| `static` | Pertence à classe | Estado static mutável pode ser problemático |
| `final` | Restringe alteração/herança | Não garante imutabilidade profunda |
| Record | Modelo conciso orientado a dados | Não é profundamente imutável |
| Sealed Class | Restringe hierarquia | Adequado para conjuntos fechados de tipos |

---

## Resposta de 30 segundos

> POO em Java organiza o software em objetos com estado e comportamento. Os pilares principais são encapsulamento, abstração, herança e polimorfismo. Eu uso encapsulamento para proteger invariantes, abstrações para desacoplar implementações e polimorfismo para permitir comportamentos diferentes através do mesmo contrato. Herança eu reservo para relações IS-A reais; quando quero flexibilidade e reutilização de comportamento, geralmente prefiro composição.

---

## Perguntas que normalmente vêm depois

Depois de responder sobre POO, é comum aprofundarem em:

```text
1. Interface vs classe abstrata
2. Overload vs Override
3. Composição vs Herança
4. equals e hashCode
5. Encapsulamento e invariantes
6. Polimorfismo e dynamic dispatch
7. final / static / this
8. Imutabilidade
9. Acoplamento e coesão
10. Records e sealed classes
```
