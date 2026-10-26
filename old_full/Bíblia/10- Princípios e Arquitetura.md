# 5.1 Princípios de Engenharia de Software

Para nível Senior, o ponto principal não é apenas saber definir `SOLID`, `DRY` ou `KISS`. É saber explicar **quando esses princípios melhoram o design e quando aplicá-los de forma exagerada pode gerar complexidade desnecessária**.

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **SOLID** | Conjunto de cinco princípios para melhorar manutenção, extensibilidade e desacoplamento do software. | Aplicado de forma excessiva pode gerar muitas interfaces, classes e abstrações desnecessárias. | Sistemas de negócio que precisam evoluir sem grandes efeitos colaterais. |
| **SRP — Single Responsibility Principle** | Uma classe ou módulo deve possuir uma responsabilidade coesa e uma razão principal para mudar. | Dividir demais pode gerar classes pequenas demais e navegação excessiva. | Separar regra de negócio, persistência, integração e apresentação. |
| **OCP — Open/Closed Principle** | Software deve ser aberto para extensão e fechado para modificação recorrente. | Criar pontos de extensão antes de existir necessidade pode virar overengineering. | Adicionar novos meios de pagamento por novas implementações de uma interface. |
| **LSP — Liskov Substitution Principle** | Uma implementação deve poder substituir seu tipo base sem quebrar o comportamento esperado pelo cliente. | Hierarquias ruins podem exigir redesign e composição em vez de herança. | Implementações diferentes de `PaymentGateway` respeitando o mesmo contrato. |
| **ISP — Interface Segregation Principle** | Clientes não devem depender de métodos que não utilizam. Prefira contratos menores e focados. | Fragmentação excessiva pode produzir muitas interfaces sem benefício real. | Separar `Reader`, `Writer` e `Deleter` em vez de uma interface genérica enorme. |
| **DIP — Dependency Inversion Principle** | Módulos de alto nível não devem depender de detalhes de baixo nível; ambos devem depender de abstrações. | Abstrações sem necessidade real aumentam indireção. | `OrderService` depender de `PaymentGateway`, e não diretamente de Stripe ou banco. |
| **DRY** | Don't Repeat Yourself. Evitar duplicação de conhecimento ou regra de negócio. | Abstrair cedo demais pode unir códigos parecidos que possuem motivos diferentes para mudar. | Centralizar cálculo de imposto utilizado em diversos fluxos. |
| **KISS** | Keep It Simple. Escolher a solução mais simples que resolva corretamente o problema. | Simplicidade não deve significar ignorar requisitos futuros já conhecidos. | Evitar event-driven architecture quando uma chamada síncrona simples resolve adequadamente. |
| **YAGNI** | You Aren't Gonna Need It. Não implementar funcionalidades ou abstrações sem necessidade concreta. | Aplicado de forma rígida pode ignorar requisitos previsíveis e importantes. | Não criar suporte a cinco bancos se existe apenas um requisito atual. |
| **Separation of Concerns** | Separar partes do sistema de acordo com responsabilidades distintas. | Muitas camadas podem criar indireção e boilerplate. | Controller cuida de HTTP, Service de negócio, Repository de persistência. |
| **High Cohesion** | Elementos de um módulo devem estar fortemente relacionados ao propósito daquele módulo. | Buscar coesão perfeita pode fragmentar excessivamente o domínio. | `OrderService` concentra operações relacionadas ao ciclo de um pedido. |
| **Low Coupling** | Componentes devem possuir o menor número possível de dependências rígidas entre si. | Desacoplamento excessivo pode criar abstrações inúteis e dificultar rastreamento do fluxo. | Serviço depende de uma interface, permitindo trocar a implementação externa. |

---

# 2. SOLID

SOLID representa cinco princípios:

```text
S → Single Responsibility
O → Open/Closed
L → Liskov Substitution
I → Interface Segregation
D → Dependency Inversion
```

Eles estão relacionados, mas resolvem problemas diferentes.

---

## 3. SRP — Single Responsibility Principle

SRP normalmente é explicado como:

> Uma classe deve ter uma única responsabilidade.

Mas uma definição melhor é:

> **Uma classe deve ter uma razão principal para mudar.**

Considere:

```java
class OrderService {

    void createOrder() {
        // regra de negócio
    }

    void saveDatabase() {
        // SQL
    }

    void sendEmail() {
        // SMTP
    }

    void generatePdf() {
        // PDF
    }
}
```

Essa classe pode mudar porque:

- a regra de pedido mudou;
- o banco mudou;
- o provedor de e-mail mudou;
- o formato do PDF mudou.

Existem várias razões diferentes para mudança.

Um design mais coeso poderia separar:

```text
OrderService
    ↓
regra de negócio

OrderRepository
    ↓
persistência

NotificationService
    ↓
notificação

InvoiceGenerator
    ↓
documento
```

SRP não significa:

> uma classe deve possuir apenas um método.

Significa que suas responsabilidades devem pertencer ao mesmo contexto.

---

# 4. OCP — Open/Closed Principle

OCP significa:

> **aberto para extensão e fechado para modificação.**

Imagine:

```java
public void pay(String type) {

    if (type.equals("PIX")) {
        // ...
    }

    if (type.equals("CARD")) {
        // ...
    }

    if (type.equals("BOLETO")) {
        // ...
    }
}
```

Sempre que aparece um novo pagamento:

```text
PIX
CARD
BOLETO
PAYPAL
CRIPTO
...
```

precisamos modificar a mesma classe.

Uma alternativa:

```java
public interface PaymentProcessor {
    void pay(Payment payment);
}
```

Implementações:

```text
PixPaymentProcessor
CardPaymentProcessor
BoletoPaymentProcessor
```

Agora uma nova estratégia normalmente significa:

```text
nova classe
```

em vez de:

```text
alterar vários if/else existentes
```

Esse é o espírito do OCP.

Mas existe um cuidado:

não devemos criar uma arquitetura extensível para vinte cenários hipotéticos que talvez nunca existam.

Aí entra o YAGNI.

---

# 5. LSP — Liskov Substitution Principle

LSP significa que uma implementação deve respeitar o comportamento esperado pelo contrato.

Imagine:

```java
interface Account {

    void withdraw(BigDecimal value);
}
```

Temos:

```java
class CheckingAccount implements Account {
    // permite saque
}
```

E:

```java
class LockedAccount implements Account {

    public void withdraw(BigDecimal value) {
        throw new UnsupportedOperationException();
    }
}
```

Se todo cliente de `Account` espera que:

```java
withdraw()
```

funcione, então `LockedAccount` não é uma substituição válida.

O problema não é apenas de herança.

É de **contrato comportamental**.

Uma implementação não deveria:

- quebrar invariantes;
- exigir condições mais fortes;
- oferecer garantias mais fracas;
- surpreender quem utiliza a abstração.

---

# 6. ISP — Interface Segregation Principle

Considere:

```java
interface Employee {

    void work();

    void drive();

    void approveLoan();

    void manageTeam();
}
```

Agora temos:

```java
class Developer implements Employee
```

e o desenvolvedor precisa implementar:

```java
approveLoan()
```

mesmo sem fazer sentido.

A interface é grande demais.

Uma alternativa:

```text
Worker
Driver
LoanApprover
TeamManager
```

Cada cliente depende apenas do contrato necessário.

A regra é:

> **prefira interfaces focadas em capacidades específicas.**

Mas não transforme cada método automaticamente em uma interface diferente.

---

# 7. DIP — Dependency Inversion Principle

Esse é um dos princípios mais importantes no ecossistema Spring.

Considere:

```java
class UserService {

    private final OracleUserRepository repository =
            new OracleUserRepository();
}
```

Agora:

```text
UserService
      ↓
depende diretamente
      ↓
Oracle
```

O módulo de negócio está acoplado a um detalhe de infraestrutura.

Com DIP:

```java
interface UserRepository {
    void save(User user);
}
```

E:

```java
class UserService {

    private final UserRepository repository;

    UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

Implementações:

```text
OracleUserRepository
PostgresUserRepository
DynamoUserRepository
```

Agora:

```text
UserService
      ↓
UserRepository
      ↑
Oracle / PostgreSQL / Dynamo
```

O domínio depende de uma abstração.

A infraestrutura implementa essa abstração.

Esse conceito aparece diretamente em:

- Clean Architecture;
- Hexagonal Architecture;
- Ports and Adapters;
- Dependency Injection.

---

# 8. Dependency Inversion não é Dependency Injection

Essa distinção é importante.

**Dependency Inversion** é um princípio de design.

**Dependency Injection** é uma técnica para fornecer dependências.

Por exemplo:

```java
public OrderService(PaymentGateway gateway) {
    this.gateway = gateway;
}
```

O fato de o Spring injetar `PaymentGateway` é:

```text
Dependency Injection
```

O fato de `OrderService` depender de uma abstração, e não de Stripe diretamente, é:

```text
Dependency Inversion
```

São relacionados, mas não são a mesma coisa.

---

# 9. DRY

DRY significa:

**Don't Repeat Yourself.**

O objetivo não é simplesmente eliminar linhas de código repetidas.

O verdadeiro problema é:

> **duplicação de conhecimento.**

Imagine que uma regra diga:

```text
clientes premium recebem 15% de desconto
```

E essa regra aparece em:

```text
CheckoutService
InvoiceService
OrderService
PromotionService
```

Se mudar para 20%, precisamos alterar quatro lugares.

Isso é um problema de DRY.

Podemos centralizar:

```java
class DiscountPolicy {

    BigDecimal calculate(Customer customer) {
        // regra única
    }
}
```

Agora existe uma única fonte da regra.

---

# 10. Cuidado com DRY

Nem todo código parecido representa a mesma regra.

Imagine:

```java
calculateEmployeeBonus()
```

e:

```java
calculateCustomerDiscount()
```

Hoje ambos fazem:

```text
valor × 10%
```

Isso não significa que deveriam compartilhar uma abstração.

Porque amanhã:

```text
bonus muda por motivo A

discount muda por motivo B
```

Uma abstração precoce pode criar acoplamento artificial.

Então:

> **DRY é sobre conhecimento duplicado, não apenas código visualmente parecido.**

---

# 11. KISS

KISS significa:

**Keep It Simple.**

A solução mais sofisticada não é automaticamente a melhor.

Imagine um sistema com:

```text
100 requisições por dia
```

E alguém propõe:

```text
Kafka
+
Kubernetes
+
Event Sourcing
+
CQRS
+
Redis
+
Saga
```

quando:

```text
Spring Boot
+
PostgreSQL
```

resolve completamente o problema.

Isso viola KISS.

A arquitetura deve possuir complexidade proporcional ao problema.

---

# 12. KISS não significa código simplista

KISS não quer dizer:

> sempre faça da forma mais fácil.

Quer dizer:

> utilize a solução mais simples que satisfaça corretamente os requisitos.

Se o requisito exige:

```text
alta disponibilidade
milhões de eventos
consistência eventual
replay
```

Kafka pode ser exatamente a solução mais simples **dentro daquele contexto**.

Simplicidade depende do problema.

---

# 13. YAGNI

YAGNI significa:

**You Aren't Gonna Need It.**

Não implemente algo apenas porque talvez um dia seja necessário.

Por exemplo:

o sistema usa apenas PostgreSQL.

Mas decidimos criar:

```text
PostgreSQLAdapter
OracleAdapter
MySQLAdapter
MongoAdapter
DynamoAdapter
```

porque:

> "Talvez um dia troquemos de banco."

Se não existe requisito, provavelmente estamos pagando complexidade hoje por uma hipótese futura.

YAGNI combate esse tipo de overengineering.

---

# 14. YAGNI x extensibilidade

YAGNI não significa ignorar arquitetura.

Por exemplo, fazer:

```java
class OrderService {

    void save() {
        // SQL JDBC diretamente aqui
    }
}
```

somente porque:

> "Hoje funciona."

pode gerar acoplamento evidente.

Podemos usar uma separação simples:

```text
OrderService
     ↓
OrderRepository
```

sem criar cinquenta abstrações.

O equilíbrio é:

```text
boa arquitetura
     +
necessidade real
     -
especulação excessiva
```

---

# 15. Separation of Concerns

Separation of Concerns significa separar responsabilidades diferentes.

Em uma aplicação Spring:

```text
HTTP Request
     ↓
Controller
     ↓
Service
     ↓
Repository
     ↓
Database
```

Cada camada resolve uma preocupação diferente.

### Controller

Cuida de:

```text
HTTP
status codes
request
response
```

### Service

Cuida de:

```text
regra de negócio
orquestração
transação
```

### Repository

Cuida de:

```text
persistência
queries
```

Um controller não deveria normalmente conter regras complexas de negócio.

Da mesma forma, um repository não deveria decidir regras comerciais.

---

# 16. High Cohesion

High Cohesion significa que elementos pertencentes ao mesmo componente estão fortemente relacionados.

Uma boa classe:

```text
PaymentService

authorizePayment()
capturePayment()
cancelPayment()
refundPayment()
```

Existe coesão.

Tudo está relacionado a pagamento.

Agora:

```text
Utils

calculateTax()
sendEmail()
formatDate()
saveFile()
validateCPF()
generatePassword()
```

possui baixa coesão.

Os métodos não pertencem claramente ao mesmo conceito.

Por isso classes como:

```text
Utils
Helper
Manager
CommonService
```

muitas vezes merecem atenção.

Podem estar escondendo responsabilidades diferentes.

---

# 17. Low Coupling

Low Coupling significa minimizar dependências rígidas entre componentes.

Imagine:

```text
OrderService
   ↓
StripeClient
```

Se `OrderService` conhece diretamente:

```text
StripeRequest
StripeResponse
StripeException
StripeConfig
```

o domínio fica fortemente acoplado ao Stripe.

Uma alternativa:

```text
OrderService
     ↓
PaymentGateway
     ↑
StripePaymentAdapter
```

Agora podemos trocar:

```text
Stripe
```

por:

```text
Adyen
MercadoPago
Outro provider
```

com menos impacto no domínio.

---

# 18. High Cohesion + Low Coupling

Esses dois conceitos devem ser estudados juntos.

Uma boa arquitetura busca:

```text
dentro do módulo
     ↓
HIGH COHESION


entre módulos
     ↓
LOW COUPLING
```

Ou seja:

as coisas que pertencem juntas ficam juntas.

As coisas que não precisam se conhecer ficam desacopladas.

Essa combinação melhora:

- manutenção;
- testabilidade;
- evolução;
- legibilidade;
- isolamento de mudanças.

---

# 19. Como os princípios se conectam

Uma visão útil é:

```text
SRP
 ↓
responsabilidade clara

Separation of Concerns
 ↓
separa responsabilidades

High Cohesion
 ↓
mantém coisas relacionadas juntas

Low Coupling
 ↓
reduz dependências entre componentes

DIP
 ↓
dependência através de abstrações

OCP
 ↓
facilita extensão

LSP
 ↓
garante contratos confiáveis

ISP
 ↓
mantém contratos específicos
```

Enquanto:

```text
DRY
 ↓
evita conhecimento duplicado

KISS
 ↓
evita complexidade desnecessária

YAGNI
 ↓
evita implementar o futuro imaginário
```

---

# 20. O maior erro: transformar princípios em regras absolutas

Princípios de engenharia não são leis.

Por exemplo:

```text
DRY levado ao extremo
       ↓
abstrações genéricas demais
```

```text
OCP levado ao extremo
       ↓
interfaces para tudo
```

```text
SRP levado ao extremo
       ↓
centenas de classes minúsculas
```

```text
Low Coupling levado ao extremo
       ↓
camadas de abstração inúteis
```

```text
YAGNI levado ao extremo
       ↓
design incapaz de evoluir
```

Uma resposta de nível Senior demonstra exatamente isso:

> **os princípios são ferramentas para gerenciar mudança e complexidade, não objetivos isolados.**

---

# Resposta objetiva para entrevista

Se perguntarem **"Quais princípios de design você utiliza no desenvolvimento?"**, uma resposta consistente seria:

> Eu utilizo princípios como SOLID, DRY, KISS, YAGNI, Separation of Concerns, High Cohesion e Low Coupling principalmente para controlar acoplamento e facilitar evolução do código.
>
> Dentro do SOLID, SRP ajuda a manter responsabilidades coesas; OCP favorece extensão sem alteração recorrente de código existente; LSP garante que implementações respeitem seus contratos; ISP evita interfaces grandes que obrigam clientes a depender de comportamentos desnecessários; e DIP faz módulos de negócio dependerem de abstrações em vez de detalhes de infraestrutura.
>
> Também aplico DRY para evitar duplicação de conhecimento, mas evito abstração prematura só porque dois trechos de código parecem semelhantes.
>
> KISS me ajuda a escolher a solução mais simples que atende corretamente aos requisitos, enquanto YAGNI evita criar funcionalidades ou extensões baseadas apenas em necessidades hipotéticas.
>
> Em arquitetura, procuro alta coesão dentro dos componentes e baixo acoplamento entre eles, normalmente aplicando Separation of Concerns entre responsabilidades como apresentação, negócio e persistência.
>
> O ponto principal para mim é não aplicar esses princípios de maneira dogmática. Eles servem para **reduzir o custo de mudança e controlar complexidade**, e não para aumentar o número de interfaces, classes ou camadas sem necessidade.
