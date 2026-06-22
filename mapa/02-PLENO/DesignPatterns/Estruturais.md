Lucas, resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **Padrões de Projeto Estruturais**.

# Versão em Português

## Mapa mental geral

| Padrão estrutural | Ideia principal                                        | Problema que resolve                               | Exemplo comum em Java/Spring                       |
| ----------------- | ------------------------------------------------------ | -------------------------------------------------- | -------------------------------------------------- |
| **Adapter**       | Adapta uma interface incompatível para outra esperada  | Integrar classes/sistemas com contratos diferentes | Adaptar client externo para interface interna      |
| **Bridge**        | Separa abstração da implementação                      | Evitar explosão de subclasses                      | Separar canal de notificação e tipo de mensagem    |
| **Composite**     | Trata objetos individuais e grupos da mesma forma      | Representar estruturas em árvore                   | Menu, permissões, categorias, arquivos             |
| **Decorator**     | Adiciona comportamento sem alterar a classe original   | Evitar herança excessiva                           | Filtros, interceptadores, wrappers                 |
| **Facade**        | Cria uma interface simples para um subsistema complexo | Esconder complexidade interna                      | Service que coordena vários clients/repositories   |
| **Flyweight**     | Compartilha objetos repetidos para economizar memória  | Muitos objetos parecidos e imutáveis               | Cache de objetos imutáveis                         |
| **Proxy**         | Controla acesso a outro objeto                         | Lazy loading, segurança, cache, transação          | Spring AOP, `@Transactional`, proxies do Hibernate |

---

# 1. Adapter

## Conceito

| Aspecto          | Resumo                                                                      |
| ---------------- | --------------------------------------------------------------------------- |
| **Objetivo**     | Permitir que uma classe com interface incompatível seja usada pelo sistema. |
| **Ideia mental** | “Tradutor” entre dois contratos diferentes.                                 |
| **Quando usar**  | Quando uma API externa não combina com a interface esperada pela aplicação. |
| **Benefício**    | Evita alterar código de domínio para se adaptar a detalhes externos.        |
| **Risco**        | Criar muitos adapters desnecessários para casos simples.                    |

## Exemplo mental

| Sistema espera                   | API externa fornece              | Adapter faz                    |
| -------------------------------- | -------------------------------- | ------------------------------ |
| `PaymentGateway.pay()`           | `StripeClient.charge()`          | Converte `pay()` em `charge()` |
| `NotificationSender.send()`      | `EmailProvider.dispatch()`       | Adapta chamada                 |
| `CustomerRepository.findByCpf()` | Serviço legado com outro formato | Traduz entrada e saída         |

## Exemplo Java

```java
public interface PaymentGateway {
    void pay(Payment payment);
}
```

```java
public class StripeClient {
    public void charge(String customerId, BigDecimal amount) {
        // chamada para API externa
    }
}
```

```java
public class StripePaymentAdapter implements PaymentGateway {

    private final StripeClient stripeClient;

    public StripePaymentAdapter(StripeClient stripeClient) {
        this.stripeClient = stripeClient;
    }

    @Override
    public void pay(Payment payment) {
        stripeClient.charge(payment.customerId(), payment.amount());
    }
}
```

---

# 2. Bridge

## Conceito

| Aspecto          | Resumo                                               |
| ---------------- | ---------------------------------------------------- |
| **Objetivo**     | Separar uma abstração da sua implementação.          |
| **Ideia mental** | Duas hierarquias independentes que se combinam.      |
| **Quando usar**  | Quando há variações em duas dimensões diferentes.    |
| **Benefício**    | Evita multiplicação de subclasses.                   |
| **Risco**        | Pode parecer complexo se existirem poucas variações. |

## Problema comum

Imagine notificações com:

| Tipo de mensagem | Canal    |
| ---------------- | -------- |
| Cobrança         | E-mail   |
| Cobrança         | SMS      |
| Cobrança         | WhatsApp |
| Alerta           | E-mail   |
| Alerta           | SMS      |
| Alerta           | WhatsApp |

Sem Bridge, você poderia criar classes como:

```text
EmailBillingNotification
SmsBillingNotification
WhatsAppBillingNotification
EmailAlertNotification
SmsAlertNotification
WhatsAppAlertNotification
```

Com Bridge, você separa:

| Abstração             | Implementação         |
| --------------------- | --------------------- |
| `Notification`        | `NotificationChannel` |
| `BillingNotification` | `EmailChannel`        |
| `AlertNotification`   | `SmsChannel`          |

## Exemplo Java

```java
public interface NotificationChannel {
    void send(String message);
}
```

```java
public class EmailChannel implements NotificationChannel {
    public void send(String message) {
        System.out.println("Sending e-mail: " + message);
    }
}
```

```java
public abstract class Notification {

    protected final NotificationChannel channel;

    protected Notification(NotificationChannel channel) {
        this.channel = channel;
    }

    public abstract void notifyUser();
}
```

```java
public class BillingNotification extends Notification {

    public BillingNotification(NotificationChannel channel) {
        super(channel);
    }

    @Override
    public void notifyUser() {
        channel.send("Your invoice is available.");
    }
}
```

---

# 3. Composite

## Conceito

| Aspecto          | Resumo                                                             |
| ---------------- | ------------------------------------------------------------------ |
| **Objetivo**     | Tratar objeto individual e grupo de objetos da mesma forma.        |
| **Ideia mental** | Árvore de objetos.                                                 |
| **Quando usar**  | Quando existe relação parte-todo.                                  |
| **Benefício**    | Cliente não precisa saber se está lidando com item único ou grupo. |
| **Risco**        | Pode complicar regras se folhas e grupos forem muito diferentes.   |

## Exemplo mental

| Estrutura           | Folha             | Composto            |
| ------------------- | ----------------- | ------------------- |
| Sistema de arquivos | Arquivo           | Pasta               |
| Menu                | Item de menu      | Menu com submenus   |
| Permissões          | Permissão simples | Grupo de permissões |
| Produto             | Produto simples   | Combo de produtos   |

## Exemplo Java

```java
public interface MenuComponent {
    void show();
}
```

```java
public class MenuItem implements MenuComponent {

    private final String name;

    public MenuItem(String name) {
        this.name = name;
    }

    @Override
    public void show() {
        System.out.println(name);
    }
}
```

```java
public class MenuGroup implements MenuComponent {

    private final String name;
    private final List<MenuComponent> children = new ArrayList<>();

    public MenuGroup(String name) {
        this.name = name;
    }

    public void add(MenuComponent component) {
        children.add(component);
    }

    @Override
    public void show() {
        System.out.println(name);

        for (MenuComponent child : children) {
            child.show();
        }
    }
}
```

---

# 4. Decorator

## Conceito

| Aspecto          | Resumo                                                                 |
| ---------------- | ---------------------------------------------------------------------- |
| **Objetivo**     | Adicionar comportamento a um objeto sem modificar sua classe original. |
| **Ideia mental** | Envolver um objeto com camadas extras.                                 |
| **Quando usar**  | Quando comportamentos opcionais podem ser combinados.                  |
| **Benefício**    | Mais flexível que herança.                                             |
| **Risco**        | Muitas camadas podem dificultar debug.                                 |

## Exemplo mental

| Objeto base          | Decorator             |
| -------------------- | --------------------- |
| Café simples         | Café com leite        |
| Café simples         | Café com açúcar       |
| Serviço de pagamento | Serviço com log       |
| Serviço de pagamento | Serviço com cache     |
| Serviço de pagamento | Serviço com validação |

## Exemplo Java

```java
public interface PaymentProcessor {
    void process(Payment payment);
}
```

```java
public class BasicPaymentProcessor implements PaymentProcessor {

    @Override
    public void process(Payment payment) {
        System.out.println("Processing payment");
    }
}
```

```java
public class LoggingPaymentProcessor implements PaymentProcessor {

    private final PaymentProcessor paymentProcessor;

    public LoggingPaymentProcessor(PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    @Override
    public void process(Payment payment) {
        System.out.println("Before payment");
        paymentProcessor.process(payment);
        System.out.println("After payment");
    }
}
```

## Comparação com herança

| Herança                            | Decorator                            |
| ---------------------------------- | ------------------------------------ |
| Cria subclasses para cada variação | Combina comportamentos dinamicamente |
| Pode gerar muitas classes          | Mais flexível                        |
| Comportamento fixo na hierarquia   | Comportamento montado por composição |
| Menos flexível                     | Mais flexível                        |

---

# 5. Facade

## Conceito

| Aspecto          | Resumo                                                                             |
| ---------------- | ---------------------------------------------------------------------------------- |
| **Objetivo**     | Fornecer uma interface simples para um subsistema complexo.                        |
| **Ideia mental** | Uma porta de entrada simplificada.                                                 |
| **Quando usar**  | Quando o cliente precisa interagir com várias classes para completar uma operação. |
| **Benefício**    | Reduz acoplamento com detalhes internos.                                           |
| **Risco**        | A facade pode virar uma God Class se acumular regra demais.                        |

## Exemplo mental

Para finalizar uma compra, talvez o sistema precise:

| Etapa interna       |
| ------------------- |
| Validar carrinho    |
| Calcular frete      |
| Reservar estoque    |
| Processar pagamento |
| Gerar pedido        |
| Enviar notificação  |

A `CheckoutFacade` pode esconder essa complexidade.

## Exemplo Java

```java
public class CheckoutFacade {

    private final CartService cartService;
    private final StockService stockService;
    private final PaymentService paymentService;
    private final NotificationService notificationService;

    public CheckoutFacade(
            CartService cartService,
            StockService stockService,
            PaymentService paymentService,
            NotificationService notificationService
    ) {
        this.cartService = cartService;
        this.stockService = stockService;
        this.paymentService = paymentService;
        this.notificationService = notificationService;
    }

    public void checkout(CheckoutRequest request) {
        cartService.validate(request.cartId());
        stockService.reserve(request.cartId());
        paymentService.pay(request.payment());
        notificationService.sendConfirmation(request.customerId());
    }
}
```

## Diferença entre Facade e Service

| Facade                            | Service                                |
| --------------------------------- | -------------------------------------- |
| Simplifica acesso a um subsistema | Executa regra ou caso de uso           |
| Esconde várias classes internas   | Pode conter orquestração de negócio    |
| Foco em simplificação             | Foco em operação da aplicação          |
| Pode chamar vários services       | Pode ser chamado por controller/facade |

---

# 6. Flyweight

## Conceito

| Aspecto          | Resumo                                                                            |
| ---------------- | --------------------------------------------------------------------------------- |
| **Objetivo**     | Economizar memória compartilhando objetos repetidos.                              |
| **Ideia mental** | Reutilizar objetos iguais em vez de criar novos.                                  |
| **Quando usar**  | Quando existem muitos objetos parecidos e imutáveis.                              |
| **Benefício**    | Reduz consumo de memória.                                                         |
| **Risco**        | Pode adicionar complexidade desnecessária se não houver problema real de memória. |

## Exemplo mental

| Cenário            | Objeto compartilhável                     |
| ------------------ | ----------------------------------------- |
| Editor de texto    | Caracteres/fonte                          |
| Jogo               | Texturas, árvores, partículas             |
| Sistema financeiro | Moedas, tipos de tarifa, categorias fixas |
| Aplicação Java     | Objetos imutáveis cacheados               |

## Exemplo Java

```java
public final class Currency {

    private final String code;

    private Currency(String code) {
        this.code = code;
    }

    public String code() {
        return code;
    }

    public static Currency of(String code) {
        return CurrencyFactory.get(code);
    }
}
```

```java
public class CurrencyFactory {

    private static final Map<String, Currency> CACHE = new HashMap<>();

    public static Currency get(String code) {
        return CACHE.computeIfAbsent(code, Currency::new);
    }
}
```

## Atenção

| Quando faz sentido      | Quando não faz sentido    |
| ----------------------- | ------------------------- |
| Muitos objetos iguais   | Poucos objetos            |
| Objetos imutáveis       | Objetos mutáveis          |
| Memória é problema real | Otimização prematura      |
| Reuso é seguro          | Estado muda por instância |

---

# 7. Proxy

## Conceito

| Aspecto          | Resumo                                                         |
| ---------------- | -------------------------------------------------------------- |
| **Objetivo**     | Controlar acesso a outro objeto.                               |
| **Ideia mental** | Um representante que intercepta chamadas.                      |
| **Quando usar**  | Lazy loading, cache, segurança, transação, log, acesso remoto. |
| **Benefício**    | Adiciona controle sem alterar o objeto real.                   |
| **Risco**        | Pode esconder comportamento e dificultar debug.                |

## Tipos comuns de Proxy

| Tipo                    | Uso                                            |
| ----------------------- | ---------------------------------------------- |
| **Virtual Proxy**       | Carrega objeto pesado apenas quando necessário |
| **Protection Proxy**    | Controla permissão de acesso                   |
| **Remote Proxy**        | Representa objeto remoto                       |
| **Cache Proxy**         | Evita chamadas repetidas                       |
| **Logging Proxy**       | Registra chamadas                              |
| **Transactional Proxy** | Controla transações                            |

## Exemplo Java

```java
public interface ReportService {
    Report generate();
}
```

```java
public class RealReportService implements ReportService {

    @Override
    public Report generate() {
        System.out.println("Generating expensive report");
        return new Report();
    }
}
```

```java
public class CachedReportServiceProxy implements ReportService {

    private final ReportService reportService;
    private Report cachedReport;

    public CachedReportServiceProxy(ReportService reportService) {
        this.reportService = reportService;
    }

    @Override
    public Report generate() {
        if (cachedReport == null) {
            cachedReport = reportService.generate();
        }

        return cachedReport;
    }
}
```

## Proxy no Spring

| Recurso Spring         | Relação com Proxy                             |
| ---------------------- | --------------------------------------------- |
| `@Transactional`       | Spring cria proxy para abrir/fechar transação |
| `@Cacheable`           | Proxy intercepta chamada e consulta cache     |
| `@Async`               | Proxy executa método de forma assíncrona      |
| Spring Security        | Proxy/interceptor valida acesso               |
| Hibernate Lazy Loading | Proxy carrega entidade sob demanda            |

---

# Comparação rápida

| Padrão        | Palavra-chave    | Usa composição? | Foco                              |
| ------------- | ---------------- | --------------- | --------------------------------- |
| **Adapter**   | Adaptação        | Sim             | Compatibilidade entre interfaces  |
| **Bridge**    | Separação        | Sim             | Separar abstração e implementação |
| **Composite** | Árvore           | Sim             | Parte-todo                        |
| **Decorator** | Envoltório       | Sim             | Adicionar comportamento           |
| **Facade**    | Simplificação    | Sim             | Esconder complexidade             |
| **Flyweight** | Compartilhamento | Sim             | Economizar memória                |
| **Proxy**     | Controle         | Sim             | Controlar acesso                  |

---

# Como escolher

| Situação                                                     | Padrão recomendado |
| ------------------------------------------------------------ | ------------------ |
| Preciso integrar uma API com contrato diferente              | **Adapter**        |
| Tenho duas variações independentes que crescem separadamente | **Bridge**         |
| Preciso representar árvore ou hierarquia parte-todo          | **Composite**      |
| Quero adicionar comportamento sem alterar a classe original  | **Decorator**      |
| Quero simplificar acesso a várias classes internas           | **Facade**         |
| Tenho muitos objetos iguais e imutáveis consumindo memória   | **Flyweight**      |
| Quero controlar acesso, cache, lazy loading ou transação     | **Proxy**          |

---

# Erros comuns

| Erro                                                                      | Padrão envolvido | Correção                                                     |
| ------------------------------------------------------------------------- | ---------------- | ------------------------------------------------------------ |
| Usar Adapter para qualquer conversão simples                              | Adapter          | Usar apenas quando há incompatibilidade real                 |
| Criar Bridge sem múltiplas dimensões de variação                          | Bridge           | Aplicar somente quando há crescimento independente           |
| Usar Composite quando folhas e grupos têm comportamentos muito diferentes | Composite        | Separar contratos                                            |
| Criar Decorator demais                                                    | Decorator        | Limitar camadas e documentar composição                      |
| Transformar Facade em God Class                                           | Facade           | Manter regra nos serviços/domínio corretos                   |
| Usar Flyweight sem problema de memória                                    | Flyweight        | Evitar otimização prematura                                  |
| Ignorar proxies do Spring                                                 | Proxy            | Entender efeitos de `@Transactional`, `@Cacheable`, `@Async` |

---

# Perguntas de revisão

| Pergunta                                                  | Resposta curta |
| --------------------------------------------------------- | -------------- |
| Qual padrão adapta uma interface incompatível?            | Adapter        |
| Qual padrão separa abstração da implementação?            | Bridge         |
| Qual padrão representa árvore de objetos?                 | Composite      |
| Qual padrão adiciona comportamento dinamicamente?         | Decorator      |
| Qual padrão simplifica acesso a subsistemas complexos?    | Facade         |
| Qual padrão compartilha objetos para economizar memória?  | Flyweight      |
| Qual padrão controla acesso a outro objeto?               | Proxy          |
| Qual padrão aparece muito no Spring com `@Transactional`? | Proxy          |
| Qual padrão é útil para integrar API externa?             | Adapter        |
| Qual padrão ajuda a evitar explosão de subclasses?        | Bridge         |

---

# English Version

## General mind map

| Structural pattern | Main idea                                            | Problem solved                                      | Common Java/Spring example                           |
| ------------------ | ---------------------------------------------------- | --------------------------------------------------- | ---------------------------------------------------- |
| **Adapter**        | Adapts an incompatible interface to the expected one | Integrates classes/systems with different contracts | Adapting an external client to an internal interface |
| **Bridge**         | Separates abstraction from implementation            | Avoids subclass explosion                           | Separate notification type from channel              |
| **Composite**      | Treats individual objects and groups uniformly       | Represents tree structures                          | Menu, permissions, categories, files                 |
| **Decorator**      | Adds behavior without changing the original class    | Avoids excessive inheritance                        | Filters, interceptors, wrappers                      |
| **Facade**         | Provides a simple interface to a complex subsystem   | Hides internal complexity                           | Service coordinating clients/repositories            |
| **Flyweight**      | Shares repeated objects to save memory               | Many similar immutable objects                      | Immutable object cache                               |
| **Proxy**          | Controls access to another object                    | Lazy loading, security, cache, transaction          | Spring AOP, `@Transactional`, Hibernate proxies      |

---

# Quick summary

| Pattern       | Keyword        | Main use                                   |
| ------------- | -------------- | ------------------------------------------ |
| **Adapter**   | Compatibility  | Make incompatible interfaces work together |
| **Bridge**    | Separation     | Separate abstraction and implementation    |
| **Composite** | Tree           | Represent part-whole hierarchies           |
| **Decorator** | Wrapper        | Add behavior dynamically                   |
| **Facade**    | Simplification | Hide subsystem complexity                  |
| **Flyweight** | Sharing        | Reduce memory usage                        |
| **Proxy**     | Control        | Control access to another object           |

---

# 1. Adapter

| Aspect           | Summary                                                                |
| ---------------- | ---------------------------------------------------------------------- |
| **Goal**         | Allow a class with an incompatible interface to be used by the system. |
| **Mental model** | Translator between two different contracts.                            |
| **When to use**  | When an external API does not match the application interface.         |
| **Benefit**      | Avoids changing domain code because of external details.               |

```java
public interface PaymentGateway {
    void pay(Payment payment);
}
```

```java
public class StripePaymentAdapter implements PaymentGateway {

    private final StripeClient stripeClient;

    public StripePaymentAdapter(StripeClient stripeClient) {
        this.stripeClient = stripeClient;
    }

    @Override
    public void pay(Payment payment) {
        stripeClient.charge(payment.customerId(), payment.amount());
    }
}
```

---

# 2. Bridge

| Aspect           | Summary                                                |
| ---------------- | ------------------------------------------------------ |
| **Goal**         | Separate an abstraction from its implementation.       |
| **Mental model** | Two independent hierarchies that can be combined.      |
| **When to use**  | When there are variations in two different dimensions. |
| **Benefit**      | Avoids subclass explosion.                             |

```java
public interface NotificationChannel {
    void send(String message);
}
```

```java
public abstract class Notification {

    protected final NotificationChannel channel;

    protected Notification(NotificationChannel channel) {
        this.channel = channel;
    }

    public abstract void notifyUser();
}
```

```java
public class BillingNotification extends Notification {

    public BillingNotification(NotificationChannel channel) {
        super(channel);
    }

    @Override
    public void notifyUser() {
        channel.send("Your invoice is available.");
    }
}
```

---

# 3. Composite

| Aspect           | Summary                                                              |
| ---------------- | -------------------------------------------------------------------- |
| **Goal**         | Treat individual objects and groups of objects the same way.         |
| **Mental model** | Object tree.                                                         |
| **When to use**  | When there is a part-whole relationship.                             |
| **Benefit**      | Client code does not need to know if it handles one item or a group. |

```java
public interface MenuComponent {
    void show();
}
```

```java
public class MenuGroup implements MenuComponent {

    private final List<MenuComponent> children = new ArrayList<>();

    public void add(MenuComponent component) {
        children.add(component);
    }

    @Override
    public void show() {
        for (MenuComponent child : children) {
            child.show();
        }
    }
}
```

---

# 4. Decorator

| Aspect           | Summary                                                         |
| ---------------- | --------------------------------------------------------------- |
| **Goal**         | Add behavior to an object without modifying its original class. |
| **Mental model** | Wrap an object with extra layers.                               |
| **When to use**  | When optional behaviors can be combined.                        |
| **Benefit**      | More flexible than inheritance.                                 |

```java
public interface PaymentProcessor {
    void process(Payment payment);
}
```

```java
public class LoggingPaymentProcessor implements PaymentProcessor {

    private final PaymentProcessor paymentProcessor;

    public LoggingPaymentProcessor(PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    @Override
    public void process(Payment payment) {
        System.out.println("Before payment");
        paymentProcessor.process(payment);
        System.out.println("After payment");
    }
}
```

---

# 5. Facade

| Aspect           | Summary                                                                     |
| ---------------- | --------------------------------------------------------------------------- |
| **Goal**         | Provide a simple interface to a complex subsystem.                          |
| **Mental model** | A simplified entry point.                                                   |
| **When to use**  | When a client needs to interact with many classes to complete an operation. |
| **Benefit**      | Reduces coupling to internal details.                                       |

```java
public class CheckoutFacade {

    private final CartService cartService;
    private final StockService stockService;
    private final PaymentService paymentService;

    public void checkout(CheckoutRequest request) {
        cartService.validate(request.cartId());
        stockService.reserve(request.cartId());
        paymentService.pay(request.payment());
    }
}
```

---

# 6. Flyweight

| Aspect           | Summary                                                     |
| ---------------- | ----------------------------------------------------------- |
| **Goal**         | Save memory by sharing repeated objects.                    |
| **Mental model** | Reuse equal immutable objects instead of creating new ones. |
| **When to use**  | When there are many similar immutable objects.              |
| **Benefit**      | Reduces memory consumption.                                 |

```java
public class CurrencyFactory {

    private static final Map<String, Currency> CACHE = new HashMap<>();

    public static Currency get(String code) {
        return CACHE.computeIfAbsent(code, Currency::new);
    }
}
```

---

# 7. Proxy

| Aspect           | Summary                                                             |
| ---------------- | ------------------------------------------------------------------- |
| **Goal**         | Control access to another object.                                   |
| **Mental model** | A representative that intercepts calls.                             |
| **When to use**  | Lazy loading, cache, security, transaction, logging, remote access. |
| **Benefit**      | Adds control without changing the real object.                      |

```java
public class CachedReportServiceProxy implements ReportService {

    private final ReportService reportService;
    private Report cachedReport;

    public CachedReportServiceProxy(ReportService reportService) {
        this.reportService = reportService;
    }

    @Override
    public Report generate() {
        if (cachedReport == null) {
            cachedReport = reportService.generate();
        }

        return cachedReport;
    }
}
```

---

# How to choose

| Situation                                          | Recommended pattern |
| -------------------------------------------------- | ------------------- |
| Integrate an API with a different contract         | **Adapter**         |
| Two independent dimensions vary separately         | **Bridge**          |
| Represent a tree or part-whole hierarchy           | **Composite**       |
| Add behavior without changing the original class   | **Decorator**       |
| Simplify access to many internal classes           | **Facade**          |
| Share many equal immutable objects                 | **Flyweight**       |
| Control access, cache, lazy loading or transaction | **Proxy**           |

---

# Review questions

| Question                                                 | Short answer |
| -------------------------------------------------------- | ------------ |
| Which pattern adapts an incompatible interface?          | Adapter      |
| Which pattern separates abstraction from implementation? | Bridge       |
| Which pattern represents object trees?                   | Composite    |
| Which pattern adds behavior dynamically?                 | Decorator    |
| Which pattern simplifies access to complex subsystems?   | Facade       |
| Which pattern shares objects to save memory?             | Flyweight    |
| Which pattern controls access to another object?         | Proxy        |
| Which pattern is common in Spring with `@Transactional`? | Proxy        |
| Which pattern is useful for external API integration?    | Adapter      |
| Which pattern helps avoid subclass explosion?            | Bridge       |
