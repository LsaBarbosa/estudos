
Lucas, resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **Padrões de Projeto Comportamentais**.

# Versão em Português

## Mapa mental geral

| Padrão comportamental       | Ideia principal                                            | Problema que resolve                                               | Exemplo comum em Java/Spring              |
| --------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------- |
| **Chain of Responsibility** | Passa uma requisição por uma cadeia de handlers            | Evita `if/else` centralizado para várias validações/processamentos | Filtros, validações, interceptadores      |
| **Command**                 | Encapsula uma ação como objeto                             | Permite enfileirar, desfazer, logar ou executar ações depois       | Jobs, filas, comandos de negócio          |
| **Iterator**                | Percorre elementos sem expor estrutura interna             | Padroniza navegação em coleções                                    | `Iterator`, `Iterable`, Streams           |
| **Mediator**                | Centraliza comunicação entre objetos                       | Evita muitos objetos se comunicando diretamente                    | Orquestradores, services de coordenação   |
| **Memento**                 | Salva e restaura estado anterior                           | Permite undo/rollback lógico                                       | Histórico de edição, snapshots            |
| **Observer**                | Notifica interessados quando algo acontece                 | Desacopla emissor e ouvintes                                       | Eventos Spring, listeners                 |
| **State**                   | Altera comportamento conforme estado interno               | Evita muitos `if` por status                                       | Pedido, pagamento, workflow               |
| **Strategy**                | Troca algoritmo/comportamento em tempo de execução         | Evita `if/switch` por tipo de regra                                | Cálculo de frete, desconto, pagamento     |
| **Template Method**         | Define esqueleto de algoritmo e permite customizar etapas  | Reaproveita fluxo fixo com variações                               | Classes abstratas, processos padronizados |
| **Visitor**                 | Adiciona operação a uma estrutura sem alterar suas classes | Separa operações de objetos complexos                              | Relatórios, validações, exportações       |
| **Interpreter**             | Interpreta uma linguagem/regra                             | Avalia expressões ou DSLs simples                                  | Filtros, regras, expressões               |

---

# 1. Chain of Responsibility

| Aspecto          | Resumo                                                                  |
| ---------------- | ----------------------------------------------------------------------- |
| **Objetivo**     | Fazer uma requisição passar por vários manipuladores até ser tratada.   |
| **Ideia mental** | “Cada handler decide se trata ou passa adiante.”                        |
| **Quando usar**  | Quando há uma sequência de validações, filtros ou etapas independentes. |
| **Evita**        | Um método gigante com vários `if/else`.                                 |
| **Exemplo**      | Validação de pagamento: saldo → antifraude → limite → autorização.      |

## Exemplo mental

| Handler                | Responsabilidade             |
| ---------------------- | ---------------------------- |
| `BalanceValidator`     | Verifica saldo               |
| `FraudValidator`       | Verifica suspeita de fraude  |
| `LimitValidator`       | Verifica limite transacional |
| `AuthorizationHandler` | Autoriza operação            |

```java
public interface PaymentHandler {
    void handle(Payment payment);
}
```

---

# 2. Command

| Aspecto          | Resumo                                                                             |
| ---------------- | ---------------------------------------------------------------------------------- |
| **Objetivo**     | Transformar uma ação em objeto.                                                    |
| **Ideia mental** | “Um comando representa algo que deve ser executado.”                               |
| **Quando usar**  | Quando você precisa enfileirar, logar, auditar, desfazer ou executar ações depois. |
| **Evita**        | Acoplamento direto entre quem solicita e quem executa.                             |
| **Exemplo**      | `CreateOrderCommand`, `CancelOrderCommand`, `RefundPaymentCommand`.                |

## Exemplo mental

| Comando                | Ação               |
| ---------------------- | ------------------ |
| `CreateOrderCommand`   | Criar pedido       |
| `CancelOrderCommand`   | Cancelar pedido    |
| `SendEmailCommand`     | Enviar e-mail      |
| `RefundPaymentCommand` | Estornar pagamento |

```java
public interface Command {
    void execute();
}
```

---

# 3. Iterator

| Aspecto          | Resumo                                                               |
| ---------------- | -------------------------------------------------------------------- |
| **Objetivo**     | Percorrer uma coleção sem expor sua estrutura interna.               |
| **Ideia mental** | “Percorrer sem saber como os dados estão armazenados.”               |
| **Quando usar**  | Quando o cliente precisa navegar por elementos de forma padronizada. |
| **Evita**        | Expor detalhes internos de listas, árvores ou coleções customizadas. |
| **Exemplo Java** | `Iterator`, `Iterable`, `for-each`, `Stream`.                        |

## Exemplo mental

| Sem Iterator                      | Com Iterator                 |
| --------------------------------- | ---------------------------- |
| Cliente conhece estrutura interna | Cliente apenas percorre      |
| Mais acoplamento                  | Menos acoplamento            |
| Difícil trocar estrutura          | Estrutura interna pode mudar |

```java
for (OrderItem item : orderItems) {
    // processa item
}
```

---

# 4. Mediator

| Aspecto          | Resumo                                                                      |
| ---------------- | --------------------------------------------------------------------------- |
| **Objetivo**     | Centralizar a comunicação entre vários objetos.                             |
| **Ideia mental** | “Os objetos falam com o mediador, não diretamente entre si.”                |
| **Quando usar**  | Quando muitos componentes estão acoplados uns aos outros.                   |
| **Evita**        | Comunicação caótica muitos-para-muitos.                                     |
| **Exemplo**      | Um `CheckoutMediator` coordenando estoque, pagamento, pedido e notificação. |

## Exemplo mental

| Sem Mediator                                                                      | Com Mediator                        |
| --------------------------------------------------------------------------------- | ----------------------------------- |
| `PaymentService` chama `StockService`, `EmailService`, `OrderService` diretamente | `CheckoutMediator` coordena o fluxo |
| Alto acoplamento                                                                  | Coordenação centralizada            |
| Difícil alterar fluxo                                                             | Fluxo mais controlado               |

---

# 5. Memento

| Aspecto          | Resumo                                                      |
| ---------------- | ----------------------------------------------------------- |
| **Objetivo**     | Salvar e restaurar estado anterior de um objeto.            |
| **Ideia mental** | “Tirar uma foto do estado atual para restaurar depois.”     |
| **Quando usar**  | Undo, histórico, snapshots e rollback lógico.               |
| **Evita**        | Expor atributos internos para restaurar estado manualmente. |
| **Exemplo**      | Editor de texto, carrinho, workflow, configuração.          |

## Exemplo mental

| Conceito   | Função                   |
| ---------- | ------------------------ |
| Originator | Objeto que possui estado |
| Memento    | Snapshot do estado       |
| Caretaker  | Guarda os snapshots      |

```java
OrderSnapshot snapshot = order.createSnapshot();
order.restore(snapshot);
```

---

# 6. Observer

| Aspecto          | Resumo                                                                   |
| ---------------- | ------------------------------------------------------------------------ |
| **Objetivo**     | Notificar automaticamente vários interessados quando um evento acontece. |
| **Ideia mental** | “Quando algo acontece, os inscritos são avisados.”                       |
| **Quando usar**  | Quando uma ação gera efeitos secundários desacoplados.                   |
| **Evita**        | Classe principal chamando diretamente todos os serviços dependentes.     |
| **Exemplo**      | Pedido criado → enviar e-mail, baixar estoque, gerar auditoria.          |

## Exemplo mental

| Evento                 | Observadores                     |
| ---------------------- | -------------------------------- |
| `OrderCreatedEvent`    | E-mail, estoque, auditoria       |
| `PaymentApprovedEvent` | Pedido, nota fiscal, notificação |
| `UserRegisteredEvent`  | Boas-vindas, CRM, métricas       |

## No Spring

```java
applicationEventPublisher.publishEvent(new OrderCreatedEvent(orderId));
```

```java
@EventListener
public void handle(OrderCreatedEvent event) {
    // reage ao evento
}
```

---

# 7. State

| Aspecto          | Resumo                                                            |
| ---------------- | ----------------------------------------------------------------- |
| **Objetivo**     | Alterar o comportamento de um objeto conforme seu estado interno. |
| **Ideia mental** | “Cada estado sabe o que pode fazer.”                              |
| **Quando usar**  | Quando há muitos `if/switch` baseados em status.                  |
| **Evita**        | Regras espalhadas como `if status == X`.                          |
| **Exemplo**      | Pedido: criado, pago, enviado, cancelado.                         |

## Exemplo mental

| Estado           | Ações permitidas       |
| ---------------- | ---------------------- |
| `CreatedState`   | pagar, cancelar        |
| `PaidState`      | enviar, estornar       |
| `ShippedState`   | entregar               |
| `CancelledState` | nenhuma ação de avanço |

```java
public interface OrderState {
    void pay(Order order);
    void cancel(Order order);
}
```

---

# 8. Strategy

| Aspecto          | Resumo                                                                        |
| ---------------- | ----------------------------------------------------------------------------- |
| **Objetivo**     | Encapsular algoritmos intercambiáveis.                                        |
| **Ideia mental** | “Escolha a estratégia correta e execute.”                                     |
| **Quando usar**  | Quando existem variações reais de uma regra.                                  |
| **Evita**        | `if/else` ou `switch` por tipo.                                               |
| **Exemplo**      | Desconto por tipo de cliente, frete por transportadora, pagamento por método. |

## Exemplo mental

| Estratégia                  | Regra           |
| --------------------------- | --------------- |
| `PixPaymentStrategy`        | Paga via Pix    |
| `CreditCardPaymentStrategy` | Paga via cartão |
| `BoletoPaymentStrategy`     | Paga via boleto |

```java
public interface PaymentStrategy {
    void pay(Payment payment);
}
```

---

# 9. Template Method

| Aspecto          | Resumo                                                                         |
| ---------------- | ------------------------------------------------------------------------------ |
| **Objetivo**     | Definir o esqueleto de um algoritmo e permitir variação em etapas específicas. |
| **Ideia mental** | “O fluxo geral é fixo, mas alguns passos mudam.”                               |
| **Quando usar**  | Quando vários processos têm a mesma sequência, mas etapas diferentes.          |
| **Evita**        | Duplicação de fluxo entre classes parecidas.                                   |
| **Exemplo**      | Processamento de pagamento: validar → autorizar → confirmar → notificar.       |

## Exemplo mental

| Fluxo fixo          | Variação                   |
| ------------------- | -------------------------- |
| Validar pagamento   | Pix valida chave           |
| Autorizar pagamento | Cartão chama adquirente    |
| Confirmar pagamento | Boleto aguarda compensação |
| Notificar cliente   | Mesmo passo geral          |

```java
public abstract class PaymentTemplate {

    public final void process(Payment payment) {
        validate(payment);
        authorize(payment);
        confirm(payment);
    }

    protected abstract void validate(Payment payment);
    protected abstract void authorize(Payment payment);

    private void confirm(Payment payment) {
        // confirmação comum
    }
}
```

---

# 10. Visitor

| Aspecto          | Resumo                                                                         |
| ---------------- | ------------------------------------------------------------------------------ |
| **Objetivo**     | Adicionar novas operações a uma estrutura de objetos sem alterar suas classes. |
| **Ideia mental** | “O visitante executa uma operação sobre vários tipos de objeto.”               |
| **Quando usar**  | Quando a estrutura de objetos é estável, mas as operações mudam.               |
| **Evita**        | Colocar muitas operações diferentes dentro das entidades.                      |
| **Exemplo**      | Exportar relatório, calcular imposto, gerar XML/JSON para tipos diferentes.    |

## Exemplo mental

| Elemento     | Visitor                  |
| ------------ | ------------------------ |
| `Book`       | `PriceCalculatorVisitor` |
| `Electronic` | `TaxCalculatorVisitor`   |
| `Food`       | `ExportVisitor`          |

```java
public interface ProductVisitor {
    void visit(Book book);
    void visit(Electronic electronic);
}
```

---

# 11. Interpreter

| Aspecto          | Resumo                                                             |
| ---------------- | ------------------------------------------------------------------ |
| **Objetivo**     | Interpretar expressões de uma linguagem ou regra.                  |
| **Ideia mental** | “Transformar uma expressão em objetos que sabem avaliá-la.”        |
| **Quando usar**  | Quando há regras simples, repetitivas e combináveis.               |
| **Evita**        | Parser manual desorganizado ou vários `if` para interpretar regra. |
| **Exemplo**      | Filtros, regras de negócio configuráveis, expressões booleanas.    |

## Exemplo mental

| Expressão                         | Interpretação   |
| --------------------------------- | --------------- |
| `amount > 1000`                   | Verifica valor  |
| `country == BR`                   | Verifica país   |
| `status == APPROVED`              | Verifica status |
| `amount > 1000 AND country == BR` | Combina regras  |

---

# Comparação rápida

| Padrão                      | Palavra-chave    | Foco principal                          |
| --------------------------- | ---------------- | --------------------------------------- |
| **Chain of Responsibility** | Cadeia           | Passar requisição por handlers          |
| **Command**                 | Ação como objeto | Encapsular execução                     |
| **Iterator**                | Percorrer        | Navegar coleções                        |
| **Mediator**                | Coordenação      | Reduzir acoplamento entre objetos       |
| **Memento**                 | Snapshot         | Salvar/restaurar estado                 |
| **Observer**                | Evento           | Notificar interessados                  |
| **State**                   | Estado           | Mudar comportamento por status          |
| **Strategy**                | Algoritmo        | Trocar regra dinamicamente              |
| **Template Method**         | Fluxo fixo       | Reaproveitar sequência com variações    |
| **Visitor**                 | Operação externa | Adicionar operações sem mudar estrutura |
| **Interpreter**             | Expressão        | Avaliar regras/linguagens simples       |

---

# Como escolher

| Situação                                          | Padrão recomendado          |
| ------------------------------------------------- | --------------------------- |
| Tenho várias validações em sequência              | **Chain of Responsibility** |
| Quero representar uma ação como objeto            | **Command**                 |
| Quero percorrer coleção sem expor estrutura       | **Iterator**                |
| Muitos objetos se comunicam diretamente           | **Mediator**                |
| Preciso de undo ou histórico de estado            | **Memento**                 |
| Uma ação dispara várias reações desacopladas      | **Observer**                |
| O comportamento muda conforme status              | **State**                   |
| Existem várias formas de executar uma regra       | **Strategy**                |
| Vários algoritmos têm o mesmo fluxo geral         | **Template Method**         |
| Quero adicionar operações a uma estrutura estável | **Visitor**                 |
| Preciso interpretar expressões ou regras simples  | **Interpreter**             |

---

# Diferenças importantes

## Strategy vs State

| Strategy                          | State                                      |
| --------------------------------- | ------------------------------------------ |
| Escolhe uma variação de algoritmo | Comportamento muda conforme estado interno |
| Normalmente selecionada de fora   | Normalmente transiciona dentro do objeto   |
| Exemplo: forma de pagamento       | Exemplo: status do pedido                  |
| Foco em algoritmo                 | Foco em ciclo de vida                      |

## Observer vs Mediator

| Observer                           | Mediator                       |
| ---------------------------------- | ------------------------------ |
| Um evento notifica vários ouvintes | Um objeto coordena comunicação |
| Mais desacoplado                   | Mais orquestrado               |
| Bom para efeitos colaterais        | Bom para fluxo coordenado      |
| Exemplo: evento de pedido criado   | Exemplo: checkout completo     |

## Command vs Strategy

| Command                                    | Strategy                             |
| ------------------------------------------ | ------------------------------------ |
| Representa uma ação executável             | Representa uma variação de algoritmo |
| Pode ser enfileirado, auditado ou desfeito | Normalmente apenas executado         |
| Exemplo: `CancelOrderCommand`              | Exemplo: `DiscountStrategy`          |
| Foco na requisição                         | Foco no comportamento                |

## Template Method vs Strategy

| Template Method                  | Strategy                       |
| -------------------------------- | ------------------------------ |
| Usa herança                      | Usa composição                 |
| Fluxo geral fixo                 | Algoritmo inteiro pode variar  |
| Classe abstrata define sequência | Interface define comportamento |
| Menos flexível                   | Mais flexível                  |

---

# Aplicação em Java/Spring

| Recurso                                       | Padrão relacionado                  |
| --------------------------------------------- | ----------------------------------- |
| `@EventListener`                              | Observer                            |
| `ApplicationEventPublisher`                   | Observer                            |
| Spring Security Filter Chain                  | Chain of Responsibility             |
| Servlet Filters                               | Chain of Responsibility             |
| `CommandLineRunner` em alguns usos            | Command                             |
| `Iterator`, `Iterable`                        | Iterator                            |
| `@TransactionalEventListener`                 | Observer                            |
| Services orquestradores                       | Mediator/Facade, dependendo do caso |
| Beans por interface com várias implementações | Strategy                            |
| Máquina de estados de pedido/pagamento        | State                               |
| Classes abstratas de processamento            | Template Method                     |

---

# Erros comuns

| Erro                                                             | Padrão envolvido        | Correção                                             |
| ---------------------------------------------------------------- | ----------------------- | ---------------------------------------------------- |
| Usar Strategy para uma única regra                               | Strategy                | Aplicar apenas quando há variação real               |
| Fazer State com muitos `if` ainda dentro dos estados             | State                   | Cada estado deve encapsular seu comportamento        |
| Usar Observer para fluxo que precisa ser fortemente transacional | Observer                | Avaliar orquestração síncrona ou saga                |
| Criar Mediator gigante                                           | Mediator                | Separar casos de uso e responsabilidades             |
| Usar Template Method quando composição seria melhor              | Template Method         | Preferir Strategy quando precisar mais flexibilidade |
| Criar Chain difícil de debugar                                   | Chain of Responsibility | Deixar ordem explícita e handlers pequenos           |
| Usar Memento com objetos muito grandes                           | Memento                 | Salvar apenas estado necessário                      |
| Usar Visitor em estrutura que muda muito                         | Visitor                 | Evitar, pois cada novo tipo exige alterar visitors   |

---

# Perguntas de revisão

| Pergunta                                                      | Resposta curta          |
| ------------------------------------------------------------- | ----------------------- |
| Qual padrão passa uma requisição por vários handlers?         | Chain of Responsibility |
| Qual padrão transforma uma ação em objeto?                    | Command                 |
| Qual padrão percorre coleções sem expor estrutura?            | Iterator                |
| Qual padrão centraliza comunicação entre objetos?             | Mediator                |
| Qual padrão salva e restaura estado?                          | Memento                 |
| Qual padrão notifica interessados sobre eventos?              | Observer                |
| Qual padrão muda comportamento conforme estado?               | State                   |
| Qual padrão troca algoritmos dinamicamente?                   | Strategy                |
| Qual padrão define fluxo fixo com etapas customizáveis?       | Template Method         |
| Qual padrão adiciona operações sem alterar classes visitadas? | Visitor                 |
| Qual padrão interpreta expressões ou regras simples?          | Interpreter             |

---

# English Version

## General mind map

| Behavioral pattern          | Main idea                                                   | Problem solved                                         | Common Java/Spring example               |
| --------------------------- | ----------------------------------------------------------- | ------------------------------------------------------ | ---------------------------------------- |
| **Chain of Responsibility** | Passes a request through a chain of handlers                | Avoids centralized `if/else` for validations/processes | Filters, validators, interceptors        |
| **Command**                 | Encapsulates an action as an object                         | Allows queueing, undoing, logging or delayed execution | Jobs, queues, business commands          |
| **Iterator**                | Traverses elements without exposing internal structure      | Standardizes collection navigation                     | `Iterator`, `Iterable`, Streams          |
| **Mediator**                | Centralizes communication between objects                   | Avoids many objects talking directly to each other     | Coordinators, orchestration services     |
| **Memento**                 | Saves and restores previous state                           | Enables undo/logical rollback                          | Edit history, snapshots                  |
| **Observer**                | Notifies interested objects when something happens          | Decouples publisher and listeners                      | Spring events, listeners                 |
| **State**                   | Changes behavior depending on internal state                | Avoids many status-based `if` statements               | Order, payment, workflow                 |
| **Strategy**                | Swaps algorithm/behavior at runtime                         | Avoids `if/switch` by rule type                        | Shipping, discount, payment              |
| **Template Method**         | Defines algorithm skeleton and customizes steps             | Reuses fixed flow with variations                      | Abstract classes, standardized processes |
| **Visitor**                 | Adds operations to a structure without changing its classes | Separates operations from object structures            | Reports, validations, exports            |
| **Interpreter**             | Interprets a language/rule                                  | Evaluates simple expressions or DSLs                   | Filters, rules, expressions              |

---

# Quick summary

| Pattern                     | Keyword            | Main use                                  |
| --------------------------- | ------------------ | ----------------------------------------- |
| **Chain of Responsibility** | Chain              | Pass request through handlers             |
| **Command**                 | Action object      | Encapsulate execution                     |
| **Iterator**                | Traverse           | Navigate collections                      |
| **Mediator**                | Coordination       | Reduce object-to-object coupling          |
| **Memento**                 | Snapshot           | Save/restore state                        |
| **Observer**                | Event              | Notify interested listeners               |
| **State**                   | Status             | Change behavior by state                  |
| **Strategy**                | Algorithm          | Swap rules dynamically                    |
| **Template Method**         | Fixed flow         | Reuse sequence with variations            |
| **Visitor**                 | External operation | Add operations without changing structure |
| **Interpreter**             | Expression         | Evaluate simple rules/languages           |

---

# Main patterns

| Pattern                     | When to use                                           | Java/Spring example                |
| --------------------------- | ----------------------------------------------------- | ---------------------------------- |
| **Chain of Responsibility** | Sequential validations, filters or handlers           | Spring Security filter chain       |
| **Command**                 | Actions that can be queued, logged, retried or undone | Job command, queue message handler |
| **Iterator**                | Standard traversal over internal collections          | `Iterable`, `Iterator`, `for-each` |
| **Mediator**                | Many components need coordinated communication        | Checkout coordinator               |
| **Memento**                 | Need undo, restore or historical snapshots            | Editor history, order snapshot     |
| **Observer**                | One event should trigger several reactions            | `@EventListener`                   |
| **State**                   | Object behavior depends heavily on status             | Payment workflow states            |
| **Strategy**                | Multiple interchangeable business rules               | Discount/payment strategy          |
| **Template Method**         | Same algorithm flow with variable steps               | Abstract payment processor         |
| **Visitor**                 | Stable object structure, changing operations          | Report/export visitor              |
| **Interpreter**             | Simple rule/expression evaluation                     | Filter expression engine           |

---

# How to choose

| Situation                                        | Recommended pattern         |
| ------------------------------------------------ | --------------------------- |
| Several validations in sequence                  | **Chain of Responsibility** |
| Represent an action as an executable object      | **Command**                 |
| Traverse a collection without exposing structure | **Iterator**                |
| Many objects communicate directly                | **Mediator**                |
| Need undo or state history                       | **Memento**                 |
| One action triggers several decoupled reactions  | **Observer**                |
| Behavior changes by status                       | **State**                   |
| Several ways to execute a rule                   | **Strategy**                |
| Several algorithms share the same flow           | **Template Method**         |
| Add operations to a stable object structure      | **Visitor**                 |
| Interpret simple rules or expressions            | **Interpreter**             |

---

# Important differences

## Strategy vs State

| Strategy                       | State                                        |
| ------------------------------ | -------------------------------------------- |
| Chooses an algorithm variation | Behavior changes according to internal state |
| Usually selected from outside  | Usually transitions inside the object        |
| Example: payment method        | Example: order status                        |
| Focus on algorithm             | Focus on lifecycle                           |

## Observer vs Mediator

| Observer                             | Mediator                             |
| ------------------------------------ | ------------------------------------ |
| One event notifies several listeners | One object coordinates communication |
| More decoupled                       | More orchestrated                    |
| Good for side effects                | Good for coordinated flow            |
| Example: order created event         | Example: checkout process            |

## Command vs Strategy

| Command                          | Strategy                          |
| -------------------------------- | --------------------------------- |
| Represents an executable action  | Represents an algorithm variation |
| Can be queued, audited or undone | Usually just executed             |
| Example: `CancelOrderCommand`    | Example: `DiscountStrategy`       |
| Focus on request                 | Focus on behavior                 |

## Template Method vs Strategy

| Template Method                 | Strategy                   |
| ------------------------------- | -------------------------- |
| Uses inheritance                | Uses composition           |
| Fixed general flow              | Whole algorithm may vary   |
| Abstract class defines sequence | Interface defines behavior |
| Less flexible                   | More flexible              |

---

# Review questions

| Question                                                        | Short answer            |
| --------------------------------------------------------------- | ----------------------- |
| Which pattern passes a request through handlers?                | Chain of Responsibility |
| Which pattern turns an action into an object?                   | Command                 |
| Which pattern traverses collections without exposing structure? | Iterator                |
| Which pattern centralizes communication?                        | Mediator                |
| Which pattern saves and restores state?                         | Memento                 |
| Which pattern notifies listeners about events?                  | Observer                |
| Which pattern changes behavior by state?                        | State                   |
| Which pattern swaps algorithms dynamically?                     | Strategy                |
| Which pattern defines fixed flow with customizable steps?       | Template Method         |
| Which pattern adds operations without changing visited classes? | Visitor                 |
| Which pattern interprets simple expressions or rules?           | Interpreter             |
