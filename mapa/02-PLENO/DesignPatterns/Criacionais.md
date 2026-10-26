
Lucas, resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **Padrões de Projeto Criacionais**.

# Versão em Português

## Mapa mental geral

| Padrão criacional    | Ideia principal                                                       | Problema que resolve                                       | Exemplo comum em Java/Spring                 |
| -------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------- | -------------------------------------------- |
| **Factory Method**   | Delega a criação de objetos para subclasses ou métodos especializados | Evita `new` espalhado e criação acoplada                   | Criar diferentes tipos de pagamento          |
| **Abstract Factory** | Cria famílias de objetos relacionados                                 | Garante compatibilidade entre objetos de uma mesma família | Criar componentes para diferentes provedores |
| **Builder**          | Constrói objetos complexos passo a passo                              | Evita construtores enormes                                 | Criar DTOs, requests, objetos imutáveis      |
| **Prototype**        | Cria objetos copiando uma instância existente                         | Evita recriar objetos complexos do zero                    | Clonar configurações ou templates            |
| **Singleton**        | Garante uma única instância global controlada                         | Compartilhar recurso único                                 | Beans singleton do Spring                    |

---

# 1. Factory Method

## Conceito

| Aspecto          | Resumo                                                                 |
| ---------------- | ---------------------------------------------------------------------- |
| **Objetivo**     | Criar objetos sem acoplar diretamente o código à classe concreta.      |
| **Ideia mental** | “Peça para uma fábrica criar o objeto correto.”                        |
| **Quando usar**  | Quando o tipo concreto depende de uma condição, configuração ou regra. |
| **Benefício**    | Centraliza a lógica de criação.                                        |
| **Risco**        | Criar factory para casos simples demais pode ser excesso.              |

## Exemplo mental

| Entrada       | Objeto criado                |
| ------------- | ---------------------------- |
| `PIX`         | `PixPaymentProcessor`        |
| `CREDIT_CARD` | `CreditCardPaymentProcessor` |
| `BOLETO`      | `BoletoPaymentProcessor`     |

## Exemplo Java resumido

```java
public interface PaymentProcessor {
    void process(Payment payment);
}
```

```java
public class PaymentProcessorFactory {

    public PaymentProcessor create(PaymentType type) {
        return switch (type) {
            case PIX -> new PixPaymentProcessor();
            case CREDIT_CARD -> new CreditCardPaymentProcessor();
            case BOLETO -> new BoletoPaymentProcessor();
        };
    }
}
```

## Quando usar

| Use Factory Method quando...              | Evite quando...                           |
| ----------------------------------------- | ----------------------------------------- |
| A criação depende de tipo ou regra        | Existe apenas uma classe concreta         |
| O `new` está espalhado no código          | A criação é simples e direta              |
| Você quer esconder detalhes de construção | A factory só repassa `new` sem valor real |

---

# 2. Abstract Factory

## Conceito

| Aspecto          | Resumo                                                                   |
| ---------------- | ------------------------------------------------------------------------ |
| **Objetivo**     | Criar famílias de objetos relacionados sem expor classes concretas.      |
| **Ideia mental** | “Fábrica de fábricas.”                                                   |
| **Quando usar**  | Quando vários objetos precisam ser criados de forma compatível entre si. |
| **Benefício**    | Mantém consistência entre objetos da mesma família.                      |
| **Risco**        | Pode aumentar complexidade se houver poucas variações.                   |

## Exemplo mental

Imagine integração com provedores de pagamento:

| Provedor | Gateway         | Antifraude           | Notificação      |
| -------- | --------------- | -------------------- | ---------------- |
| Stripe   | `StripeGateway` | `StripeFraudChecker` | `StripeNotifier` |
| Adyen    | `AdyenGateway`  | `AdyenFraudChecker`  | `AdyenNotifier`  |

A **Abstract Factory** garante que, ao escolher `Stripe`, todos os componentes sejam da família Stripe.

## Exemplo Java resumido

```java
public interface PaymentProviderFactory {
    PaymentGateway gateway();
    FraudChecker fraudChecker();
}
```

```java
public class StripeFactory implements PaymentProviderFactory {

    public PaymentGateway gateway() {
        return new StripeGateway();
    }

    public FraudChecker fraudChecker() {
        return new StripeFraudChecker();
    }
}
```

```java
public class AdyenFactory implements PaymentProviderFactory {

    public PaymentGateway gateway() {
        return new AdyenGateway();
    }

    public FraudChecker fraudChecker() {
        return new AdyenFraudChecker();
    }
}
```

## Diferença para Factory Method

| Factory Method                          | Abstract Factory                                  |
| --------------------------------------- | ------------------------------------------------- |
| Cria um tipo de objeto                  | Cria famílias de objetos relacionados             |
| Mais simples                            | Mais estrutural                                   |
| Útil para uma decisão de criação        | Útil para conjuntos compatíveis                   |
| Exemplo: criar processador de pagamento | Exemplo: criar gateway + antifraude + notificador |

---

# 3. Builder

## Conceito

| Aspecto          | Resumo                                                            |
| ---------------- | ----------------------------------------------------------------- |
| **Objetivo**     | Construir objetos complexos passo a passo.                        |
| **Ideia mental** | “Monte o objeto de forma legível e controlada.”                   |
| **Quando usar**  | Quando há muitos parâmetros, campos opcionais ou objeto imutável. |
| **Benefício**    | Evita construtores gigantes e melhora legibilidade.               |
| **Risco**        | Pode ser exagero para objetos simples.                            |

## Problema comum

```java
new Customer("Lucas", "email@email.com", "11999999999", true, "PREMIUM", address);
```

Esse construtor é difícil de ler porque não fica claro o significado de cada parâmetro.

## Exemplo Java resumido

```java
Customer customer = Customer.builder()
        .name("Lucas")
        .email("email@email.com")
        .phone("11999999999")
        .active(true)
        .plan("PREMIUM")
        .build();
```

## Quando usar

| Use Builder quando...         | Evite quando...                  |
| ----------------------------- | -------------------------------- |
| Objeto tem muitos campos      | Objeto tem 2 ou 3 campos simples |
| Existem campos opcionais      | O construtor já é claro          |
| Você quer objeto imutável     | O objeto é apenas DTO simples    |
| A criação precisa ser legível | Não há complexidade na criação   |

## Builder em Java moderno

| Recurso               | Relação                                                 |
| --------------------- | ------------------------------------------------------- |
| **Lombok `@Builder`** | Gera builder automaticamente                            |
| **Records**           | Podem reduzir necessidade de builder em objetos simples |
| **DTOs complexos**    | Builder pode melhorar legibilidade                      |
| **Test Data Builder** | Muito útil em testes unitários                          |

---

# 4. Prototype

## Conceito

| Aspecto          | Resumo                                                           |
| ---------------- | ---------------------------------------------------------------- |
| **Objetivo**     | Criar novos objetos copiando uma instância existente.            |
| **Ideia mental** | “Clone um modelo pronto.”                                        |
| **Quando usar**  | Quando criar o objeto do zero é caro ou repetitivo.              |
| **Benefício**    | Reutiliza configurações existentes.                              |
| **Risco**        | Clonagem pode gerar bugs se houver estado mutável compartilhado. |

## Exemplo mental

| Situação              | Prototype ajuda porque...              |
| --------------------- | -------------------------------------- |
| Template de relatório | Copia estrutura base e altera detalhes |
| Configuração padrão   | Reutiliza configuração inicial         |
| Objeto caro de montar | Evita reconstrução completa            |
| Criação de massa      | Clona e personaliza                    |

## Exemplo Java resumido

```java
public class ReportTemplate {

    private String title;
    private List<String> sections;

    public ReportTemplate copy() {
        ReportTemplate copy = new ReportTemplate();
        copy.title = this.title;
        copy.sections = new ArrayList<>(this.sections);
        return copy;
    }
}
```

## Atenção com cópia

| Tipo de cópia    | Explicação                                                       |
| ---------------- | ---------------------------------------------------------------- |
| **Shallow copy** | Copia referências internas. Pode compartilhar estado sem querer. |
| **Deep copy**    | Copia também os objetos internos. Mais segura, porém mais cara.  |

---

# 5. Singleton

## Conceito

| Aspecto          | Resumo                                                                 |
| ---------------- | ---------------------------------------------------------------------- |
| **Objetivo**     | Garantir que exista apenas uma instância de uma classe.                |
| **Ideia mental** | “Um único objeto compartilhado.”                                       |
| **Quando usar**  | Recursos globais, configuração, registry ou infraestrutura controlada. |
| **Benefício**    | Controla instância única.                                              |
| **Risco**        | Pode criar acoplamento global e dificultar testes.                     |

## Exemplo Java clássico

```java
public class AppConfig {

    private static final AppConfig INSTANCE = new AppConfig();

    private AppConfig() {
    }

    public static AppConfig getInstance() {
        return INSTANCE;
    }
}
```

## Singleton no Spring

No Spring, por padrão, os beans são singleton dentro do contexto da aplicação.

```java
@Service
public class PaymentService {
}
```

| Contexto                                | Significado                                                           |
| --------------------------------------- | --------------------------------------------------------------------- |
| Singleton clássico                      | A própria classe controla sua instância                               |
| Singleton no Spring                     | O container controla a instância                                      |
| `@Service`, `@Component`, `@Repository` | Normalmente são singleton por padrão                                  |
| Testabilidade                           | Singleton gerenciado pelo Spring é mais flexível que singleton manual |

## Cuidados

| Problema                     | Explicação                                 |
| ---------------------------- | ------------------------------------------ |
| Estado mutável compartilhado | Pode gerar bugs em aplicações concorrentes |
| Dificuldade de teste         | Singleton manual é difícil de mockar       |
| Acoplamento global           | Código passa a depender de acesso estático |
| Uso excessivo                | Pode esconder dependências reais           |

---

# Comparação rápida

| Padrão               | Palavra-chave    | Foco principal                          |
| -------------------- | ---------------- | --------------------------------------- |
| **Factory Method**   | Criação por tipo | Criar objeto correto sem acoplar        |
| **Abstract Factory** | Família          | Criar conjunto de objetos compatíveis   |
| **Builder**          | Montagem         | Construir objeto complexo passo a passo |
| **Prototype**        | Cópia            | Criar objeto a partir de outro          |
| **Singleton**        | Instância única  | Controlar uma única instância           |

---

# Como escolher

| Situação                                                  | Padrão recomendado                                     |
| --------------------------------------------------------- | ------------------------------------------------------ |
| Preciso escolher qual implementação criar                 | **Factory Method**                                     |
| Preciso criar vários objetos compatíveis da mesma família | **Abstract Factory**                                   |
| Tenho construtor com muitos parâmetros                    | **Builder**                                            |
| Preciso copiar um objeto base e alterar alguns dados      | **Prototype**                                          |
| Preciso garantir uma única instância controlada           | **Singleton**                                          |
| Estou usando Spring para injetar services                 | Singleton gerenciado pelo Spring, não singleton manual |
| Tenho muitos `new` espalhados para tipos diferentes       | Factory Method                                         |
| Tenho objetos de teste difíceis de montar                 | Builder ou Test Data Builder                           |

---

# Erros comuns

| Erro                                       | Padrão envolvido         | Correção                                               |
| ------------------------------------------ | ------------------------ | ------------------------------------------------------ |
| Criar factory para qualquer `new` simples  | Factory Method           | Usar factory só quando houver regra de criação         |
| Usar Abstract Factory sem famílias reais   | Abstract Factory         | Aplicar apenas quando objetos precisam ser compatíveis |
| Usar Builder para objeto simples           | Builder                  | Preferir construtor ou record                          |
| Fazer clone raso sem perceber              | Prototype                | Usar cópia profunda quando houver estado mutável       |
| Criar singleton manual em aplicação Spring | Singleton                | Preferir bean gerenciado pelo container                |
| Colocar regra de negócio dentro da factory | Factory/Abstract Factory | Factory deve criar, não executar o caso de uso         |
| Singleton com estado mutável               | Singleton                | Tornar imutável ou controlar concorrência              |

---

# Relação com Java/Spring

| Recurso                                    | Padrão relacionado                          |
| ------------------------------------------ | ------------------------------------------- |
| `@Bean` com lógica de criação              | Factory Method                              |
| `@Configuration`                           | Factory/Abstract Factory em alguns cenários |
| Beans singleton padrão                     | Singleton                                   |
| Lombok `@Builder`                          | Builder                                     |
| Test Data Builder                          | Builder                                     |
| `ObjectProvider<T>` / `ApplicationContext` | Factory/Service Locator, usar com critério  |
| Clonagem de templates/configurações        | Prototype                                   |

---

# Perguntas de revisão

| Pergunta                                              | Resposta curta                            |
| ----------------------------------------------------- | ----------------------------------------- |
| Qual padrão centraliza a criação de objetos por tipo? | Factory Method                            |
| Qual padrão cria famílias de objetos relacionados?    | Abstract Factory                          |
| Qual padrão ajuda com objetos de muitos parâmetros?   | Builder                                   |
| Qual padrão cria objetos copiando outro objeto?       | Prototype                                 |
| Qual padrão garante instância única?                  | Singleton                                 |
| Qual padrão é comum com Lombok `@Builder`?            | Builder                                   |
| Qual padrão é comum em beans do Spring?               | Singleton                                 |
| Qual é o risco do Singleton manual?                   | Acoplamento global e dificuldade de teste |
| Qual é o risco do Prototype?                          | Compartilhar estado mutável sem querer    |
| Qual é o risco de Factory mal usada?                  | Criar abstração desnecessária             |

---

# English Version

## General mind map

| Creational pattern   | Main idea                                                | Problem solved                                             | Common Java/Spring example                |
| -------------------- | -------------------------------------------------------- | ---------------------------------------------------------- | ----------------------------------------- |
| **Factory Method**   | Delegates object creation to specialized methods/classes | Avoids scattered `new` and creation coupling               | Create different payment processors       |
| **Abstract Factory** | Creates families of related objects                      | Ensures compatibility between objects from the same family | Create components for different providers |
| **Builder**          | Builds complex objects step by step                      | Avoids huge constructors                                   | Create DTOs, requests, immutable objects  |
| **Prototype**        | Creates objects by copying an existing instance          | Avoids rebuilding complex objects from scratch             | Clone templates or configurations         |
| **Singleton**        | Ensures a single controlled instance                     | Shares a unique resource                                   | Spring singleton beans                    |

---

# Quick summary

| Pattern              | Keyword          | Main use                                 |
| -------------------- | ---------------- | ---------------------------------------- |
| **Factory Method**   | Creation by type | Create the right object without coupling |
| **Abstract Factory** | Family           | Create compatible groups of objects      |
| **Builder**          | Assembly         | Build complex objects step by step       |
| **Prototype**        | Copy             | Create an object from another object     |
| **Singleton**        | Single instance  | Control one shared instance              |

---

# 1. Factory Method

| Aspect           | Summary                                                               |
| ---------------- | --------------------------------------------------------------------- |
| **Goal**         | Create objects without directly coupling code to concrete classes.    |
| **Mental model** | Ask a factory to create the correct object.                           |
| **When to use**  | When the concrete type depends on a condition, configuration or rule. |
| **Benefit**      | Centralizes creation logic.                                           |

```java
public class PaymentProcessorFactory {

    public PaymentProcessor create(PaymentType type) {
        return switch (type) {
            case PIX -> new PixPaymentProcessor();
            case CREDIT_CARD -> new CreditCardPaymentProcessor();
            case BOLETO -> new BoletoPaymentProcessor();
        };
    }
}
```

---

# 2. Abstract Factory

| Aspect           | Summary                                                               |
| ---------------- | --------------------------------------------------------------------- |
| **Goal**         | Create families of related objects without exposing concrete classes. |
| **Mental model** | Factory of factories.                                                 |
| **When to use**  | When multiple objects must be created in a compatible way.            |
| **Benefit**      | Keeps consistency between objects from the same family.               |

```java
public interface PaymentProviderFactory {
    PaymentGateway gateway();
    FraudChecker fraudChecker();
}
```

```java
public class StripeFactory implements PaymentProviderFactory {

    public PaymentGateway gateway() {
        return new StripeGateway();
    }

    public FraudChecker fraudChecker() {
        return new StripeFraudChecker();
    }
}
```

---

# 3. Builder

| Aspect           | Summary                                                |
| ---------------- | ------------------------------------------------------ |
| **Goal**         | Build complex objects step by step.                    |
| **Mental model** | Assemble the object in a readable and controlled way.  |
| **When to use**  | Many parameters, optional fields or immutable objects. |
| **Benefit**      | Avoids huge constructors and improves readability.     |

```java
Customer customer = Customer.builder()
        .name("Lucas")
        .email("email@email.com")
        .phone("11999999999")
        .active(true)
        .plan("PREMIUM")
        .build();
```

---

# 4. Prototype

| Aspect           | Summary                                                           |
| ---------------- | ----------------------------------------------------------------- |
| **Goal**         | Create new objects by copying an existing instance.               |
| **Mental model** | Clone a ready-made model.                                         |
| **When to use**  | When creating the object from scratch is expensive or repetitive. |
| **Benefit**      | Reuses existing configurations.                                   |

```java
public class ReportTemplate {

    private String title;
    private List<String> sections;

    public ReportTemplate copy() {
        ReportTemplate copy = new ReportTemplate();
        copy.title = this.title;
        copy.sections = new ArrayList<>(this.sections);
        return copy;
    }
}
```

---

# 5. Singleton

| Aspect           | Summary                                                                 |
| ---------------- | ----------------------------------------------------------------------- |
| **Goal**         | Ensure only one instance of a class exists.                             |
| **Mental model** | One shared object.                                                      |
| **When to use**  | Global resources, configuration, registry or controlled infrastructure. |
| **Benefit**      | Controls a single instance.                                             |

```java
public class AppConfig {

    private static final AppConfig INSTANCE = new AppConfig();

    private AppConfig() {
    }

    public static AppConfig getInstance() {
        return INSTANCE;
    }
}
```

## Singleton in Spring

| Context                                 | Meaning                                                         |
| --------------------------------------- | --------------------------------------------------------------- |
| Classic Singleton                       | The class controls its own instance                             |
| Spring Singleton                        | The container controls the instance                             |
| `@Service`, `@Component`, `@Repository` | Usually singleton by default                                    |
| Testability                             | Spring-managed singleton is more flexible than manual singleton |

---

# How to choose

| Situation                                              | Recommended pattern                            |
| ------------------------------------------------------ | ---------------------------------------------- |
| Choose which implementation to create                  | **Factory Method**                             |
| Create several compatible objects from the same family | **Abstract Factory**                           |
| Constructor has too many parameters                    | **Builder**                                    |
| Copy a base object and change a few details            | **Prototype**                                  |
| Guarantee one controlled instance                      | **Singleton**                                  |
| Using Spring services                                  | Spring-managed singleton, not manual singleton |
| Many `new` calls for different types                   | Factory Method                                 |
| Hard-to-build test objects                             | Builder or Test Data Builder                   |

---

# Review questions

| Question                                                 | Short answer                       |
| -------------------------------------------------------- | ---------------------------------- |
| Which pattern centralizes object creation by type?       | Factory Method                     |
| Which pattern creates families of related objects?       | Abstract Factory                   |
| Which pattern helps with many constructor parameters?    | Builder                            |
| Which pattern creates objects by copying another object? | Prototype                          |
| Which pattern guarantees a single instance?              | Singleton                          |
| Which pattern is common with Lombok `@Builder`?          | Builder                            |
| Which pattern is common with Spring beans?               | Singleton                          |
| What is the risk of manual Singleton?                    | Global coupling and hard testing   |
| What is the risk of Prototype?                           | Accidentally sharing mutable state |
| What is the risk of misused Factory?                     | Unnecessary abstraction            |
