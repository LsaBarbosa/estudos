Lucas, correção do termo: **Concorrência e Paralelismo**.
Esse tema é importante para entrevistas Java porque aparece dentro de **Java 17+, concorrência básica, boas práticas, microsserviços, mensageria, performance e sistemas distribuídos**.  Também está alinhado ao seu plano de estudo técnico em Java, arquitetura, sistemas distribuídos e performance.

# 🇧🇷 Versão em Português — Mapa Mental em Tabelas

## 1. Visão geral

| Nó central       | Resumo                                                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Concorrência** | Capacidade de lidar com várias tarefas em andamento ao mesmo tempo, mesmo que não executem exatamente no mesmo instante. |
| **Paralelismo**  | Execução real de várias tarefas ao mesmo tempo, geralmente usando múltiplos núcleos de CPU.                              |
| Ideia principal  | Concorrência é sobre **organização**; paralelismo é sobre **execução simultânea**.                                       |
| Em Java          | Threads, ExecutorService, CompletableFuture, Locks, Atomic, Concurrent Collections, Virtual Threads.                     |
| Onde aparece     | APIs REST, filas, processamento em lote, chamadas externas, tarefas assíncronas, sistemas distribuídos.                  |
| Principal risco  | Bugs difíceis: race condition, deadlock, starvation, inconsistência e problemas de visibilidade.                         |

---

## 2. Diferença entre concorrência e paralelismo

| Conceito                        | Explicação                                               | Exemplo                                                      |
| ------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| **Concorrência**                | Várias tarefas progridem alternando o uso do processador | Um único caixa atendendo vários clientes por revezamento     |
| **Paralelismo**                 | Várias tarefas executam literalmente ao mesmo tempo      | Vários caixas atendendo vários clientes simultaneamente      |
| Concorrência sem paralelismo    | Possível                                                 | Uma CPU alternando entre threads                             |
| Paralelismo exige concorrência? | Geralmente sim                                           | Para executar em paralelo, há múltiplas tarefas concorrentes |
| Foco da concorrência            | Estruturação do programa                                 | Não bloquear, coordenar tarefas                              |
| Foco do paralelismo             | Acelerar execução                                        | Usar múltiplos núcleos                                       |

---

## 3. Analogia rápida

| Situação                      | Concorrência                                    | Paralelismo                       |
| ----------------------------- | ----------------------------------------------- | --------------------------------- |
| Restaurante com 1 cozinheiro  | O cozinheiro alterna entre vários pratos        | Não há execução simultânea real   |
| Restaurante com 4 cozinheiros | Cada cozinheiro prepara um prato ao mesmo tempo | Há paralelismo                    |
| Sistema com 1 core            | Threads alternam execução                       | Concorrência sem paralelismo real |
| Sistema com 8 cores           | Várias threads podem rodar ao mesmo tempo       | Concorrência com paralelismo      |

---

## 4. Mapa mental principal

| Centro                         | Ramos principais  |
| ------------------------------ | ----------------- |
| **Concorrência e Paralelismo** | Threads           |
|                                | ExecutorService   |
|                                | CompletableFuture |
|                                | Locks             |
|                                | Atomicidade       |
|                                | Visibilidade      |
|                                | Race condition    |
|                                | Deadlock          |
|                                | Thread safety     |
|                                | CPU-bound         |
|                                | I/O-bound         |
|                                | Virtual Threads   |
|                                | Observabilidade   |

---

# 5. Threads

## 5.1 Conceito

| Conceito                  | Explicação                                                      |
| ------------------------- | --------------------------------------------------------------- |
| **Thread**                | Unidade de execução dentro de um processo.                      |
| Processo                  | Programa em execução, com memória própria.                      |
| Thread dentro de processo | Compartilha memória com outras threads do mesmo processo.       |
| Benefício                 | Permite executar tarefas de forma concorrente.                  |
| Risco                     | Como threads compartilham memória, podem causar inconsistência. |

---

## 5.2 Exemplo simples em Java

```java
public class ThreadExample {

    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Executando em outra thread: " + Thread.currentThread().getName());
        });

        thread.start();

        System.out.println("Executando na thread principal: " + Thread.currentThread().getName());
    }
}
```

| Parte                              | Explicação                            |
| ---------------------------------- | ------------------------------------- |
| `new Thread(...)`                  | Cria uma nova thread                  |
| `Runnable`                         | Código que será executado pela thread |
| `thread.start()`                   | Inicia a execução concorrente         |
| `Thread.currentThread().getName()` | Mostra a thread atual                 |
| `main`                             | Thread principal da aplicação         |

---

## 5.3 Erro comum: usar `run()` diretamente

```java
Thread thread = new Thread(() -> {
    System.out.println("Executando tarefa");
});

thread.run(); // errado para concorrência
```

| Método    | O que faz                                          |
| --------- | -------------------------------------------------- |
| `start()` | Cria uma nova thread e executa o código nela       |
| `run()`   | Executa como método comum, na thread atual         |
| Regra     | Para concorrência real com `Thread`, use `start()` |

---

# 6. ExecutorService

## 6.1 Conceito

| Conceito            | Explicação                                                      |
| ------------------- | --------------------------------------------------------------- |
| **ExecutorService** | Abstração para executar tarefas usando um pool de threads.      |
| Vantagem            | Evita criar threads manualmente o tempo todo.                   |
| Pool de threads     | Conjunto reutilizável de threads.                               |
| Melhor uso          | Processamento controlado de múltiplas tarefas.                  |
| Cuidado             | Pool mal dimensionado pode causar lentidão, fila ou sobrecarga. |

---

## 6.2 Exemplo com pool fixo

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorServiceExample {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);

        for (int i = 1; i <= 10; i++) {
            int taskId = i;

            executor.submit(() -> {
                System.out.println("Tarefa " + taskId + " executada por " +
                        Thread.currentThread().getName());
            });
        }

        executor.shutdown();
    }
}
```

| Parte                   | Explicação                              |
| ----------------------- | --------------------------------------- |
| `newFixedThreadPool(4)` | Cria um pool com 4 threads              |
| `submit(...)`           | Envia uma tarefa para execução          |
| `executor.shutdown()`   | Encerra o pool após concluir as tarefas |
| `taskId`                | Variável local usada dentro da lambda   |
| Benefício               | Controla o número de threads ativas     |

---

## 6.3 Tipos comuns de pool

| Tipo                       | Uso                            | Cuidado                    |
| -------------------------- | ------------------------------ | -------------------------- |
| `newFixedThreadPool`       | Número fixo de threads         | Pode criar fila grande     |
| `newCachedThreadPool`      | Cria threads conforme demanda  | Pode crescer demais        |
| `newSingleThreadExecutor`  | Uma tarefa por vez             | Pode virar gargalo         |
| `ScheduledExecutorService` | Tarefas agendadas              | Cuidado com tarefas longas |
| Virtual thread executor    | Muitas tarefas bloqueantes/I/O | Exige Java moderno         |

---

# 7. CPU-bound vs I/O-bound

## 7.1 Diferença

| Tipo          | Gargalo principal                          | Exemplo                                                           |
| ------------- | ------------------------------------------ | ----------------------------------------------------------------- |
| **CPU-bound** | Processador                                | Cálculo pesado, compressão, criptografia, processamento de imagem |
| **I/O-bound** | Espera externa                             | Banco de dados, API externa, arquivo, rede, fila                  |
| CPU-bound     | Paralelismo ajuda até o limite dos núcleos | Usar muitas threads pode piorar                                   |
| I/O-bound     | Mais concorrência pode ajudar              | Threads ficam esperando resposta externa                          |

---

## 7.2 Regra prática para número de threads

| Cenário                | Estratégia                                                         |
| ---------------------- | ------------------------------------------------------------------ |
| CPU-bound              | Aproximar número de threads ao número de cores                     |
| I/O-bound              | Pode usar mais threads que cores                                   |
| Chamadas a banco       | Limite também depende do pool de conexões                          |
| Chamadas HTTP externas | Limite depende de timeout, rate limit e pool HTTP                  |
| Fila/mensageria        | Limite depende de throughput, idempotência e capacidade downstream |

---

# 8. Race condition

## 8.1 Conceito

| Conceito           | Explicação                                                                            |
| ------------------ | ------------------------------------------------------------------------------------- |
| **Race condition** | Bug causado quando múltiplas threads acessam e alteram o mesmo estado ao mesmo tempo. |
| Sintoma            | Resultado inconsistente e difícil de reproduzir.                                      |
| Causa comum        | Operações compostas não atômicas.                                                     |
| Exemplo clássico   | Incrementar contador compartilhado.                                                   |

---

## 8.2 Exemplo com problema

```java
public class Counter {

    private int value = 0;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }
}
```

| Linha                 | Problema                                       |
| --------------------- | ---------------------------------------------- |
| `value++`             | Parece uma operação única, mas não é           |
| Internamente          | Lê valor, soma 1, grava valor                  |
| Com múltiplas threads | Uma atualização pode sobrescrever outra        |
| Resultado             | Contador final pode ficar menor que o esperado |

---

## 8.3 Solução com `synchronized`

```java
public class SafeCounter {

    private int value = 0;

    public synchronized void increment() {
        value++;
    }

    public synchronized int getValue() {
        return value;
    }
}
```

| Parte               | Explicação                                      |
| ------------------- | ----------------------------------------------- |
| `synchronized`      | Garante exclusão mútua                          |
| Uma thread por vez  | Apenas uma thread executa o método sincronizado |
| Resolve atomicidade | Evita perda de atualização                      |
| Custo               | Pode reduzir paralelismo                        |

---

## 8.4 Solução com `AtomicInteger`

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounter {

    private final AtomicInteger value = new AtomicInteger(0);

    public void increment() {
        value.incrementAndGet();
    }

    public int getValue() {
        return value.get();
    }
}
```

| Parte               | Explicação                               |
| ------------------- | ---------------------------------------- |
| `AtomicInteger`     | Tipo thread-safe para operações atômicas |
| `incrementAndGet()` | Incrementa de forma atômica              |
| `get()`             | Lê o valor atual                         |
| Vantagem            | Mais simples para contadores             |
| Limite              | Não resolve regras compostas complexas   |

---

# 9. Atomicidade, visibilidade e ordenação

## 9.1 Conceitos importantes

| Conceito              | Explicação                                                                |
| --------------------- | ------------------------------------------------------------------------- |
| **Atomicidade**       | Operação acontece inteira ou não acontece.                                |
| **Visibilidade**      | Uma thread consegue enxergar mudanças feitas por outra.                   |
| **Ordenação**         | Compilador/CPU/JVM podem reordenar instruções, respeitando certas regras. |
| **Java Memory Model** | Define como threads interagem com memória em Java.                        |

---

## 9.2 Exemplo de problema de visibilidade

```java
public class Worker {

    private boolean running = true;

    public void stop() {
        running = false;
    }

    public void work() {
        while (running) {
            // executando
        }
    }
}
```

| Problema                   | Explicação                               |
| -------------------------- | ---------------------------------------- |
| `running` não é `volatile` | Outra thread pode não enxergar a mudança |
| Loop pode não parar        | A JVM pode otimizar leitura              |
| Bug difícil                | Depende de tempo, CPU e otimizações      |

---

## 9.3 Solução com `volatile`

```java
public class Worker {

    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    public void work() {
        while (running) {
            // executando
        }
    }
}
```

| Parte                            | Explicação                                      |
| -------------------------------- | ----------------------------------------------- |
| `volatile`                       | Garante visibilidade entre threads              |
| Bom para flags                   | Exemplo: parar execução                         |
| Não garante atomicidade composta | `volatile int count; count++` continua inseguro |
| Uso ideal                        | Estado simples de controle                      |

---

# 10. `synchronized`, `Lock` e `Atomic`

## 10.1 Comparação

| Recurso                  | Uso                            | Vantagem                           | Cuidado                                     |
| ------------------------ | ------------------------------ | ---------------------------------- | ------------------------------------------- |
| `synchronized`           | Proteger bloco/método crítico  | Simples                            | Menos flexível                              |
| `ReentrantLock`          | Controle manual de lock        | Mais flexível                      | Precisa liberar no `finally`                |
| `AtomicInteger`          | Contadores e operações simples | Eficiente e simples                | Não serve para regras complexas             |
| `volatile`               | Visibilidade de estado simples | Leve                               | Não garante incremento atômico              |
| Collections concorrentes | Estruturas thread-safe         | Reduz necessidade de locks manuais | Ainda exige cuidado com operações compostas |

---

## 10.2 Exemplo com `ReentrantLock`

```java
import java.util.concurrent.locks.ReentrantLock;

public class LockCounter {

    private final ReentrantLock lock = new ReentrantLock();
    private int value = 0;

    public void increment() {
        lock.lock();

        try {
            value++;
        } finally {
            lock.unlock();
        }
    }

    public int getValue() {
        lock.lock();

        try {
            return value;
        } finally {
            lock.unlock();
        }
    }
}
```

| Parte           | Explicação                                |
| --------------- | ----------------------------------------- |
| `lock.lock()`   | Entra na seção crítica                    |
| `try/finally`   | Garante liberação do lock                 |
| `lock.unlock()` | Libera para outras threads                |
| Vantagem        | Controle maior que `synchronized`         |
| Cuidado         | Esquecer `unlock()` pode travar o sistema |

---

# 11. Deadlock

## 11.1 Conceito

| Conceito      | Explicação                                                                 |
| ------------- | -------------------------------------------------------------------------- |
| **Deadlock**  | Duas ou mais threads ficam esperando recursos umas das outras para sempre. |
| Sintoma       | Sistema trava parcialmente ou totalmente.                                  |
| Causa comum   | Locks adquiridos em ordens diferentes.                                     |
| Solução comum | Definir ordem fixa de aquisição de locks.                                  |

---

## 11.2 Exemplo conceitual

| Thread 1       | Thread 2       |
| -------------- | -------------- |
| Pega Lock A    | Pega Lock B    |
| Espera Lock B  | Espera Lock A  |
| Nunca continua | Nunca continua |

---

## 11.3 Prevenção

| Estratégia                   | Explicação                           |
| ---------------------------- | ------------------------------------ |
| Ordem fixa de locks          | Sempre adquirir locks na mesma ordem |
| Timeout em locks             | Evita espera infinita                |
| Reduzir escopo do lock       | Menos tempo segurando recurso        |
| Evitar locks aninhados       | Reduz complexidade                   |
| Usar estruturas concorrentes | Evita lock manual em muitos casos    |
| Monitorar thread dumps       | Ajuda detectar deadlocks em produção |

---

# 12. Starvation e livelock

| Problema                | Significado                                                                 | Exemplo                                                   |
| ----------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------- |
| **Starvation**          | Uma thread nunca consegue executar porque outras sempre têm prioridade      | Thread de baixa prioridade não recebe CPU                 |
| **Livelock**            | Threads continuam ativas, mas não fazem progresso real                      | Duas threads cedem recurso uma para outra indefinidamente |
| Diferença para deadlock | No deadlock, threads ficam paradas; no livelock, ficam ativas sem progresso |                                                           |
| Prevenção               | Justiça no lock, backoff, filas, redução de contenção                       |                                                           |

---

# 13. Thread safety

## 13.1 Conceito

| Conceito                     | Explicação                                                                 |
| ---------------------------- | -------------------------------------------------------------------------- |
| **Thread-safe**              | Código que funciona corretamente mesmo quando usado por múltiplas threads. |
| Estado imutável              | Geralmente mais seguro.                                                    |
| Estado compartilhado mutável | Principal fonte de bugs.                                                   |
| Regra mental                 | Quanto menos estado compartilhado, menor o risco.                          |

---

## 13.2 Estratégias para thread safety

| Estratégia               | Exemplo                                     |
| ------------------------ | ------------------------------------------- |
| Imutabilidade            | `record`, classes com campos `final`        |
| Variáveis locais         | Cada thread tem sua própria pilha           |
| Locks                    | `synchronized`, `ReentrantLock`             |
| Atomic                   | `AtomicInteger`, `AtomicLong`               |
| Collections concorrentes | `ConcurrentHashMap`, `CopyOnWriteArrayList` |
| Evitar compartilhamento  | Passar dados por mensagem/evento            |
| Transações no banco      | Controle de concorrência em persistência    |

---

# 14. Collections concorrentes

## 14.1 Comparação

| Collection              | Uso                                         | Observação                                                  |
| ----------------------- | ------------------------------------------- | ----------------------------------------------------------- |
| `ConcurrentHashMap`     | Map thread-safe para alta concorrência      | Melhor que `Collections.synchronizedMap` em muitos cenários |
| `CopyOnWriteArrayList`  | Lista com muitas leituras e poucas escritas | Escrita é cara                                              |
| `BlockingQueue`         | Coordenação produtor-consumidor             | Muito usada em pipelines internos                           |
| `ConcurrentLinkedQueue` | Fila não bloqueante                         | Boa para alta concorrência                                  |
| `SynchronizedList`      | Lista sincronizada simples                  | Pode ter contenção maior                                    |

---

## 14.2 Exemplo com `ConcurrentHashMap`

```java
import java.util.concurrent.ConcurrentHashMap;

public class ProductCache {

    private final ConcurrentHashMap<Long, String> cache = new ConcurrentHashMap<>();

    public String getProductName(Long productId) {
        return cache.computeIfAbsent(productId, id -> loadFromDatabase(id));
    }

    private String loadFromDatabase(Long productId) {
        return "Product " + productId;
    }
}
```

| Parte               | Explicação                                             |
| ------------------- | ------------------------------------------------------ |
| `ConcurrentHashMap` | Map seguro para acesso concorrente                     |
| `computeIfAbsent`   | Calcula valor se a chave não existir                   |
| Uso comum           | Cache local simples                                    |
| Cuidado             | Para cache real, considerar TTL, invalidação e memória |

---

# 15. CompletableFuture

## 15.1 Conceito

| Conceito              | Explicação                                                         |
| --------------------- | ------------------------------------------------------------------ |
| **CompletableFuture** | API Java para programação assíncrona e composição de tarefas.      |
| Uso comum             | Executar chamadas independentes em paralelo e combinar resultados. |
| Benefício             | Melhor aproveitamento quando há chamadas I/O-bound.                |
| Cuidado               | Pode esconder complexidade, exceções e uso indevido de pools.      |

---

## 15.2 Exemplo: chamadas independentes

```java
import java.util.concurrent.CompletableFuture;

public class ProductPageService {

    public ProductPageResponse loadPage(Long productId) {
        CompletableFuture<Product> productFuture =
                CompletableFuture.supplyAsync(() -> findProduct(productId));

        CompletableFuture<List<Review>> reviewsFuture =
                CompletableFuture.supplyAsync(() -> findReviews(productId));

        CompletableFuture<Stock> stockFuture =
                CompletableFuture.supplyAsync(() -> findStock(productId));

        CompletableFuture.allOf(productFuture, reviewsFuture, stockFuture).join();

        return new ProductPageResponse(
                productFuture.join(),
                reviewsFuture.join(),
                stockFuture.join()
        );
    }

    private Product findProduct(Long productId) {
        return new Product(productId);
    }

    private List<Review> findReviews(Long productId) {
        return List.of();
    }

    private Stock findStock(Long productId) {
        return new Stock(productId, 10);
    }
}
```

| Parte         | Explicação                                                |
| ------------- | --------------------------------------------------------- |
| `supplyAsync` | Executa tarefa de forma assíncrona                        |
| `allOf`       | Aguarda todas as tarefas                                  |
| `join()`      | Obtém resultado ou lança exceção unchecked                |
| Benefício     | Produto, reviews e estoque podem ser buscados em paralelo |
| Cuidado       | Usar pool adequado para produção                          |

---

## 15.3 Exemplo com executor customizado

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class AsyncService {

    private final ExecutorService executor = Executors.newFixedThreadPool(10);

    public CompletableFuture<String> processAsync() {
        return CompletableFuture.supplyAsync(() -> {
            return "Resultado";
        }, executor);
    }
}
```

| Parte                    | Explicação                                   |
| ------------------------ | -------------------------------------------- |
| Executor customizado     | Evita usar o pool comum sem controle         |
| `newFixedThreadPool(10)` | Limita concorrência                          |
| Produção                 | Idealmente configurar como bean no Spring    |
| Cuidado                  | Encerrar executor ou deixar Spring gerenciar |

---

# 16. Parallel Stream

## 16.1 Conceito

| Conceito           | Explicação                                                           |
| ------------------ | -------------------------------------------------------------------- |
| `parallelStream()` | Executa operações de stream em paralelo usando o ForkJoinPool comum. |
| Bom para           | Processamento CPU-bound, coleções grandes e operações independentes. |
| Ruim para          | I/O bloqueante, chamadas HTTP, banco de dados, transações JPA.       |
| Cuidado            | Pode piorar performance e causar contenção.                          |

---

## 16.2 Exemplo

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> result = numbers.parallelStream()
        .map(number -> number * 2)
        .toList();
```

| Ponto      | Explicação                                  |
| ---------- | ------------------------------------------- |
| Simples    | Fácil de aplicar                            |
| Perigoso   | Fácil de usar no lugar errado               |
| Pool comum | Usa `ForkJoinPool.commonPool()`             |
| Não ideal  | Para chamadas bloqueantes ou acesso a banco |

---

# 17. Virtual Threads

## 17.1 Conceito

| Conceito            | Explicação                                                               |
| ------------------- | ------------------------------------------------------------------------ |
| **Virtual Threads** | Threads leves gerenciadas pela JVM.                                      |
| Principal benefício | Lidar melhor com grande quantidade de operações bloqueantes/I/O-bound.   |
| Boa aplicação       | APIs que fazem chamadas a banco, HTTP, filas ou arquivos.                |
| Não resolvem        | CPU-bound pesado, query lenta, lock mal projetado, banco saturado.       |
| Mentalidade         | Permitem alto número de tarefas concorrentes com menor custo por thread. |

---

## 17.2 Exemplo simples

```java
public class VirtualThreadExample {

    public static void main(String[] args) throws InterruptedException {
        Thread thread = Thread.startVirtualThread(() -> {
            System.out.println("Rodando em virtual thread");
        });

        thread.join();
    }
}
```

| Parte                | Explicação                                         |
| -------------------- | -------------------------------------------------- |
| `startVirtualThread` | Cria e inicia uma virtual thread                   |
| `join()`             | Aguarda a conclusão                                |
| Uso                  | Bom para muitas tarefas bloqueantes                |
| Cuidado              | Ainda precisa controlar acesso a recursos externos |

---

## 17.3 Executor com virtual threads

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class VirtualThreadExecutorExample {

    public static void main(String[] args) {
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 1; i <= 1000; i++) {
                int taskId = i;

                executor.submit(() -> {
                    System.out.println("Tarefa " + taskId + " em " + Thread.currentThread());
                });
            }
        }
    }
}
```

| Parte                               | Explicação                                                       |
| ----------------------------------- | ---------------------------------------------------------------- |
| `newVirtualThreadPerTaskExecutor()` | Cria uma virtual thread por tarefa                               |
| Bom para                            | Muitas tarefas bloqueantes                                       |
| Não significa infinito              | Banco, APIs e filas continuam tendo limites                      |
| Regra                               | Virtual thread reduz custo da thread, não remove gargalo externo |

---

# 18. Concorrência em aplicações Spring Boot

## 18.1 Onde aparece

| Cenário         | Exemplo                                    |
| --------------- | ------------------------------------------ |
| API REST        | Muitas requisições simultâneas             |
| Banco de dados  | Transações concorrentes                    |
| Mensageria      | Vários consumidores processando mensagens  |
| Jobs            | Processamento paralelo em lote             |
| Integrações     | Chamadas simultâneas a APIs externas       |
| Cache           | Várias threads acessando Redis/cache local |
| Upload/download | Operações I/O-bound                        |

---

## 18.2 Pontos críticos no Spring

| Ponto             | Risco                                                |
| ----------------- | ---------------------------------------------------- |
| Beans singleton   | Estado mutável compartilhado entre requisições       |
| `@Async`          | Executor mal configurado                             |
| `@Transactional`  | Transações longas e locks                            |
| JPA EntityManager | Não deve ser compartilhado manualmente entre threads |
| Cache local       | Pode precisar de estrutura thread-safe               |
| Pool de conexões  | Pode saturar com excesso de concorrência             |
| Mensageria        | Precisa idempotência e controle de retry             |

---

## 18.3 Exemplo de bean perigoso

```java
@Service
public class ReportService {

    private BigDecimal total = BigDecimal.ZERO;

    public void add(BigDecimal value) {
        total = total.add(value);
    }

    public BigDecimal getTotal() {
        return total;
    }
}
```

| Problema                               | Explicação                                                  |
| -------------------------------------- | ----------------------------------------------------------- |
| `@Service` é singleton por padrão      | Uma instância atende várias requisições                     |
| `total` é estado mutável compartilhado | Pode ser alterado por múltiplas threads                     |
| Não é thread-safe                      | Resultado pode ficar inconsistente                          |
| Melhor abordagem                       | Usar variável local, banco, cache controlado ou atomic/lock |

---

## 18.4 Melhor com variável local

```java
@Service
public class ReportService {

    public BigDecimal calculateTotal(List<Order> orders) {
        return orders.stream()
                .map(Order::getTotal)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

| Melhoria                       | Explicação                               |
| ------------------------------ | ---------------------------------------- |
| Sem estado compartilhado       | Mais seguro                              |
| Variável local                 | Cada requisição tem sua própria execução |
| Bean singleton continua seguro | Porque não guarda estado mutável         |
| Mais previsível                | Menos risco de race condition            |

---

# 19. `@Async` no Spring

## 19.1 Conceito

| Conceito   | Explicação                                                                  |
| ---------- | --------------------------------------------------------------------------- |
| `@Async`   | Executa método em outra thread.                                             |
| Uso comum  | Envio de e-mail, notificação, integração externa, processamento assíncrono. |
| Precisa de | `@EnableAsync` e executor configurado.                                      |
| Cuidado    | Exceções e transações podem se comportar diferente.                         |

---

## 19.2 Exemplo

```java
@EnableAsync
@Configuration
public class AsyncConfig {
}
```

```java
@Service
public class NotificationService {

    @Async
    public void sendEmailAsync(String email) {
        System.out.println("Enviando e-mail para " + email);
    }
}
```

| Parte          | Explicação                                                  |
| -------------- | ----------------------------------------------------------- |
| `@EnableAsync` | Habilita execução assíncrona                                |
| `@Async`       | Método roda em outra thread                                 |
| Retorno `void` | Exceções precisam de tratamento específico                  |
| Melhor retorno | `CompletableFuture<T>` quando precisar acompanhar resultado |

---

## 19.3 Executor configurado

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
public class AsyncExecutorConfig {

    @Bean(name = "applicationTaskExecutor")
    public Executor applicationTaskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();

        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(30);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("app-async-");

        executor.initialize();

        return executor;
    }
}
```

| Configuração       | Explicação                      |
| ------------------ | ------------------------------- |
| `corePoolSize`     | Threads principais              |
| `maxPoolSize`      | Máximo de threads               |
| `queueCapacity`    | Tamanho da fila                 |
| `threadNamePrefix` | Ajuda observabilidade           |
| Importância        | Evita concorrência sem controle |

---

# 20. Banco de dados e concorrência

## 20.1 Problemas comuns

| Problema            | Explicação                                                       |
| ------------------- | ---------------------------------------------------------------- |
| Lost update         | Duas transações atualizam o mesmo dado e uma sobrescreve a outra |
| Dirty read          | Ler dado não confirmado por outra transação                      |
| Non-repeatable read | Ler o mesmo registro duas vezes e obter valores diferentes       |
| Phantom read        | Reexecutar consulta e encontrar novas linhas                     |
| Deadlock            | Transações bloqueiam recursos em ordem conflitante               |
| Lock wait timeout   | Transação espera lock por tempo demais                           |

---

## 20.2 Controle em JPA

| Estratégia          | Como funciona                                     |
| ------------------- | ------------------------------------------------- |
| Optimistic Lock     | Usa versão no registro, geralmente com `@Version` |
| Pessimistic Lock    | Bloqueia o registro durante a transação           |
| Transação curta     | Reduz tempo de lock                               |
| Idempotência        | Evita duplicidade em retry                        |
| Índices em updates  | Evita bloquear registros demais                   |
| Isolamento adequado | Controla fenômenos de concorrência                |

---

## 20.3 Exemplo com Optimistic Lock

```java
@Entity
public class Product {

    @Id
    private Long id;

    private String name;

    private BigDecimal price;

    @Version
    private Long version;
}
```

| Parte                   | Explicação                                      |
| ----------------------- | ----------------------------------------------- |
| `@Version`              | Campo usado para controle de versão             |
| Atualização concorrente | Se outra transação alterou antes, a versão muda |
| Resultado               | JPA detecta conflito                            |
| Uso bom                 | Cenários com baixa chance de conflito           |
| Exemplo                 | Atualização de cadastro, produto, perfil        |

---

# 21. Mensageria e concorrência

| Ponto                  | Explicação                                               |
| ---------------------- | -------------------------------------------------------- |
| Consumidores paralelos | Aumentam throughput                                      |
| Ordem das mensagens    | Pode ser perdida se processar em paralelo sem cuidado    |
| Idempotência           | Necessária para lidar com retries                        |
| DLQ                    | Guarda mensagens que falharam várias vezes               |
| Backpressure           | Evita consumir mais do que o sistema suporta             |
| Banco downstream       | Pode virar gargalo se consumidores demais forem ativados |

---

## Exemplo mental

| Situação                        | Risco                          | Solução                      |
| ------------------------------- | ------------------------------ | ---------------------------- |
| 20 consumidores Kafka           | Mais vazão                     | Pode sobrecarregar banco     |
| Retry automático                | Reprocessa falhas              | Pode duplicar efeito         |
| Mensagem de pagamento           | Duplicidade crítica            | Usar idempotency key         |
| Mensagens ordenadas por cliente | Paralelismo pode quebrar ordem | Particionar por `customerId` |

---

# 22. Rate limit, backpressure e bulkhead

| Conceito            | Explicação                                                 |
| ------------------- | ---------------------------------------------------------- |
| **Rate limit**      | Limita quantidade de chamadas em um período.               |
| **Backpressure**    | Sistema reduz consumo quando downstream está lento.        |
| **Bulkhead**        | Isola recursos para evitar que uma falha derrube tudo.     |
| **Timeout**         | Evita espera infinita.                                     |
| **Circuit breaker** | Interrompe chamadas para serviço instável temporariamente. |

---

## Exemplo em arquitetura

| Sem controle                              | Com controle                   |
| ----------------------------------------- | ------------------------------ |
| Todas as threads chamam API externa lenta | Pool dedicado para API externa |
| Fila cresce indefinidamente               | Fila com limite                |
| Banco recebe carga excessiva              | Rate limit e backpressure      |
| Falha externa trava aplicação             | Timeout + circuit breaker      |
| Um recurso consome tudo                   | Bulkhead por integração        |

---

# 23. Observabilidade em concorrência

| Métrica                     | O que indica                            |
| --------------------------- | --------------------------------------- |
| Número de threads ativas    | Carga concorrente                       |
| Tamanho da fila do executor | Tarefas aguardando execução             |
| Uso de CPU                  | Saturação de processamento              |
| Latência P95/P99            | Impacto em usuários                     |
| Pool de conexões            | Saturação do banco                      |
| Deadlocks                   | Problemas de locks                      |
| Timeouts                    | Downstream lento                        |
| Taxa de erro                | Falhas sob concorrência                 |
| Throughput                  | Quantas tarefas/requisições por segundo |
| Thread dump                 | Diagnóstico de travamentos              |

---

# 24. Boas práticas

| Boa prática                                              | Motivo                                   |
| -------------------------------------------------------- | ---------------------------------------- |
| Evitar estado mutável compartilhado                      | Reduz race condition                     |
| Preferir imutabilidade                                   | Facilita thread safety                   |
| Usar ExecutorService em vez de criar threads manualmente | Melhor controle                          |
| Configurar pools com limites                             | Evita sobrecarga                         |
| Não usar `parallelStream` para I/O bloqueante            | Pode saturar pool comum                  |
| Usar timeouts                                            | Evita threads presas                     |
| Manter transações curtas                                 | Reduz locks                              |
| Usar idempotência em processamento concorrente           | Evita duplicidade                        |
| Monitorar filas e pools                                  | Detecta saturação                        |
| Testar com carga                                         | Muitos bugs só aparecem sob concorrência |

---

# 25. Erros comuns em entrevistas

| Erro                                           | Melhor resposta                                                                   |
| ---------------------------------------------- | --------------------------------------------------------------------------------- |
| “Concorrência e paralelismo são a mesma coisa” | Concorrência é lidar com várias tarefas; paralelismo é executá-las ao mesmo tempo |
| “Thread resolve performance sempre”            | Depende se o gargalo é CPU, I/O, banco, lock ou rede                              |
| “`volatile` resolve tudo”                      | `volatile` resolve visibilidade, não atomicidade composta                         |
| “`synchronized` sempre é ruim”                 | É útil, mas deve ter escopo pequeno                                               |
| “Parallel Stream é sempre mais rápido”         | Pode piorar se usado com I/O ou coleções pequenas                                 |
| “Virtual threads eliminam gargalo”             | Elas reduzem custo de threads, mas banco/API externa continuam limitados          |
| “Bean Spring pode guardar estado sem problema” | Bean singleton com estado mutável pode causar race condition                      |
| “Mais consumidores sempre aumentam vazão”      | Pode sobrecarregar banco ou quebrar ordem                                         |

---

# 26. Resumo mental final

| Centro                | Ramos                                                     |
| --------------------- | --------------------------------------------------------- |
| **Concorrência**      | Várias tarefas em progresso                               |
| **Paralelismo**       | Várias tarefas executando ao mesmo tempo                  |
| **Thread**            | Unidade de execução                                       |
| **ExecutorService**   | Gerencia pool de threads                                  |
| **Race condition**    | Bug por acesso simultâneo a estado compartilhado          |
| **synchronized/Lock** | Protegem seção crítica                                    |
| **Atomic**            | Operações atômicas simples                                |
| **volatile**          | Visibilidade entre threads                                |
| **CompletableFuture** | Composição assíncrona                                     |
| **Virtual Threads**   | Muitas tarefas bloqueantes com menor custo                |
| **Spring**            | Cuidado com beans singleton, `@Async`, transações e pools |
| **Produção**          | Medir threads, filas, locks, timeouts, P95/P99            |

---

# 🇺🇸 English Version — Mind Map in Tables

## 1. Overview

| Central node     | Summary                                                                                              |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| **Concurrency**  | Ability to deal with multiple tasks in progress at the same time.                                    |
| **Parallelism**  | Actual simultaneous execution of multiple tasks, usually on multiple CPU cores.                      |
| Core idea        | Concurrency is about **structure**; parallelism is about **simultaneous execution**.                 |
| In Java          | Threads, ExecutorService, CompletableFuture, Locks, Atomic, Concurrent Collections, Virtual Threads. |
| Common scenarios | REST APIs, queues, batch processing, external calls, async tasks, distributed systems.               |
| Main risk        | Hard bugs: race condition, deadlock, starvation, visibility issues and inconsistent state.           |

---

## 2. Concurrency vs Parallelism

| Concept                           | Explanation                                            | Example                                                      |
| --------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| **Concurrency**                   | Multiple tasks make progress by sharing execution time | One cashier serving many customers by switching between them |
| **Parallelism**                   | Multiple tasks run literally at the same time          | Many cashiers serving many customers simultaneously          |
| Concurrency without parallelism   | Possible                                               | One CPU core switching between threads                       |
| Parallelism requires concurrency? | Usually yes                                            | Multiple concurrent tasks can run in parallel                |
| Focus of concurrency              | Program structure                                      | Coordinate tasks                                             |
| Focus of parallelism              | Speeding up execution                                  | Use multiple cores                                           |

---

## 3. Main mind map

| Center                          | Main branches     |
| ------------------------------- | ----------------- |
| **Concurrency and Parallelism** | Threads           |
|                                 | ExecutorService   |
|                                 | CompletableFuture |
|                                 | Locks             |
|                                 | Atomicity         |
|                                 | Visibility        |
|                                 | Race condition    |
|                                 | Deadlock          |
|                                 | Thread safety     |
|                                 | CPU-bound         |
|                                 | I/O-bound         |
|                                 | Virtual Threads   |
|                                 | Observability     |

---

# 4. Threads

| Concept                  | Explanation                                            |
| ------------------------ | ------------------------------------------------------ |
| **Thread**               | Unit of execution inside a process.                    |
| Process                  | Running program with its own memory.                   |
| Threads inside a process | Share memory with other threads from the same process. |
| Benefit                  | Allows concurrent execution.                           |
| Risk                     | Shared memory can cause inconsistent state.            |

Example:

```java
public class ThreadExample {

    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Running in another thread: " + Thread.currentThread().getName());
        });

        thread.start();

        System.out.println("Running in main thread: " + Thread.currentThread().getName());
    }
}
```

| Part              | Explanation                 |
| ----------------- | --------------------------- |
| `new Thread(...)` | Creates a new thread        |
| `Runnable`        | Code executed by the thread |
| `thread.start()`  | Starts concurrent execution |
| `main`            | Main application thread     |

---

# 5. ExecutorService

| Concept             | Explanation                                          |
| ------------------- | ---------------------------------------------------- |
| **ExecutorService** | Abstraction for running tasks using a thread pool.   |
| Advantage           | Avoids creating raw threads repeatedly.              |
| Thread pool         | Reusable set of worker threads.                      |
| Best use            | Controlled execution of multiple tasks.              |
| Risk                | Poor sizing can create queues, timeouts or overload. |

Example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorServiceExample {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);

        for (int i = 1; i <= 10; i++) {
            int taskId = i;

            executor.submit(() -> {
                System.out.println("Task " + taskId + " executed by " +
                        Thread.currentThread().getName());
            });
        }

        executor.shutdown();
    }
}
```

| Part                    | Explanation                   |
| ----------------------- | ----------------------------- |
| `newFixedThreadPool(4)` | Creates a pool with 4 threads |
| `submit(...)`           | Submits a task                |
| `shutdown()`            | Stops accepting new tasks     |
| Benefit                 | Controls active concurrency   |

---

# 6. CPU-bound vs I/O-bound

| Type          | Main bottleneck                         | Example                                          |
| ------------- | --------------------------------------- | ------------------------------------------------ |
| **CPU-bound** | Processor                               | Heavy calculations, encryption, image processing |
| **I/O-bound** | External waiting                        | Database, external API, file, network, queue     |
| CPU-bound     | Parallelism helps up to CPU core limits | Too many threads may hurt                        |
| I/O-bound     | More concurrency can help               | Threads spend time waiting                       |

---

# 7. Race condition

| Concept            | Explanation                                                                       |
| ------------------ | --------------------------------------------------------------------------------- |
| **Race condition** | Bug caused when multiple threads access and modify shared state at the same time. |
| Symptom            | Inconsistent and hard-to-reproduce result.                                        |
| Common cause       | Non-atomic compound operations.                                                   |
| Classic example    | Shared counter increment.                                                         |

Problem:

```java
public class Counter {

    private int value = 0;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }
}
```

Solution:

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounter {

    private final AtomicInteger value = new AtomicInteger(0);

    public void increment() {
        value.incrementAndGet();
    }

    public int getValue() {
        return value.get();
    }
}
```

| Solution            | Explanation                 |
| ------------------- | --------------------------- |
| `AtomicInteger`     | Thread-safe atomic counter  |
| `incrementAndGet()` | Atomic increment            |
| Good for            | Simple counters             |
| Not enough for      | Complex business invariants |

---

# 8. Atomicity, visibility and ordering

| Concept               | Explanation                                                     |
| --------------------- | --------------------------------------------------------------- |
| **Atomicity**         | Operation happens as a single indivisible unit.                 |
| **Visibility**        | One thread can see changes made by another thread.              |
| **Ordering**          | JVM/CPU/compiler may reorder instructions under specific rules. |
| **Java Memory Model** | Defines how threads interact through memory.                    |

Example with `volatile`:

```java
public class Worker {

    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    public void work() {
        while (running) {
            // working
        }
    }
}
```

| Part            | Explanation                                   |
| --------------- | --------------------------------------------- |
| `volatile`      | Guarantees visibility between threads         |
| Good for        | Control flags                                 |
| Limitation      | Does not make compound operations atomic      |
| Example problem | `volatile int count; count++` is still unsafe |

---

# 9. Locks

| Resource               | Use                         | Advantage              | Risk                            |
| ---------------------- | --------------------------- | ---------------------- | ------------------------------- |
| `synchronized`         | Protect critical section    | Simple                 | Less flexible                   |
| `ReentrantLock`        | Manual lock control         | More flexible          | Must unlock in `finally`        |
| `AtomicInteger`        | Simple atomic operations    | Efficient              | Limited to simple cases         |
| `volatile`             | Visibility                  | Lightweight            | Not enough for compound updates |
| Concurrent collections | Thread-safe data structures | Reduces manual locking | Compound logic still needs care |

Example:

```java
import java.util.concurrent.locks.ReentrantLock;

public class LockCounter {

    private final ReentrantLock lock = new ReentrantLock();
    private int value = 0;

    public void increment() {
        lock.lock();

        try {
            value++;
        } finally {
            lock.unlock();
        }
    }
}
```

---

# 10. Deadlock

| Concept         | Explanation                                                        |
| --------------- | ------------------------------------------------------------------ |
| **Deadlock**    | Two or more threads wait forever for resources held by each other. |
| Symptom         | System partially or fully freezes.                                 |
| Common cause    | Locks acquired in different orders.                                |
| Common solution | Define a fixed lock acquisition order.                             |

Prevention:

| Strategy           | Explanation                            |
| ------------------ | -------------------------------------- |
| Fixed lock order   | Always acquire locks in the same order |
| Lock timeout       | Avoid infinite waiting                 |
| Small lock scope   | Hold lock for less time                |
| Avoid nested locks | Reduce complexity                      |
| Thread dumps       | Diagnose deadlocks in production       |

---

# 11. CompletableFuture

| Concept               | Explanation                                                    |
| --------------------- | -------------------------------------------------------------- |
| **CompletableFuture** | Java API for asynchronous programming and task composition.    |
| Common use            | Run independent calls concurrently and combine results.        |
| Benefit               | Useful for I/O-bound workflows.                                |
| Risk                  | Exceptions and thread pool usage can become harder to control. |

Example:

```java
CompletableFuture<Product> productFuture =
        CompletableFuture.supplyAsync(() -> findProduct(productId));

CompletableFuture<List<Review>> reviewsFuture =
        CompletableFuture.supplyAsync(() -> findReviews(productId));

CompletableFuture<Stock> stockFuture =
        CompletableFuture.supplyAsync(() -> findStock(productId));

CompletableFuture.allOf(productFuture, reviewsFuture, stockFuture).join();

ProductPageResponse response = new ProductPageResponse(
        productFuture.join(),
        reviewsFuture.join(),
        stockFuture.join()
);
```

| Part          | Explanation                        |
| ------------- | ---------------------------------- |
| `supplyAsync` | Runs a task asynchronously         |
| `allOf`       | Waits for all tasks                |
| `join()`      | Gets the result                    |
| Use case      | Combine independent external calls |

---

# 12. Virtual Threads

| Concept             | Explanation                                                            |
| ------------------- | ---------------------------------------------------------------------- |
| **Virtual Threads** | Lightweight threads managed by the JVM.                                |
| Main benefit        | Handle many blocking/I/O-bound tasks with lower thread cost.           |
| Good for            | APIs with database, HTTP, queues or file operations.                   |
| Not a solution for  | Slow SQL, CPU-heavy logic, bad locks or overloaded downstream systems. |

Example:

```java
public class VirtualThreadExample {

    public static void main(String[] args) throws InterruptedException {
        Thread thread = Thread.startVirtualThread(() -> {
            System.out.println("Running in a virtual thread");
        });

        thread.join();
    }
}
```

---

# 13. Spring Boot concerns

| Point               | Risk                                         |
| ------------------- | -------------------------------------------- |
| Singleton beans     | Mutable shared state between requests        |
| `@Async`            | Bad executor configuration                   |
| `@Transactional`    | Long transactions and locks                  |
| JPA EntityManager   | Should not be manually shared across threads |
| Local cache         | May need thread-safe structures              |
| Connection pool     | Can saturate under high concurrency          |
| Messaging consumers | Need idempotency and retry control           |

Bad Spring example:

```java
@Service
public class ReportService {

    private BigDecimal total = BigDecimal.ZERO;

    public void add(BigDecimal value) {
        total = total.add(value);
    }

    public BigDecimal getTotal() {
        return total;
    }
}
```

| Problem           | Explanation                                        |
| ----------------- | -------------------------------------------------- |
| Singleton service | One instance serves many requests                  |
| Mutable field     | Shared by multiple threads                         |
| Not thread-safe   | Result may become inconsistent                     |
| Better design     | Use local variables or external consistent storage |

---

# Revisão rápida

| Pergunta                                         | Resposta curta                                                                            |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| O que é concorrência?                            | Lidar com várias tarefas em andamento.                                                    |
| O que é paralelismo?                             | Executar várias tarefas ao mesmo tempo.                                                   |
| Concorrência e paralelismo são iguais?           | Não. Concorrência é organização; paralelismo é execução simultânea.                       |
| O que é thread?                                  | Unidade de execução dentro de um processo.                                                |
| O que é race condition?                          | Bug por acesso simultâneo a estado compartilhado.                                         |
| `volatile` resolve atomicidade?                  | Não. Resolve principalmente visibilidade.                                                 |
| Quando usar `AtomicInteger`?                     | Para operações atômicas simples, como contador.                                           |
| O que é deadlock?                                | Threads esperando recursos umas das outras indefinidamente.                               |
| Para que serve ExecutorService?                  | Gerenciar execução de tarefas com pool de threads.                                        |
| Quando usar CompletableFuture?                   | Para compor tarefas assíncronas independentes.                                            |
| `parallelStream` sempre melhora performance?     | Não. Pode piorar, especialmente com I/O.                                                  |
| Virtual threads resolvem gargalo de banco?       | Não. Elas reduzem custo de thread, mas o banco continua tendo limite.                     |
| Qual risco de bean singleton com estado mutável? | Race condition entre requisições.                                                         |
| Como reduzir bugs de concorrência?               | Evitar estado compartilhado, usar imutabilidade, locks corretos e estruturas thread-safe. |

---

# Exercícios progressivos

| Nível         | Exercício                                                                                      |
| ------------- | ---------------------------------------------------------------------------------------------- |
| Básico        | Explique com suas palavras a diferença entre concorrência e paralelismo.                       |
| Básico        | Dê um exemplo de sistema concorrente que não é paralelo.                                       |
| Básico        | Explique a diferença entre `thread.start()` e `thread.run()`.                                  |
| Básico        | Explique por que `value++` não é uma operação atômica.                                         |
| Intermediário | Implemente um contador inseguro e depois corrija usando `synchronized`.                        |
| Intermediário | Reimplemente o contador usando `AtomicInteger`.                                                |
| Intermediário | Explique quando `volatile` é adequado e quando não é.                                          |
| Intermediário | Crie um exemplo usando `ExecutorService` com 5 tarefas.                                        |
| Intermediário | Explique por que um bean Spring singleton com campo mutável pode ser perigoso.                 |
| Avançado      | Modele um fluxo com `CompletableFuture` para buscar produto, estoque e avaliações em paralelo. |
| Avançado      | Explique como deadlock pode acontecer em duas transferências bancárias concorrentes.           |
| Avançado      | Defina uma estratégia para processar mensagens em paralelo sem quebrar idempotência.           |
| Avançado      | Compare `ExecutorService`, `CompletableFuture`, `parallelStream` e virtual threads.            |
| Avançado      | Explique como excesso de concorrência pode esgotar o pool de conexões do banco.                |
| Avançado      | Descreva quais métricas você analisaria para investigar lentidão causada por concorrência.     |
