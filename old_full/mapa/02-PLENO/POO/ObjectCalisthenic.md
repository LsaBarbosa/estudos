Lucas, segue o resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **Object Calisthenics**.

# Versão em Português

## Mapa mental geral

| Tópico central          | Ideia principal                                               | Objetivo                                     |
| ----------------------- | ------------------------------------------------------------- | -------------------------------------------- |
| **Object Calisthenics** | Conjunto de regras práticas para treinar orientação a objetos | Melhorar modelagem, encapsulamento e coesão  |
| **Foco**                | Escrever código mais orientado a objetos e menos procedural   | Evitar classes anêmicas e services gigantes  |
| **Origem da ideia**     | Exercício de design, não regra absoluta                       | Forçar boas práticas de OO                   |
| **Uso em Java**         | Muito útil para treinar domínio, DDD, Clean Code e SOLID      | Criar objetos com comportamento real         |
| **Atenção**             | Algumas regras são rígidas de propósito                       | Devem ser usadas como treino, não como dogma |

---

# 1. Conceito de Object Calisthenics

| Aspecto                 | Resumo                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Definição**           | Object Calisthenics é um conjunto de restrições para treinar melhor design orientado a objetos.                    |
| **Objetivo**            | Fazer o desenvolvedor escrever código com mais encapsulamento, menos acoplamento e mais comportamento nos objetos. |
| **Não é**               | Um padrão de arquitetura obrigatório.                                                                              |
| **É**                   | Um exercício para melhorar disciplina de modelagem.                                                                |
| **Combina com**         | Clean Code, SOLID, DDD, TDD e refatoração.                                                                         |
| **Principal benefício** | Ajuda a sair do código procedural disfarçado de orientação a objetos.                                              |

---

# 2. As 9 regras de Object Calisthenics

| Nº    | Regra                                          | Ideia principal                                                     |
| ----- | ---------------------------------------------- | ------------------------------------------------------------------- |
| **1** | Um nível de indentação por método              | Evitar métodos complexos e muitos `if` aninhados                    |
| **2** | Não usar `else`                                | Melhorar clareza com retornos antecipados e polimorfismo            |
| **3** | Encapsular tipos primitivos e Strings          | Criar objetos de valor com significado de domínio                   |
| **4** | Coleções de primeira classe                    | Não deixar listas soltas com regras espalhadas                      |
| **5** | Um ponto por linha                             | Evitar navegar profundamente em objetos                             |
| **6** | Não abreviar nomes                             | Melhorar legibilidade                                               |
| **7** | Manter entidades pequenas                      | Classes menores, coesas e fáceis de testar                          |
| **8** | No máximo 2 atributos por classe               | Forçar composição e separação de responsabilidades                  |
| **9** | Não usar getters/setters/properties livremente | Proteger encapsulamento e mover comportamento para dentro do objeto |

---

# 3. Regra 1 — Um nível de indentação por método

## Conceito

| Aspecto          | Resumo                                                                              |
| ---------------- | ----------------------------------------------------------------------------------- |
| **Problema**     | Muitos `if`, `for`, `while` e blocos aninhados tornam o método difícil de entender. |
| **Objetivo**     | Cada método deve ter fluxo simples.                                                 |
| **Como aplicar** | Extrair métodos menores ou usar retorno antecipado.                                 |
| **Benefício**    | Código mais legível e testável.                                                     |

## Exemplo ruim

```java
public void approve(Order order) {
    if (order != null) {
        if (order.isPaid()) {
            if (!order.isCancelled()) {
                order.approve();
            }
        }
    }
}
```

## Exemplo melhor

```java
public void approve(Order order) {
    validateOrderCanBeApproved(order);
    order.approve();
}

private void validateOrderCanBeApproved(Order order) {
    if (order == null) {
        throw new IllegalArgumentException("Order cannot be null");
    }

    if (!order.isPaid()) {
        throw new IllegalStateException("Order must be paid");
    }

    if (order.isCancelled()) {
        throw new IllegalStateException("Cancelled order cannot be approved");
    }
}
```

## Leitura mental

| Antes                         | Depois                  |
| ----------------------------- | ----------------------- |
| Lógica aninhada               | Fluxo linear            |
| Difícil saber o caminho feliz | Caminho principal claro |
| Vários níveis de `if`         | Validações explícitas   |
| Baixa legibilidade            | Alta legibilidade       |

---

# 4. Regra 2 — Não usar `else`

## Conceito

| Aspecto          | Resumo                                                            |
| ---------------- | ----------------------------------------------------------------- |
| **Problema**     | `else` pode esconder fluxos alternativos e aumentar complexidade. |
| **Objetivo**     | Deixar o fluxo principal mais direto.                             |
| **Como aplicar** | Usar retorno antecipado, exceções ou polimorfismo.                |
| **Benefício**    | Métodos com leitura mais simples.                                 |

## Exemplo ruim

```java
public BigDecimal calculateDiscount(Customer customer, BigDecimal amount) {
    if (customer.isPremium()) {
        return amount.multiply(new BigDecimal("0.10"));
    } else {
        return amount.multiply(new BigDecimal("0.02"));
    }
}
```

## Exemplo melhor

```java
public BigDecimal calculateDiscount(Customer customer, BigDecimal amount) {
    if (customer.isPremium()) {
        return amount.multiply(new BigDecimal("0.10"));
    }

    return amount.multiply(new BigDecimal("0.02"));
}
```

## Quando o `else` indica problema maior

| Situação                              | Melhor solução                |
| ------------------------------------- | ----------------------------- |
| Muitos `else if` por tipo             | Strategy                      |
| `else` com regra de negócio diferente | Polimorfismo                  |
| `else` para erro                      | Retorno antecipado ou exceção |
| `else` com fluxo complexo             | Extrair métodos               |

---

# 5. Regra 3 — Encapsular tipos primitivos e Strings

## Conceito

| Aspecto        | Resumo                                                                                |
| -------------- | ------------------------------------------------------------------------------------- |
| **Problema**   | `String`, `int`, `BigDecimal` e `boolean` podem não expressar significado de domínio. |
| **Objetivo**   | Criar objetos que protejam regras e significado.                                      |
| **Nome comum** | Value Object.                                                                         |
| **Benefício**  | Validação centralizada e código mais expressivo.                                      |

## Exemplo ruim

```java
public class Customer {
    private String email;
    private String cpf;
    private BigDecimal balance;
}
```

## Exemplo melhor

```java
public class Customer {
    private Email email;
    private Cpf cpf;
    private Money balance;
}
```

## Exemplo de Value Object

```java
public final class Email {

    private final String value;

    public Email(String value) {
        if (value == null || !value.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }

        this.value = value;
    }

    public String value() {
        return value;
    }
}
```

## Benefício prático

| Sem Value Object                     | Com Value Object       |
| ------------------------------------ | ---------------------- |
| `String email` aceita qualquer texto | `Email` valida formato |
| Regra repetida em vários lugares     | Regra centralizada     |
| Baixa expressividade                 | Alta expressividade    |
| Fácil passar dado errado             | Tipo comunica intenção |

---

# 6. Regra 4 — Coleções de primeira classe

## Conceito

| Aspecto        | Resumo                                          |
| -------------- | ----------------------------------------------- |
| **Problema**   | Listas soltas espalham regra de negócio.        |
| **Objetivo**   | Encapsular coleções em uma classe própria.      |
| **Nome comum** | First-Class Collection.                         |
| **Benefício**  | Regras sobre a coleção ficam em um único lugar. |

## Exemplo ruim

```java
public class Order {
    private List<OrderItem> items;

    public BigDecimal calculateTotal() {
        return items.stream()
                .map(OrderItem::subtotal)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

## Exemplo melhor

```java
public class Order {
    private OrderItems items;

    public BigDecimal calculateTotal() {
        return items.total();
    }
}
```

```java
public class OrderItems {

    private final List<OrderItem> items;

    public OrderItems(List<OrderItem> items) {
        if (items == null || items.isEmpty()) {
            throw new IllegalArgumentException("Order must have at least one item");
        }

        this.items = List.copyOf(items);
    }

    public BigDecimal total() {
        return items.stream()
                .map(OrderItem::subtotal)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }

    public int quantity() {
        return items.size();
    }
}
```

## Benefício

| Antes                       | Depois                         |
| --------------------------- | ------------------------------ |
| `List<OrderItem>` espalhada | `OrderItems` encapsula a lista |
| Validação duplicada         | Validação centralizada         |
| Cálculo fora da coleção     | Cálculo dentro da coleção      |
| Lista exposta               | Coleção protegida              |

---

# 7. Regra 5 — Um ponto por linha

## Conceito

| Aspecto           | Resumo                                                                       |
| ----------------- | ---------------------------------------------------------------------------- |
| **Problema**      | Encadeamento profundo expõe estrutura interna dos objetos.                   |
| **Objetivo**      | Evitar violação da Lei de Demeter.                                           |
| **Ideia prática** | Não navegue demais dentro dos objetos. Peça o comportamento ao objeto certo. |
| **Benefício**     | Menor acoplamento.                                                           |

## Exemplo ruim

```java
String city = order.getCustomer().getAddress().getCity().getName();
```

## Exemplo melhor

```java
String city = order.customerCityName();
```

Ou melhor ainda, dependendo do caso:

```java
boolean sameCity = order.wasPlacedByCustomerFrom(city);
```

## Problema do encadeamento

| Código                                       | Problema                                |
| -------------------------------------------- | --------------------------------------- |
| `order.getCustomer().getAddress().getCity()` | Código conhece detalhes internos demais |
| Muitos getters encadeados                    | Alto acoplamento                        |
| Mudança em `Address` quebra código externo   | Baixo encapsulamento                    |
| Regra fora do objeto                         | Modelo anêmico                          |

---

# 8. Regra 6 — Não abreviar nomes

## Conceito

| Aspecto                    | Resumo                                    |
| -------------------------- | ----------------------------------------- |
| **Problema**               | Abreviações reduzem clareza.              |
| **Objetivo**               | Nomes devem comunicar intenção.           |
| **Benefício**              | Código mais legível para o time inteiro.  |
| **Relação com Clean Code** | Nome bom reduz necessidade de comentário. |

## Exemplos

| Ruim        | Melhor             |
| ----------- | ------------------ |
| `usr`       | `user`             |
| `addr`      | `address`          |
| `calcTot()` | `calculateTotal()` |
| `qty`       | `quantity`         |
| `pmt`       | `payment`          |
| `cust`      | `customer`         |
| `repo`      | `repository`       |
| `svc`       | `service`          |

## Exemplo Java

```java
// Ruim
public BigDecimal calcTot(List<Item> its) {
    return its.stream()
            .map(Item::sub)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

```java
// Melhor
public BigDecimal calculateTotal(List<OrderItem> items) {
    return items.stream()
            .map(OrderItem::subtotal)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

---

# 9. Regra 7 — Manter entidades pequenas

## Conceito

| Aspecto               | Resumo                                                                  |
| --------------------- | ----------------------------------------------------------------------- |
| **Problema**          | Classes grandes tendem a acumular responsabilidades.                    |
| **Objetivo**          | Criar classes coesas e pequenas.                                        |
| **Benefício**         | Mais fácil entender, testar e modificar.                                |
| **Sinal de problema** | Classe com muitos métodos, muitos atributos e muitas razões para mudar. |

## Exemplo mental

| Classe grande                                                     | Possível separação                                                                        |
| ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `Order` calcula total, frete, desconto, imposto e pagamento       | `Order`, `OrderItems`, `DiscountPolicy`, `TaxCalculator`, `ShippingCalculator`            |
| `Customer` guarda dados, valida CPF, calcula score e envia e-mail | `Customer`, `Cpf`, `CreditScore`, `CustomerNotificationService`                           |
| `PaymentService` valida, persiste, chama gateway e audita         | `ProcessPaymentUseCase`, `Payment`, `PaymentGateway`, `PaymentRepository`, `AuditService` |

---

# 10. Regra 8 — No máximo 2 atributos por classe

## Conceito

| Aspecto             | Resumo                                                                         |
| ------------------- | ------------------------------------------------------------------------------ |
| **Problema**        | Muitos atributos podem indicar classe com responsabilidades demais.            |
| **Objetivo**        | Forçar composição.                                                             |
| **Atenção**         | É uma regra bastante rígida para treino.                                       |
| **Na prática real** | Nem sempre será aplicada literalmente, especialmente com entidades JPA e DTOs. |
| **Benefício**       | Ajuda a identificar agrupamentos naturais de dados.                            |

## Exemplo ruim

```java
public class Customer {
    private String name;
    private String email;
    private String street;
    private String city;
    private String zipCode;
}
```

## Exemplo melhor

```java
public class Customer {
    private CustomerName name;
    private Contact contact;
    private Address address;
}
```

```java
public class Contact {
    private Email email;
    private PhoneNumber phoneNumber;
}
```

```java
public class Address {
    private Street street;
    private City city;
    private ZipCode zipCode;
}
```

## Benefício

| Antes                 | Depois                          |
| --------------------- | ------------------------------- |
| Muitos dados soltos   | Objetos com significado         |
| Classe inchada        | Composição                      |
| Validações espalhadas | Validações nos objetos corretos |
| Baixa expressividade  | Modelo mais rico                |

---

# 11. Regra 9 — Não usar getters e setters livremente

## Conceito

| Aspecto             | Resumo                                                                               |
| ------------------- | ------------------------------------------------------------------------------------ |
| **Problema**        | Getters e setters expõem estado interno e incentivam lógica fora do objeto.          |
| **Objetivo**        | Objetos devem receber comandos, não apenas expor dados.                              |
| **Benefício**       | Encapsulamento real.                                                                 |
| **Atenção em Java** | Frameworks como JPA e Jackson podem exigir construtores e getters; use com critério. |

## Exemplo ruim

```java
public class Account {
    private BigDecimal balance;

    public BigDecimal getBalance() {
        return balance;
    }

    public void setBalance(BigDecimal balance) {
        this.balance = balance;
    }
}
```

```java
BigDecimal currentBalance = account.getBalance();

if (currentBalance.compareTo(amount) >= 0) {
    account.setBalance(currentBalance.subtract(amount));
}
```

## Exemplo melhor

```java
public class Account {

    private BigDecimal balance;

    public void withdraw(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        if (balance.compareTo(amount) < 0) {
            throw new InsufficientBalanceException("Insufficient balance");
        }

        this.balance = this.balance.subtract(amount);
    }
}
```

## Comparação

| Modelo anêmico                     | Modelo rico              |
| ---------------------------------- | ------------------------ |
| Dados expostos por getters/setters | Estado protegido         |
| Regra no service                   | Regra dentro do objeto   |
| Fácil quebrar invariante           | Invariante protegida     |
| Objeto parece tabela               | Objeto tem comportamento |

---

# 12. Object Calisthenics aplicado a Java/Spring

| Camada                | Aplicação                                                                                         |
| --------------------- | ------------------------------------------------------------------------------------------------- |
| **Controller**        | Deve evitar regra de negócio; apenas recebe requisição e chama caso de uso.                       |
| **DTO**               | Pode ter getters/setters porque é objeto de transporte, mas não deve concentrar regra de domínio. |
| **Service/Use Case**  | Deve orquestrar fluxo, não manipular dados internos das entidades diretamente.                    |
| **Domain Entity**     | Deve ter comportamento, proteger invariantes e evitar setters públicos.                           |
| **Value Object**      | Deve encapsular conceitos como `Email`, `Cpf`, `Money`, `Quantity`, `OrderId`.                    |
| **Repository**        | Deve persistir objetos, não decidir regras de negócio.                                            |
| **Collection Object** | Deve encapsular listas com regras próprias, como `OrderItems`, `Payments`, `Installments`.        |

---

# 13. Relação com Clean Code, SOLID e DDD

| Tema            | Relação com Object Calisthenics                                    |
| --------------- | ------------------------------------------------------------------ |
| **Clean Code**  | Melhora nomes, métodos pequenos, legibilidade e baixo acoplamento. |
| **SOLID — SRP** | Força classes com responsabilidades menores.                       |
| **SOLID — OCP** | Incentiva polimorfismo no lugar de `if/else` excessivo.            |
| **SOLID — LSP** | Ajuda a evitar heranças falsas.                                    |
| **SOLID — ISP** | Estimula interfaces menores e específicas.                         |
| **SOLID — DIP** | Favorece dependência de abstrações.                                |
| **DDD**         | Incentiva entidades ricas, Value Objects e invariantes no domínio. |
| **TDD**         | Facilita testes porque objetos ficam menores e mais previsíveis.   |

---

# 14. Comparação rápida

| Código procedural                  | Código com Object Calisthenics     |
| ---------------------------------- | ---------------------------------- |
| Service manipula dados diretamente | Objeto protege seus próprios dados |
| Muitos getters e setters           | Métodos com intenção de negócio    |
| Primitivos espalhados              | Value Objects                      |
| Listas soltas                      | First-Class Collections            |
| Muitos `if/else`                   | Polimorfismo e retorno antecipado  |
| Classes grandes                    | Classes pequenas e coesas          |
| Encadeamento de chamadas           | Comportamento no objeto correto    |
| Baixo encapsulamento               | Alto encapsulamento                |

---

# 15. Exemplo completo mental: pedido

## Modelo fraco

```java
public class Order {
    private List<OrderItem> items;
    private String status;
    private BigDecimal total;

    public List<OrderItem> getItems() {
        return items;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public void setTotal(BigDecimal total) {
        this.total = total;
    }
}
```

```java
public class OrderService {

    public void approve(Order order) {
        BigDecimal total = order.getItems().stream()
                .map(OrderItem::getSubtotal)
                .reduce(BigDecimal.ZERO, BigDecimal::add);

        if (total.compareTo(BigDecimal.ZERO) > 0) {
            order.setTotal(total);
            order.setStatus("APPROVED");
        }
    }
}
```

## Modelo melhor

```java
public class Order {

    private final OrderItems items;
    private OrderStatus status;

    public Order(OrderItems items) {
        this.items = items;
        this.status = OrderStatus.CREATED;
    }

    public void approve() {
        if (!items.hasAtLeastOneItem()) {
            throw new IllegalStateException("Order must have at least one item");
        }

        this.status = OrderStatus.APPROVED;
    }

    public Money total() {
        return items.total();
    }
}
```

```java
public class OrderItems {

    private final List<OrderItem> items;

    public OrderItems(List<OrderItem> items) {
        if (items == null || items.isEmpty()) {
            throw new IllegalArgumentException("Order must have items");
        }

        this.items = List.copyOf(items);
    }

    public boolean hasAtLeastOneItem() {
        return !items.isEmpty();
    }

    public Money total() {
        return items.stream()
                .map(OrderItem::subtotal)
                .reduce(Money.zero(), Money::add);
    }
}
```

## Melhorias aplicadas

| Regra                      | Onde aparece                            |
| -------------------------- | --------------------------------------- |
| Um nível de indentação     | Métodos simples                         |
| Sem `else`                 | Fluxo direto                            |
| Primitivos encapsulados    | `Money`, `OrderStatus`                  |
| Coleção de primeira classe | `OrderItems`                            |
| Sem encadeamento excessivo | `order.total()`                         |
| Nomes claros               | `approve`, `total`, `hasAtLeastOneItem` |
| Entidades menores          | `Order` e `OrderItems` separados        |
| Poucos atributos           | Objetos compostos                       |
| Menos getters/setters      | Comportamento dentro do objeto          |

---

# 16. Cuidados práticos

| Cuidado                                    | Explicação                                                                |
| ------------------------------------------ | ------------------------------------------------------------------------- |
| Não aplicar como dogma                     | É exercício de design, não lei universal.                                 |
| DTOs podem ter getters/setters             | DTO é transporte de dados, não domínio.                                   |
| JPA pode exigir adaptações                 | Entidades JPA podem precisar de construtor protegido e métodos acessores. |
| Nem todo primitivo precisa virar objeto    | Priorize conceitos importantes do domínio.                                |
| Nem todo `else` é proibido em produção     | A regra serve para treinar alternativas mais claras.                      |
| Nem toda classe precisa ter só 2 atributos | Use como alerta para avaliar coesão.                                      |

---

# 17. Como pensar em entrevistas

| Pergunta mental                                     | O que demonstrar        |
| --------------------------------------------------- | ----------------------- |
| Esse objeto tem comportamento ou só dados?          | Encapsulamento          |
| Essa regra está no domínio ou espalhada no service? | Modelagem               |
| Estou expondo estado demais com getters/setters?    | Proteção de invariantes |
| Essa lista deveria ser uma classe própria?          | First-Class Collection  |
| Esse `String` representa algo importante?           | Value Object            |
| O método está aninhado demais?                      | Legibilidade            |
| Estou navegando demais por objetos internos?        | Baixo acoplamento       |
| Essa classe tem atributos demais?                   | Coesão                  |

---

# 18. Exercícios progressivos

| Nível | Exercício                                                                                     |
| ----- | --------------------------------------------------------------------------------------------- |
| 1     | Pegue um método com `if` aninhado e refatore para retorno antecipado.                         |
| 2     | Remova um `else` simples sem alterar o comportamento.                                         |
| 3     | Transforme `String email` em um Value Object `Email`.                                         |
| 4     | Transforme `List<OrderItem>` em uma classe `OrderItems`.                                      |
| 5     | Substitua `order.getCustomer().getAddress().getCity()` por um método de domínio.              |
| 6     | Renomeie abreviações como `usr`, `addr`, `qty`, `pmt`.                                        |
| 7     | Divida uma classe grande em objetos menores.                                                  |
| 8     | Reduza atributos agrupando conceitos relacionados.                                            |
| 9     | Remova setters públicos e crie métodos de negócio como `approve()`, `cancel()`, `withdraw()`. |

---

# 19. Perguntas de revisão

| Pergunta                                        | Resposta curta                                                                              |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------- |
| O que é Object Calisthenics?                    | Um conjunto de regras para treinar melhor design orientado a objetos.                       |
| Object Calisthenics é obrigatório?              | Não. É uma prática de treino.                                                               |
| Qual regra combate métodos muito complexos?     | Um nível de indentação por método.                                                          |
| Por que evitar `else`?                          | Para simplificar o fluxo e incentivar alternativas como retorno antecipado ou polimorfismo. |
| Por que encapsular primitivos?                  | Para dar significado de domínio e centralizar validações.                                   |
| O que é First-Class Collection?                 | Uma classe que encapsula uma coleção e suas regras.                                         |
| Qual problema existe em muitos getters/setters? | Eles expõem estado interno e espalham regra de negócio.                                     |
| O que significa “um ponto por linha”?           | Evitar encadeamentos profundos como `a.getB().getC().getD()`.                               |
| Qual relação com DDD?                           | Incentiva entidades ricas, Value Objects e regras dentro do domínio.                        |
| Qual relação com SOLID?                         | Ajuda a criar classes menores, coesas e desacopladas.                                       |

---

# English Version

## General mind map

| Central topic           | Main idea                                                | Goal                                         |
| ----------------------- | -------------------------------------------------------- | -------------------------------------------- |
| **Object Calisthenics** | A set of practical rules to train object-oriented design | Improve modeling, encapsulation and cohesion |
| **Focus**               | Writing more object-oriented and less procedural code    | Avoid anemic models and huge services        |
| **Nature**              | Design exercise, not an absolute rule                    | Force better OO habits                       |
| **Use in Java**         | Useful for domain modeling, DDD, Clean Code and SOLID    | Create objects with real behavior            |
| **Warning**             | Some rules are intentionally strict                      | Use them as training, not dogma              |

---

# 1. Object Calisthenics concept

| Aspect              | Summary                                                                                    |
| ------------------- | ------------------------------------------------------------------------------------------ |
| **Definition**      | Object Calisthenics is a set of constraints used to train better object-oriented design.   |
| **Goal**            | Help developers write code with stronger encapsulation, lower coupling and richer objects. |
| **It is not**       | A mandatory architecture pattern.                                                          |
| **It is**           | A discipline exercise for improving modeling skills.                                       |
| **Works well with** | Clean Code, SOLID, DDD, TDD and refactoring.                                               |
| **Main benefit**    | Helps avoid procedural code disguised as object-oriented code.                             |

---

# 2. The 9 Object Calisthenics rules

| No.   | Rule                                           | Main idea                                            |
| ----- | ---------------------------------------------- | ---------------------------------------------------- |
| **1** | One level of indentation per method            | Avoid complex methods and deeply nested `if` blocks  |
| **2** | Do not use `else`                              | Improve clarity with early returns and polymorphism  |
| **3** | Wrap primitives and Strings                    | Create value objects with domain meaning             |
| **4** | First-class collections                        | Keep collection rules in one place                   |
| **5** | One dot per line                               | Avoid deep object navigation                         |
| **6** | Do not abbreviate names                        | Improve readability                                  |
| **7** | Keep entities small                            | Smaller, cohesive and testable classes               |
| **8** | No classes with more than 2 instance variables | Force composition and responsibility separation      |
| **9** | Do not use getters/setters/properties freely   | Protect encapsulation and move behavior into objects |

---

# 3. Rule 1 — One level of indentation per method

| Aspect           | Summary                                                 |
| ---------------- | ------------------------------------------------------- |
| **Problem**      | Too many nested blocks make methods hard to understand. |
| **Goal**         | Keep method flow simple.                                |
| **How to apply** | Extract smaller methods or use early validation.        |
| **Benefit**      | More readable and testable code.                        |

```java
public void approve(Order order) {
    validateOrderCanBeApproved(order);
    order.approve();
}

private void validateOrderCanBeApproved(Order order) {
    if (order == null) {
        throw new IllegalArgumentException("Order cannot be null");
    }

    if (!order.isPaid()) {
        throw new IllegalStateException("Order must be paid");
    }

    if (order.isCancelled()) {
        throw new IllegalStateException("Cancelled order cannot be approved");
    }
}
```

| Before                     | After                |
| -------------------------- | -------------------- |
| Nested logic               | Linear flow          |
| Hard to see the happy path | Clear main path      |
| Many `if` levels           | Explicit validations |
| Low readability            | Higher readability   |

---

# 4. Rule 2 — Do not use `else`

| Aspect           | Summary                                                    |
| ---------------- | ---------------------------------------------------------- |
| **Problem**      | `else` can hide alternative flows and increase complexity. |
| **Goal**         | Make the main flow more direct.                            |
| **How to apply** | Use early return, exceptions or polymorphism.              |
| **Benefit**      | Simpler method reading.                                    |

```java
public BigDecimal calculateDiscount(Customer customer, BigDecimal amount) {
    if (customer.isPremium()) {
        return amount.multiply(new BigDecimal("0.10"));
    }

    return amount.multiply(new BigDecimal("0.02"));
}
```

| Situation                           | Better solution           |
| ----------------------------------- | ------------------------- |
| Many `else if` blocks by type       | Strategy                  |
| `else` with different business rule | Polymorphism              |
| `else` for error flow               | Early return or exception |
| Complex `else` block                | Extract methods           |

---

# 5. Rule 3 — Wrap primitives and Strings

| Aspect          | Summary                                                                     |
| --------------- | --------------------------------------------------------------------------- |
| **Problem**     | `String`, `int`, `BigDecimal` and `boolean` may not express domain meaning. |
| **Goal**        | Create objects that protect rules and meaning.                              |
| **Common name** | Value Object.                                                               |
| **Benefit**     | Centralized validation and more expressive code.                            |

```java
public class Customer {
    private Email email;
    private Cpf cpf;
    private Money balance;
}
```

```java
public final class Email {

    private final String value;

    public Email(String value) {
        if (value == null || !value.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }

        this.value = value;
    }

    public String value() {
        return value;
    }
}
```

| Without Value Object            | With Value Object           |
| ------------------------------- | --------------------------- |
| `String email` accepts any text | `Email` validates format    |
| Rule repeated in many places    | Rule centralized            |
| Low expressiveness              | High expressiveness         |
| Easy to pass wrong data         | Type communicates intention |

---

# 6. Rule 4 — First-class collections

| Aspect          | Summary                                              |
| --------------- | ---------------------------------------------------- |
| **Problem**     | Loose lists spread business rules across the system. |
| **Goal**        | Encapsulate collections inside a dedicated class.    |
| **Common name** | First-Class Collection.                              |
| **Benefit**     | Collection-related rules stay in one place.          |

```java
public class Order {
    private OrderItems items;

    public BigDecimal calculateTotal() {
        return items.total();
    }
}
```

```java
public class OrderItems {

    private final List<OrderItem> items;

    public OrderItems(List<OrderItem> items) {
        if (items == null || items.isEmpty()) {
            throw new IllegalArgumentException("Order must have at least one item");
        }

        this.items = List.copyOf(items);
    }

    public BigDecimal total() {
        return items.stream()
                .map(OrderItem::subtotal)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

| Before                         | After                              |
| ------------------------------ | ---------------------------------- |
| `List<OrderItem>` everywhere   | `OrderItems` encapsulates the list |
| Duplicated validation          | Centralized validation             |
| Calculation outside collection | Calculation inside collection      |
| Exposed list                   | Protected collection               |

---

# 7. Rule 5 — One dot per line

| Aspect             | Summary                                                                            |
| ------------------ | ---------------------------------------------------------------------------------- |
| **Problem**        | Deep chaining exposes internal object structure.                                   |
| **Goal**           | Avoid violating the Law of Demeter.                                                |
| **Practical idea** | Do not navigate too much inside objects. Ask the right object to perform behavior. |
| **Benefit**        | Lower coupling.                                                                    |

```java
// Bad
String city = order.getCustomer().getAddress().getCity().getName();
```

```java
// Better
String city = order.customerCityName();
```

| Code                                         | Problem                      |
| -------------------------------------------- | ---------------------------- |
| `order.getCustomer().getAddress().getCity()` | External code knows too much |
| Many chained getters                         | High coupling                |
| Change in `Address` breaks external code     | Weak encapsulation           |
| Rule outside object                          | Anemic model                 |

---

# 8. Rule 6 — Do not abbreviate names

| Bad         | Better             |
| ----------- | ------------------ |
| `usr`       | `user`             |
| `addr`      | `address`          |
| `calcTot()` | `calculateTotal()` |
| `qty`       | `quantity`         |
| `pmt`       | `payment`          |
| `cust`      | `customer`         |
| `repo`      | `repository`       |
| `svc`       | `service`          |

```java
public BigDecimal calculateTotal(List<OrderItem> items) {
    return items.stream()
            .map(OrderItem::subtotal)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

---

# 9. Rule 7 — Keep entities small

| Large class                                                              | Possible separation                                                                       |
| ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| `Order` calculates total, shipping, discount, tax and payment            | `Order`, `OrderItems`, `DiscountPolicy`, `TaxCalculator`, `ShippingCalculator`            |
| `Customer` stores data, validates CPF, calculates score and sends e-mail | `Customer`, `Cpf`, `CreditScore`, `CustomerNotificationService`                           |
| `PaymentService` validates, persists, calls gateway and audits           | `ProcessPaymentUseCase`, `Payment`, `PaymentGateway`, `PaymentRepository`, `AuditService` |

---

# 10. Rule 8 — No more than 2 instance variables

| Aspect               | Summary                                                     |
| -------------------- | ----------------------------------------------------------- |
| **Problem**          | Too many attributes may indicate too many responsibilities. |
| **Goal**             | Force composition.                                          |
| **Warning**          | This is a strict training rule.                             |
| **Real-world usage** | Not always literal, especially with JPA entities and DTOs.  |
| **Benefit**          | Helps identify natural groups of data.                      |

```java
public class Customer {
    private CustomerName name;
    private Contact contact;
    private Address address;
}
```

```java
public class Contact {
    private Email email;
    private PhoneNumber phoneNumber;
}
```

| Before             | After                        |
| ------------------ | ---------------------------- |
| Many loose fields  | Meaningful objects           |
| Bloated class      | Composition                  |
| Spread validations | Validation in proper objects |
| Low expressiveness | Richer model                 |

---

# 11. Rule 9 — Avoid free getters and setters

| Aspect           | Summary                                                                                |
| ---------------- | -------------------------------------------------------------------------------------- |
| **Problem**      | Getters and setters expose internal state and encourage external business logic.       |
| **Goal**         | Objects should receive commands, not only expose data.                                 |
| **Benefit**      | Real encapsulation.                                                                    |
| **Java warning** | Frameworks like JPA and Jackson may require constructors and accessors. Use carefully. |

```java
public class Account {

    private BigDecimal balance;

    public void withdraw(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        if (balance.compareTo(amount) < 0) {
            throw new InsufficientBalanceException("Insufficient balance");
        }

        this.balance = this.balance.subtract(amount);
    }
}
```

| Anemic model                         | Rich model           |
| ------------------------------------ | -------------------- |
| Data exposed through getters/setters | State protected      |
| Rule in service                      | Rule inside object   |
| Easy to break invariants             | Invariants protected |
| Object looks like a table            | Object has behavior  |

---

# 12. Object Calisthenics applied to Java/Spring

| Layer                 | Application                                                                                |
| --------------------- | ------------------------------------------------------------------------------------------ |
| **Controller**        | Avoid business rules; receive request and call the use case.                               |
| **DTO**               | Can have getters/setters because it is a transport object.                                 |
| **Service/Use Case**  | Orchestrates the flow without manipulating entity internals directly.                      |
| **Domain Entity**     | Has behavior, protects invariants and avoids public setters.                               |
| **Value Object**      | Encapsulates concepts such as `Email`, `Cpf`, `Money`, `Quantity`, `OrderId`.              |
| **Repository**        | Persists objects, does not decide business rules.                                          |
| **Collection Object** | Encapsulates lists with their own rules, such as `OrderItems`, `Payments`, `Installments`. |

---

# 13. Quick comparison

| Procedural code                   | Object Calisthenics code       |
| --------------------------------- | ------------------------------ |
| Service manipulates data directly | Object protects its own data   |
| Many getters and setters          | Business-intention methods     |
| Primitive values everywhere       | Value Objects                  |
| Loose lists                       | First-Class Collections        |
| Many `if/else` blocks             | Polymorphism and early returns |
| Large classes                     | Small cohesive classes         |
| Chained calls                     | Behavior in the correct object |
| Weak encapsulation                | Strong encapsulation           |

---

# 14. Review questions

| Question                                         | Short answer                                                  |
| ------------------------------------------------ | ------------------------------------------------------------- |
| What is Object Calisthenics?                     | A set of rules to train better object-oriented design.        |
| Is Object Calisthenics mandatory?                | No. It is a training practice.                                |
| Which rule fights complex methods?               | One level of indentation per method.                          |
| Why avoid `else`?                                | To simplify flow and encourage early returns or polymorphism. |
| Why wrap primitives?                             | To give domain meaning and centralize validations.            |
| What is a First-Class Collection?                | A class that encapsulates a collection and its rules.         |
| What is the issue with too many getters/setters? | They expose internal state and spread business logic.         |
| What does “one dot per line” mean?               | Avoid deep chains like `a.getB().getC().getD()`.              |
| How does it relate to DDD?                       | It encourages rich entities, Value Objects and domain rules.  |
| How does it relate to SOLID?                     | It helps create smaller, cohesive and decoupled classes.      |
