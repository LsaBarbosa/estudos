
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



```java

public record PaymentCommand(
        UUID orderId,
        PaymentMethod method,
        Money amount
) {
}

// Abstração e ISP(SOLID) Contrato Coeso
public interface PaymentStrategy {

    PaymentMethod supportedMethod();

    PaymentResult pay(PaymentCommand command);
}


Sealed interface
public sealed interface PaymentResult

        // Records + sealed subtypes
        permits PaymentApproved,
                PaymentRejected,
                PaymentUnavailable {
}

// Polimorfismo + OCP (SOLID) possível extensão 
PaymentStrategy
        │
        ├── PixPaymentStrategy
        ├── CardPaymentStrategy
        └── BoletoPaymentStrategy



// Composição
@Service
public class PaymentService {

    // Inversão de Dependência (DIP-SOLID)
    private final PaymentStrategyRegistry registry;
    private final OrderRepository orderRepository;

    public PaymentService(
            PaymentStrategyRegistry registry,
            OrderRepository orderRepository) {

        this.registry = registry;
        this.orderRepository = orderRepository;
    }


    public PaymentResult pay(
            PaymentCommand command) {

        // Protege transição (encapsulamento)
         var order =
                orderRepository
                    .findById(command.orderId())
                    .orElseThrow();

        var strategy =
                registry.strategyFor(
                    command.method()
                );

        var result =
                strategy.pay(command);

        if (result instanceof PaymentApproved) {
            order.markAsPaid();
            orderRepository.save(order);
        }

        return result;
    }
}



```
