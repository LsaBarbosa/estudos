Lucas, o principal problema do material não é conteúdo incorreto, e sim **classificação**. Há conceitos de POO misturados com SOLID e Design Patterns, `Classe abstrata` misturada com Streams, mecanismos de concorrência local misturados com concorrência distribuída e conceitos de GC misturados com organização de memória.

Abaixo reorganizei em uma ordem de estudo mais coerente, indo de **fundamentos → mecanismos → aplicação**.

---

# 1. Programação Orientada a Objetos — POO

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Encapsulamento** | Manter estado e comportamento relacionados dentro do objeto, protegendo suas invariantes e evitando estados inválidos. | Centraliza regras de negócio, aumenta coesão e protege o estado. | Pode gerar objetos excessivamente complexos se responsabilidades demais forem colocadas neles. | `Order`, `Account`, `Payment`, `Stock`, `Customer`. |
| **Abstração** | Expor apenas aquilo que o cliente precisa conhecer, escondendo detalhes internos de implementação. | Reduz acoplamento e permite alterar implementações. | Abstrações prematuras geram interfaces e camadas sem necessidade. | `PaymentGateway`, `Repository`, `NotificationService`. |
| **Polimorfismo** | Diferentes implementações podem ser utilizadas através do mesmo contrato. | Facilita extensão e reduz condicionais baseadas em tipo. | Muitas implementações podem dificultar rastreamento do fluxo. | Diferentes formas de pagamento, notificações, descontos e persistência. |
| **Herança** | Uma classe especializa outra herdando comportamento e estrutura. | Permite reutilização e modelagem de relações reais de especialização. | Forte acoplamento entre classe pai e subclasses; pode violar LSP. | Frameworks, Template Method e hierarquias realmente estáveis. |
| **Composição** | Um objeto utiliza outros objetos para executar partes do seu comportamento. | Maior flexibilidade, menor acoplamento e melhor testabilidade. | Pode aumentar a quantidade de objetos e dependências. | `OrderService` usa `OrderRepository`; `PaymentService` usa `PaymentStrategy`. |
| **Classe abstrata** | Classe que não pode ser instanciada diretamente e pode conter estado, comportamento concreto e métodos abstratos. | Compartilha comportamento e estado entre classes relacionadas. | Herança única e acoplamento forte com subclasses. | Classes-base quando existe comportamento comum legítimo. |
| **Método abstrato** | Método sem implementação cuja implementação deve ser fornecida por subclasses concretas. | Obriga subclasses a implementar determinado comportamento. | Aumenta dependência entre superclasse e subclasses. | `generateContent()`, `calculatePrice()`, `process()`. |

### Relação

```text
POO
├── Encapsulamento
├── Abstração
├── Polimorfismo
├── Herança
└── Composição
```

---

# 2. SOLID

SOLID não é POO propriamente dito. É um conjunto de **princípios de design orientado a objetos**.

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **SRP — Single Responsibility Principle** | Uma unidade deve possuir uma responsabilidade coesa e um motivo principal para mudança. | Facilita manutenção, teste e evolução. | Aplicação excessiva pode gerar fragmentação artificial. | Separar regras de negócio, persistência e integração externa. |
| **OCP — Open/Closed Principle** | Software deve permitir extensão sem exigir alteração constante do código existente. | Reduz impacto de novas funcionalidades. | Requer identificar corretamente os pontos de variação. | Adicionar nova forma de pagamento através de nova implementação. |
| **LSP — Liskov Substitution Principle** | Uma implementação deve poder substituir sua abstração sem violar o comportamento esperado. | Permite polimorfismo confiável. | Exige contratos bem definidos e modelagem cuidadosa. | Toda `PaymentStrategy` deve respeitar o mesmo contrato sem comportamentos inesperados. |
| **ISP — Interface Segregation Principle** | Clientes não devem depender de operações que não utilizam. | Interfaces menores e mais coesas. | Pode aumentar a quantidade de interfaces. | Separar interfaces de leitura e escrita quando os clientes possuem necessidades diferentes. |
| **DIP — Dependency Inversion Principle** | Módulos de alto nível devem depender de abstrações e não diretamente de detalhes de implementação. | Reduz acoplamento e facilita testes. | Adiciona abstrações e configuração de dependências. | `PaymentService → PaymentGateway`, não `PaymentService → StripeGateway`. |

```text
SRP → responsabilidade
OCP → extensibilidade
LSP → substituição
ISP → contratos pequenos
DIP → dependência de abstrações
```

---

# 3. Design Patterns

## Strategy

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Strategy Pattern** | Encapsula algoritmos ou comportamentos intercambiáveis atrás de uma abstração comum. | Reduz `if/else`, melhora OCP e facilita testes. | Pode gerar muitas classes quando a variação é pequena. | Pagamento, frete, desconto, autenticação, cálculo de preço. |
| **Strategy + Spring DI** | Spring encontra e injeta implementações do contrato Strategy. | Reduz acoplamento com implementações concretas. | É necessário resolver qual implementação utilizar. | `List<PaymentStrategy>`, `Map<PaymentType, PaymentStrategy>` ou resolver dedicado. |

### Relação com POO e SOLID

```text
                    POO
                     │
             abstração/polimorfismo
                     │
                     ▼
                  Strategy
                     │
              composição
                     │
                     ▼
              PaymentService
                     │
                     ▼
                  SOLID

OCP → adicionamos uma Strategy
DIP → Service depende da interface
LSP → implementações respeitam contrato
SRP → cada Strategy possui responsabilidade específica
```

---

# 4. Template Method

`Template Method` também pertence a **Design Patterns**, e não a Stream ou POO diretamente.

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Template Method** | Uma classe-base define a estrutura geral de um algoritmo enquanto subclasses implementam etapas específicas. | Reutiliza fluxo comum e padroniza processos. | Cria dependência por herança e hierarquias mais rígidas. | Importação de arquivos, processamento de pagamentos, geração de relatórios, jobs. |

Exemplo conceitual:

```text
AbstractImporter

import()
 ├── validate()
 ├── read()
 ├── process()   ← especializado
 └── save()
```
Diferença
| Strategy                             | Template Method                                |
| ------------------------------------ | ---------------------------------------------- |
| Baseado principalmente em composição | Baseado principalmente em herança              |
| Troca algoritmo/comportamento        | Especializa etapas de um algoritmo             |
| Pode variar facilmente em runtime    | Estrutura costuma ser definida pela hierarquia |
| Cliente escolhe Strategy             | Superclasse controla fluxo                     |
| Menor acoplamento estrutural         | Maior acoplamento com classe-base              |
| Excelente para Spring DI             | Útil quando existe workflow realmente estável  |

---

# 5. Records

Records pertencem ao conjunto de **recursos da linguagem Java**, embora sejam muito úteis na modelagem orientada a objetos.

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Record** | Tipo especial de classe destinado principalmente à representação de dados. O compilador gera construtor canônico, accessors, `equals()`, `hashCode()` e `toString()`. | Reduz boilerplate e favorece objetos imutáveis. | Não é apropriado para entidades com ciclo de vida mutável complexo. Não pode estender outra classe. | DTOs, Commands, Responses, Events e Value Objects. |
| **Record como Value Object** | Uso de `record` para representar um conceito de domínio definido por seus valores e sem identidade própria. | Comparação por valor, imutabilidade e invariantes centralizadas. | Não funciona bem quando o domínio exige mutabilidade controlada complexa. | `Money`, `Email`, `Cpf`, `Coordinates`, `DateRange`. |
| **Construtor compacto** | Forma especial do construtor de um record utilizada para validar ou normalizar componentes. | Permite garantir invariantes durante a criação. | Muitas regras podem transformar um record simples em uma abstração difícil de manter. | Validação de e-mail, CPF, valores monetários e intervalos. |

Exemplo:

```java
public record Money(BigDecimal value) {

    public Money {
        Objects.requireNonNull(value);

        if (value.signum() < 0) {
            throw new IllegalArgumentException(
                "Money cannot be negative"
            );
        }
    }
}
```

---

# 6. Sealed Classes e Sealed Interfaces

Também são recursos da linguagem Java relacionados principalmente à **modelagem de hierarquias fechadas**.

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Sealed Class / Interface** | Restringe quais tipos podem herdar ou implementar determinada abstração. | Hierarquia explícita, previsível e controlada. | Impede extensões arbitrárias por terceiros. | Estados, comandos, eventos e resultados. |
| **Sealed + Polimorfismo** | Um contrato possui um conjunto conhecido e restrito de implementações. | Torna o domínio mais seguro e explícito. | Nova implementação exige alteração da hierarquia. | `PaymentResult`, `OrderResult`, `CommandResult`. |
| **Sealed + Pattern Matching** | Combina hierarquias fechadas com pattern matching em `switch`. | Permite tratamento exaustivo e elimina casts manuais. | Pode concentrar comportamento demais em `switch`, reduzindo o benefício do polimorfismo. | Processamento de resultados e eventos. |

Exemplo:

```java
sealed interface PaymentResult
        permits PaymentApproved, PaymentRejected {
}

record PaymentApproved(String transactionId)
        implements PaymentResult {
}

record PaymentRejected(String reason)
        implements PaymentResult {
}
```

---

# 7. Stream API

Agora os conceitos exclusivamente relacionados a Stream.

| Conceito | O que é | Vantagem | Trade-off | Aplicação real |
|---|---|---|---|---|
| **Stream** | API declarativa para processamento de sequências de elementos. Não armazena dados; opera sobre uma fonte. | Código declarativo e facilidade para transformar, filtrar e agregar dados. | Pipelines complexos podem prejudicar legibilidade e debugging. | Processamento de coleções, DTOs e resultados de APIs. |
| **Pipeline** | Fluxo `source → intermediate operations → terminal operation`. | Permite composição clara das transformações. | Pipelines muito extensos dificultam entendimento. | `orders.stream().filter(...).map(...).toList()`. |
| **Operações intermediárias** | Operações que retornam outro Stream. | Permitem construção incremental do pipeline. | Não executam processamento até existir operação terminal. | `filter`, `map`, `flatMap`, `sorted`, `distinct`. |
| **Operações terminais** | Finalizam o pipeline e produzem um resultado ou efeito. | Materializam o processamento. | Depois da operação terminal o Stream não pode ser reutilizado. | `toList`, `reduce`, `count`, `forEach`, `findFirst`. |
| **Lazy Evaluation** | Operações intermediárias são avaliadas apenas quando necessário. | Evita processamento desnecessário. | Pode confundir debugging. | `filter` antes de `map` caro. |
| **Short-circuit** | Permite terminar o processamento antes de consumir todos os elementos. | Pode reduzir consideravelmente processamento. | Benefício depende dos dados e da operação. | `findFirst`, `anyMatch`, `limit`. |
| **map** | Transforma cada elemento em outro valor. | Expressa transformação claramente. | Cadeias muito longas podem esconder lógica de negócio. | Entity → DTO. |
| **flatMap** | Transforma elementos e achata estruturas aninhadas. | Facilita manipulação de coleções dentro de coleções. | Mais difícil de compreender inicialmente. | `List<Order>` → itens de todos os pedidos. |
| **filter** | Mantém somente elementos que satisfazem uma condição. | Expressa seleção de maneira declarativa. | Muitos filtros podem tornar o fluxo difícil de acompanhar. | Pedidos ativos, clientes elegíveis. |
| **reduce** | Combina vários elementos em um único resultado. | Permite agregações genéricas. | Pode ficar menos legível que collectors específicos. | Soma, composição e agregação. |
| **Collectors** | Utilitários para acumular elementos do Stream em estruturas ou agregações. | Simplificam agrupamentos e transformações complexas. | Collectors muito aninhados prejudicam legibilidade. | `groupingBy`, `partitioningBy`, `toMap`. |
| **Primitive Streams** | `IntStream`, `LongStream`, `DoubleStream`. | Evitam boxing/unboxing e fornecem operações numéricas. | API parcialmente diferente de `Stream<T>`. | Cálculos numéricos intensivos. |
| **Stream single-use** | Um Stream só pode ser consumido uma vez. | Simplifica o modelo de execução. | Reutilização causa `IllegalStateException`. | Recriar Stream a partir da coleção original. |
| **Efeitos colaterais** | Alterações de estado externo dentro do pipeline. Devem ser evitadas sempre que possível. | Operações puras facilitam testes, paralelização e raciocínio. | Pode exigir mudança de estilo de programação. | Preferir `map().toList()` em vez de alterar lista externa. |
| **parallelStream()** | Processamento paralelo utilizando normalmente `ForkJoinPool.commonPool()`. | Pode melhorar tarefas CPU-bound suficientemente grandes. | Overhead, concorrência, compartilhamento do common pool e comportamento menos previsível. | Processamento CPU-bound independente e cuidadosamente medido. |

---

# 8. Concorrência — Fundamentos

Primeiro devem vir os conceitos conceituais.

| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Concorrência** | Múltiplas tarefas fazendo progresso durante o mesmo intervalo de tempo. | Maior complexidade, races, sincronização e debugging difícil. | APIs, workers, Kafka consumers. |
| **Paralelismo** | Execução efetivamente simultânea em múltiplos núcleos. | Limitado pela capacidade física da CPU. | Processamento CPU-bound. |
| **Thread** | Unidade de execução dentro de um processo/JVM. | Consome recursos e introduz problemas de sincronização. | Requisições, workers e jobs. |
| **Platform Thread** | Thread Java tradicional normalmente associada a thread nativa do SO. | Custo relativamente alto de memória e scheduling. | Pools tradicionais e workloads CPU-bound. |
| **Virtual Thread** | Thread leve gerenciada principalmente pela JVM, projetada para alta concorrência especialmente em I/O bloqueante. | Não aumenta capacidade de CPU e não elimina sincronização ou race conditions. | Muitas chamadas JDBC, HTTP e I/O bloqueantes. |
| **I/O-bound** | Trabalho cujo tempo é gasto principalmente aguardando recursos externos. | Muitas threads podem permanecer bloqueadas. | JDBC, HTTP, filesystem. |
| **CPU-bound** | Trabalho limitado principalmente pela capacidade de processamento da CPU. | Threads além da capacidade de CPU aumentam context switching. | Compressão, criptografia, cálculos. |

---

# 9. Concorrência — Problemas fundamentais

| Conceito | O que é | Trade-off / risco | Uso real |
|---|---|---|---|
| **Race Condition** | Resultado depende da ordem de execução concorrente entre threads. | Bugs intermitentes e difíceis de reproduzir. | Saldo, estoque, counters. |
| **Critical Section** | Região que acessa estado compartilhado e precisa de coordenação. | Regiões críticas grandes reduzem paralelismo. | Atualização de invariantes compartilhadas. |
| **Atomicidade** | Uma operação é observada como indivisível. | Operações compostas exigem mecanismos adicionais. | Incrementar contador, check-and-update. |
| **Visibilidade** | Garantia de que uma thread observe alterações realizadas por outra. | Visibilidade não significa atomicidade. | Flags, estados compartilhados. |
| **Operação composta** | Sequência de ações que precisa funcionar como uma unidade lógica. | Métodos individualmente thread-safe não garantem atomicidade da sequência. | `get + update`, `containsKey + put`. |
| **Deadlock** | Threads aguardam recursos umas das outras indefinidamente. | Paralisa parte do sistema. | Locks adquiridos em ordem diferente. |
| **Starvation** | Uma thread recebe pouca ou nenhuma oportunidade de executar. | Pode gerar latência indefinidamente alta. | Locks disputados, pools saturados. |
| **Livelock** | Threads continuam executando, mas sem progresso útil. | CPU consumida sem trabalho efetivo. | Retries concorrentes mal coordenados. |

---

# 10. Java Memory Model — JMM

Essa parte deve vir **antes de `volatile` e `synchronized`**, porque esses mecanismos dependem conceitualmente do JMM.

| Conceito | O que é | Trade-off | Uso real |
|---|---|---|---|
| **Java Memory Model — JMM** | Define as regras de visibilidade, ordenação e sincronização entre threads Java. | Conceitualmente complexo. | Base teórica de toda programação concorrente Java. |
| **happens-before** | Relação que garante que os efeitos de uma operação sejam visíveis para outra. | Precisa ser estabelecida por mecanismos definidos pelo JMM. | `volatile`, `synchronized`, `start()`, `join()`. |
| **Visibilidade** | Uma thread observa corretamente as alterações feitas por outra. | Não garante atomicidade. | Flags compartilhadas. |
| **Ordenação** | Define quais reordenações de instruções são permitidas sem violar garantias do JMM. | Pode contrariar a intuição de ordem sequencial do código-fonte. | Explica por que sincronização correta é necessária. |

---

# 11. Sincronização e controle de estado

| Conceito | O que é | Trade-off | Uso real |
|---|---|---|---|
| **`synchronized`** | Monitor intrínseco que fornece exclusão mútua e garantias de visibilidade. | Contenção e bloqueio. | Proteção de invariantes compostas. |
| **`volatile`** | Garante visibilidade e determinadas garantias de ordenação sobre uma variável. | Não torna operações compostas como `count++` atômicas. | Flags como `running`. |
| **`AtomicInteger` / `AtomicLong`** | Tipos com operações atômicas normalmente baseadas em CAS. | Não resolvem facilmente invariantes envolvendo múltiplos valores. | Counters e métricas. |
| **CAS — Compare-And-Set** | Atualiza um valor somente se ele continuar igual ao valor esperado. | Alta contenção pode provocar retries sucessivos. | `Atomic*` e estruturas lock-free. |
| **`ReentrantLock`** | Lock explícito com funcionalidades adicionais. | Mais flexível, porém mais fácil de utilizar incorretamente. | `tryLock`, timeout, lock interruptível. |
| **Imutabilidade** | Estado não muda após criação do objeto. | Pode gerar novas alocações em vez de mutações. | Records, eventos, Value Objects. |
| **`ConcurrentHashMap`** | Map projetado para acesso concorrente eficiente. | Operações isoladas são seguras; várias chamadas em sequência podem não formar operação atômica. | Cache, registros e lookup compartilhado. |

---

# 12. Execução assíncrona e gerenciamento de threads

| Conceito | O que é | Trade-off | Uso real |
|---|---|---|---|
| **`Runnable`** | Representa tarefa sem retorno. | Não retorna valor nem declara checked exception diretamente. | Jobs simples. |
| **`Callable<V>`** | Representa tarefa que retorna valor e pode lançar exceção. | Normalmente usado com executor/Future. | Processamentos assíncronos com resultado. |
| **`ExecutorService`** | Abstração para submissão e gerenciamento de tarefas. | Configuração incorreta pode causar saturação. | Workers e tarefas paralelas. |
| **Thread Pool** | Conjunto reutilizável e normalmente limitado de threads. | Pequeno demais gera filas; grande demais consome recursos. | Spring executors e workers. |
| **`Future<V>`** | Representa resultado futuro de uma tarefa. | `get()` bloqueia e composição é limitada. | Resultado de `submit()`. |
| **`CompletableFuture`** | Abstração para composição assíncrona de tarefas. | Pipelines complexos podem ficar difíceis de manter. | Agregação de APIs independentes. |
| **`ForkJoinPool`** | Executor voltado para divisão recursiva de trabalhos CPU-bound. | Não é adequado para I/O bloqueante prolongado. | Divide-and-conquer e `parallelStream`. |

---

# 13. Coordenação entre threads

| Conceito | O que é | Trade-off | Uso real |
|---|---|---|---|
| **`Semaphore`** | Controla quantas execuções podem acessar um recurso simultaneamente. | Threads podem ficar aguardando permits. | Limitar concorrência para API ou banco. |
| **`CountDownLatch`** | Aguarda determinado número de eventos antes de prosseguir. | Não é reutilizável depois que chega a zero. | Inicialização e testes concorrentes. |
| **`CyclicBarrier`** | Faz várias threads aguardarem umas pelas outras em determinada fase. | Uma thread atrasada pode bloquear todo o grupo. | Processamentos paralelos em fases. |
| **Backpressure / limitação de concorrência** | Controla quanto trabalho entra simultaneamente em determinada parte do sistema. | Sacrifica throughput máximo para manter estabilidade. | Filas, bulkhead, semaphore, rate limiting. |

---

# 14. Concorrência distribuída

Esses conceitos **não devem ficar no mesmo grupo que `synchronized`, `volatile` e `AtomicInteger`**.

Eles solucionam concorrência entre **múltiplas JVMs, pods, instâncias ou consumidores**.

| Conceito | O que é | Trade-off | Uso real |
|---|---|---|---|
| **Concorrência distribuída** | Concorrência envolvendo processos/JVMs distintos compartilhando recursos externos. | Sincronização local da JVM não resolve o problema. | Kubernetes, microsserviços e múltiplas réplicas. |
| **Optimistic Locking** | Detecta conflitos através de versão ou condição equivalente. | Conflitos geram retry/falha. | `@Version` no JPA. |
| **Pessimistic Locking** | Adquire lock no recurso antes da alteração. | Reduz concorrência e pode produzir waits/deadlocks. | Recursos altamente disputados. |
| **Atomic UPDATE** | Banco executa validação e alteração como uma única operação atômica. | Pode deslocar parte da regra para SQL. | Atualização de estoque e counters. |
| **Idempotência** | Repetir a mesma requisição produz o mesmo efeito lógico. | Necessita mecanismo de identificação e armazenamento de requisições já processadas. | Pagamentos, retries e mensageria. |
| **Kafka Partitioning** | Mensagens de uma mesma key são direcionadas à mesma partição, preservando ordem dentro da partição. | Uma key concentrada pode gerar hot partition. | Eventos por `orderId` ou `customerId`. |
| **Distributed Lock** | Lock coordenado externamente entre múltiplos processos. | Complexidade de falhas, timeout e consistência distribuída. | Jobs únicos executando em múltiplas réplicas. |

### Fronteira importante

```text
             Concorrência dentro da JVM

synchronized
volatile
Atomic*
ReentrantLock
ConcurrentHashMap
Semaphore
ExecutorService

                    │
                    │ não protege
                    ▼

         Concorrência entre instâncias

Banco de dados
Optimistic Locking
Pessimistic Locking
Atomic SQL
Idempotência
Kafka Partitioning
Distributed Lock
```

Essa distinção é **muito importante em entrevistas de Java/Spring**.

---

# 15. Garbage Collection — memória da JVM

Primeiro coloque os conceitos estruturais.

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Garbage Collection — GC** | Processo automático que identifica objetos não mais alcançáveis e recupera memória. | Elimina necessidade de gerenciamento manual da memória. | Consome CPU e pode produzir pausas. | Toda aplicação Java. |
| **Heap** | Região da memória utilizada principalmente para alocação de objetos Java. | Gerenciamento automático pelo GC. | Dimensionamento inadequado pode causar pressão de memória. | Objetos, DTOs, collections e caches. |
| **Young Generation** | Região destinada principalmente a objetos recém-criados. | Explora o fato de muitos objetos morrerem rapidamente. | Grande allocation rate gera coleções frequentes. | DTOs e objetos temporários. |
| **Old Generation** | Região associada a objetos que permanecem vivos por mais tempo. | Evita reprocessar objetos longevos constantemente. | Acúmulo pode aumentar pressão de memória. | Caches e estruturas longevas. |

---

# 16. Garbage Collection — eventos de coleta

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **Young / Minor GC** | Coleta focada principalmente nos objetos jovens. | Normalmente mais rápida. | Frequência excessiva pode indicar allocation rate muito elevado. | APIs com criação intensa de objetos temporários. |
| **Major GC** | Termo usado para coletas relacionadas predominantemente à geração velha; o significado exato depende do coletor. | Recupera objetos longevos não utilizados. | Normalmente mais custoso que coleta jovem. | Pressão na Old Generation. |
| **Full GC** | Coleta abrangente da heap, frequentemente envolvendo fases Stop-The-World significativas. | Pode recuperar grande quantidade de memória. | Pode causar grande impacto de latência. | Situações de pressão severa de memória. |

Aqui vale uma correção no resumo original:

> **Major GC e Full GC não são necessariamente sinônimos.**

Isso depende do coletor e da terminologia utilizada pelas ferramentas/JVM.

---

# 17. Garbage Collectors

| Conceito | O que é | Vantagem | Trade-off | Aplicação prática |
|---|---|---|---|---|
| **G1 GC** | Coletor baseado em regiões, projetado para equilibrar throughput e controle de pausas. | Bom comportamento geral e maturidade. | Não necessariamente oferece as menores pausas possíveis. | APIs e aplicações corporativas Java. |
| **ZGC** | Coletor concorrente focado em latência extremamente baixa e escalabilidade de heap. | Pausas extremamente pequenas. | Pode utilizar mais recursos dependendo do workload. | Sistemas sensíveis à latência e heaps grandes. |
| **Shenandoah** | Coletor concorrente focado na redução de pausas. | Baixa latência mesmo com heaps grandes. | Maior custo concorrente de CPU em determinadas cargas. | Serviços com requisitos de latência. |

---

# 18. Problemas de memória

| Conceito | O que é | Vantagem | Trade-off / risco | Aplicação prática |
|---|---|---|---|---|
| **Memory Leak em Java** | Objetos continuam alcançáveis por alguma referência mesmo não sendo mais necessários para a aplicação. | — | Crescimento contínuo do heap e possível `OutOfMemoryError`. | Cache ilimitado, listeners, collections estáticas, `ThreadLocal`. |
| **Alta taxa de alocação** | Aplicação cria grande quantidade de objetos em pouco tempo. | — | Aumenta pressão sobre Young Generation e atividade do GC. | DTOs intermediários, serialização, pipelines excessivos. |
| **`OutOfMemoryError`** | JVM não consegue atender determinada necessidade de memória. | — | Pode derrubar ou comprometer severamente a aplicação. | Heap esgotado, Metaspace, direct memory, native threads. |

---

# Ordem recomendada para seu estudo

Considerando sua preparação para avançar de **Engenheiro Java para Arquitetura de Software**, eu organizaria seu resumo nesta sequência:

```text
JAVA
│
├── 1. Orientação a Objetos
│   ├── Encapsulamento
│   ├── Abstração
│   ├── Polimorfismo
│   ├── Herança
│   └── Composição
│
├── 2. SOLID
│   ├── SRP
│   ├── OCP
│   ├── LSP
│   ├── ISP
│   └── DIP
│
├── 3. Design Patterns
│   ├── Strategy
│   └── Template Method
│
├── 4. Recursos da linguagem
│   ├── Abstract Class
│   ├── Record
│   └── Sealed Classes
│
├── 5. Stream API
│   ├── Pipeline
│   ├── Lazy Evaluation
│   ├── map / flatMap / filter
│   ├── reduce
│   ├── Collectors
│   └── parallelStream
│
├── 6. Concorrência
│   │
│   ├── Fundamentos
│   │   ├── Thread
│   │   ├── Concorrência
│   │   ├── Paralelismo
│   │   ├── CPU-bound
│   │   └── I/O-bound
│   │
│   ├── JMM
│   │   ├── Visibilidade
│   │   ├── Atomicidade
│   │   └── happens-before
│   │
│   ├── Sincronização
│   │   ├── synchronized
│   │   ├── volatile
│   │   ├── Atomic*
│   │   ├── CAS
│   │   └── ReentrantLock
│   │
│   ├── Problemas
│   │   ├── Race Condition
│   │   ├── Deadlock
│   │   ├── Starvation
│   │   └── Livelock
│   │
│   ├── Execução
│   │   ├── Runnable
│   │   ├── Callable
│   │   ├── ExecutorService
│   │   ├── Thread Pool
│   │   ├── Future
│   │   ├── CompletableFuture
│   │   └── ForkJoinPool
│   │
│   ├── Coordenação
│   │   ├── Semaphore
│   │   ├── CountDownLatch
│   │   └── CyclicBarrier
│   │
│   ├── Virtual Threads
│   │
│   └── Concorrência distribuída
│       ├── Optimistic Locking
│       ├── Pessimistic Locking
│       ├── Atomic SQL
│       ├── Idempotência
│       ├── Kafka Partitioning
│       └── Distributed Lock
│
└── 7. JVM / Memory Management
    ├── Heap
    ├── Young Generation
    ├── Old Generation
    │
    ├── Garbage Collection
    │   ├── Young GC
    │   ├── Major GC
    │   └── Full GC
    │
    ├── Garbage Collectors
    │   ├── G1
    │   ├── ZGC
    │   └── Shenandoah
    │
    └── Problemas
        ├── Allocation Rate
        ├── Memory Leak
        └── OutOfMemoryError
```

Essa estrutura elimina os principais cruzamentos conceituais do resumo original: **SOLID deixa de ser tratado como POO**, **Strategy e Template Method ficam em Design Patterns**, **Record/Sealed ficam como recursos de linguagem/modelagem**, **Stream fica isolado como API**, e principalmente **concorrência dentro da JVM fica claramente separada da concorrência distribuída**.
