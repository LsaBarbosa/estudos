Lucas, segue em formato de **quadro/tabela** para estudar os temas de Java em nível **sênior**, com foco em entrevistas e aplicação real.

# Mapa geral dos temas Java para nível sênior

| Tema                  | O que precisa dominar                                                                               | O que esperam de um sênior                                                                         | Erros comuns em entrevista                                     |
| --------------------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| **Java Core**         | POO, generics, exceptions, records, sealed classes, streams, lambdas, Optional, imutabilidade       | Saber explicar decisões de design, trade-offs e impactos em manutenção, performance e legibilidade | Saber sintaxe, mas não saber justificar escolhas               |
| **Collections**       | List, Set, Map, Queue, Deque, hashCode/equals, Big-O, ordenação, concorrência                       | Escolher a estrutura correta para cada cenário e entender custo de memória/performance             | Dizer que `HashMap` é sempre O(1) sem explicar colisões        |
| **Concorrência**      | Threads, ExecutorService, locks, synchronized, volatile, atomic, CompletableFuture, virtual threads | Entender race condition, deadlock, visibility, thread-safety e desenho de sistemas concorrentes    | Usar `synchronized` como solução universal                     |
| **JVM e Memória**     | Stack, heap, metaspace, classloader, JIT, bytecode, memory model                                    | Diagnosticar problemas de memória, performance e entender como o Java executa internamente         | Confundir stack com heap                                       |
| **JPA/Hibernate**     | Entity lifecycle, persistence context, lazy/eager, dirty checking, N+1, transaction, cache          | Saber modelar entidades, evitar queries ruins e controlar transações corretamente                  | Usar entidade como DTO e abusar de relacionamento bidirecional |
| **Garbage Collector** | Young/Old generation, GC roots, minor/major GC, G1, ZGC, memory leak                                | Interpretar logs, entender pausas, tuning básico e causas de vazamento                             | Achar que GC elimina qualquer problema de memória              |

---

# 1. Java Core

| Conceito                 | Explicação                                                                                 | Nível sênior precisa saber                                         | Exemplo prático                                                               |
| ------------------------ | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| **POO**                  | Programação baseada em classes, objetos, encapsulamento, herança, polimorfismo e abstração | Usar POO para modelar domínio sem criar hierarquias desnecessárias | Criar `PaymentProcessor` com implementações `PixPayment`, `CreditCardPayment` |
| **Encapsulamento**       | Esconder estado interno e expor comportamento controlado                                   | Evitar entidades anêmicas e setters públicos sem regra             | `order.cancel()` em vez de `order.setStatus(CANCELED)`                        |
| **Herança**              | Reutilização e especialização por meio de `extends`                                        | Saber quando evitar herança e preferir composição                  | `Car extends Vehicle` pode ser aceitável; `User extends Address` é errado     |
| **Polimorfismo**         | Diferentes implementações usando o mesmo contrato                                          | Usar interfaces para reduzir acoplamento                           | `NotificationService` com `EmailNotification` e `SmsNotification`             |
| **Interfaces**           | Contrato de comportamento                                                                  | Separar regra de negócio de infraestrutura                         | `UserRepository` como porta na arquitetura hexagonal                          |
| **Classes abstratas**    | Classe base com comportamento compartilhado                                                | Usar quando existe estado ou lógica comum real                     | `AbstractReportGenerator` com fluxo base                                      |
| **Generics**             | Tipagem parametrizada                                                                      | Criar APIs reutilizáveis e seguras em tempo de compilação          | `Repository<T, ID>`                                                           |
| **Exceptions checked**   | Exceções obrigatoriamente tratadas                                                         | Usar com cautela; podem poluir APIs                                | `IOException`                                                                 |
| **Exceptions unchecked** | Exceções de runtime                                                                        | Usadas para falhas de regra, validação ou programação              | `IllegalArgumentException`, `BusinessException`                               |
| **Imutabilidade**        | Objeto que não muda após criação                                                           | Ajuda em concorrência, segurança e previsibilidade                 | `record Money(BigDecimal value, String currency)`                             |
| **Records**              | Classes imutáveis para dados                                                               | Bons para DTOs, responses, value objects simples                   | `record UserResponse(Long id, String name)`                                   |
| **Sealed classes**       | Restringem quem pode herdar/implementar                                                    | Útil para modelar domínios fechados                                | `sealed interface Payment permits Pix, CreditCard`                            |
| **Streams**              | API funcional para manipular coleções                                                      | Usar sem comprometer legibilidade/performance                      | `orders.stream().filter(...).toList()`                                        |
| **Optional**             | Representa ausência de valor                                                               | Bom em retorno de método; ruim em atributo de entidade ou DTO      | `Optional<User> findByEmail(String email)`                                    |

---

## Java Core — perguntas de entrevista

| Pergunta                                      | Resposta esperada em nível sênior                                                                                                                                                           |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Quando usar herança e quando usar composição? | Herança quando existe relação real de especialização. Composição quando se quer reutilizar comportamento sem acoplamento rígido. Em sistemas grandes, composição costuma ser mais flexível. |
| `record` substitui classe comum?              | Não. `record` é ideal para dados imutáveis e simples. Não é ideal quando existe estado mutável, herança complexa ou comportamento rico de domínio.                                          |
| `Optional` deve ser usado em atributos?       | Geralmente não. `Optional` foi pensado para retorno de método. Em atributos de entidade, DTO ou parâmetros, pode prejudicar serialização, JPA e clareza.                                    |
| Checked exception ou unchecked exception?     | Checked força tratamento, mas pode poluir camadas. Unchecked é comum em regras de negócio e falhas não recuperáveis. O importante é padronizar a estratégia da aplicação.                   |

---

# 2. Collections

| Estrutura         | Como funciona                        | Melhor uso                             | Custo médio                                            | Cuidados                                                   |
| ----------------- | ------------------------------------ | -------------------------------------- | ------------------------------------------------------ | ---------------------------------------------------------- |
| **ArrayList**     | Array dinâmico interno               | Acesso por índice, iteração rápida     | Busca por índice O(1), inserção no fim O(1) amortizado | Inserção/remoção no meio é custosa                         |
| **LinkedList**    | Lista duplamente encadeada           | Inserção/remoção quando já se tem o nó | Acesso por índice O(n)                                 | Ruim para cache locality; raramente melhor que `ArrayList` |
| **HashMap**       | Tabela hash com buckets              | Busca rápida por chave                 | O(1) médio, O(log n) em colisões com tree bins         | Depende de `hashCode` e `equals` corretos                  |
| **LinkedHashMap** | HashMap com ordem de inserção/acesso | Cache LRU simples, manter ordem        | O(1) médio                                             | Consome mais memória                                       |
| **TreeMap**       | Árvore Red-Black                     | Mapa ordenado por chave                | O(log n)                                               | Mais lento que `HashMap`                                   |
| **HashSet**       | Baseado internamente em `HashMap`    | Evitar duplicidade                     | O(1) médio                                             | Também depende de `hashCode` e `equals`                    |
| **TreeSet**       | Baseado em árvore                    | Conjunto ordenado                      | O(log n)                                               | Precisa de `Comparable` ou `Comparator`                    |
| **Queue**         | Fila FIFO                            | Processamento em ordem                 | Depende da implementação                               | Escolher implementação correta                             |
| **Deque**         | Fila dupla                           | Pilha ou fila                          | Depende da implementação                               | `ArrayDeque` costuma ser melhor que `Stack`                |
| **PriorityQueue** | Heap binário                         | Processar por prioridade               | Inserção/remoção O(log n)                              | Não mantém ordenação total na iteração                     |

---

## Collections — escolhas práticas

| Cenário                         | Estrutura recomendada              | Motivo                                       |
| ------------------------------- | ---------------------------------- | -------------------------------------------- |
| Buscar usuário por ID           | `HashMap<Long, User>`              | Lookup rápido por chave                      |
| Manter ordem de inserção        | `LinkedHashMap` ou `LinkedHashSet` | Preserva ordem                               |
| Ordenar por chave               | `TreeMap`                          | Mantém ordenado automaticamente              |
| Evitar duplicados               | `HashSet`                          | Garante unicidade                            |
| Fila de tarefas simples         | `ArrayDeque`                       | Eficiente para FIFO                          |
| Fila concorrente não bloqueante | `ConcurrentLinkedQueue`            | Boa para alta concorrência                   |
| Fila concorrente bloqueante     | `LinkedBlockingQueue`              | Boa para producer/consumer                   |
| Ranking ou agendamento          | `PriorityQueue`                    | Remove primeiro o item de maior prioridade   |
| Cache local thread-safe         | `ConcurrentHashMap`                | Melhor que sincronizar `HashMap` manualmente |

---

## Collections — pontos sênior

| Ponto                                           | Explicação                                                                                                                      |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `HashMap` não é sempre O(1) absoluto            | Em média é O(1), mas colisões podem degradar. Desde Java 8, buckets com muitas colisões podem virar árvore, indo para O(log n). |
| `equals` e `hashCode` devem ser consistentes    | Se dois objetos são iguais por `equals`, precisam ter o mesmo `hashCode`.                                                       |
| Mutabilidade da chave é perigosa                | Se um objeto usado como chave no `HashMap` muda seu estado, ele pode ficar “perdido” no mapa.                                   |
| `ArrayList` é melhor para leitura sequencial    | Usa array contínuo em memória, favorecendo cache de CPU.                                                                        |
| `LinkedList` costuma ser superestimada          | Apesar de inserção teórica eficiente, tem overhead de ponteiros e pior localidade de memória.                                   |
| `ConcurrentHashMap` não bloqueia o mapa inteiro | Trabalha com estratégia interna mais eficiente para concorrência.                                                               |

---

# 3. Concorrência

| Conceito                     | Explicação                                     | Nível sênior precisa saber                                        |
| ---------------------------- | ---------------------------------------------- | ----------------------------------------------------------------- |
| **Thread**                   | Unidade de execução                            | Criar thread manualmente raramente é ideal em aplicações modernas |
| **Runnable**                 | Tarefa sem retorno                             | Usado para executar lógica assíncrona simples                     |
| **Callable**                 | Tarefa com retorno e exceção                   | Usado com `ExecutorService`                                       |
| **ExecutorService**          | Gerencia pool de threads                       | Evita criar threads manualmente                                   |
| **Future**                   | Resultado assíncrono futuro                    | Bloqueia ao chamar `get()`                                        |
| **CompletableFuture**        | API assíncrona encadeável                      | Permite composição, callbacks e pipelines assíncronos             |
| **synchronized**             | Bloqueia seção crítica                         | Simples, mas pode limitar escalabilidade                          |
| **Lock/ReentrantLock**       | Controle explícito de lock                     | Mais flexível que `synchronized`                                  |
| **volatile**                 | Garante visibilidade de variável entre threads | Não garante atomicidade                                           |
| **AtomicInteger/AtomicLong** | Operações atômicas sem lock explícito          | Útil para contadores concorrentes                                 |
| **ThreadLocal**              | Valor isolado por thread                       | Muito usado em contexto transacional, segurança e logs            |
| **Virtual Threads**          | Threads leves do Project Loom                  | Úteis para I/O concorrente com modelo simples                     |

---

## Concorrência — problemas clássicos

| Problema               | O que é                                                  | Exemplo                                                                        |
| ---------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Race condition**     | Duas ou mais threads alteram o mesmo estado sem controle | Dois requests debitando saldo ao mesmo tempo                                   |
| **Deadlock**           | Threads ficam bloqueadas esperando locks uma da outra    | Thread A segura lock 1 e espera lock 2; Thread B segura lock 2 e espera lock 1 |
| **Starvation**         | Uma thread nunca consegue executar                       | Threads de baixa prioridade sempre preteridas                                  |
| **Livelock**           | Threads continuam ativas, mas sem progresso              | Ambas cedem recurso repetidamente                                              |
| **Visibility problem** | Uma thread não enxerga alteração feita por outra         | Flag alterada em uma thread não vista por outra                                |
| **Atomicity problem**  | Operação parece única, mas não é                         | `count++` não é atômico                                                        |

---

## Concorrência — escolhas práticas

| Cenário                         | Solução adequada               |
| ------------------------------- | ------------------------------ |
| Contador concorrente simples    | `AtomicInteger` ou `LongAdder` |
| Cache compartilhado             | `ConcurrentHashMap`            |
| Producer/Consumer               | `BlockingQueue`                |
| Execução assíncrona controlada  | `ExecutorService`              |
| Pipeline assíncrono             | `CompletableFuture`            |
| I/O massivo com código simples  | Virtual Threads                |
| Região crítica pequena          | `synchronized`                 |
| Lock com timeout/tentativa      | `ReentrantLock`                |
| Leitura muito maior que escrita | `ReadWriteLock`                |

---

## Concorrência — perguntas de entrevista

| Pergunta                                        | Resposta esperada                                                                                                                                                                    |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `volatile` resolve race condition?              | Não necessariamente. `volatile` garante visibilidade, mas não atomicidade. Para incremento concorrente, use `AtomicInteger`, lock ou sincronização.                                  |
| Diferença entre `synchronized` e `Lock`?        | `synchronized` é mais simples e automático. `Lock` permite timeout, tentativa de lock, interrupção e maior controle.                                                                 |
| Quando usar `CompletableFuture`?                | Quando existe composição assíncrona de tarefas, chamadas paralelas ou pipelines não bloqueantes.                                                                                     |
| Virtual threads substituem programação reativa? | Não completamente. Virtual threads simplificam concorrência baseada em bloqueio, mas programação reativa ainda pode fazer sentido em cenários específicos de backpressure e streams. |

---

# 4. JVM e Memória

| Área/Conceito           | Explicação                                       | Importância prática                                        |
| ----------------------- | ------------------------------------------------ | ---------------------------------------------------------- |
| **Bytecode**            | Código intermediário gerado pelo compilador Java | Permite portabilidade entre sistemas                       |
| **ClassLoader**         | Carrega classes na JVM                           | Importante em aplicações, servidores, plugins e frameworks |
| **JIT Compiler**        | Compila bytecode quente para código nativo       | Melhora performance em runtime                             |
| **Heap**                | Área onde objetos vivem                          | Principal área monitorada em problemas de memória          |
| **Stack**               | Área de chamadas de métodos e variáveis locais   | Cada thread possui sua stack                               |
| **Metaspace**           | Armazena metadados de classes                    | Pode crescer com muitos carregamentos de classe            |
| **PC Register**         | Guarda instrução atual da thread                 | Interno da JVM                                             |
| **Native Method Stack** | Suporte a chamadas nativas                       | Usado com JNI                                              |
| **Java Memory Model**   | Define regras de visibilidade entre threads      | Essencial para concorrência correta                        |

---

## JVM — Heap vs Stack

| Item          | Stack                                            | Heap                              |
| ------------- | ------------------------------------------------ | --------------------------------- |
| Armazena      | Frames de métodos, variáveis locais, referências | Objetos                           |
| Escopo        | Por thread                                       | Compartilhado entre threads       |
| Gerenciamento | Automático por chamada de método                 | Gerenciado pelo Garbage Collector |
| Erro comum    | `StackOverflowError`                             | `OutOfMemoryError`                |
| Exemplo       | Chamada recursiva infinita                       | Lista crescendo sem limite        |

---

## JVM — erros comuns

| Erro                                | Causa comum                                         | Exemplo                                          |
| ----------------------------------- | --------------------------------------------------- | ------------------------------------------------ |
| `StackOverflowError`                | Recursão infinita ou muito profunda                 | Método chamando ele mesmo sem condição de parada |
| `OutOfMemoryError: Java heap space` | Heap insuficiente ou vazamento de memória           | Cache infinito em `HashMap`                      |
| `OutOfMemoryError: Metaspace`       | Muitas classes carregadas                           | ClassLoader leak                                 |
| Alto uso de CPU                     | Loop infinito, GC excessivo ou contenção de threads | Aplicação travando com CPU em 100%               |
| Latência alta                       | Pausas de GC, locks, queries lentas                 | API responde lentamente em picos                 |

---

## JVM — pontos sênior

| Ponto                                | Explicação                                                                                 |
| ------------------------------------ | ------------------------------------------------------------------------------------------ |
| Java não interpreta tudo para sempre | A JVM começa executando bytecode, mas o JIT compila trechos frequentes para código nativo. |
| Aquecimento da aplicação importa     | Aplicações Java podem melhorar performance após o warm-up.                                 |
| Tuning sem métrica é chute           | Antes de alterar parâmetros da JVM, é necessário medir heap, GC, threads, CPU e latência.  |
| Heap maior nem sempre resolve        | Heap muito grande pode aumentar pausas de GC dependendo do coletor e cenário.              |
| Thread também consome memória        | Muitas threads tradicionais aumentam consumo de stack e overhead de contexto.              |

---

# 5. JPA/Hibernate

| Conceito                | Explicação                                          | Nível sênior precisa saber                                   |
| ----------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| **Entity**              | Classe mapeada para tabela                          | Não deve ser tratada como simples DTO                        |
| **Persistence Context** | Cache de primeiro nível da transação                | Garante identidade dos objetos gerenciados                   |
| **EntityManager**       | API principal da JPA                                | Controla ciclo de vida das entidades                         |
| **Managed**             | Entidade controlada pelo contexto                   | Alterações podem ser persistidas automaticamente             |
| **Detached**            | Entidade fora do contexto                           | Alterações não são sincronizadas automaticamente             |
| **Transient**           | Objeto novo ainda não persistido                    | Não existe no banco                                          |
| **Removed**             | Entidade marcada para remoção                       | Será deletada no flush/commit                                |
| **Dirty Checking**      | Hibernate detecta alterações automaticamente        | Evita chamar `save` desnecessariamente em entidade managed   |
| **Flush**               | Sincroniza contexto com banco                       | Pode ocorrer antes do commit                                 |
| **Lazy Loading**        | Carrega relacionamento sob demanda                  | Pode causar `LazyInitializationException`                    |
| **Eager Loading**       | Carrega relacionamento imediatamente                | Pode gerar queries pesadas                                   |
| **N+1 Problem**         | Uma query principal gera várias queries secundárias | Deve ser resolvido com fetch join, entity graph ou projeções |
| **Transaction**         | Unidade de trabalho atômica                         | Define escopo do persistence context                         |
| **JPQL**                | Query orientada a entidade                          | Diferente de SQL puro                                        |
| **Native Query**        | SQL direto no banco                                 | Útil para queries específicas ou complexas                   |
| **Cache L1**            | Cache do persistence context                        | Sempre existe por transação                                  |
| **Cache L2**            | Cache compartilhado opcional                        | Precisa ser configurado e usado com critério                 |

---

## JPA/Hibernate — ciclo de vida da entidade

| Estado        | Significado                      | Exemplo                                             |
| ------------- | -------------------------------- | --------------------------------------------------- |
| **Transient** | Objeto ainda não salvo           | `new User()`                                        |
| **Managed**   | Objeto controlado pelo Hibernate | Após `persist`, `find` ou query dentro da transação |
| **Detached**  | Objeto já não está no contexto   | Entidade retornada fora da transação                |
| **Removed**   | Objeto marcado para exclusão     | Após `entityManager.remove(user)`                   |

---

## JPA/Hibernate — problemas comuns

| Problema                                  | Causa                                | Solução                                                  |
| ----------------------------------------- | ------------------------------------ | -------------------------------------------------------- |
| **N+1 queries**                           | Relacionamento lazy acessado em loop | `JOIN FETCH`, `EntityGraph`, projeção DTO                |
| **LazyInitializationException**           | Acesso lazy fora da transação        | Buscar dados dentro da transação ou usar DTO/projeção    |
| **Cartesiano gigante**                    | Muitos `JOIN FETCH` em coleções      | Separar queries, usar batch size ou projeções            |
| **Entidade como DTO**                     | Expor entidade direto na API         | Usar DTOs para entrada/saída                             |
| **Cascade indevido**                      | Propagação mal configurada           | Usar cascade apenas quando há relação de ciclo de vida   |
| **Relacionamento bidirecional excessivo** | Modelo acoplado e difícil de manter  | Preferir unidirecional quando possível                   |
| **Open Session in View**                  | Sessão aberta até a camada web       | Pode mascarar problemas e gerar queries fora de controle |
| **Paginação com fetch join em coleção**   | Hibernate pode paginar em memória    | Usar estratégia em duas etapas                           |

---

## JPA/Hibernate — escolhas práticas

| Cenário                    | Melhor abordagem                                   |
| -------------------------- | -------------------------------------------------- |
| Listagem simples para tela | DTO projection                                     |
| Buscar agregado completo   | `JOIN FETCH` com cuidado                           |
| Atualização de entidade    | Buscar entidade managed e alterar comportamento    |
| Consulta muito específica  | Native Query ou projection                         |
| Grande volume de leitura   | Paginação, DTO e índices no banco                  |
| Relatório pesado           | Query otimizada, projection ou processo assíncrono |
| Relação `ManyToOne`        | Normalmente `LAZY`                                 |
| Relação `OneToMany`        | Usar com cautela; pode crescer muito               |
| Exclusão em cascata        | Só quando o filho não faz sentido sem o pai        |

---

## JPA/Hibernate — perguntas de entrevista

| Pergunta                                    | Resposta esperada                                                                                                                  |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| O que é persistence context?                | É o contexto que gerencia entidades dentro de uma transação, funcionando como cache de primeiro nível e permitindo dirty checking. |
| O que é dirty checking?                     | É o mecanismo pelo qual o Hibernate detecta mudanças em entidades managed e sincroniza com o banco no flush/commit.                |
| Como resolver N+1?                          | Usando `JOIN FETCH`, `EntityGraph`, batch size ou projeções DTO, dependendo do caso.                                               |
| Por que evitar entidade na resposta da API? | Porque expõe modelo interno, pode causar lazy loading acidental, recursão em JSON e acoplamento entre API e banco.                 |

---

# 6. Garbage Collector

| Conceito                | Explicação                                     | Importância prática                                |
| ----------------------- | ---------------------------------------------- | -------------------------------------------------- |
| **GC**                  | Processo que libera objetos não referenciados  | Evita desalocação manual de memória                |
| **GC Roots**            | Pontos de partida para descobrir objetos vivos | Objetos alcançáveis por GC Roots não são coletados |
| **Young Generation**    | Região para objetos novos                      | A maioria dos objetos morre cedo                   |
| **Old Generation**      | Região para objetos que sobrevivem mais tempo  | Objetos promovidos ficam aqui                      |
| **Minor GC**            | Coleta na Young Generation                     | Geralmente mais frequente e rápida                 |
| **Major/Full GC**       | Coleta envolvendo Old Generation               | Pode causar pausas maiores                         |
| **Stop-the-world**      | Pausa em threads da aplicação para GC          | Afeta latência                                     |
| **Memory leak em Java** | Objeto ainda referenciado sem necessidade      | GC não coleta porque ainda há referência           |
| **Finalization**        | Mecanismo antigo de limpeza                    | Deve ser evitado                                   |
| **WeakReference**       | Referência fraca que não impede GC             | Usada em caches específicos                        |

---

## Principais Garbage Collectors

| GC              | Característica                            | Quando faz sentido                            |
| --------------- | ----------------------------------------- | --------------------------------------------- |
| **Serial GC**   | Simples, single-thread                    | Aplicações pequenas ou ambientes limitados    |
| **Parallel GC** | Foco em throughput                        | Batch processing, jobs, processamento pesado  |
| **G1 GC**       | Equilíbrio entre throughput e baixa pausa | Default comum em aplicações modernas          |
| **ZGC**         | Baixíssima pausa                          | Aplicações com baixa latência e heaps grandes |
| **Shenandoah**  | Baixa pausa                               | Alternativa ao ZGC em algumas distribuições   |
| **Epsilon GC**  | Não coleta memória                        | Testes específicos de performance             |

---

## Garbage Collector — problemas e diagnóstico

| Sintoma                  | Possível causa                       | Como investigar                  |
| ------------------------ | ------------------------------------ | -------------------------------- |
| API lenta em picos       | Pausas de GC                         | Logs de GC, métricas de latência |
| CPU alta                 | GC frequente ou loop na aplicação    | Monitorar CPU, GC time e threads |
| `OutOfMemoryError`       | Heap insuficiente ou vazamento       | Heap dump                        |
| Full GC frequente        | Old Generation pressionada           | GC logs e análise de alocação    |
| Memória sobe e nunca cai | Cache sem limite ou referência presa | Heap dump com dominator tree     |
| Latência instável        | Pausas stop-the-world                | JFR, logs de GC, APM             |

---

## Garbage Collector — pontos sênior

| Ponto                                                | Explicação                                                                    |
| ---------------------------------------------------- | ----------------------------------------------------------------------------- |
| GC não impede memory leak                            | Se ainda existe referência, o objeto é considerado vivo.                      |
| Nem todo aumento de memória é vazamento              | A JVM pode reservar heap e não devolver imediatamente ao sistema operacional. |
| Objeto elegível para GC não é coletado imediatamente | A coleta depende do ciclo do GC.                                              |
| Tuning depende de objetivo                           | Pode ser throughput, baixa latência ou menor consumo de memória.              |
| Logs de GC são essenciais                            | Sem logs, tuning vira tentativa e erro.                                       |
| Heap dump mostra quem segura memória                 | Útil para descobrir caches, listas, mapas ou listeners acumulando objetos.    |

---

# Quadro comparativo final para entrevistas

| Tema              | Pergunta comum                | Resposta sênior resumida                                                                       |
| ----------------- | ----------------------------- | ---------------------------------------------------------------------------------------------- |
| **Java Core**     | Quando usar interface?        | Quando quero definir contrato, reduzir acoplamento e permitir múltiplas implementações.        |
| **Java Core**     | Quando usar record?           | Para dados imutáveis e simples, como DTOs e value objects.                                     |
| **Collections**   | `HashMap` é O(1)?             | Em média sim, mas depende de hash, colisões e implementação interna.                           |
| **Collections**   | `ArrayList` ou `LinkedList`?  | Na maioria dos casos `ArrayList`. `LinkedList` só faz sentido em cenários específicos.         |
| **Concorrência**  | `volatile` resolve tudo?      | Não. Resolve visibilidade, não atomicidade.                                                    |
| **Concorrência**  | Como evitar race condition?   | Usando sincronização, locks, estruturas concorrentes ou objetos imutáveis.                     |
| **JVM**           | Diferença entre heap e stack? | Heap armazena objetos; stack armazena frames de método e referências locais por thread.        |
| **JVM**           | O que é JIT?                  | Compilador que transforma bytecode executado frequentemente em código nativo otimizado.        |
| **JPA/Hibernate** | O que é N+1?                  | Uma query inicial seguida de várias queries adicionais por relacionamento lazy.                |
| **JPA/Hibernate** | O que é dirty checking?       | Detecção automática de mudanças em entidades managed.                                          |
| **GC**            | GC elimina memory leak?       | Não. Se houver referência ativa indevida, o objeto não será coletado.                          |
| **GC**            | Como investigar OOM?          | Coletar heap dump, analisar dominator tree, revisar caches, coleções e referências long-lived. |

---

# Ordem recomendada de estudo

| Ordem | Tema              | Motivo                                              |
| ----: | ----------------- | --------------------------------------------------- |
|     1 | Java Core         | Base para todos os outros temas                     |
|     2 | Collections       | Essencial para performance e modelagem              |
|     3 | Concorrência      | Fundamental para sistemas back-end reais            |
|     4 | JVM e Memória     | Ajuda a entender performance, threads e execução    |
|     5 | Garbage Collector | Complementa JVM e diagnóstico de produção           |
|     6 | JPA/Hibernate     | Aplica conceitos em persistência, transação e banco |

---

# Checklist de domínio sênior

| Tema                  | Você está em nível sênior quando consegue...                                       |
| --------------------- | ---------------------------------------------------------------------------------- |
| **Java Core**         | Explicar não só como usar, mas por que usar determinada abordagem                  |
| **Collections**       | Escolher estruturas considerando Big-O, memória, concorrência e legibilidade       |
| **Concorrência**      | Identificar race condition, deadlock, visibility problem e desenhar solução segura |
| **JVM**               | Entender stack, heap, JIT, classloader, threads e impacto em produção              |
| **JPA/Hibernate**     | Evitar N+1, controlar transações e separar entidade de contrato de API             |
| **Garbage Collector** | Interpretar sintomas, logs, heap dump e propor ajustes com base em evidência       |
