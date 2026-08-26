Lucas, para **Java moderno**, o objetivo não é decorar todas as JEPs. Para entrevistas Senior/Tech Lead, o mais importante é entender **como a linguagem evoluiu do Java 17 para o 21 e 25**, quais recursos já são estáveis e quais ainda estão amadurecendo.

Java 17, 21 e 25 são releases LTS na política da Oracle. Java 25 foi lançado em setembro de 2025. 

# 1. Java moderno — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Java 17** | LTS que consolidou recursos modernos como Records, Pattern Matching para `instanceof`, Text Blocks e Switch Expressions; Sealed Classes foram finalizadas no 17. | Muito estável e amplamente adotado, mas não possui as principais melhorias de concorrência do Java 21. | Modernização de sistemas Java 8/11 e baseline corporativo. |
| **Java 21** | LTS que trouxe Virtual Threads como recurso final e consolidou Record Patterns e Pattern Matching para `switch`. | Grande ganho de expressividade e escalabilidade para I/O, mas exige entender novos modelos de concorrência. | Microsserviços, APIs, backend moderno. |
| **Java 25** | LTS que continua a evolução de concorrência e pattern matching. Structured Concurrency ainda é preview; Scoped Values tornou-se final. | Recursos preview não devem ser tratados como APIs estáveis de produção sem análise cuidadosa. | Conhecimento estratégico de evolução da plataforma. |
| **Records** | Classe especializada para representar dados, gerando automaticamente construtor, accessors, `equals`, `hashCode` e `toString`. | Reduz boilerplate, mas não é substituto universal para entidades mutáveis ou classes com identidade complexa. | DTOs, Value Objects, respostas de APIs, mensagens. |
| **Sealed Classes** | Permitem controlar quais classes ou interfaces podem estender ou implementar determinado tipo. | Aumentam controle do domínio, mas reduzem extensibilidade aberta. | Hierarquias fechadas de domínio e pattern matching. |
| **Pattern Matching para `instanceof`** | Combina verificação de tipo com criação da variável convertida. | Código mais simples; exige compreender escopo da pattern variable. | Eliminar `instanceof` seguido de cast manual. |
| **Record Patterns** | Permitem testar e desconstruir Records diretamente. Foram finalizados no Java 21. | Excelente expressividade, mas padrões muito aninhados podem prejudicar legibilidade. | Processar modelos hierárquicos e Value Objects. |
| **Pattern Matching para `switch`** | Permite selecionar comportamento baseado no tipo e em patterns. Tornou-se final no Java 21. | Muito poderoso, mas hierarquias mal projetadas podem produzir switches grandes e complexos. | Processamento de tipos de domínio, especialmente com sealed types. |
| **Switch Expressions** | Permitem que `switch` retorne valores e utilize sintaxe `case ->`, evitando fall-through acidental. | Mais seguro e expressivo; exige adaptação de código legado. | Mapeamentos, regras e transformação de valores. |
| **Text Blocks** | Strings multilinha utilizando três aspas duplas. | Melhor legibilidade, mas whitespace e indentação precisam ser entendidos. | JSON, SQL, XML e textos multilinha. |
| **Virtual Threads** | Threads leves gerenciadas pela JVM, finalizadas no Java 21. | Melhoram throughput em aplicações I/O-bound, mas não aceleram processamento CPU-bound. | HTTP, JDBC, microsserviços e aplicações com muito I/O bloqueante. |
| **Structured Concurrency** | Trata tarefas concorrentes relacionadas como uma única unidade de trabalho. | Modelo muito interessante, mas no Java 25 ainda é **preview**. | Agregar chamadas paralelas com tratamento coordenado de falhas e cancelamento. |
| **Scoped Values** | Forma de compartilhar dados imutáveis com métodos chamados e threads filhas dentro de um escopo. Final no Java 25. | Mais restritivo que `ThreadLocal`, propositalmente; não serve para estado mutável arbitrário. | Contexto de request, tenant, autenticação e tracing. |
| **Primitive Patterns** | Evolução do pattern matching para trabalhar também com tipos primitivos. No Java 25 está em terceiro preview. | Ainda não é feature final no Java 25. | Pattern matching uniforme entre tipos primitivos e referências. |
| **Concorrência moderna** | Evolução formada principalmente por Virtual Threads, Structured Concurrency e Scoped Values. | Simplifica I/O concorrente, mas não elimina problemas como contention, race condition e limites de recursos externos. | Backend moderno de alta concorrência. |

Um detalhe importante: `Records`, `Switch Expressions`, `Text Blocks` e Pattern Matching para `instanceof` ficaram finais antes do Java 17 — respectivamente nos JDKs 16, 14, 15 e 16. Eles aparecem no estudo de Java 17 porque fazem parte do conjunto moderno disponível nessa LTS. Sealed Classes, por sua vez, foram finalizadas especificamente no JDK 17. 

---

# 2. Mapa mental das versões

Para entrevista, memorize assim:

```text
Java 17
│
├── Records
├── Sealed Classes
├── Pattern Matching instanceof
├── Switch Expressions
└── Text Blocks


Java 21
│
├── Virtual Threads
├── Record Patterns
├── Pattern Matching switch
└── Structured Concurrency (preview)


Java 25
│
├── Structured Concurrency (preview)
├── Scoped Values
├── Primitive Patterns (preview)
└── evolução de Virtual Threads
```

O salto mais importante é:

```text
Java 17
    ↓
modernização da linguagem

Java 21
    ↓
modernização da concorrência

Java 25
    ↓
amadurecimento dessas APIs
```

---

# 3. Records

Antes:

```java
public class CustomerDto {

    private final Long id;
    private final String name;

    public CustomerDto(Long id, String name) {
        this.id = id;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    // equals
    // hashCode
    // toString
}
```

Com Record:

```java
public record CustomerDto(
        Long id,
        String name
) {}
```

O compilador fornece automaticamente componentes, construtor canônico, accessors, `equals`, `hashCode` e `toString`.

O objetivo do Record é representar:

> **dados como dados.**

Records foram finalizados no Java 16 e são classes que atuam como carriers transparentes de dados. 

### Um detalhe importante

Record não significa objeto profundamente imutável.

```java
public record Order(
        List<Item> items
) {}
```

A referência `items` não pode ser substituída dentro do Record, mas a própria lista pode continuar mutável.

Portanto:

```text
Record
≠
deep immutability
```

Esse é um bom detalhe de entrevista.

---

# 4. Sealed Classes

Sealed Classes permitem modelar hierarquias fechadas.

```java
public sealed interface Payment
        permits Pix, CreditCard, Boleto {
}
```

Depois:

```java
public final class Pix implements Payment {
}

public final class CreditCard implements Payment {
}

public final class Boleto implements Payment {
}
```

Agora sabemos exatamente quais tipos fazem parte daquele domínio.

Isso combina muito bem com Pattern Matching.

```text
Payment
│
├── Pix
├── CreditCard
└── Boleto
```

Sealed Classes foram finalizadas no Java 17. 

Uma boa aplicação é representar:

```text
Payment

OrderResult

Command

Event

DomainResult
```

quando o conjunto de tipos deve ser controlado.

---

# 5. Pattern Matching para `instanceof`

Antes:

```java
if (object instanceof Customer) {

    Customer customer = (Customer) object;

    process(customer);
}
```

Agora:

```java
if (object instanceof Customer customer) {
    process(customer);
}
```

Fazemos:

```text
teste de tipo
+
cast
+
declaração
```

em uma única operação.

Esse recurso tornou-se final no Java 16. 

Não é apenas açúcar sintático.

É parte de uma evolução maior da linguagem em direção ao:

> **data-oriented programming.**

---

# 6. Switch Expressions

O `switch` moderno pode retornar um valor.

Antes:

```java
String description;

switch (status) {

    case APPROVED:
        description = "Approved";
        break;

    case REJECTED:
        description = "Rejected";
        break;

    default:
        description = "Unknown";
}
```

Agora:

```java
String description = switch (status) {

    case APPROVED -> "Approved";

    case REJECTED -> "Rejected";

    default -> "Unknown";
};
```

Isso reduz:

- variável mutável;
- `break`;
- fall-through acidental;
- boilerplate.

Switch Expressions ficaram finais no Java 14. 

---

# 7. Pattern Matching para `switch`

Aqui começa uma mudança bem maior.

Imagine:

```java
sealed interface Payment {}

record Pix(String key) implements Payment {}

record CreditCard(String number) implements Payment {}

record Boleto(String barcode) implements Payment {}
```

Podemos escrever:

```java
return switch (payment) {

    case Pix pix ->
        processPix(pix);

    case CreditCard card ->
        processCard(card);

    case Boleto boleto ->
        processBoleto(boleto);
};
```

Pattern Matching para `switch` tornou-se final no Java 21. 

Agora combine isso com Sealed Classes.

O compilador conhece:

```text
Payment
├── Pix
├── CreditCard
└── Boleto
```

Então consegue analisar a **exhaustividade** do `switch`.

Essa combinação é muito importante:

```text
sealed hierarchy
       +
pattern matching
       +
switch expression
       =
modelagem de domínio muito expressiva
```

---

# 8. Record Patterns

Agora podemos ir além.

Imagine:

```java
record Address(
        String city,
        String state
) {}

record Customer(
        String name,
        Address address
) {}
```

Podemos desconstruir:

```java
if (object instanceof
        Customer(String name,
                 Address(String city, String state))) {

    System.out.println(name);
    System.out.println(city);
}
```

A ideia é:

```text
objeto
 ↓
pattern
 ↓
desestruturação
 ↓
dados
```

Record Patterns foram finalizados no Java 21 e podem inclusive ser aninhados. 

---

# 9. Text Blocks

Antes:

```java
String json =
        "{\n" +
        "  \"name\": \"Lucas\",\n" +
        "  \"active\": true\n" +
        "}";
```

Agora:

```java
String json = """
        {
          "name": "Lucas",
          "active": true
        }
        """;
```

É especialmente interessante para:

```text
JSON
SQL
HTML
XML
GraphQL
```

Text Blocks tornaram-se finais no Java 15. 

---

# 10. Virtual Threads — principal mudança do Java 21

Essa é provavelmente a feature mais importante para backend Java moderno.

Antes, o modelo tradicional era:

```text
Request
   ↓
Platform Thread
   ↓
HTTP / JDBC
   ↓
thread bloqueada
```

Platform Threads utilizam recursos relativamente caros do sistema operacional.

Por isso historicamente usamos:

```text
Thread Pools
```

para controlar o número de threads.

Virtual Threads mudam esse modelo.

```text
Request
   ↓
Virtual Thread
   ↓
I/O bloqueante

carrier thread é liberada quando possível
```

Virtual Threads são implementadas pelo JDK e permitem que muitas threads virtuais compartilhem um conjunto muito menor de platform threads. Elas se tornaram feature final no Java 21. 

Podemos ter:

```java
try (var executor =
        Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> repository.findAll());
}
```

O modelo passa a ser:

```text
1 tarefa
   ↓
1 Virtual Thread
```

em vez de:

```text
milhares de tarefas
        ↓
pool pequeno
        ↓
fila
```

---

# 11. Virtual Threads não tornam CPU mais rápida

Essa pergunta pode aparecer em entrevista.

Imagine:

```text
Máquina
8 CPUs

10.000 Virtual Threads
```

Isso não significa:

```text
10.000 tarefas CPU-bound
executando simultaneamente
```

Continuamos limitados pelos cores disponíveis.

Virtual Threads são especialmente úteis quando temos:

```text
HTTP
JDBC
Socket
File I/O
esperas bloqueantes
```

Portanto:

```text
I/O-bound
    ↓
Virtual Threads podem ajudar muito


CPU-bound
    ↓
paralelismo continua limitado pela CPU
```

---

# 12. Evolução importante: `synchronized` + Virtual Threads

Existe uma atualização moderna que vale conhecer.

No Java 21, determinadas situações envolvendo Virtual Threads e `synchronized` podiam causar **pinning** da Virtual Thread à carrier thread.

No JDK 24 isso foi significativamente melhorado: Virtual Threads bloqueadas em `synchronized` passaram a conseguir liberar a carrier thread na maioria desses casos. Portanto, no Java 25 você já herda essa melhoria. 

Isso é importante porque significa que o conselho antigo:

> "Troque `synchronized` por `ReentrantLock` por causa de Virtual Threads"

não deve ser repetido automaticamente para Java 25.

Hoje você escolhe principalmente considerando o problema de sincronização que precisa resolver.

---

# 13. Structured Concurrency

Considere uma requisição:

```text
GET /dashboard

     ┌──────────── Customer
     │
Request ───────── Orders
     │
     └──────────── Balance
```

Precisamos executar três operações independentes.

Com concorrência tradicional, começamos a lidar com:

```text
Future
cancelamento
exception
timeout
lifecycle
```

Structured Concurrency tenta tratar essas três tarefas como:

> **uma única unidade de trabalho.**

```text
Request
│
└── Task Scope
    ├── Customer
    ├── Orders
    └── Balance
```

Se uma tarefa crítica falha, podemos cancelar as outras de maneira coordenada.

Structured Concurrency melhora principalmente:

- lifecycle;
- propagação de falhas;
- cancelamento;
- observabilidade;
- legibilidade.

No Java 25, porém, Structured Concurrency está em **quinta preview**, não é uma API final. 

Esse detalhe precisa ser dito em entrevista.

---

# 14. Scoped Values

Scoped Values complementam muito bem Virtual Threads.

Imagine um contexto:

```text
request
│
├── userId
├── tenantId
└── correlationId
```

Historicamente poderíamos pensar em:

```java
ThreadLocal
```

Com muitas Virtual Threads, surgiu a necessidade de um modelo melhor para dados contextuais imutáveis.

Scoped Values permitem compartilhar dados dentro de determinado escopo com os métodos chamados e threads filhas.

Eles foram finalizados no Java 25 e foram projetados para serem especialmente úteis junto com Virtual Threads e Structured Concurrency. 

Mapa mental:

```text
Virtual Threads
      ↓
execução concorrente


Structured Concurrency
      ↓
organização das tarefas


Scoped Values
      ↓
propagação de contexto
```

---

# 15. Java 25 e evolução de Pattern Matching

Java 25 continua expandindo Pattern Matching.

Uma das evoluções é permitir padrões envolvendo tipos primitivos.

Conceitualmente:

```java
if (value instanceof byte b) {
    // conversão segura
}
```

Além disso, `switch` pode evoluir para trabalhar de maneira mais uniforme com tipos primitivos.

Porém, no Java 25 isso ainda é **preview**, especificamente a terceira preview da feature. 

Portanto:

```text
Java 21

pattern matching
com referências
+
record patterns
       ↓

Java 25

continua avançando
em direção a patterns
mais uniformes
```

---

# 16. Preview não significa feature de produção consolidada

Essa distinção é muito importante.

Quando uma feature está:

```text
Final
```

ela faz oficialmente parte da plataforma.

Quando está:

```text
Preview
```

ela está disponível para experimentação, mas pode mudar.

Por exemplo, no Java 25:

```text
Virtual Threads
→ FINAL

Scoped Values
→ FINAL

Structured Concurrency
→ PREVIEW

Primitive Patterns
→ PREVIEW
```

Por isso não diga em entrevista:

> "Java 25 trouxe Structured Concurrency como feature final."

Não trouxe.

Inclusive, no Java 26 ela continua em sexta preview, enquanto primitive patterns continuam em quarta preview. Isso mostra que ambas ainda estão sendo refinadas. 

---

# 17. O que realmente estudar para entrevistas

Eu colocaria a prioridade assim:

### Prioridade máxima

```text
Records
Sealed Classes
Pattern Matching
Switch Expressions
Virtual Threads
```

### Prioridade alta

```text
Record Patterns
Pattern Matching switch
Text Blocks
```

### Conhecer arquitetura e conceito

```text
Structured Concurrency
Scoped Values
Primitive Patterns
```

E principalmente saber conectar:

```text
Records
+
Sealed Classes
+
Pattern Matching
+
Switch
```

e:

```text
Virtual Threads
+
Structured Concurrency
+
Scoped Values
```

Esses dois grupos representam bem as duas grandes evoluções recentes do Java:

**modelagem de dados** e **concorrência**.

---

# 18. Resposta objetiva para entrevista

Se o entrevistador perguntar **"Quais recursos de Java moderno você considera mais relevantes?"**, eu responderia:

> Eu separaria a evolução recente do Java principalmente entre melhorias de linguagem e de concorrência.
>
> Em linguagem, considero muito importantes Records, Sealed Classes, Switch Expressions e Pattern Matching. Records reduzem boilerplate para objetos que representam dados, enquanto Sealed Classes permitem criar hierarquias controladas. No Java 21, Record Patterns e Pattern Matching para switch permitem combinar essas estruturas de forma bastante expressiva e com verificação de exhaustividade pelo compilador. 
>
> Em concorrência, a principal mudança do Java 21 são Virtual Threads. Elas tornam o modelo thread-per-request novamente viável em grande escala para workloads I/O-bound, como HTTP e JDBC, aumentando throughput sem exigir o mesmo número de threads do sistema operacional. Elas não tornam processamento CPU-bound mais rápido. 
>
> No Java 25 eu também acompanharia Scoped Values e Structured Concurrency. Scoped Values já são uma feature final e ajudam na propagação de contexto, especialmente com Virtual Threads. Structured Concurrency organiza tarefas relacionadas como uma unidade, melhorando cancelamento, tratamento de falhas e observabilidade, mas ainda é preview no Java 25. 
>
> Então, em produção eu priorizo recursos consolidados das versões LTS e acompanho as previews para entender a direção da plataforma, sem tratar uma API experimental como se já estivesse estabilizada.