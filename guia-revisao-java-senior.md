# Guia de revisão — Java Sênior (Java 17/21 + Spring)

> **Objetivo:** revisar os conceitos das três vagas com foco em decisões técnicas, trade-offs e respostas de entrevista. Use como checklist: se consegue explicar o tópico e citar um caso real, avance.

**Prioridade:** **P0** = dominar; **P1** = saber aplicar e discutir; **P2** = diferencial.

| Prioridade | Blocos de estudo |
| --- | --- |
| **P0** | Java, Spring Boot, REST/Security, SQL/JDBC, testes, SOLID, arquitetura, concorrência |
| **P1** | Redis, MongoDB, mensageria, microsserviços, resiliência, Docker, CI/CD, observabilidade |
| **P2** | Azure específica, Kubernetes avançado, React, Gitflow detalhado |

---

## 1. Java 17/21 e fundamentos — P0 (aprofundamento para entrevista)

**Como responder no nível sênior:** comece pelo conceito em uma frase; mostre um exemplo de código ou de negócio; explique um trade-off; termine com o risco que você observaria em produção. As perguntas abaixo já incluem o essencial de uma boa resposta.

### 1.1 POO — pilares e composição versus herança

**Pergunta: quais são os quatro pilares da orientação a objetos?**

- **Encapsulamento:** protege o estado e obriga alterações a passar por operações que preservam regras. Ex.: `Pedido.confirmar()` valida itens e status; um `setStatus()` público permitiria estados inválidos.
- **Abstração:** expõe o que um objeto faz sem revelar detalhes. Ex.: `GatewayPagamento.autorizar()` esconde chamadas HTTP e provedores.
- **Herança:** permite especializar um tipo quando existe uma verdadeira relação **é um** e um contrato substituível. Ex.: diferentes implementações de uma classe base de notificação, se de fato compartilham contrato.
- **Polimorfismo:** a mesma chamada funciona com implementações diferentes. Ex.: um serviço recebe `GatewayPagamento`, mas executa `PixGateway` ou `CartaoGateway`.

**Pergunta: por que preferir composição à herança?** Composição usa objetos colaboradores (*tem um*), evitando subclasses que dependem de detalhes internos da classe-pai. Escolha herança só quando a substituição fizer sentido e respeitar contratos (Liskov). Uma classe `PedidoService` **tem um** `GatewayPagamento`; ela não **é um** gateway.

```java
interface GatewayPagamento {
    void autorizar(Pedido pedido);
}

class PedidoService {
    private final GatewayPagamento gateway;

    PedidoService(GatewayPagamento gateway) { this.gateway = gateway; }

    void pagar(Pedido pedido) { gateway.autorizar(pedido); }
}
```

**Follow-up provável:** “Isso também é inversão de dependência?” Sim: a regra de negócio depende do **contrato**, não da implementação específica nem do framework.

### 1.2 `equals()` e `hashCode()` — contrato e problemas reais

**Pergunta: por que sobrescrever `hashCode()` quando sobrescrevemos `equals()`?** O contrato exige que **dois objetos iguais retornem o mesmo hash**. `HashMap`/`HashSet` usam o hash para localizar a região de busca e `equals()` para distinguir chaves/elementos equivalentes. Hashes iguais **não** significam que os objetos são iguais; colisões são normais.

**Pergunta: o que acontece se a chave do `HashMap` mudar depois da inserção?** Se um campo usado em `equals()`/`hashCode()` mudar, a busca pode procurar em outra posição e não encontrar a chave, mesmo que ela esteja armazenada. Use chaves imutáveis; cuidado com entidades cujo ID é atribuído só após persistência.

**Pontos que o entrevistador pode explorar:**

- `equals()` deve ser reflexivo, simétrico, transitivo, consistente e retornar `false` para `null`.
- Mesmo hash é **permitido** para objetos diferentes; hashes diferentes são **proibidos** para objetos iguais.
- `==` compara identidade de referências; `equals()` compara igualdade lógica **conforme implementado**.
- `record` fornece `equals()`, `hashCode()` e `toString()` baseados em seus componentes, o que o torna conveniente para objetos de valor simples.

### 1.3 `record`, `sealed` e novidades do Java 17/21

**Pergunta: quando usar `record` em vez de classe tradicional?** Para carregar dados com identidade definida pelo valor, principalmente DTOs e resultados imutáveis. O compilador gera construtor, acessores, `equals()`, `hashCode()` e `toString()`. O `record` é `final` e não pode estender outra classe; pode implementar interfaces.

```java
record PedidoResponse(Long id, String status, List<String> itens) {
    PedidoResponse {
        itens = List.copyOf(itens); // cópia defensiva: lista não modificável
    }
}
```

**Armadilha:** `record` dá imutabilidade **superficial**. Uma lista mutável recebida sem cópia poderia continuar sendo alterada fora do objeto. Evite usar `record` indiscriminadamente em entidades JPA cujo ciclo de vida depende de identidade, proxies e mutabilidade.

**Pergunta: para que serve `sealed`?** Controlar quais tipos podem implementar ou estender uma hierarquia. Isso explicita todas as variantes relevantes e combina bem com `switch` por padrões.

```java
sealed interface Resultado permits Sucesso, Falha {}
record Sucesso(String protocolo) implements Resultado {}
record Falha(String motivo) implements Resultado {}

class ResultadoFormatter {
    static String mensagem(Resultado resultado) {
        return switch (resultado) {
            case Sucesso(var protocolo) -> "OK: " + protocolo;
            case Falha(var motivo) -> "Erro: " + motivo;
        };
    }
}
```

**Pergunta: o que é relevante nas versões?**

| No Java 17 (já disponível) | No Java 21 (novidade consolidada) |
| --- | --- |
| `record`, `sealed`, *switch expressions*, *text blocks*, `instanceof` com padrão | **Virtual threads**; **sequenced collections**; **record patterns**; *pattern matching* em `switch` |

> Atenção à cronologia: nem tudo da coluna “Java 17” estreou no 17; são recursos já disponíveis nessa versão. `record` tornou-se definitivo no 16; `sealed`, no 17. No 21, os recursos listados são finais (não dependem de preview).

**Pergunta: o que são sequenced collections?** Interfaces como `SequencedCollection` expõem operações padronizadas sobre primeiro/último elemento, ordem de encontro e visão reversa (`reversed()`), quando o tipo implementa esse contrato. Não significa que todo `Set` ou `Map` comum passou a ter ordem definida.

### 1.4 Collections, estruturas de dados e complexidade

**Pergunta: quando usar `List`, `Set`, `Map`, `Queue` e `Deque`?**

- **`List`:** sequência acessível por índice; permite duplicatas. Use em itens de um pedido.
- **`Set`:** conjunto de valores únicos segundo igualdade/comparação; use para permissões ou IDs já vistos.
- **`Map`:** associa chaves a valores; use para buscar configurações por chave. `Map` não estende `Collection`.
- **`Queue`:** processamento em fila, geralmente FIFO; **`Deque`** insere/remove nas duas extremidades e serve também como pilha LIFO.

**Pergunta: por que `ArrayList` costuma ser preferível a `LinkedList`?** `ArrayList` armazena referências contiguamente, permite acesso O(1) por índice e costuma ter melhor localidade de memória. `LinkedList` precisa percorrer nós para acessar posição; a inserção pode ser O(1) **depois de localizar o nó**, mas localizar por índice é O(n). Na prática, alocação e overhead de nós também pesam.

| Implementação | Busca/acesso | Inserir/remover | Trade-off principal |
| --- | --- | --- | --- |
| `ArrayList` | índice O(1); valor O(n) | fim amortizado O(1); meio O(n) | boa para leitura e iteração |
| `LinkedList` | índice/valor O(n) | extremidades O(1) | maior custo de memória e percurso |
| `HashMap` / `HashSet` | média O(1) | média O(1) | sem ordem garantida; colisões possíveis |
| `LinkedHashMap` | média O(1) | média O(1) | preserva ordem definida (ex.: inserção), com overhead |
| `TreeMap` / `TreeSet` | O(log n) | O(log n) | mantém ordenação por chave/comparador |
| `ArrayDeque` | extremidades O(1) amortizado | extremidades O(1) amortizado | ótima fila/pilha, não aceita `null` |
| `PriorityQueue` (heap) | consultar mínimo O(1) | adicionar/remover mínimo O(log n) | prioridade, não FIFO e não iteração ordenada |

**Pergunta: uma fila e uma pilha resolvem quais problemas?** **BFS** (*busca em largura*) usa fila e é útil para menor caminho em grafo não ponderado; **DFS** (*busca em profundidade*) usa pilha/recursão, útil para percorrer árvores, detectar ciclos e explorar caminhos. **Heap** é uma árvore com propriedade de prioridade; não equivale a árvore binária de busca.

**Pergunta: `HashMap` × `ConcurrentHashMap`?** `HashMap` não é seguro para mutações concorrentes; `ConcurrentHashMap` coordena acesso de múltiplas threads e oferece operações atômicas específicas como `computeIfAbsent`. Mesmo nele, a sequência `if (!containsKey) put(...)` pode ter corrida: prefira operações compostas atômicas. `ConcurrentHashMap` não admite chaves/valores `null`.

### 1.5 Generics e PECS

**Pergunta: por que `List<Integer>` não pode ser atribuída a `List<Number>`?** Generics são **invariantes**: se fosse permitido, alguém poderia adicionar um `Double` à lista de inteiros, quebrando a segurança de tipos.

```java
static double somar(List<? extends Number> valores) {
    return valores.stream().mapToDouble(Number::doubleValue).sum();
}

static void adicionarInteiros(List<? super Integer> destino) {
    destino.add(10);
}
```

**Pergunta: explique PECS.** **Producer Extends, Consumer Super**. Use `? extends T` quando a estrutura **produz** valores que você lê como `T`; use `? super T` quando ela **consome** valores do tipo `T` que você insere. Em `extends`, não se pode adicionar normalmente um `T` arbitrário; em `super`, ler produz apenas `Object` com segurança.

**Pergunta: o que é *type erasure*?** O compilador verifica os tipos genéricos, mas apaga boa parte dessa informação em runtime. Por isso, não é possível usar `new T()` ou `instanceof List<String>` diretamente; também não se pode sobrecarregar métodos que diferem apenas pelo parâmetro genérico apagado.

### 1.6 Stream API — semântica, armadilhas e performance

**Pergunta: diferença entre `map`, `filter`, `flatMap` e `reduce`?**

- `filter`: conserva elementos que satisfazem um predicado.
- `map`: transforma cada elemento em outro valor.
- `flatMap`: transforma cada elemento em um fluxo e **achata** os fluxos resultantes; útil para obter todos os itens de vários pedidos.
- `reduce`: combina elementos em um valor; para coleções mutáveis, `collect` costuma ser a operação apropriada.

```java
List<String> skus = pedidos.stream()
    .flatMap(p -> p.itens().stream())
    .filter(item -> item.ativo())
    .map(Item::sku)
    .distinct()
    .toList();
```

**Pergunta: o que significa *lazy evaluation*?** Operações intermediárias (`filter`, `map`) montam o pipeline; a execução ocorre com uma operação terminal (`toList`, `collect`, `forEach`, `count`). Um `Stream` é consumível **uma vez**. `toList()` retorna lista não modificável; não assuma isso de qualquer `Collectors.toList()`.

**Pergunta: quando evitar `parallelStream()`?** Em cargas pequenas, I/O bloqueante, código com efeitos colaterais, coletores inadequados ou quando competir pelo *common pool* prejudica outros fluxos. Pode ser útil em tarefas CPU-bound independentes, com dados suficientes e ganho **medido**. Não faça mutação de um `ArrayList` compartilhado dentro de `parallelStream()`.

**Follow-up:** Stream ou `for` tradicional? Use o que comunica melhor a intenção; quando fluxo contém regras complexas, mutação delicada ou otimização comprovada, um loop explícito pode ser mais claro.

### 1.7 Concorrência — visibilidade, atomicidade, locks e deadlock

**Pergunta: qual é a diferença entre `volatile`, `synchronized` e `AtomicInteger`?**

- **`volatile`:** garante visibilidade de leitura/escrita e ordenação relevante pelo modelo de memória. **Não** transforma `contador++` em operação atômica, pois há leitura, cálculo e escrita.
- **`synchronized`:** estabelece exclusão mútua no mesmo monitor e relações de visibilidade (*happens-before*); protege invariantes envolvendo **várias operações/campos**.
- **`AtomicInteger`:** disponibiliza operações atômicas (`incrementAndGet`, CAS) para um contador/valor específico, frequentemente sem precisar de um lock explícito.
- **`ReentrantLock`:** permite `tryLock`, timeout, lock interruptível e maior controle; exige `unlock()` em `finally`.

```java
AtomicInteger total = new AtomicInteger();
total.incrementAndGet(); // incremento atômico entre threads da mesma JVM
```

**Pergunta: o que são race condition e deadlock?** *Race condition* ocorre quando o resultado depende de uma intercalação imprevisível de threads, como duas requisições decrementando saldo sem sincronização. *Deadlock* ocorre quando threads ficam presas esperando recursos umas das outras. Reduza deadlocks com ordem global de aquisição de locks, tempo limitado de espera, regiões críticas curtas e evitando I/O dentro de locks.

**Pergunta sênior: isso protege duas instâncias de um microsserviço?** **Não.** `synchronized`, `AtomicInteger` e `ConcurrentHashMap` atuam dentro de **uma JVM**. Para integridade entre réplicas, use transação e restrições do banco, controle de versão/lock otimista ou coordenação distribuída justificada. Locks distribuídos precisam de tratamento de expiração e falhas; não são substituto automático de transações.

### 1.8 Virtual threads, ExecutorService e CompletableFuture

**Pergunta: por que virtual threads são importantes no Java 21?** São threads leves gerenciadas pelo runtime, permitindo atender muitas tarefas concorrentes que **esperam I/O**, preservando um estilo sequencial de programação. Uma thread virtual bloqueada em I/O suportado pode liberar sua thread de plataforma para executar outra tarefa. **Não acelera processamento CPU-bound**.

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    var cliente = executor.submit(() -> consultarClienteExterno(1L));
    var estoque = executor.submit(() -> consultarEstoqueExterno(1L));
    System.out.println(cliente.get() + " / " + estoque.get());
}
```

**Pergunta: virtual threads eliminam pool JDBC e limites de concorrência?** **Não.** O banco tem um número limitado de conexões, e APIs externas têm quotas. Não crie um *pool de virtual threads* apenas para economizá-las; limite o acesso a **recursos escassos** por pools, semáforos ou controles de carga. No JDK 21, operações bloqueantes dentro de certas regiões `synchronized` podem causar *pinning*, reduzindo escalabilidade.

**Pergunta: virtual thread × `CompletableFuture`?** Virtual threads permitem concorrência com código bloqueante legível; `CompletableFuture` permite **compor** tarefas assíncronas, transformar resultados e tratar falhas. Não são equivalentes e podem coexistir.

- **`thenApply(fn)`** transforma o valor disponível.
- **`thenCompose(fn)`** encadeia outra operação que retorna `CompletableFuture`, evitando futuro aninhado.
- **`thenCombine(...)`** combina duas operações independentes.
- **`exceptionally(...)` / `handle(...)`** tratam falhas; defina se haverá fallback, propagação ou erro de negócio.
- **`ExecutorService`:** controla execução e ciclo de vida; não use indiscriminadamente o *common pool* para I/O pesado. Faça shutdown/cancelamento e configure timeouts.

**Follow-up:** “Qual você usaria para chamar três APIs?” Depende de latência, complexidade de composição, bibliotecas, observabilidade, limites do provedor e clareza. Explique também **timeouts, falhas parciais e propagação de contexto** (trace/security).

### 1.9 Exceções — checked, unchecked e desenho da API

**Pergunta: checked vs unchecked?** *Checked exceptions* (`IOException`, por exemplo) exigem captura ou declaração; *unchecked* (`RuntimeException`) não exigem. Checked pode explicitar falha recuperável em contrato; unchecked costuma representar violações de pré-condições ou falhas que a camada atual não consegue resolver. Não é regra absoluta: considere o contrato público e o contexto.

**Pergunta: como tratar exceções num serviço Spring?** Preserve a causa (`new MinhaException("...", causa)`), traduza erros técnicos nas fronteiras adequadas, exponha erro HTTP consistente por `@RestControllerAdvice` e não retorne detalhes internos sensíveis. Faça log com contexto **uma vez em nível apropriado**; evite capturar `Exception` e devolver sucesso artificial.

**Armadilha:** `finally`/`try-with-resources` liberam recursos; `@Transactional` reverte por padrão em `RuntimeException`/`Error`, mas **não automaticamente** em toda checked exception, sem configuração.

### 1.10 Design Patterns — reconhecer o problema antes do nome

| Padrão | Problema que resolve | Exemplo de entrevista |
| --- | --- | --- |
| **Strategy** | Variar algoritmo sem vários `if/else` espalhados | cálculo de frete por modalidade |
| **Factory** | Centralizar decisão/criação de objetos | instanciar adaptador segundo provedor |
| **Adapter** | Converter interface externa para contrato interno | gateway que traduz API de pagamento |
| **Decorator** | Acrescentar comportamento sem modificar classe-base | adicionar métricas/retry a um cliente |
| **Observer** | Notificar assinantes sobre mudança | eventos internos de domínio; atenção à entrega |
| **Builder** | Montar objeto complexo com opções legíveis | configuração de cliente/objeto de teste |
| **Singleton** | Uma instância sob determinado escopo | bean singleton no contexto Spring; estado mutável exige cuidado |

**Pergunta: Strategy e Factory se complementam?** Sim. **Strategy** define como variar o comportamento; **Factory** pode selecionar/criar qual implementação usar. Em Spring, injeção de dependência pode reduzir a necessidade de factories manuais.

**Armadilha sênior:** padrão não é objetivo. Evite criar interfaces e camadas para regras sem variação real; explique custo de abstração, teste e manutenção.

### 1.11 Simulado rápido — responda sem consultar

1. Como garantir que um objeto `Pedido` não entre em estado inválido? **Encapsulamento**: operações de domínio e invariantes, evitando setters irrestritos.
2. Por que `HashSet` pode permitir dois objetos “iguais” na visão do negócio? `equals()`/`hashCode()` incorretos ou sem igualdade de negócio definida.
3. Como evitar que um `record` exponha uma lista mutável? Fazer **cópia defensiva** ao construir e devolver apenas estrutura não modificável.
4. Quando você escolheria `TreeMap`? Quando precisa de chaves ordenadas, navegação por intervalo ou consultas predecessor/sucessor; aceita O(log n).
5. Por que `List<? extends Number>` não permite `add(10)`? O tipo real pode ser `List<Double>`; adicionar `Integer` quebraria a segurança de tipos.
6. `map` versus `flatMap`? Um transforma valores; o outro transforma em fluxos e os achata.
7. Por que `volatile int contador` ainda perde incrementos? `++` exige três etapas; **visibilidade não é atomicidade**.
8. `ConcurrentHashMap` protege seu saldo em três pods? Não: apenas concorrência local à JVM, sem transação distribuída.
9. Quando virtual threads não dão ganho? Trabalho CPU-bound ou quando o gargalo é banco/serviço externo saturado.
10. Como usar `CompletableFuture` sem criar `CompletableFuture<CompletableFuture<T>>`? Encadeando com **`thenCompose`**.
11. Quando `parallelStream()` piora performance? Overhead, tarefas pequenas, disputa pelo pool, bloqueio e efeitos colaterais.
12. O que você diria ao ser perguntado “qual pattern usaria?” Primeiro identificaria **a variação ou acoplamento concreto**; só depois escolheria o padrão mais simples.

**Treino recomendado:** escolha 4 perguntas por dia e responda em **60–90 segundos**, com exemplo real e trade-off. Se a resposta ficar só na definição, aprofunde antes de avançar para Spring.

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
