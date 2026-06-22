Lucas, segue o resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **SOLID**.

# Versão em Português

## Mapa mental geral

| Letra | Princípio                       | Ideia principal                                                           | Objetivo                        |
| ----- | ------------------------------- | ------------------------------------------------------------------------- | ------------------------------- |
| **S** | Single Responsibility Principle | Uma classe deve ter uma única responsabilidade principal                  | Aumentar coesão                 |
| **O** | Open/Closed Principle           | Aberto para extensão, fechado para modificação                            | Evitar alterar código estável   |
| **L** | Liskov Substitution Principle   | Subclasses devem poder substituir suas classes base sem quebrar o sistema | Garantir herança correta        |
| **I** | Interface Segregation Principle | Interfaces pequenas e específicas são melhores que interfaces grandes     | Evitar contratos desnecessários |
| **D** | Dependency Inversion Principle  | Dependa de abstrações, não de implementações concretas                    | Reduzir acoplamento             |

---

# 1. S — Single Responsibility Principle

## Conceito

| Aspecto                | Resumo                                                                                        |
| ---------------------- | --------------------------------------------------------------------------------------------- |
| **Nome**               | Princípio da Responsabilidade Única                                                           |
| **Ideia**              | Uma classe deve ter apenas um motivo principal para mudar.                                    |
| **Foco**               | Coesão.                                                                                       |
| **Problema que evita** | Classes grandes, confusas e difíceis de testar.                                               |
| **Sinal de violação**  | Uma mesma classe valida, calcula, persiste, envia e-mail, gera PDF e registra log de negócio. |

---

## Exemplo ruim

```java
public class OrderService {

    public void createOrder(Order order) {
        validateOrder(order);
        calculateTotal(order);
        saveToDatabase(order);
        sendConfirmationEmail(order);
    }

    private void validateOrder(Order order) {
        // valida pedido
    }

    private void calculateTotal(Order order) {
        // calcula total
    }

    private void saveToDatabase(Order order) {
        // salva no banco
    }

    private void sendConfirmationEmail(Order order) {
        // envia e-mail
    }
}
```

## Problema

| Responsabilidade | Está dentro de `OrderService`? |
| ---------------- | ------------------------------ |
| Validar pedido   | Sim                            |
| Calcular total   | Sim                            |
| Persistir pedido | Sim                            |
| Enviar e-mail    | Sim                            |
| Orquestrar fluxo | Sim                            |

A classe tem responsabilidades demais.

---

## Exemplo melhor

```java
public class OrderService {

    private final OrderValidator orderValidator;
    private final OrderCalculator orderCalculator;
    private final OrderRepository orderRepository;
    private final EmailService emailService;

    public OrderService(
            OrderValidator orderValidator,
            OrderCalculator orderCalculator,
            OrderRepository orderRepository,
            EmailService emailService
    ) {
        this.orderValidator = orderValidator;
        this.orderCalculator = orderCalculator;
        this.orderRepository = orderRepository;
        this.emailService = emailService;
    }

    public void createOrder(Order order) {
        orderValidator.validate(order);
        orderCalculator.calculateTotal(order);
        orderRepository.save(order);
        emailService.sendConfirmation(order);
    }
}
```

## Separação de responsabilidades

| Classe            | Responsabilidade         |
| ----------------- | ------------------------ |
| `OrderService`    | Orquestrar o caso de uso |
| `OrderValidator`  | Validar regras do pedido |
| `OrderCalculator` | Calcular valores         |
| `OrderRepository` | Persistir dados          |
| `EmailService`    | Enviar notificação       |

---

# 2. O — Open/Closed Principle

## Conceito

| Aspecto                | Resumo                                                                    |
| ---------------------- | ------------------------------------------------------------------------- |
| **Nome**               | Princípio Aberto/Fechado                                                  |
| **Ideia**              | O código deve permitir novos comportamentos sem alterar código existente. |
| **Aberto para**        | Extensão.                                                                 |
| **Fechado para**       | Modificação direta de código já testado e estável.                        |
| **Problema que evita** | Muitos `if`, `else` ou `switch` crescendo com novas regras.               |

---

## Exemplo ruim

```java
public class DiscountCalculator {

    public BigDecimal calculate(String customerType, BigDecimal amount) {
        if (customerType.equals("REGULAR")) {
            return amount.multiply(new BigDecimal("0.05"));
        }

        if (customerType.equals("PREMIUM")) {
            return amount.multiply(new BigDecimal("0.10"));
        }

        if (customerType.equals("VIP")) {
            return amount.multiply(new BigDecimal("0.20"));
        }

        return BigDecimal.ZERO;
    }
}
```

## Problema

| Nova regra                | Consequência                                         |
| ------------------------- | ---------------------------------------------------- |
| Novo tipo de cliente      | Precisa alterar `DiscountCalculator`                 |
| Nova política de desconto | Precisa adicionar mais `if`                          |
| Código cresce             | Fica mais difícil testar                             |
| Risco de bug              | Alterar código existente pode quebrar regras antigas |

---

## Exemplo melhor com Strategy

```java
public interface DiscountPolicy {
    boolean supports(CustomerType customerType);
    BigDecimal calculate(BigDecimal amount);
}
```

```java
public class RegularCustomerDiscount implements DiscountPolicy {

    @Override
    public boolean supports(CustomerType customerType) {
        return customerType == CustomerType.REGULAR;
    }

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.05"));
    }
}
```

```java
public class PremiumCustomerDiscount implements DiscountPolicy {

    @Override
    public boolean supports(CustomerType customerType) {
        return customerType == CustomerType.PREMIUM;
    }

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.10"));
    }
}
```

```java
public class DiscountCalculator {

    private final List<DiscountPolicy> policies;

    public DiscountCalculator(List<DiscountPolicy> policies) {
        this.policies = policies;
    }

    public BigDecimal calculate(CustomerType customerType, BigDecimal amount) {
        return policies.stream()
                .filter(policy -> policy.supports(customerType))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("Unsupported customer type"))
                .calculate(amount);
    }
}
```

## Benefício

| Antes                       | Depois                                   |
| --------------------------- | ---------------------------------------- |
| Alterava a classe principal | Cria uma nova implementação              |
| Muitos `if`                 | Polimorfismo                             |
| Código rígido               | Código extensível                        |
| Alto risco de regressão     | Menor risco de quebrar regras existentes |

---

# 3. L — Liskov Substitution Principle

## Conceito

| Aspecto                | Resumo                                                                                  |
| ---------------------- | --------------------------------------------------------------------------------------- |
| **Nome**               | Princípio da Substituição de Liskov                                                     |
| **Ideia**              | Uma subclasse deve poder substituir a classe base sem quebrar o comportamento esperado. |
| **Foco**               | Herança correta.                                                                        |
| **Problema que evita** | Hierarquias falsas ou subclasses que não cumprem o contrato da superclasse.             |
| **Sinal de violação**  | Subclasse sobrescreve método lançando exceção inesperada ou mudando regra essencial.    |

---

## Exemplo ruim

```java
public class Bird {

    public void fly() {
        System.out.println("Flying...");
    }
}
```

```java
public class Penguin extends Bird {

    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

## Problema

```java
public void makeBirdFly(Bird bird) {
    bird.fly();
}
```

| Entrada         | Resultado         |
| --------------- | ----------------- |
| `new Bird()`    | Funciona          |
| `new Penguin()` | Quebra em runtime |

O problema é que `Penguin` herda um comportamento que não consegue cumprir.

---

## Exemplo melhor

```java
public interface Bird {
}
```

```java
public interface FlyingBird extends Bird {
    void fly();
}
```

```java
public class Eagle implements FlyingBird {

    @Override
    public void fly() {
        System.out.println("Eagle flying...");
    }
}
```

```java
public class Penguin implements Bird {

    public void swim() {
        System.out.println("Penguin swimming...");
    }
}
```

## Benefício

| Antes                            | Depois                                            |
| -------------------------------- | ------------------------------------------------- |
| Todo pássaro era obrigado a voar | Apenas pássaros voadores implementam `FlyingBird` |
| Subclasse quebrava contrato      | Contratos mais precisos                           |
| Herança incorreta                | Modelagem mais fiel                               |
| Erro em runtime                  | Erro evitado por design                           |

---

## Exemplo em domínio financeiro

### Ruim

```java
public class Account {

    public void withdraw(BigDecimal amount) {
        // saque
    }
}
```

```java
public class FixedTermInvestmentAccount extends Account {

    @Override
    public void withdraw(BigDecimal amount) {
        throw new UnsupportedOperationException("Cannot withdraw before maturity");
    }
}
```

### Melhor

```java
public interface Withdrawable {
    void withdraw(BigDecimal amount);
}
```

```java
public class CheckingAccount implements Withdrawable {

    @Override
    public void withdraw(BigDecimal amount) {
        // saque permitido
    }
}
```

```java
public class FixedTermInvestmentAccount {

    public void redeemAtMaturity() {
        // resgate apenas no vencimento
    }
}
```

---

# 4. I — Interface Segregation Principle

## Conceito

| Aspecto                | Resumo                                                                             |
| ---------------------- | ---------------------------------------------------------------------------------- |
| **Nome**               | Princípio da Segregação de Interface                                               |
| **Ideia**              | Uma classe não deve ser obrigada a implementar métodos que não usa.                |
| **Foco**               | Interfaces pequenas e específicas.                                                 |
| **Problema que evita** | Interfaces grandes, genéricas e difíceis de manter.                                |
| **Sinal de violação**  | Métodos implementados com corpo vazio ou lançando `UnsupportedOperationException`. |

---

## Exemplo ruim

```java
public interface Worker {
    void work();
    void eat();
    void sleep();
}
```

```java
public class HumanWorker implements Worker {

    @Override
    public void work() {
        System.out.println("Human working...");
    }

    @Override
    public void eat() {
        System.out.println("Human eating...");
    }

    @Override
    public void sleep() {
        System.out.println("Human sleeping...");
    }
}
```

```java
public class RobotWorker implements Worker {

    @Override
    public void work() {
        System.out.println("Robot working...");
    }

    @Override
    public void eat() {
        throw new UnsupportedOperationException("Robot does not eat");
    }

    @Override
    public void sleep() {
        throw new UnsupportedOperationException("Robot does not sleep");
    }
}
```

## Problema

| Classe        | Problema                                   |
| ------------- | ------------------------------------------ |
| `HumanWorker` | Usa todos os métodos                       |
| `RobotWorker` | É obrigado a implementar métodos inválidos |
| `Worker`      | Interface genérica demais                  |

---

## Exemplo melhor

```java
public interface Workable {
    void work();
}
```

```java
public interface Eatable {
    void eat();
}
```

```java
public interface Sleepable {
    void sleep();
}
```

```java
public class HumanWorker implements Workable, Eatable, Sleepable {

    @Override
    public void work() {
        System.out.println("Human working...");
    }

    @Override
    public void eat() {
        System.out.println("Human eating...");
    }

    @Override
    public void sleep() {
        System.out.println("Human sleeping...");
    }
}
```

```java
public class RobotWorker implements Workable {

    @Override
    public void work() {
        System.out.println("Robot working...");
    }
}
```

## Benefício

| Antes                                 | Depois                              |
| ------------------------------------- | ----------------------------------- |
| Interface grande                      | Interfaces específicas              |
| Classes implementavam métodos inúteis | Cada classe implementa só o que usa |
| Contrato poluído                      | Contrato mais claro                 |
| Maior acoplamento                     | Menor acoplamento                   |

---

# 5. D — Dependency Inversion Principle

## Conceito

| Aspecto                | Resumo                                                                                                  |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| **Nome**               | Princípio da Inversão de Dependência                                                                    |
| **Ideia**              | Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações. |
| **Foco**               | Baixo acoplamento.                                                                                      |
| **Problema que evita** | Código preso a frameworks, banco de dados, APIs externas ou implementações concretas.                   |
| **Sinal de violação**  | Classe de negócio criando dependências com `new`.                                                       |

---

## Exemplo ruim

```java
public class PaymentService {

    private final StripePaymentGateway gateway = new StripePaymentGateway();

    public void pay(Payment payment) {
        gateway.charge(payment);
    }
}
```

## Problema

| Problema                                      | Explicação                                                     |
| --------------------------------------------- | -------------------------------------------------------------- |
| Alto acoplamento                              | `PaymentService` depende diretamente de `StripePaymentGateway` |
| Difícil testar                                | É mais difícil substituir por mock/fake                        |
| Difícil trocar fornecedor                     | Trocar Stripe por PayPal exige alterar a classe                |
| Regra de negócio dependente de infraestrutura | O domínio fica preso a detalhes externos                       |

---

## Exemplo melhor

```java
public interface PaymentGateway {
    void charge(Payment payment);
}
```

```java
public class StripePaymentGateway implements PaymentGateway {

    @Override
    public void charge(Payment payment) {
        // integração com Stripe
    }
}
```

```java
public class PaymentService {

    private final PaymentGateway paymentGateway;

    public PaymentService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public void pay(Payment payment) {
        paymentGateway.charge(payment);
    }
}
```

## Com Spring Boot

```java
@Service
public class PaymentService {

    private final PaymentGateway paymentGateway;

    public PaymentService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public void pay(Payment payment) {
        paymentGateway.charge(payment);
    }
}
```

```java
@Component
public class StripePaymentGateway implements PaymentGateway {

    @Override
    public void charge(Payment payment) {
        // chamada HTTP para provedor externo
    }
}
```

## Benefício

| Antes                                  | Depois                     |
| -------------------------------------- | -------------------------- |
| Classe cria dependência concreta       | Dependência é injetada     |
| Difícil testar                         | Fácil usar mock            |
| Forte acoplamento                      | Baixo acoplamento          |
| Infraestrutura domina regra de negócio | Regra depende de abstração |

---

# Comparação rápida dos princípios

| Princípio | Pergunta principal                                         | Problema comum           | Solução típica                   |
| --------- | ---------------------------------------------------------- | ------------------------ | -------------------------------- |
| **SRP**   | Essa classe tem mais de um motivo para mudar?              | Classe faz coisas demais | Separar responsabilidades        |
| **OCP**   | Preciso alterar código existente para adicionar uma regra? | Muitos `if/switch`       | Strategy, polimorfismo, extensão |
| **LSP**   | A subclasse substitui a classe base sem quebrar o sistema? | Herança forçada          | Ajustar abstrações               |
| **ISP**   | A interface obriga métodos desnecessários?                 | Interface grande         | Interfaces menores               |
| **DIP**   | Estou dependendo de implementação concreta?                | Alto acoplamento         | Depender de abstrações           |

---

# SOLID aplicado a Java/Spring

| Camada                        | Aplicação de SOLID                                                    |
| ----------------------------- | --------------------------------------------------------------------- |
| **Controller**                | Deve apenas receber requisições, validar entrada e chamar caso de uso |
| **Service/Use Case**          | Deve orquestrar o fluxo sem conhecer detalhes de infraestrutura       |
| **Domain**                    | Deve concentrar regras de negócio e proteger invariantes              |
| **Repository Interface**      | Deve ser abstração para persistência                                  |
| **Repository Implementation** | Deve conter detalhes técnicos de banco de dados                       |
| **Gateway/Client**            | Deve esconder integração com APIs externas                            |
| **DTO**                       | Deve transportar dados sem regra de negócio complexa                  |
| **Mapper**                    | Deve converter objetos sem poluir service/controller                  |

---

# Exemplo mental em arquitetura limpa

```text
src/main/java/com/example/payment
 ├── domain
 │    ├── Payment.java
 │    ├── PaymentStatus.java
 │    └── PaymentGateway.java
 │
 ├── application
 │    └── ProcessPaymentUseCase.java
 │
 ├── infrastructure
 │    ├── StripePaymentGateway.java
 │    └── PaymentJpaRepository.java
 │
 └── presentation
      ├── PaymentController.java
      └── PaymentRequest.java
```

| Princípio | Onde aparece                                                         |
| --------- | -------------------------------------------------------------------- |
| **SRP**   | Cada pacote/classe tem uma responsabilidade clara                    |
| **OCP**   | Novos gateways podem ser adicionados sem alterar o use case          |
| **LSP**   | Toda implementação de `PaymentGateway` deve respeitar o contrato     |
| **ISP**   | Interfaces pequenas, como `PaymentGateway`, `PaymentRepository`      |
| **DIP**   | `ProcessPaymentUseCase` depende de interfaces, não de implementações |

---

# Code smells relacionados a SOLID

| Code smell                            | Princípio violado | Exemplo                                         |
| ------------------------------------- | ----------------- | ----------------------------------------------- |
| God Class                             | SRP               | Classe faz regra, banco, e-mail e log           |
| Muitos `if/switch`                    | OCP               | Código precisa ser alterado para cada novo tipo |
| Herança forçada                       | LSP               | Subclasse lança `UnsupportedOperationException` |
| Interface gigante                     | ISP               | Classe implementa métodos que não usa           |
| `new` dentro de service               | DIP               | Service cria client externo diretamente         |
| Controller com regra de negócio       | SRP/DIP           | Controller valida regra central do domínio      |
| Repository com regra de domínio       | SRP               | Persistência misturada com regra                |
| Service dependente de classe concreta | DIP               | `private final StripeClient stripeClient`       |

---

# Como pensar SOLID em entrevistas

| Pergunta                              | O que demonstrar                                                                |
| ------------------------------------- | ------------------------------------------------------------------------------- |
| O que é SOLID?                        | Conjunto de princípios para criar código mais coeso, flexível e testável        |
| SOLID é regra obrigatória?            | Não. É um guia de design                                                        |
| SOLID elimina complexidade?           | Não. Ele organiza dependências e responsabilidades                              |
| Quando usar Strategy?                 | Quando há variações de comportamento                                            |
| Quando evitar herança?                | Quando a relação “é um” não é verdadeira                                        |
| Por que depender de interface?        | Para reduzir acoplamento e facilitar testes                                     |
| O que é classe coesa?                 | Classe com responsabilidade bem definida                                        |
| SOLID combina com Clean Architecture? | Sim. Ambos favorecem separação de responsabilidades e dependência de abstrações |

---

# Exercícios progressivos

| Nível | Exercício                                                                                        |
| ----- | ------------------------------------------------------------------------------------------------ |
| 1     | Identifique uma classe com responsabilidades demais e diga quais responsabilidades ela possui.   |
| 2     | Refatore uma classe que calcula descontos usando `if/else` para usar Strategy.                   |
| 3     | Crie uma hierarquia incorreta de herança e depois corrija usando interfaces específicas.         |
| 4     | Pegue uma interface grande e divida em interfaces menores.                                       |
| 5     | Remova o uso de `new` dentro de um service e injete uma dependência por interface.               |
| 6     | Crie testes unitários usando mock para validar o uso de DIP.                                     |
| 7     | Modele um caso de pagamento com `PaymentGateway`, `PaymentRepository` e `ProcessPaymentUseCase`. |

---

# Perguntas de revisão

| Pergunta                                     | Resposta curta                                                                     |
| -------------------------------------------- | ---------------------------------------------------------------------------------- |
| O que significa SRP?                         | Uma classe deve ter uma responsabilidade principal.                                |
| O que significa OCP?                         | O código deve ser extensível sem precisar modificar o código existente.            |
| O que significa LSP?                         | Uma subclasse deve substituir a classe base sem quebrar o comportamento esperado.  |
| O que significa ISP?                         | Interfaces devem ser pequenas e específicas.                                       |
| O que significa DIP?                         | Código de alto nível deve depender de abstrações, não de implementações concretas. |
| Qual princípio combate God Class?            | SRP.                                                                               |
| Qual princípio combate muitos `if/switch`?   | OCP.                                                                               |
| Qual princípio combate herança mal modelada? | LSP.                                                                               |
| Qual princípio combate interface grande?     | ISP.                                                                               |
| Qual princípio facilita testes com mock?     | DIP.                                                                               |

---

# English Version

## General mind map

| Letter | Principle                       | Main idea                                                        | Goal                        |
| ------ | ------------------------------- | ---------------------------------------------------------------- | --------------------------- |
| **S**  | Single Responsibility Principle | A class should have one main responsibility                      | Increase cohesion           |
| **O**  | Open/Closed Principle           | Open for extension, closed for modification                      | Avoid changing stable code  |
| **L**  | Liskov Substitution Principle   | Subclasses should replace base classes without breaking behavior | Ensure correct inheritance  |
| **I**  | Interface Segregation Principle | Small and specific interfaces are better than large ones         | Avoid unnecessary contracts |
| **D**  | Dependency Inversion Principle  | Depend on abstractions, not concrete implementations             | Reduce coupling             |

---

# 1. S — Single Responsibility Principle

| Aspect             | Summary                                                                            |
| ------------------ | ---------------------------------------------------------------------------------- |
| **Name**           | Single Responsibility Principle                                                    |
| **Idea**           | A class should have only one main reason to change.                                |
| **Focus**          | Cohesion.                                                                          |
| **Avoids**         | Large, confusing and hard-to-test classes.                                         |
| **Violation sign** | One class validates, calculates, persists, sends e-mails and logs business events. |

```java
public class OrderService {

    private final OrderValidator orderValidator;
    private final OrderCalculator orderCalculator;
    private final OrderRepository orderRepository;
    private final EmailService emailService;

    public OrderService(
            OrderValidator orderValidator,
            OrderCalculator orderCalculator,
            OrderRepository orderRepository,
            EmailService emailService
    ) {
        this.orderValidator = orderValidator;
        this.orderCalculator = orderCalculator;
        this.orderRepository = orderRepository;
        this.emailService = emailService;
    }

    public void createOrder(Order order) {
        orderValidator.validate(order);
        orderCalculator.calculateTotal(order);
        orderRepository.save(order);
        emailService.sendConfirmation(order);
    }
}
```

| Class             | Responsibility            |
| ----------------- | ------------------------- |
| `OrderService`    | Orchestrates the use case |
| `OrderValidator`  | Validates order rules     |
| `OrderCalculator` | Calculates values         |
| `OrderRepository` | Persists data             |
| `EmailService`    | Sends notifications       |

---

# 2. O — Open/Closed Principle

| Aspect         | Summary                                                         |
| -------------- | --------------------------------------------------------------- |
| **Name**       | Open/Closed Principle                                           |
| **Idea**       | Code should allow new behavior without modifying existing code. |
| **Open for**   | Extension.                                                      |
| **Closed for** | Direct modification of tested and stable code.                  |
| **Avoids**     | Growing `if`, `else` and `switch` blocks.                       |

```java
public interface DiscountPolicy {
    boolean supports(CustomerType customerType);
    BigDecimal calculate(BigDecimal amount);
}
```

```java
public class PremiumCustomerDiscount implements DiscountPolicy {

    @Override
    public boolean supports(CustomerType customerType) {
        return customerType == CustomerType.PREMIUM;
    }

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.10"));
    }
}
```

```java
public class DiscountCalculator {

    private final List<DiscountPolicy> policies;

    public DiscountCalculator(List<DiscountPolicy> policies) {
        this.policies = policies;
    }

    public BigDecimal calculate(CustomerType customerType, BigDecimal amount) {
        return policies.stream()
                .filter(policy -> policy.supports(customerType))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("Unsupported customer type"))
                .calculate(amount);
    }
}
```

| Before                 | After                  |
| ---------------------- | ---------------------- |
| Many `if` blocks       | Polymorphism           |
| Modify existing class  | Add new implementation |
| Higher regression risk | Lower regression risk  |
| Rigid code             | Extensible code        |

---

# 3. L — Liskov Substitution Principle

| Aspect             | Summary                                                                      |
| ------------------ | ---------------------------------------------------------------------------- |
| **Name**           | Liskov Substitution Principle                                                |
| **Idea**           | A subclass should replace its base class without breaking expected behavior. |
| **Focus**          | Correct inheritance.                                                         |
| **Avoids**         | False hierarchies and subclasses that violate the parent contract.           |
| **Violation sign** | A subclass overrides a method by throwing an unexpected exception.           |

## Bad example

```java
public class Bird {

    public void fly() {
        System.out.println("Flying...");
    }
}
```

```java
public class Penguin extends Bird {

    @Override
    public void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

## Better example

```java
public interface Bird {
}
```

```java
public interface FlyingBird extends Bird {
    void fly();
}
```

```java
public class Eagle implements FlyingBird {

    @Override
    public void fly() {
        System.out.println("Eagle flying...");
    }
}
```

```java
public class Penguin implements Bird {

    public void swim() {
        System.out.println("Penguin swimming...");
    }
}
```

| Before                       | After                                    |
| ---------------------------- | ---------------------------------------- |
| Every bird was forced to fly | Only flying birds implement `FlyingBird` |
| Broken subclass contract     | More precise contracts                   |
| Incorrect inheritance        | Better modeling                          |
| Runtime error                | Error avoided by design                  |

---

# 4. I — Interface Segregation Principle

| Aspect             | Summary                                                              |
| ------------------ | -------------------------------------------------------------------- |
| **Name**           | Interface Segregation Principle                                      |
| **Idea**           | A class should not be forced to implement methods it does not use.   |
| **Focus**          | Small and specific interfaces.                                       |
| **Avoids**         | Large, generic and hard-to-maintain interfaces.                      |
| **Violation sign** | Empty methods or `UnsupportedOperationException` in implementations. |

```java
public interface Workable {
    void work();
}
```

```java
public interface Eatable {
    void eat();
}
```

```java
public interface Sleepable {
    void sleep();
}
```

```java
public class HumanWorker implements Workable, Eatable, Sleepable {

    @Override
    public void work() {
        System.out.println("Human working...");
    }

    @Override
    public void eat() {
        System.out.println("Human eating...");
    }

    @Override
    public void sleep() {
        System.out.println("Human sleeping...");
    }
}
```

```java
public class RobotWorker implements Workable {

    @Override
    public void work() {
        System.out.println("Robot working...");
    }
}
```

| Before                           | After                                 |
| -------------------------------- | ------------------------------------- |
| Large interface                  | Specific interfaces                   |
| Classes implement unused methods | Classes implement only what they need |
| Polluted contract                | Clear contract                        |
| Higher coupling                  | Lower coupling                        |

---

# 5. D — Dependency Inversion Principle

| Aspect             | Summary                                                                                        |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| **Name**           | Dependency Inversion Principle                                                                 |
| **Idea**           | High-level modules should not depend on low-level modules. Both should depend on abstractions. |
| **Focus**          | Low coupling.                                                                                  |
| **Avoids**         | Code tied to frameworks, databases, external APIs or concrete implementations.                 |
| **Violation sign** | Business class creating dependencies with `new`.                                               |

```java
public interface PaymentGateway {
    void charge(Payment payment);
}
```

```java
public class StripePaymentGateway implements PaymentGateway {

    @Override
    public void charge(Payment payment) {
        // Stripe integration
    }
}
```

```java
public class PaymentService {

    private final PaymentGateway paymentGateway;

    public PaymentService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public void pay(Payment payment) {
        paymentGateway.charge(payment);
    }
}
```

| Before                                   | After                                 |
| ---------------------------------------- | ------------------------------------- |
| Class creates concrete dependency        | Dependency is injected                |
| Hard to test                             | Easy to mock                          |
| Strong coupling                          | Low coupling                          |
| Business logic depends on infrastructure | Business logic depends on abstraction |

---

# SOLID quick comparison

| Principle | Main question                                        | Common problem          | Typical solution                  |
| --------- | ---------------------------------------------------- | ----------------------- | --------------------------------- |
| **SRP**   | Does this class have more than one reason to change? | Class does too much     | Split responsibilities            |
| **OCP**   | Do I need to modify existing code to add a new rule? | Many `if/switch` blocks | Strategy, polymorphism, extension |
| **LSP**   | Can the subclass replace the base class safely?      | Forced inheritance      | Improve abstractions              |
| **ISP**   | Does the interface force unnecessary methods?        | Large interface         | Smaller interfaces                |
| **DIP**   | Am I depending on a concrete implementation?         | High coupling           | Depend on abstractions            |

---

# SOLID applied to Java/Spring

| Layer                         | SOLID application                                            |
| ----------------------------- | ------------------------------------------------------------ |
| **Controller**                | Receives requests, validates input and calls the use case    |
| **Service/Use Case**          | Orchestrates the flow without knowing infrastructure details |
| **Domain**                    | Holds business rules and protects invariants                 |
| **Repository Interface**      | Provides persistence abstraction                             |
| **Repository Implementation** | Contains database details                                    |
| **Gateway/Client**            | Hides external API integration                               |
| **DTO**                       | Transfers data without complex business logic                |
| **Mapper**                    | Converts objects without polluting service/controller        |

---

# Review questions

| Question                                        | Short answer                                                                 |
| ----------------------------------------------- | ---------------------------------------------------------------------------- |
| What does SRP mean?                             | A class should have one main responsibility.                                 |
| What does OCP mean?                             | Code should be extensible without modifying existing code.                   |
| What does LSP mean?                             | A subclass should replace its base class without breaking behavior.          |
| What does ISP mean?                             | Interfaces should be small and specific.                                     |
| What does DIP mean?                             | High-level code should depend on abstractions, not concrete implementations. |
| Which principle fights God Class?               | SRP.                                                                         |
| Which principle fights many `if/switch` blocks? | OCP.                                                                         |
| Which principle fights bad inheritance?         | LSP.                                                                         |
| Which principle fights large interfaces?        | ISP.                                                                         |
| Which principle helps testing with mocks?       | DIP.                                                                         |
