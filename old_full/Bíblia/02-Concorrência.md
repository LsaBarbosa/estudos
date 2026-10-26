  
---

# Concorrência em Java

<a id="indice"></a>

## Índice

- [1. Visão Geral](#visao-geral)
- [2. Conceitos Fundamentais](#conceitos-fundamentais)
    - [2.1 Thread](#thread)
    - [2.2 Runnable](#runnable)
    - [2.3 Callable](#callable)
    - [2.4 Future](#future)
    - [2.5 CompletableFuture](#completablefuture)
- [3. Problemas de Concorrência](#problemas-concorrencia)
    - [3.1 Race Condition](#race-condition)
    - [3.2 Deadlock](#deadlock)
    - [3.3 Livelock](#livelock)
    - [3.4 Contention](#contention)
- [4. Java Memory Model](#jmm)
    - [4.1 Visibilidade](#visibilidade)
    - [4.2 Happens-before](#happens-before)
- [5. Sincronização](#sincronizacao)
    - [5.1 synchronized](#synchronized)
    - [5.2 volatile](#volatile)
    - [5.3 ReentrantLock](#reentrantlock)
    - [5.4 AtomicInteger](#atomicinteger)
    - [5.5 ConcurrentHashMap](#concurrenthashmap)
- [6. Virtual Threads](#virtual-threads)
    - [6.1 Funcionamento](#virtual-funcionamento)
    - [6.2 CPU-bound vs I/O-bound](#cpu-io)
    - [6.3 Carrier Thread](#carrier)
    - [6.4 Pinning: Java 21 vs 25](#pinning)
    - [6.5 Trade-offs](#virtual-tradeoffs)
- [7. Diagnóstico em Produção](#producao)
- [8. Resposta Completa de Entrevista](#resposta-entrevista)

---

<a id="visao-geral"></a>
## 1. Visão Geral

Concorrência é a capacidade de uma aplicação lidar com **múltiplas tarefas progredindo durante o mesmo período de tempo**.

Concorrência não significa necessariamente paralelismo:

```text
Concorrência
    │
    └── múltiplas tarefas em progresso

Paralelismo
    │
    └── múltiplas tarefas executando simultaneamente
        em diferentes CPUs/cores
```

No Java moderno, os principais mecanismos são:

```text
Threads
   │
   ├── Platform Threads
   └── Virtual Threads

Tasks
   │
   ├── Runnable
   └── Callable

Execução
   │
   └── ExecutorService

Resultado assíncrono
   │
   ├── Future
   └── CompletableFuture

Sincronização
   │
   ├── synchronized
   ├── Lock
   ├── volatile
   ├── Atomic*
   └── Concurrent Collections
```

### Resposta de entrevista

> Concorrência em Java é a execução coordenada de múltiplas tarefas durante o mesmo período. O Java fornece abstrações como Threads, ExecutorService, locks, atomics e coleções concorrentes. O principal desafio é coordenar acesso a estado compartilhado garantindo atomicidade, visibilidade e ordenação sem gerar contenção excessiva.

[↑ Índice](#indice)

---

<a id="conceitos-fundamentais"></a>
# 2. Conceitos Fundamentais

<a id="thread"></a>
## 2.1 Thread

Uma `Thread` representa um **fluxo de execução**.

É importante distinguir:

```text
Platform Thread
      ↓
associada a uma OS Thread

Virtual Thread
      ↓
gerenciada pela JVM
executada sobre carrier threads
```

Platform Threads possuem custo relevante de:

- stack;
- memória;
- escalonamento pelo SO;
- context switching.

Por isso normalmente não criamos centenas de milhares de Platform Threads.

### Resposta de entrevista

> Uma Thread representa um fluxo de execução. Platform Threads são relativamente caras porque dependem de threads do sistema operacional, enquanto Virtual Threads são muito mais leves e gerenciadas pela JVM.

---

<a id="runnable"></a>
## 2.2 Runnable

Representa uma **tarefa que executa código e não retorna resultado**.

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

---

<a id="callable"></a>
## 2.3 Callable

Representa uma tarefa que:

- retorna resultado;
- pode lançar exceptions.

```java
Callable<Integer> task = () -> {
    return 10;
};
```

Assinatura:

```java
V call() throws Exception;
```

É muito utilizada com:

```java
ExecutorService.submit(...)
```

que retorna um:

```java
Future<V>
```

### Resposta de entrevista

> Callable é semelhante ao Runnable, mas permite retornar um resultado e lançar exceptions. Quando submetido a um ExecutorService, normalmente recebo um Future representando o resultado futuro.

---

<a id="future"></a>
## 2.4 Future

Representa o **resultado de uma computação assíncrona**.

```java
Future<Integer> future = executor.submit(task);

Integer resultado = future.get();
```

Ponto importante:

```java
future.get()
```

pode **bloquear a thread atual** até o resultado ficar disponível.

Também permite:

```java
future.isDone();
future.cancel(true);
```

### Trade-off

`Future` representa bem um resultado futuro, mas possui capacidade limitada de **composição**.

### Resposta de entrevista

> Future representa o resultado de uma operação assíncrona. O principal cuidado é que `get()` pode bloquear até a operação terminar. Para fluxos assíncronos mais complexos normalmente utilizo CompletableFuture.

---

<a id="completablefuture"></a>
## 2.5 CompletableFuture

`CompletableFuture` estende a ideia de `Future`, permitindo criar **pipelines assíncronos**.

Exemplo:

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

Permite fluxos poderosos, mas pipelines muito extensos podem ficar:

- difíceis de ler;
- difíceis de depurar;
- difíceis de rastrear.

### Resposta de entrevista

> CompletableFuture permite compor operações assíncronas sem precisar bloquear explicitamente esperando cada etapa. É útil para executar tarefas independentes e combinar seus resultados, mas pipelines excessivamente complexos prejudicam legibilidade e observabilidade.

[↑ Índice](#indice)

---

<a id="problemas-concorrencia"></a>
# 3. Problemas de Concorrência

<a id="race-condition"></a>
## 3.1 Race Condition

O resultado depende da **ordem imprevisível de execução das threads** sobre estado compartilhado.

Exemplo:

```java
saldo = saldo - valor;
```

Isso parece uma operação única, mas conceitualmente envolve:

```text
ler saldo
   ↓
calcular novo saldo
   ↓
escrever saldo
```

Duas threads podem intercalar essas operações.

### Consequência

- valores incorretos;
- perda de atualizações;
- bugs intermitentes;
- difícil reprodução.

### Soluções

Dependendo do problema:

```text
synchronized
ReentrantLock
Atomic*
ConcurrentHashMap
imutabilidade
redução de estado compartilhado
```

### Regra mental

> **Race Condition → sincronizar ou eliminar o estado compartilhado.**

### Resposta de entrevista

> Race Condition ocorre quando múltiplas threads acessam estado compartilhado e o resultado depende da ordem de execução. Eu resolveria garantindo atomicidade através de synchronized, locks, atomics ou reduzindo o compartilhamento de estado.

---

<a id="deadlock"></a>
## 3.2 Deadlock

Duas ou mais threads ficam esperando indefinidamente por recursos umas das outras.

```text
Thread A
Lock 1 adquirido
      ↓
espera Lock 2


Thread B
Lock 2 adquirido
      ↓
espera Lock 1
```

Nenhuma consegue progredir.

### Prevenção

Principal estratégia:

```text
adquirir locks sempre na mesma ordem
```

Também ajuda:

- reduzir locks aninhados;
- diminuir tempo mantendo locks;
- utilizar `tryLock()`;
- usar timeout quando adequado.

### Regra mental

> **Deadlock → ordenar aquisição de locks.**

### Resposta de entrevista

> Deadlock acontece quando threads criam uma dependência circular esperando locks umas das outras. A principal prevenção é manter uma ordem consistente de aquisição de locks e evitar locking aninhado desnecessário.

---

<a id="livelock"></a>
## 3.3 Livelock

As threads **não estão bloqueadas**, mas continuam reagindo umas às outras sem conseguir progredir.

```text
Thread A → cede recurso
Thread B → cede recurso
Thread A → cede novamente
Thread B → cede novamente
...
```

### Soluções

- backoff;
- jitter;
- retries limitados;
- evitar políticas excessivamente cooperativas.

### Regra mental

> **Deadlock:** ninguém executa.  
> **Livelock:** todos executam, mas ninguém progride.

### Resposta de entrevista

> No livelock as threads continuam executando, diferente do deadlock, mas suas ações impedem progresso real. Backoff, jitter e políticas de retry limitadas ajudam a quebrar esse ciclo.

---

<a id="contention"></a>
## 3.4 Contention

Ocorre quando várias threads disputam o **mesmo recurso limitado**.

Exemplo:

```java
synchronized (lock) {
    operacaoDemorada();
}
```

Se muitas threads precisam desse monitor:

```text
Thread 1 ─┐
Thread 2 ─┤
Thread 3 ─┼──► LOCK ───► execução serial
Thread 4 ─┤
Thread 5 ─┘
```

### Consequências

- aumento de espera;
- queda de throughput;
- aumento de latência;
- redução da escalabilidade.

### Soluções

- reduzir região crítica;
- diminuir estado compartilhado;
- reduzir tempo segurando o lock;
- locks mais granulares;
- estruturas concorrentes apropriadas.

### Regra mental

> **Contention → reduzir disputa pelo recurso.**

[↑ Índice](#indice)

---

<a id="jmm"></a>
# 4. Java Memory Model

<a id="visibilidade"></a>
## 4.1 Visibilidade

Uma thread modificar uma variável **não significa automaticamente** que outra thread observará imediatamente aquela alteração.

O Java Memory Model define as garantias necessárias para comunicação segura entre threads.

Problema conceitual:

```text
Thread A
valor = 10

Thread B
valor = ?
```

Sem sincronização adequada, não existe necessariamente a garantia de visibilidade desejada.

---

<a id="happens-before"></a>
## 4.2 Happens-before

`happens-before` é uma relação definida pelo **Java Memory Model** que fornece garantias de:

- visibilidade;
- ordenação de memória.

Se:

```text
A happens-before B
```

os efeitos de memória relevantes de `A` são visíveis para `B`.

Exemplos importantes:

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

Também existem garantias relacionadas a:

```java
Thread.start()
Thread.join()
```

### Resposta de entrevista

> Happens-before é uma regra do Java Memory Model que define quando os efeitos de uma operação são garantidamente visíveis para outra. Por exemplo, liberar um monitor acontece antes de uma aquisição posterior do mesmo monitor, e uma escrita volatile acontece antes de uma leitura posterior da mesma variável.

[↑ Índice](#indice)

---

<a id="sincronizacao"></a>
# 5. Sincronização

<a id="synchronized"></a>
## 5.1 synchronized

`synchronized` utiliza um **monitor** para proteger uma região crítica.

Fornece duas garantias principais:

### Exclusão mútua

Para o mesmo monitor:

```text
Thread A ──► synchronized
Thread B ──► espera
```

Somente uma thread executa aquela região crítica por vez.

### Visibilidade

Existe happens-before entre:

```text
liberação do monitor
        ↓
aquisição posterior do mesmo monitor
```

Portanto, alterações realizadas pela thread que liberou o monitor ficam visíveis para quem o adquirir posteriormente.

### Trade-off

Quanto maior a contenção:

```text
mais espera
   ↓
menos paralelismo
   ↓
maior latência
```

### Importante

`synchronized` só protege corretamente o estado se o acesso concorrente seguir **a mesma estratégia de sincronização**.

### Resposta de entrevista

> synchronized fornece exclusão mútua e garantia de visibilidade através do monitor. É simples e seguro para proteger regiões críticas, mas alta contenção sobre o mesmo monitor pode reduzir paralelismo e throughput.

---

<a id="volatile"></a>
## 5.2 volatile

`volatile` fornece principalmente:

- visibilidade;
- garantias de ordenação.

Exemplo:

```java
private volatile boolean running = true;
```

Uma escrita:

```java
running = false;
```

fica visível para outras threads que posteriormente leem aquela variável.

### Não garante atomicidade composta

Isto continua problemático:

```java
volatile int contador;

contador++;
```

Porque `contador++` envolve:

```text
ler
↓
incrementar
↓
escrever
```

### Quando usar

Bom para:

- flags;
- estados simples;
- publicação de valores.

### Trade-off

É mais leve que lock para casos simples, mas **não resolve invariantes envolvendo múltiplas operações**.

### Resposta de entrevista

> volatile garante visibilidade e ordenação de memória para uma variável, mas não transforma operações compostas em atômicas. Por isso funciona bem para flags e estados simples, mas não substitui locks ou atomics quando preciso de operações read-modify-write.

---

<a id="reentrantlock"></a>
## 5.3 ReentrantLock

Fornece exclusão mútua semelhante ao `synchronized`, mas oferece maior controle.

```java
lock.lock();

try {
    alterarEstado();
} finally {
    lock.unlock();
}
```

Recursos importantes:

```java
tryLock()
tryLock(timeout)
lockInterruptibly()
Condition
```

Também suporta política opcional de fairness.

### Trade-off

Mais flexível, porém:

- mais código;
- maior complexidade;
- `unlock()` precisa ser feito explicitamente.

Por isso normalmente:

```java
try {
   ...
} finally {
   lock.unlock();
}
```

### Resposta de entrevista

> ReentrantLock fornece exclusão mútua como synchronized, mas permite maior controle, como tryLock, timeout, interrupção e Conditions. O trade-off é maior complexidade e a obrigação de liberar explicitamente o lock.

---

<a id="atomicinteger"></a>
## 5.4 AtomicInteger

Fornece operações atômicas sobre um `int` compartilhado.

```java
AtomicInteger contador = new AtomicInteger();

contador.incrementAndGet();
```

Operações como:

```java
incrementAndGet()
compareAndSet()
getAndIncrement()
```

podem ser realizadas atomicamente sem precisar proteger toda a operação com um lock tradicional.

Internamente, operações desse tipo utilizam mecanismos atômicos disponibilizados pela JVM/CPU, frequentemente baseados em **CAS — Compare-And-Set**.

### Bom para

```text
contadores
sequências
flags numéricas
estado simples
```

### Não substitui locks quando existe:

```text
invariante envolvendo múltiplas variáveis
```

Exemplo:

```java
saldo -= valor;
limite += valor;
```

Manter as duas alterações consistentes exige uma estratégia de sincronização mais ampla.

### Resposta de entrevista

> AtomicInteger é adequado para operações atômicas simples sobre um inteiro compartilhado. Evita locking explícito em muitos casos, mas não substitui locks quando preciso manter invariantes envolvendo múltiplos estados.

---

<a id="concurrenthashmap"></a>
## 5.5 ConcurrentHashMap

É uma implementação de `Map` **thread-safe**, projetada para alto acesso concorrente.

```java
ConcurrentHashMap<String, Cliente> clientes =
        new ConcurrentHashMap<>();
```

Fornece operações atômicas importantes:

```java
putIfAbsent()
compute()
computeIfAbsent()
merge()
replace()
```

Evita a necessidade de bloquear o mapa inteiro para a maioria das operações.

### Cuidado

Isto:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

é uma sequência de duas operações.

Outra thread pode interferir entre elas.

Prefira:

```java
map.putIfAbsent(key, value);
```

### Trade-off

Ganha:

- thread-safety;
- escalabilidade concorrente.

Custa:

- maior overhead;
- comportamento mais complexo que `HashMap`.

### Resposta de entrevista

> ConcurrentHashMap é um Map thread-safe otimizado para concorrência. Ele permite que múltiplas threads trabalhem no mapa sem serializar todas as operações em um lock global e oferece operações compostas atômicas, como putIfAbsent e compute.

[↑ Índice](#indice)

---

<a id="virtual-threads"></a>
# 6. Virtual Threads

Virtual Threads foram finalizadas como recurso da plataforma no **Java 21** pela JEP 444. :chatgpt-content-reference{index="1"}

Elas são threads leves gerenciadas pela JVM.

```text
Platform Threads

Task ──► OS Thread
Task ──► OS Thread
Task ──► OS Thread
```

Com Virtual Threads:

```text
Virtual Thread ─┐
Virtual Thread ─┤
Virtual Thread ─┼──► conjunto menor de Carrier Threads
Virtual Thread ─┤
Virtual Thread ─┘
```

---

<a id="virtual-funcionamento"></a>
## 6.1 Funcionamento

Quando uma Virtual Thread executa normalmente:

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
      ↓
outra Virtual Thread pode executar
```

É justamente esse comportamento que permite manter um número muito grande de tarefas concorrentes. A documentação do Java 25 descreve o mounting/unmounting das Virtual Threads sobre carrier threads dessa forma. :chatgpt-content-reference{index="2"}

---

<a id="cpu-io"></a>
## 6.2 CPU-bound vs I/O-bound

Virtual Threads são especialmente úteis para tarefas:

```text
I/O-bound
```

Exemplos:

- chamadas HTTP;
- banco de dados;
- acesso a arquivos;
- chamadas para APIs externas.

### Não tornam CPU-bound automaticamente mais rápido

Se você possui:

```text
8 CPU cores
```

criar:

```text
100.000 Virtual Threads
```

não significa executar 100.000 operações CPU-bound simultaneamente.

O paralelismo continua limitado principalmente pela capacidade da CPU.

### Resposta de entrevista

> Virtual Threads aumentam principalmente a escalabilidade de workloads I/O-bound. Elas não tornam código CPU-bound mais rápido, porque o paralelismo de CPU continua limitado pelos cores disponíveis.

---

<a id="carrier"></a>
## 6.3 Carrier Thread

Carrier Thread é uma **Platform Thread utilizada pela JVM para executar uma Virtual Thread**.

```text
Virtual Thread
      ↓
montada sobre
      ↓
Carrier Thread
      ↓
OS Thread
```

A aplicação normalmente **não deve depender de qual carrier está executando determinada Virtual Thread**.

---

<a id="pinning"></a>
## 6.4 Pinning — Java 21 vs Java 25

### Java 21

Uma limitação importante era:

```java
synchronized (lock) {
    chamadaBloqueante();
}
```

Se a Virtual Thread bloqueasse enquanto estivesse dentro daquele `synchronized`, ela poderia ficar **pinned** à carrier thread.

Resultado:

```text
Virtual Thread bloqueada
        +
Carrier Thread bloqueada
```

Isso podia reduzir escalabilidade. :chatgpt-content-reference{index="3"}

### Java 24+

A JEP 491 alterou o runtime para permitir que Virtual Threads bloqueadas em construções `synchronized` liberem a Platform Thread subjacente. Essa melhoria está presente no Java 25. :chatgpt-content-reference{index="4"}

Portanto, sua anotação deve mudar de:

> Java 25 reduziu problemas de pinning com synchronized.

para algo mais preciso:

> A partir do JDK 24, `synchronized` deixou de ser uma causa normal de pinning de Virtual Threads. No Java 25, pinning ainda pode ocorrer principalmente em operações envolvendo código `native` ou Foreign Functions. :chatgpt-content-reference{index="5"}

### Resposta de entrevista

> No Java 21, uma Virtual Thread podia ficar presa à carrier thread ao bloquear dentro de synchronized. A JEP 491 resolveu esse caso a partir do JDK 24. No Java 25, synchronized normalmente não causa mais esse pinning, embora ele ainda possa existir em chamadas native ou foreign.

---

<a id="virtual-tradeoffs"></a>
## 6.5 Trade-offs de Virtual Threads

O principal ganho é:

```text
mais concorrência
com muito menos Platform Threads
```

Mas Virtual Threads **não removem limites externos**.

Exemplo:

```text
100.000 Virtual Threads
         ↓
pool JDBC com 30 conexões
         ↓
somente ~30 operações simultâneas no banco
```

O gargalo continua existindo.

Por isso, recursos externos podem precisar de:

```text
Semaphore
Rate Limiter
limites de conexão
backpressure
```

### Outro ponto importante

Virtual Threads são baratas.

A ideia normalmente é:

```text
uma Virtual Thread por tarefa
```

e não criar um pool pequeno de Virtual Threads apenas para limitar concorrência.

### Resposta de entrevista

> Virtual Threads tornam barato ter muitas tarefas concorrentes, mas não removem limites de recursos externos. Se meu banco possui 30 conexões, criar milhares de Virtual Threads não aumenta esse limite. Nesse caso eu controlo a concorrência sobre o recurso, por exemplo com Semaphore ou pelo próprio pool de conexões.

[↑ Índice](#indice)

---

<a id="producao"></a>
# 7. Diagnóstico em Produção

Quando há problema de concorrência, eu começo pelo **sintoma**, e não escolhendo uma primitiva de sincronização imediatamente.

```text
CPU alta
     ↓
verificar threads executando / loops / contention


Latência alta
     ↓
thread dumps / JFR / pools / locks


Threads BLOCKED
     ↓
investigar lock contention


Aplicação travada
     ↓
investigar deadlock


Fila de Executor crescendo
     ↓
executor saturado ou dependência lenta


Muitas Virtual Threads esperando
     ↓
verificar recurso externo:
DB / HTTP / semaphore / rate limit
```

Ferramentas relevantes:

```text
Thread Dump
JFR
jcmd
métricas da aplicação
métricas de pools
CPU
p95 / p99
```

### Resposta de entrevista

> Em produção eu começaria observando o sintoma e as métricas. Para suspeita de bloqueio ou contenção, utilizaria Thread Dumps ou JFR para identificar estados das threads, locks e stacks. Também verificaria pools e dependências externas antes de atribuir o problema simplesmente ao número de threads.

[↑ Índice](#indice)

---

<a id="resposta-entrevista"></a>
# 8. Resposta Completa para Entrevista

Se perguntarem:

**“O que você entende sobre concorrência em Java?”**

Uma resposta objetiva seria:

> Concorrência em Java envolve executar e coordenar múltiplas tarefas durante o mesmo período. Para representar tarefas eu posso utilizar Runnable ou Callable e normalmente delego sua execução para abstrações apropriadas, como ExecutorService. Future representa um resultado assíncrono e CompletableFuture permite compor pipelines assíncronos.
>
> Quando existe estado compartilhado, os principais problemas são race condition, deadlock, livelock e contention. Para coordenar esse acesso posso usar synchronized, ReentrantLock, atomics ou estruturas como ConcurrentHashMap, escolhendo de acordo com a granularidade e as invariantes que preciso proteger.
>
> Também é importante entender o Java Memory Model. Happens-before estabelece garantias de visibilidade e ordenação entre threads, como ocorre na liberação e aquisição de um monitor ou em escrita e leitura de volatile.
>
> No Java moderno também temos Virtual Threads, que permitem trabalhar com um número muito maior de tarefas concorrentes principalmente em workloads I/O-bound. Elas não tornam processamento CPU-bound mais rápido e também não eliminam limites externos, como pools de conexão com banco de dados.
>
> Em produção eu observo métricas, Thread Dumps e JFR para identificar contention, bloqueios, deadlocks, saturação de executores ou dependências externas antes de decidir qualquer ajuste.

---

## Mapa mental para entrevista

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
│   ├── Race Condition → proteger estado compartilhado
│   ├── Deadlock       → ordenar locks
│   ├── Livelock       → backoff / jitter
│   └── Contention     → reduzir disputa
│
├── JMM
│   ├── Visibilidade
│   └── Happens-before
│
├── Sincronização
│   ├── synchronized   → simples + monitor
│   ├── volatile       → visibilidade, não atomicidade composta
│   ├── ReentrantLock  → maior controle
│   ├── AtomicInteger  → estado simples atômico
│   └── ConcurrentHashMap → Map concorrente
│
└── Virtual Threads
    ├── leves
    ├── I/O-bound
    ├── mount / unmount
    ├── carrier thread
    ├── não aceleram CPU-bound
    └── não removem limites externos
```

**Uma mudança de terminologia que vale memorizar:** não diga apenas que "`Thread` possui alto custo de memória". Em entrevista, diga **“Platform Threads são relativamente caras; Virtual Threads são leves”**. Isso evita uma generalização que deixou de ser correta no Java moderno.