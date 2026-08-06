Lucas, segue o resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **Clean Code**.

# Versão em Português

## Mapa mental geral

| Tópico central               | Ideia principal                                     | Objetivo                              |
| ---------------------------- | --------------------------------------------------- | ------------------------------------- |
| **Nomes claros**             | Variáveis, métodos e classes devem revelar intenção | Reduzir ambiguidade                   |
| **Métodos pequenos**         | Cada método deve fazer uma coisa bem definida       | Facilitar leitura, teste e manutenção |
| **Classes coesas**           | Uma classe deve ter uma responsabilidade principal  | Evitar acoplamento e confusão         |
| **Baixa duplicação**         | Código repetido deve ser extraído ou abstraído      | Evitar bugs e retrabalho              |
| **Comentários úteis**        | Comentário deve explicar o porquê, não o óbvio      | Melhorar compreensão                  |
| **Tratamento de erro limpo** | Exceções devem ser claras e bem modeladas           | Evitar fluxo confuso                  |
| **Testabilidade**            | Código limpo é fácil de testar                      | Aumentar segurança nas mudanças       |
| **Refatoração contínua**     | Melhorar o design sem alterar comportamento         | Manter o código saudável              |

---

## 1. Conceito de Clean Code

| Aspecto             | Resumo                                                                                        |
| ------------------- | --------------------------------------------------------------------------------------------- |
| **Definição**       | Código limpo é código fácil de ler, entender, alterar e testar.                               |
| **Não significa**   | Código curto a qualquer custo.                                                                |
| **Significa**       | Código expressivo, simples, organizado e com responsabilidades claras.                        |
| **Foco principal**  | Manutenção. Código é lido muito mais vezes do que escrito.                                    |
| **Na prática Java** | Métodos pequenos, nomes claros, classes coesas, exceções bem tratadas e testes automatizados. |

---

## 2. Nomes claros

| Regra                             | Ruim        | Melhor                   |
| --------------------------------- | ----------- | ------------------------ |
| Nome deve revelar intenção        | `int d;`    | `int daysSinceCreation;` |
| Evite abreviações obscuras        | `calcTot()` | `calculateTotalAmount()` |
| Use nomes do domínio              | `process()` | `approvePayment()`       |
| Booleanos devem parecer perguntas | `active`    | `isActive`               |
| Métodos devem indicar ação        | `status()`  | `getOrderStatus()`       |

### Exemplo em Java

| Código ruim      | Código melhor               |
| ---------------- | --------------------------- |
| `p.process();`   | `payment.approve();`        |
| `u.getN();`      | `user.getName();`           |
| `x.calculate();` | `invoice.calculateTotal();` |

```java
// Ruim
public void process(Order o) {
    // lógica de aprovação
}

// Melhor
public void approveOrder(Order order) {
    // lógica de aprovação
}
```

---

## 3. Métodos pequenos e com uma responsabilidade

| Regra                          | Explicação                                     |
| ------------------------------ | ---------------------------------------------- |
| **Faça uma coisa**             | Um método deve ter uma responsabilidade clara. |
| **Evite métodos longos**       | Métodos grandes escondem regras de negócio.    |
| **Evite muitos níveis de if**  | Muitos blocos aninhados dificultam leitura.    |
| **Extraia métodos privados**   | Divida etapas complexas em nomes claros.       |
| **Prefira retorno antecipado** | Reduz aninhamento e melhora legibilidade.      |

### Exemplo ruim

```java
public void processPayment(Payment payment) {
    if (payment != null) {
        if (payment.getAmount().compareTo(BigDecimal.ZERO) > 0) {
            if (payment.getStatus() == PaymentStatus.PENDING) {
                payment.setStatus(PaymentStatus.APPROVED);
                payment.setApprovedAt(LocalDateTime.now());
            }
        }
    }
}
```

### Exemplo melhor

```java
public void approve(Payment payment) {
    validatePayment(payment);
    payment.approve();
}

private void validatePayment(Payment payment) {
    if (payment == null) {
        throw new IllegalArgumentException("Payment cannot be null");
    }

    if (payment.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("Payment amount must be positive");
    }

    if (!payment.isPending()) {
        throw new IllegalStateException("Only pending payments can be approved");
    }
}
```

---

## 4. Classes coesas

| Conceito                | Explicação                                                                      |
| ----------------------- | ------------------------------------------------------------------------------- |
| **Coesão**              | Uma classe deve concentrar comportamentos relacionados.                         |
| **Classe limpa**        | Tem um motivo claro para existir.                                               |
| **Classe problemática** | Faz validação, persistência, regra de negócio, log e integração ao mesmo tempo. |
| **Sinal de alerta**     | Classe com nomes como `Manager`, `Processor`, `Helper`, `Utils` em excesso.     |
| **Boa prática**         | Separar domínio, aplicação e infraestrutura.                                    |

### Exemplo de separação

| Responsabilidade              | Classe recomendada                          |
| ----------------------------- | ------------------------------------------- |
| Regra de negócio do pagamento | `Payment`                                   |
| Orquestração do caso de uso   | `PaymentService` ou `ApprovePaymentUseCase` |
| Persistência                  | `PaymentRepository`                         |
| Integração externa            | `PaymentGatewayClient`                      |
| Validação de entrada da API   | `PaymentRequestDTO`                         |

---

## 5. Comentários

| Tipo de comentário                | Avaliação |
| --------------------------------- | --------- |
| Explica código óbvio              | Ruim      |
| Explica regra de negócio complexa | Útil      |
| Explica decisão técnica           | Útil      |
| Compensa nome ruim                | Ruim      |
| Documenta contrato público        | Útil      |
| Código comentado morto            | Ruim      |

### Exemplo

| Ruim                                          | Melhor                                  |
| --------------------------------------------- | --------------------------------------- |
| `// soma o total`                             | Método chamado `calculateTotalAmount()` |
| `// verifica se está ativo`                   | Método chamado `user.isActive()`        |
| Comentário explicando regra fiscal específica | Aceitável                               |
| Comentário explicando workaround temporário   | Aceitável, se tiver contexto            |

```java
// Ruim
// verifica se o pagamento está pendente
if (payment.getStatus() == PaymentStatus.PENDING) {
    ...
}

// Melhor
if (payment.isPending()) {
    ...
}
```

---

## 6. Tratamento de erros

| Boa prática                       | Explicação                                               |
| --------------------------------- | -------------------------------------------------------- |
| Use exceções específicas          | Evite `Exception` genérica.                              |
| Não engula exceções               | Nunca capture erro sem tratar ou registrar corretamente. |
| Não misture erro com regra normal | Fluxo de erro deve ser claro.                            |
| Crie exceções de domínio          | Exemplo: `InsufficientBalanceException`.                 |
| Valide cedo                       | Falhe rápido quando o estado for inválido.               |

### Exemplo

```java
public void withdraw(BigDecimal amount) {
    if (amount.compareTo(BigDecimal.ZERO) <= 0) {
        throw new InvalidAmountException("Amount must be positive");
    }

    if (balance.compareTo(amount) < 0) {
        throw new InsufficientBalanceException("Insufficient balance");
    }

    this.balance = this.balance.subtract(amount);
}
```

---

## 7. Duplicação

| Tipo de duplicação | Problema                                    | Solução                              |
| ------------------ | ------------------------------------------- | ------------------------------------ |
| Código repetido    | Mudança precisa ser feita em vários lugares | Extrair método                       |
| Regra repetida     | Regra pode ficar inconsistente              | Centralizar no domínio               |
| Query repetida     | Difícil manter filtros iguais               | Reutilizar repository/specification  |
| Validação repetida | Pode gerar divergência                      | Criar validator ou método de domínio |
| Conversão repetida | Código verboso                              | Usar mapper                          |

### Exemplo

| Ruim                             | Melhor                               |
| -------------------------------- | ------------------------------------ |
| Validar saldo em vários services | Validar dentro da entidade `Account` |
| Repetir cálculo de desconto      | Criar `DiscountPolicy`               |
| Repetir montagem de DTO          | Criar `Mapper`                       |

---

## 8. Clean Code aplicado a Java/Spring

| Camada                  | Clean Code aplicado                                                           |
| ----------------------- | ----------------------------------------------------------------------------- |
| **Controller**          | Deve ser fino. Recebe requisição, valida entrada e chama caso de uso/service. |
| **Service/Application** | Orquestra fluxo. Não deve concentrar toda regra de domínio.                   |
| **Domain/Entity**       | Deve proteger invariantes e representar regras centrais.                      |
| **Repository**          | Deve cuidar de persistência, não de regra de negócio.                         |
| **DTO**                 | Deve transportar dados, não conter regra complexa.                            |
| **Mapper**              | Deve converter entre DTO, entidade e response.                                |
| **Exception Handler**   | Deve padronizar respostas de erro.                                            |

### Exemplo de organização

```text
src/main/java/com/example/payment
 ├── application
 │    └── ApprovePaymentUseCase.java
 ├── domain
 │    ├── Payment.java
 │    ├── PaymentStatus.java
 │    └── InvalidPaymentException.java
 ├── infrastructure
 │    └── PaymentJpaRepository.java
 └── presentation
      ├── PaymentController.java
      └── PaymentRequest.java
```

---

## 9. Code smells comuns

| Code smell               | O que significa                                                           | Como corrigir                                           |
| ------------------------ | ------------------------------------------------------------------------- | ------------------------------------------------------- |
| **Long Method**          | Método grande demais                                                      | Extrair métodos menores                                 |
| **God Class**            | Classe faz coisas demais                                                  | Separar responsabilidades                               |
| **Primitive Obsession**  | Uso excessivo de `String`, `int`, `BigDecimal` sem significado de domínio | Criar Value Objects                                     |
| **Shotgun Surgery**      | Pequena mudança exige alterar muitos arquivos                             | Melhorar encapsulamento                                 |
| **Feature Envy**         | Uma classe usa demais dados de outra                                      | Mover comportamento para a classe correta               |
| **Duplicated Code**      | Código repetido                                                           | Extrair abstração                                       |
| **Large Parameter List** | Método recebe parâmetros demais                                           | Criar objeto de comando/DTO                             |
| **Switch excessivo**     | Muitos `switch/if` por tipo                                               | Usar polimorfismo, estratégia ou enum com comportamento |

---

## 10. Comparação rápida

| Código ruim                          | Código limpo              |
| ------------------------------------ | ------------------------- |
| Nomes genéricos                      | Nomes expressivos         |
| Métodos longos                       | Métodos pequenos          |
| Classes com muitas responsabilidades | Classes coesas            |
| Comentários explicando o óbvio       | Código autoexplicativo    |
| Regras espalhadas                    | Regras centralizadas      |
| Exceções genéricas                   | Exceções específicas      |
| Difícil de testar                    | Fácil de testar           |
| Muitas dependências acopladas        | Dependências bem isoladas |

---

## 11. Como pensar Clean Code em entrevistas

| Pergunta mental                          | O que avaliar    |
| ---------------------------------------- | ---------------- |
| Esse nome revela intenção?               | Clareza          |
| Esse método faz uma coisa só?            | Responsabilidade |
| Essa classe tem motivo claro para mudar? | Coesão           |
| Essa regra está no lugar correto?        | Modelagem        |
| Esse código é fácil de testar?           | Testabilidade    |
| Existe duplicação de regra?              | Manutenção       |
| O erro está sendo tratado claramente?    | Robustez         |
| Um novo dev entenderia isso rapidamente? | Legibilidade     |

---

## Exercícios progressivos

| Nível | Exercício                                                                                  |
| ----- | ------------------------------------------------------------------------------------------ |
| 1     | Renomeie variáveis genéricas como `x`, `data`, `obj`, `process`.                           |
| 2     | Pegue um método com mais de 30 linhas e extraia métodos menores.                           |
| 3     | Encontre duplicação de regra em dois services e centralize no domínio.                     |
| 4     | Substitua um `if/switch` grande por Strategy ou polimorfismo.                              |
| 5     | Refatore uma classe `Service` que faz validação, regra, persistência e integração externa. |
| 6     | Crie testes antes e depois da refatoração para garantir que o comportamento não mudou.     |

---

## Perguntas de revisão

| Pergunta                                        | Resposta curta                                                        |
| ----------------------------------------------- | --------------------------------------------------------------------- |
| O que é Clean Code?                             | Código fácil de ler, entender, modificar e testar.                    |
| Código limpo precisa ser curto?                 | Não. Precisa ser claro.                                               |
| Qual é o problema de métodos longos?            | Escondem responsabilidades e dificultam teste.                        |
| Quando um comentário é ruim?                    | Quando explica algo que o próprio código deveria deixar claro.        |
| O que é uma God Class?                          | Classe que concentra responsabilidades demais.                        |
| O que é duplicação de regra?                    | A mesma regra de negócio implementada em vários lugares.              |
| Onde regras de domínio importantes devem ficar? | Preferencialmente no domínio, não espalhadas em controllers/services. |
| Por que exceções específicas são melhores?      | Porque comunicam melhor a causa do erro.                              |

---

# English Version

## General mind map

| Central topic              | Main idea                                              | Goal                                         |
| -------------------------- | ------------------------------------------------------ | -------------------------------------------- |
| **Clear names**            | Variables, methods and classes should reveal intention | Reduce ambiguity                             |
| **Small methods**          | Each method should do one clear thing                  | Improve readability, testing and maintenance |
| **Cohesive classes**       | A class should have one main responsibility            | Avoid coupling and confusion                 |
| **Low duplication**        | Repeated code should be extracted or abstracted        | Avoid bugs and rework                        |
| **Useful comments**        | Comments should explain why, not the obvious           | Improve understanding                        |
| **Clean error handling**   | Exceptions should be clear and well modeled            | Avoid confusing flows                        |
| **Testability**            | Clean code is easy to test                             | Increase confidence during changes           |
| **Continuous refactoring** | Improve design without changing behavior               | Keep the codebase healthy                    |

---

## 1. Clean Code concept

| Aspect               | Summary                                                                              |
| -------------------- | ------------------------------------------------------------------------------------ |
| **Definition**       | Clean code is code that is easy to read, understand, change and test.                |
| **It does not mean** | Short code at any cost.                                                              |
| **It means**         | Expressive, simple, organized code with clear responsibilities.                      |
| **Main focus**       | Maintainability. Code is read much more often than it is written.                    |
| **In Java practice** | Small methods, clear names, cohesive classes, proper exceptions and automated tests. |

---

## 2. Clear names

| Rule                                 | Bad         | Better                   |
| ------------------------------------ | ----------- | ------------------------ |
| Names should reveal intention        | `int d;`    | `int daysSinceCreation;` |
| Avoid obscure abbreviations          | `calcTot()` | `calculateTotalAmount()` |
| Use domain language                  | `process()` | `approvePayment()`       |
| Booleans should sound like questions | `active`    | `isActive`               |
| Methods should indicate actions      | `status()`  | `getOrderStatus()`       |

```java
// Bad
public void process(Order o) {
    // approval logic
}

// Better
public void approveOrder(Order order) {
    // approval logic
}
```

---

## 3. Small methods with one responsibility

| Rule                        | Explanation                                    |
| --------------------------- | ---------------------------------------------- |
| **Do one thing**            | A method should have one clear responsibility. |
| **Avoid long methods**      | Large methods hide business rules.             |
| **Avoid deep nesting**      | Too many nested blocks reduce readability.     |
| **Extract private methods** | Split complex steps into clear names.          |
| **Prefer early return**     | Reduces nesting and improves readability.      |

```java
public void approve(Payment payment) {
    validatePayment(payment);
    payment.approve();
}

private void validatePayment(Payment payment) {
    if (payment == null) {
        throw new IllegalArgumentException("Payment cannot be null");
    }

    if (payment.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("Payment amount must be positive");
    }

    if (!payment.isPending()) {
        throw new IllegalStateException("Only pending payments can be approved");
    }
}
```

---

## 4. Cohesive classes

| Concept               | Explanation                                                                                         |
| --------------------- | --------------------------------------------------------------------------------------------------- |
| **Cohesion**          | A class should group related behavior.                                                              |
| **Clean class**       | Has a clear reason to exist.                                                                        |
| **Problematic class** | Handles validation, persistence, business logic, logging and external integration at the same time. |
| **Warning sign**      | Excessive use of names like `Manager`, `Processor`, `Helper`, `Utils`.                              |
| **Good practice**     | Separate domain, application and infrastructure responsibilities.                                   |

| Responsibility         | Recommended class                           |
| ---------------------- | ------------------------------------------- |
| Payment business rule  | `Payment`                                   |
| Use case orchestration | `PaymentService` or `ApprovePaymentUseCase` |
| Persistence            | `PaymentRepository`                         |
| External integration   | `PaymentGatewayClient`                      |
| API input validation   | `PaymentRequestDTO`                         |

---

## 5. Comments

| Comment type                   | Evaluation |
| ------------------------------ | ---------- |
| Explains obvious code          | Bad        |
| Explains complex business rule | Useful     |
| Explains technical decision    | Useful     |
| Compensates for poor naming    | Bad        |
| Documents public contract      | Useful     |
| Dead commented code            | Bad        |

```java
// Bad
// checks if the payment is pending
if (payment.getStatus() == PaymentStatus.PENDING) {
    ...
}

// Better
if (payment.isPending()) {
    ...
}
```

---

## 6. Error handling

| Good practice                      | Explanation                                            |
| ---------------------------------- | ------------------------------------------------------ |
| Use specific exceptions            | Avoid generic `Exception`.                             |
| Do not swallow exceptions          | Never catch errors without proper handling or logging. |
| Do not mix errors with normal flow | Error flow should be explicit.                         |
| Create domain exceptions           | Example: `InsufficientBalanceException`.               |
| Validate early                     | Fail fast when the state is invalid.                   |

```java
public void withdraw(BigDecimal amount) {
    if (amount.compareTo(BigDecimal.ZERO) <= 0) {
        throw new InvalidAmountException("Amount must be positive");
    }

    if (balance.compareTo(amount) < 0) {
        throw new InsufficientBalanceException("Insufficient balance");
    }

    this.balance = this.balance.subtract(amount);
}
```

---

## 7. Duplication

| Duplication type       | Problem                                | Solution                          |
| ---------------------- | -------------------------------------- | --------------------------------- |
| Repeated code          | Changes must be done in several places | Extract method                    |
| Repeated business rule | Rule may become inconsistent           | Centralize in the domain          |
| Repeated query         | Hard to maintain equal filters         | Reuse repository/specification    |
| Repeated validation    | Can create divergence                  | Create validator or domain method |
| Repeated conversion    | Verbose code                           | Use mapper                        |

---

## 8. Clean Code applied to Java/Spring

| Layer                   | Clean Code application                                                            |
| ----------------------- | --------------------------------------------------------------------------------- |
| **Controller**          | Should be thin. Receives request, validates input and calls the use case/service. |
| **Service/Application** | Orchestrates the flow. Should not concentrate all domain logic.                   |
| **Domain/Entity**       | Protects invariants and represents core business rules.                           |
| **Repository**          | Handles persistence, not business rules.                                          |
| **DTO**                 | Transfers data, should not contain complex logic.                                 |
| **Mapper**              | Converts between DTO, entity and response.                                        |
| **Exception Handler**   | Standardizes error responses.                                                     |

```text
src/main/java/com/example/payment
 ├── application
 │    └── ApprovePaymentUseCase.java
 ├── domain
 │    ├── Payment.java
 │    ├── PaymentStatus.java
 │    └── InvalidPaymentException.java
 ├── infrastructure
 │    └── PaymentJpaRepository.java
 └── presentation
      ├── PaymentController.java
      └── PaymentRequest.java
```

---

## 9. Common code smells

| Code smell               | Meaning                                                               | How to fix                                  |
| ------------------------ | --------------------------------------------------------------------- | ------------------------------------------- |
| **Long Method**          | Method is too large                                                   | Extract smaller methods                     |
| **God Class**            | Class does too many things                                            | Split responsibilities                      |
| **Primitive Obsession**  | Excessive use of `String`, `int`, `BigDecimal` without domain meaning | Create Value Objects                        |
| **Shotgun Surgery**      | Small change requires editing many files                              | Improve encapsulation                       |
| **Feature Envy**         | A class uses too much data from another class                         | Move behavior to the proper class           |
| **Duplicated Code**      | Repeated code                                                         | Extract abstraction                         |
| **Large Parameter List** | Method receives too many parameters                                   | Create command object/DTO                   |
| **Excessive Switch**     | Too many `switch/if` statements by type                               | Use polymorphism, strategy or enum behavior |

---

## Quick comparison

| Bad code                           | Clean code                 |
| ---------------------------------- | -------------------------- |
| Generic names                      | Expressive names           |
| Long methods                       | Small methods              |
| Classes with many responsibilities | Cohesive classes           |
| Comments explaining the obvious    | Self-explanatory code      |
| Scattered business rules           | Centralized business rules |
| Generic exceptions                 | Specific exceptions        |
| Hard to test                       | Easy to test               |
| Highly coupled dependencies        | Well-isolated dependencies |

---

## Review questions

| Question                                  | Short answer                                                         |
| ----------------------------------------- | -------------------------------------------------------------------- |
| What is Clean Code?                       | Code that is easy to read, understand, modify and test.              |
| Does clean code need to be short?         | No. It needs to be clear.                                            |
| What is the problem with long methods?    | They hide responsibilities and make testing harder.                  |
| When is a comment bad?                    | When it explains something the code itself should make clear.        |
| What is a God Class?                      | A class that concentrates too many responsibilities.                 |
| What is duplicated business rule?         | The same business rule implemented in several places.                |
| Where should important domain rules live? | Preferably in the domain, not scattered across controllers/services. |
| Why are specific exceptions better?       | Because they communicate the cause of the error more clearly.        |
