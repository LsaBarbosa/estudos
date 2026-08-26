Lucas, em concorrência o ponto central é entender **execução paralela, coordenação entre threads, compartilhamento de estado e visibilidade de memória**. No Java moderno, isso inclui também saber quando usar **Virtual Threads** em vez de pools tradicionais. A documentação atual do Java SE 26 continua tratando `java.util.concurrent` como o núcleo das abstrações de concorrência, e Virtual Threads como threads leves voltadas principalmente a aplicações de alto throughput com muito tempo de espera em I/O. 

# 1. Concorrência — conceitos, trade-offs e casos de uso

## Execução de tarefas

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Thread** | Unidade de execução concorrente dentro da JVM. Pode ser uma platform thread ou uma virtual thread. | Platform Threads possuem custo maior de memória e recursos do sistema operacional; excesso de threads pode aumentar context switching. | Execução concorrente de tarefas. |
| **Runnable** | Representa uma tarefa que executa código e não retorna resultado diretamente. | Simples, mas não possui retorno tipado nem permite declarar checked exceptions diretamente. | Fire-and-forget, tarefas assíncronas simples. |
| **Callable** | Representa uma tarefa que retorna um resultado e pode lançar exceções. | Normalmente precisa ser executada por um `ExecutorService` ou infraestrutura equivalente. | Consultas, cálculos e processamento assíncrono com retorno. |
| **Future** | Representa o resultado futuro de uma computação assíncrona. `get()` pode bloquear até o resultado ficar disponível.  | API simples, mas composição entre várias operações é limitada e chamadas a `get()` podem bloquear. | Aguardar resultado de uma tarefa submetida a um executor. |
| **CompletableFuture** | Extende a ideia de `Future`, permitindo composição de pipelines assíncronos com operações como `thenApply`, `thenCompose` e `allOf`.  | Fluxos grandes podem ficar difíceis de ler, tratar exceções e depurar. Uso descuidado do executor padrão também pode gerar contenção. | Agregar chamadas independentes, processamento assíncrono e composição de tarefas. |
| **ExecutorService** | Abstração responsável por receber, agendar e executar tarefas, além de controlar o ciclo de vida do executor.  | Pools mal dimensionados podem causar filas, latência, starvation ou excesso de threads. | Thread pools, execução assíncrona controlada. |
| **ForkJoinPool** | Executor baseado em **work stealing**, adequado principalmente para tarefas que podem ser divididas em subtarefas.  | Muito bom para trabalho computacional divisível, mas tarefas bloqueantes podem reduzir a eficiência do pool. | Algoritmos divide-and-conquer, parallel streams, processamento CPU-bound. |
| **Virtual Threads** | Threads leves gerenciadas principalmente pela JVM, permitindo um número muito maior de tarefas concorrentes sem criar uma OS thread dedicada para cada uma.  | Não tornam código CPU-bound mais rápido. Aumentam escalabilidade principalmente quando tarefas passam muito tempo bloqueadas em I/O. Recursos externos continuam tendo limites. | APIs REST, JDBC, HTTP, microsserviços e aplicações I/O-bound de alto throughput. |

---

# 2. Sincronização e coordenação

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **synchronized** | Garante exclusão mútua sobre um monitor e também estabelece garantias de visibilidade entre threads. Um unlock acontece antes de uma aquisição posterior do mesmo monitor.  | Simples e seguro, mas regiões críticas grandes podem gerar contention e serializar a aplicação. | Proteger estado mutável compartilhado. |
| **volatile** | Garante visibilidade das alterações de uma variável entre threads e estabelece relação happens-before entre escrita e leitura subsequente. Não fornece exclusão mútua.  | Excelente para estado simples, mas não torna operações compostas como `count++` atômicas. | Flags como `running`, estados simples compartilhados. |
| **Lock** | Abstração explícita para mecanismos de locking, oferecendo maior controle que `synchronized`. | Mais flexível, mas exige gerenciamento explícito e normalmente `unlock()` em `finally`. | Controle avançado de região crítica. |
| **ReentrantLock** | Implementação reentrante de `Lock`, permitindo `tryLock`, timeout, interrupção e políticas opcionais de fairness.  | Mais complexo que `synchronized`. Fair locks podem diminuir throughput. | Locks com timeout, tentativa de aquisição ou controle avançado. |
| **Semaphore** | Mantém uma quantidade de permits e limita quantas threads podem acessar determinado recurso simultaneamente.  | Introduz espera quando todos os permits estão ocupados. Configuração inadequada pode reduzir throughput. | Limitar chamadas simultâneas para API, banco ou recurso externo. |
| **CountDownLatch** | Permite que uma ou várias threads aguardem até que determinado número de eventos seja concluído. Suas operações possuem garantias de happens-before.  | É essencialmente de uso único; depois que chega a zero não é reiniciado. | Esperar vários workers terminarem antes de continuar. |
| **AtomicInteger** | Inteiro que suporta operações atômicas, como incremento e compare-and-set, sem precisar proteger cada operação com um lock explícito.  | Bom para uma variável isolada, mas não resolve invariantes envolvendo múltiplos estados compartilhados. | Contadores concorrentes, sequências, CAS. |
| **ConcurrentHashMap** | `Map` thread-safe projetado para permitir alta concorrência em leituras e atualizações, sem depender de um único lock global para todos os acessos.  | Possui overhead maior que `HashMap` simples e operações compostas precisam utilizar APIs atômicas apropriadas, como `compute`. | Cache concorrente, registry, estado compartilhado entre threads. |

---

# 3. Conceitos fundamentais

| Conceito | Definição objetiva | Trade-off / impacto | Exemplo |
|---|---|---|---|
| **Race Condition** | Resultado da aplicação depende da ordem imprevisível em que múltiplas threads acessam ou modificam estado compartilhado. | Pode gerar bugs intermitentes extremamente difíceis de reproduzir. | Duas threads executando `saldo = saldo - valor`. |
| **Deadlock** | Duas ou mais threads ficam permanentemente esperando recursos umas das outras. | Aplicação ou parte dela deixa de progredir. | Thread A segura lock 1 esperando lock 2; B segura lock 2 esperando lock 1. |
| **Livelock** | Threads continuam executando e reagindo umas às outras, mas nenhuma consegue progredir efetivamente. | CPU pode continuar sendo utilizada sem trabalho útil. | Duas threads liberando recursos repetidamente para a outra. |
| **Starvation** | Uma thread permanece por muito tempo sem conseguir CPU, lock ou outro recurso porque outras threads são constantemente favorecidas. | Pode causar latência extrema ou tarefas que praticamente nunca terminam. | Thread nunca consegue adquirir um lock altamente disputado. |
| **Visibility** | Garantia de que uma alteração realizada por uma thread será observada corretamente por outra. | Sem sincronização adequada, uma thread pode observar um valor antigo. | Flag compartilhada sem `volatile`. |
| **Atomicity** | Uma operação é observada como indivisível em relação às outras threads. | Operações que parecem simples podem envolver múltiplas etapas. | `count++` é leitura, soma e escrita; não é atomicamente seguro sozinho. |
| **Happens-before** | Relação definida pelo Java Memory Model que determina quando efeitos de uma thread são garantidamente visíveis para outra.  | É a base conceitual para entender corretamente `volatile`, locks e sincronizadores. | `unlock()` acontece-before de um `lock()` posterior do mesmo monitor. |
| **Thread Safety** | Propriedade de um código que continua correto quando utilizado simultaneamente por múltiplas threads. | Pode exigir sincronização, imutabilidade ou estruturas concorrentes, possivelmente com custo de performance. | Serviço stateless ou classe protegida corretamente contra acesso concorrente. |
| **Immutability** | Estado do objeto não muda depois de sua construção. | Pode gerar mais objetos, mas elimina grande parte dos problemas relacionados a estado mutável compartilhado. | Records, DTOs imutáveis, objetos de valor. |
| **Contention** | Várias threads disputam o mesmo recurso, lock ou região crítica. | Aumenta espera e reduz escalabilidade; muitas threads podem acabar executando de forma praticamente serial. | Muitas threads tentando adquirir o mesmo `synchronized`. |
| **Lock-free algorithm** | Algoritmo concorrente que consegue progredir sem depender de exclusão mútua tradicional, normalmente usando operações atômicas como CAS. O pacote `java.util.concurrent.atomic` fornece primitivas para esse tipo de programação.  | Pode reduzir blocking e contention, mas implementação e raciocínio ficam significativamente mais difíceis. | Contadores atômicos e algumas filas concorrentes. |

---

# 4. As diferenças que mais aparecem em entrevista

### `Runnable` x `Callable`

```text
Runnable
    ↓
executa
    ↓
não retorna valor

Callable<T>
    ↓
executa
    ↓
retorna T
    ↓
pode lançar Exception
```

Um `ExecutorService` consegue executar ambos; `Callable` é particularmente útil quando a tarefa precisa produzir resultado. 

---

### `Future` x `CompletableFuture`

```text
Future
│
├── representa resultado futuro
├── get()
├── cancel()
└── composição limitada


CompletableFuture
│
├── resultado futuro
├── thenApply
├── thenCompose
├── thenCombine
├── allOf
└── tratamento de exceções
```

`Future.get()` pode bloquear esperando a computação terminar, enquanto `CompletableFuture` oferece uma API de composição de estágios assíncronos. 

---

# 5. `synchronized` x `volatile` x Atomic

Essa diferença precisa estar muito clara.

Considere:

```java
volatile int counter = 0;

counter++;
```

Isso **não é thread-safe**.

`volatile` resolve principalmente **visibilidade**, não atomicidade de uma operação composta. A escrita em uma variável volatile estabelece happens-before para leituras posteriores dessa variável, mas não cria exclusão mútua. 

Para um contador simples:

```java
AtomicInteger counter = new AtomicInteger();

counter.incrementAndGet();
```

A operação de incremento é atômica. 

Já quando temos uma regra envolvendo vários estados:

```java
if (saldo >= valor) {
    saldo -= valor;
}
```

um `AtomicInteger` isolado pode não ser suficiente.

Talvez seja necessário proteger toda a **invariante de negócio** com um lock.

---

# 6. O conceito mais importante: happens-before

Um erro comum é pensar:

> "As duas threads estão vendo a mesma memória, então vão enxergar os mesmos valores."

O Java Memory Model não funciona assim.

A questão correta é:

> Existe uma relação de sincronização que garante que a escrita da thread A seja visível para a thread B?

Por exemplo:

```java
volatile boolean ready;
```

Thread A:

```java
data = loadData();
ready = true;
```

Thread B:

```java
if (ready) {
    use(data);
}
```

Uma escrita em `volatile` happens-before de uma leitura subsequente daquele mesmo campo, dando a garantia de visibilidade necessária. Locks, `Thread.start()`, `Thread.join()`, `Future.get()`, semáforos e outros sincronizadores também estabelecem relações específicas de happens-before. 

---

# 7. Contentions e locks

O problema de um lock normalmente não é simplesmente:

> "Lock é lento."

O problema real aparece quando existe **contention**.

Imagine:

```text
Thread 1 ──┐
Thread 2 ──┤
Thread 3 ──┤──► mesmo Lock
Thread 4 ──┤
Thread 5 ──┘
```

Se somente uma consegue acessar uma região crítica longa, o código teoricamente concorrente começa a ser serializado.

Por isso um Senior deve perguntar:

- Quanto tempo o lock fica adquirido?
- Quantas threads disputam esse lock?
- Podemos reduzir a região crítica?
- Precisamos realmente compartilhar esse estado?
- Uma estrutura concorrente resolveria melhor?
- Podemos usar imutabilidade?

---

# 8. Semaphore não é Lock

`Semaphore` possui outro objetivo.

Imagine um serviço externo que suporta no máximo 20 chamadas concorrentes.

```java
Semaphore semaphore = new Semaphore(20);
```

Cada requisição adquire um permit antes de chamar o serviço e o devolve depois.

O objetivo é:

```text
1000 tarefas
     ↓
Semaphore: 20 permits
     ↓
máximo 20 chamadas simultâneas
```

Esse padrão se torna especialmente importante com Virtual Threads, porque poder criar milhares de threads não significa que o banco, uma API externa ou outro recurso suporta milhares de operações simultâneas. A própria documentação da Oracle recomenda usar `Semaphore` para limitar concorrência com Virtual Threads, em vez de criar pools de Virtual Threads apenas para limitar a quantidade de tarefas. 

---

# 9. ForkJoinPool

O `ForkJoinPool` foi projetado principalmente para tarefas divisíveis.

Imagine:

```text
Problema grande
      │
  ┌───┴───┐
Task A   Task B
  │        │
┌─┴─┐    ┌─┴─┐
A1 A2    B1 B2
```

Ele utiliza **work stealing**.

Se uma worker termina suas tarefas, pode buscar trabalho das filas de outras workers. Isso é particularmente interessante para tarefas CPU-bound e divide-and-conquer. 

Por isso, uma regra prática é:

```text
ForkJoinPool
     ↓
CPU-bound / tarefas divisíveis

Virtual Threads
     ↓
I/O-bound / tarefas bloqueantes
```

---

# 10. Virtual Threads

Virtual Threads precisam fazer parte do repertório Java moderno.

Uma platform thread tradicional fica associada a uma OS thread durante sua execução.

Virtual Threads são gerenciadas pelo runtime Java e podem ser suspensas quando encontram operações bloqueantes suportadas, permitindo que a thread de sistema operacional execute outras Virtual Threads. 

Isso torna possível trabalhar com:

```text
1 request
    ↓
1 virtual thread
```

em grande escala.

Exemplo:

```java
try (var executor =
        Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> repository.findById(id));
}
```

A Oracle recomenda Virtual Threads principalmente para aplicações com muitas tarefas concorrentes que passam boa parte do tempo esperando I/O; elas aumentam **throughput e escalabilidade**, mas não fazem uma tarefa individual executar mais rápido. 

Portanto:

```text
CPU-bound

1000 Virtual Threads
        ↓
não criam 1000 CPUs
```

Se existem 8 cores disponíveis, o paralelismo computacional continua limitado pelo hardware.

---

# 11. Regra moderna importante sobre Virtual Threads

Não pense:

```text
Virtual Threads
      =
thread pool gigante
```

O modelo recomendado é mais próximo de:

```text
uma tarefa
    ↓
uma Virtual Thread
```

A Oracle explicitamente orienta a **não criar pools de Virtual Threads para reutilizá-las**, porque elas são suficientemente leves para representar diretamente as tarefas. Caso seja necessário limitar acesso a um recurso externo, utilize o mecanismo correspondente, como `Semaphore` ou o próprio connection pool do banco. 

Esse é um ponto muito bom para entrevista porque demonstra conhecimento de Java moderno.

---

# 12. Resposta objetiva para entrevista

Se o entrevistador perguntar **"Como você trabalha com concorrência em Java?"**, uma resposta boa seria:

> Em Java eu tento primeiro evitar estado mutável compartilhado, utilizando imutabilidade e componentes stateless sempre que possível. Quando existe compartilhamento, preciso garantir atomicidade e visibilidade de acordo com o Java Memory Model.
>
> Para sincronização simples posso usar `synchronized`; `volatile` é adequado principalmente para visibilidade de estados simples, mas não torna operações compostas atômicas. Para casos mais avançados posso usar `ReentrantLock`, e para variáveis isoladas geralmente prefiro classes atômicas como `AtomicInteger`. Happens-before é o conceito que determina quando alterações feitas por uma thread são garantidamente visíveis para outra. 
>
> Para execução de tarefas utilizo `ExecutorService`, `Future` ou `CompletableFuture`, dependendo da necessidade de composição assíncrona. Para processamento CPU-bound divisível, `ForkJoinPool` pode fazer sentido. 
>
> Em aplicações Java modernas também considero Virtual Threads, principalmente para workloads I/O-bound com muitas operações bloqueantes, como HTTP e JDBC. Elas melhoram escalabilidade e throughput, mas não tornam processamento CPU-bound mais rápido. E eu não uso pool de Virtual Threads apenas para limitar concorrência; se preciso proteger um recurso escasso, uso mecanismos como `Semaphore` ou o próprio pool do recurso. 
>
> Por fim, em produção eu observo problemas como race condition, deadlock, starvation e principalmente contention, porque adicionar mais threads não significa necessariamente aumentar throughput.