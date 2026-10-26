Lucas, em **Spring Core** o ponto central é entender que o Spring é, antes de tudo, um **container IoC**. Ele descobre ou recebe definições de objetos, cria esses objetos, resolve suas dependências, controla seu ciclo de vida e pode ainda envolvê-los em proxies para adicionar comportamentos como transações, segurança, cache e AOP. O `ApplicationContext` é a principal interface usada para esse container nas aplicações Spring. 

# 1. Spring Core — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **IoC** | Inversion of Control. A responsabilidade de criar e conectar objetos passa da aplicação para o container Spring. | Reduz acoplamento, mas exige entender o comportamento do container. | Gerenciamento da arquitetura da aplicação. |
| **Dependency Injection** | Forma de IoC em que uma classe declara suas dependências e o Spring as fornece. | Facilita testes e desacoplamento. Dependências mal estruturadas podem gerar ciclos. | `OrderService` recebendo `PaymentGateway`. |
| **ApplicationContext** | Principal interface do container Spring. Estende `BeanFactory` e adiciona eventos, internacionalização, integração AOP e outros recursos. | Mais completo e com mais infraestrutura que um `BeanFactory` simples. | Praticamente qualquer aplicação Spring/Spring Boot. |
| **BeanFactory** | Abstração fundamental responsável por registrar, criar e fornecer beans. | API de nível mais baixo; raramente utilizada diretamente em aplicações comuns. | Infraestrutura e extensões avançadas do Spring. |
| **Bean** | Objeto criado, configurado e gerenciado pelo container Spring. | O comportamento pode depender do container, lifecycle, scope e proxies. | Services, repositories, clients, configurations. |
| **Bean lifecycle** | Processo de criação, injeção, inicialização, pós-processamento, utilização e destruição de um bean. | Hooks de lifecycle excessivos podem dificultar entendimento e startup. | Inicializar recursos, validar configuração, liberar recursos. |
| **Bean scopes** | Determinam quantas instâncias de um bean existem e por quanto tempo. | Escolher scope incorreto pode causar estado compartilhado indevido ou consumo excessivo. | `singleton`, `prototype`, `request`, `session`. |
| **Component Scan** | Procura classes candidatas no classpath e registra suas `BeanDefinition`. | Scans muito amplos podem aumentar startup e registrar componentes indesejados. | Descobrir `@Service`, `@Repository`, `@Controller`, `@Component`. |
| **`@Configuration`** | Indica uma classe usada como fonte de definições de beans. | Pode adicionar comportamento de proxy dependendo da configuração. | Configuração explícita da aplicação. |
| **`@Bean`** | Registra no container o objeto retornado por um método de configuração. | Mais explícito que component scan, porém aumenta código de configuração. | Bibliotecas externas, clients, `ObjectMapper`, configurações customizadas. |
| **`@Conditional`** | Registra um componente ou bean somente quando determinada condição é satisfeita. | Poderoso, mas excesso de condições pode dificultar entender por que um bean existe ou não. | Auto-configuração e integração opcional. |
| **Profiles** | Permitem ativar grupos diferentes de beans conforme o ambiente. | Muitos profiles podem gerar explosão de combinações e configurações difíceis de testar. | `dev`, `test`, `production`. |
| **BeanPostProcessor** | Atua sobre **instâncias dos beans** durante seu processo de inicialização. Pode inclusive devolver um proxy em vez do objeto original. | Muito poderoso e interno ao container; uso customizado incorreto pode afetar muitos beans. | AOP, processamento de annotations e proxies. |
| **BeanFactoryPostProcessor** | Atua sobre **BeanDefinitions**, antes da criação normal dos beans. | Pode alterar profundamente a configuração do container. | Modificar propriedades ou registrar/configurar definições dinamicamente. |
| **AOP** | Aspect-Oriented Programming. Separa comportamentos transversais da lógica de negócio. | Pode tornar o fluxo menos explícito porque o comportamento ocorre através de proxies/interceptadores. | Transações, segurança, logging, cache, métricas. |
| **Proxy** | Objeto intermediário que intercepta chamadas antes de delegá-las ao bean real. | Chamadas internas no próprio objeto podem não passar pelo proxy. | `@Transactional`, `@Async`, `@Cacheable`, AOP. |
| **JDK Dynamic Proxy** | Proxy baseado nas interfaces implementadas pelo objeto. | Expõe principalmente os contratos das interfaces. | Services orientados a interfaces. |
| **CGLIB Proxy** | Proxy criado através de uma subclasse gerada dinamicamente. | Não consegue sobrescrever métodos `final` ou `private`; classes `final` também são problemáticas. | Beans sem interface ou proxy baseado na classe. |

O Spring define `ApplicationContext` como uma extensão de `BeanFactory`. O primeiro adiciona funcionalidades de nível de aplicação e é a opção recomendada para praticamente todos os usos normais. 

---

# 2. IoC e Dependency Injection

Sem IoC, poderíamos ter:

```java
public class OrderService {

    private final PaymentGateway paymentGateway =
            new StripePaymentGateway();
}
```

`OrderService` decidiu:

```text
qual implementação usar
        +
como criá-la
```

Isso aumenta o acoplamento.

Com Dependency Injection:

```java
@Service
public class OrderService {

    private final PaymentGateway paymentGateway;

    public OrderService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }
}
```

Agora:

```text
OrderService
     ↓
depende de
     ↓
PaymentGateway
```

e o container decide qual implementação fornecer.

Esse é o significado prático de IoC:

```text
Antes

classe
  ↓
cria dependência


Com Spring

container
  ↓
cria objetos
  ↓
resolve dependências
  ↓
injeta objetos
```

Dependency Injection é uma forma específica de IoC em que os objetos declaram suas dependências e o container as fornece durante sua criação. 

Para dependências obrigatórias, **injeção por construtor** normalmente oferece o modelo mais claro:

```java
public UserService(UserRepository repository) {
    this.repository = repository;
}
```

Ela deixa as dependências explícitas, facilita testes e permite campos `final`.

---

# 3. ApplicationContext x BeanFactory

A relação é:

```text
BeanFactory
     ↑
     │
ApplicationContext
```

O `BeanFactory` oferece a infraestrutura básica de:

```text
BeanDefinitions
criação de beans
dependency injection
scopes
lifecycle
```

O `ApplicationContext` adiciona funcionalidades como:

```text
events
i18n
AOP integration
environment
resource loading
```

Por isso, em uma aplicação normal:

> usamos `ApplicationContext`.

`BeanFactory` aparece mais quando estamos trabalhando com infraestrutura ou extensões de baixo nível do próprio container. A documentação atual recomenda `ApplicationContext` salvo quando há uma razão específica para trabalhar diretamente com `BeanFactory`. 

---

# 4. O que é um Bean

Um Bean não possui nada de especial do ponto de vista da linguagem Java.

Pode ser simplesmente:

```java
public class PaymentService {
}
```

Ele se torna um Spring Bean quando o container passa a:

```text
instanciá-lo
   ↓
configurá-lo
   ↓
resolver suas dependências
   ↓
gerenciar seu lifecycle
```

O Spring chama de beans os objetos que são instanciados, montados e gerenciados pelo IoC container. 

---

# 5. Como um Bean entra no container

Existem principalmente duas estratégias comuns.

## Component Scan

```java
@Service
public class PaymentService {
}
```

O Spring encontra a classe através do scanning.

Os principais stereotypes são:

```text
@Component
├── @Service
├── @Repository
└── @Controller
```

`@Component` é genérico.

`@Service` representa semanticamente camada de serviço.

`@Repository` representa persistência e possui integração com mecanismos como tradução de exceções.

`@Controller` representa camada web MVC. 

---

# 6. `@Configuration` e `@Bean`

Outra opção é registrar explicitamente.

```java
@Configuration
public class PaymentConfig {

    @Bean
    public PaymentGateway paymentGateway() {
        return new StripePaymentGateway();
    }
}
```

`@Configuration` indica que aquela classe contém configuração de beans.

`@Bean` significa:

> O objeto retornado por esse método será gerenciado pelo Spring.

Isso é muito útil quando você não controla a classe.

Por exemplo:

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

Você não colocaria:

```java
@Component
```

dentro do código do Jackson.

Então registra a instância explicitamente. `@Configuration` e `@Bean` são os principais elementos da configuração Java do container. 

---

# 7. Component Scan x `@Bean`

Regra prática:

```text
Classe da minha aplicação
        ↓
@Component / @Service / @Repository


Classe externa ou configuração específica
        ↓
@Bean
```

Por exemplo:

```java
@Service
class OrderService {
}
```

versus:

```java
@Bean
WebClient webClient() {
    return WebClient.builder().build();
}
```

Não é uma regra absoluta, mas é um bom modelo mental.

---

# 8. Bean lifecycle

Esse é um ponto importante em entrevistas.

De forma simplificada:

```text
BeanDefinition
      ↓
instanciação
      ↓
injeção das dependências
      ↓
Aware callbacks
      ↓
BeanPostProcessor
before initialization
      ↓
@PostConstruct
InitializingBean
init method
      ↓
BeanPostProcessor
after initialization
      ↓
Bean pronto
      ↓
uso
      ↓
@PreDestroy
DisposableBean
destroy method
```

Para inicialização moderna, normalmente podemos usar:

```java
@PostConstruct
public void init() {
}
```

Para destruição:

```java
@PreDestroy
public void destroy() {
}
```

A documentação recomenda `@PostConstruct` e `@PreDestroy` para callbacks modernos, evitando acoplamento desnecessário com interfaces específicas do Spring. 

---

# 9. Bean scopes

O scope padrão é:

```text
singleton
```

Mas atenção:

**Spring Singleton não significa exatamente o GoF Singleton global da JVM.**

Significa aproximadamente:

> uma instância daquele bean por Spring IoC container e por bean definition.

Os scopes principais são:

```text
singleton
prototype
request
session
application
websocket
```

O `singleton` é o padrão; `prototype` cria novas instâncias quando solicitado; os scopes web vinculam a instância ao request, session, application ou WebSocket. 

---

# 10. Singleton e thread safety

Esse é um ponto muito importante para backend.

Considere:

```java
@Service
public class CounterService {

    private int counter;

    public void increment() {
        counter++;
    }
}
```

Como o bean normalmente será:

```text
singleton
```

várias requisições podem executar sobre a mesma instância.

Então:

```text
Request A ──┐
Request B ──┼──► CounterService
Request C ──┘
```

Se o bean possui estado mutável compartilhado, você pode criar problemas de concorrência.

Por isso, serviços Spring normalmente devem ser:

```text
stateless
```

ou ter o estado concorrente explicitamente controlado.

`singleton` **não significa thread-safe**.

---

# 11. `@Conditional`

`@Conditional` permite registrar um bean somente quando uma condição é verdadeira.

Conceitualmente:

```java
@Bean
@Conditional(MyCondition.class)
public PaymentGateway paymentGateway() {
    return new PaymentGateway();
}
```

Fluxo:

```text
Condition
   ↓
match?
 ┌───┴───┐
sim     não
 ↓       ↓
bean    ignora
```

Isso é extremamente importante no ecossistema Spring Boot.

Grande parte da ideia de auto-configuração depende de condições.

O próprio `@Conditional` pode ser usado em componentes, configurações ou métodos `@Bean`. 

---

# 12. Profiles

Profiles também controlam quais beans são registrados.

Exemplo:

```java
@Configuration
@Profile("dev")
public class DevConfig {
}
```

e:

```java
@Configuration
@Profile("prod")
public class ProdConfig {
}
```

Podemos ter:

```text
dev
 ↓
FakePaymentGateway


prod
 ↓
RealPaymentGateway
```

Profiles representam grupos lógicos de bean definitions ativados de acordo com o ambiente. 

Mas existe um cuidado arquitetural.

Se você tiver:

```text
dev
qa
uat
prod
cloud
local
aws
gcp
client-a
client-b
```

e começar a combinar tudo, a configuração fica difícil de entender.

Portanto, profiles devem ser usados com moderação.

---

# 13. BeanFactoryPostProcessor

Essa diferença costuma aparecer em entrevistas.

O `BeanFactoryPostProcessor` trabalha sobre:

```text
BeanDefinition
```

e não sobre:

```text
bean pronto
```

Fluxo:

```text
BeanDefinitions carregadas
        ↓
BeanFactoryPostProcessor
        ↓
pode alterar metadata
        ↓
beans são criados
```

Por exemplo, ele pode alterar propriedades das definições antes que os objetos existam.

A documentação define esse extension point justamente como uma forma de modificar metadata das definições antes da instanciação normal dos beans. 

---

# 14. BeanPostProcessor

O `BeanPostProcessor` trabalha em outro momento.

```text
BeanDefinition
      ↓
bean criado
      ↓
BeanPostProcessor
```

Ele atua sobre:

> **a instância real do bean.**

Possui callbacks conceitualmente:

```text
before initialization

after initialization
```

E pode inclusive retornar:

```text
bean original
```

ou:

```text
proxy(bean original)
```

Essa é uma das peças mais importantes da infraestrutura interna do Spring.

Vários recursos do framework usam `BeanPostProcessor`, inclusive infraestrutura responsável por criação de proxies AOP. 

Memorize:

```text
BeanFactoryPostProcessor
        ↓
BeanDefinition


BeanPostProcessor
        ↓
Bean instance
```

---

# 15. AOP

AOP significa:

**Aspect-Oriented Programming.**

O objetivo é separar comportamentos transversais.

Imagine:

```java
public void processPayment() {

    iniciarTransacao();

    logar();

    validarSeguranca();

    // regra de negócio

    commit();
}
```

Esses comportamentos aparecem em vários pontos:

```text
transaction
security
logging
metrics
cache
```

AOP permite separar isso:

```text
           Proxy
             ↓
      comportamento transversal
             ↓
       objeto de negócio
```

Por exemplo:

```java
@Transactional
public void processPayment() {
}
```

O método contém a regra de negócio.

A infraestrutura de transação fica fora dele.

Spring AOP implementa esse comportamento em runtime utilizando proxies. 

---

# 16. Proxy — conceito fundamental

Quando você injeta:

```java
PaymentService
```

o objeto recebido pode não ser diretamente:

```text
PaymentService
```

Pode ser:

```text
PaymentService Proxy
        │
        ↓
PaymentService real
```

Quando chamamos:

```java
paymentService.process();
```

pode acontecer:

```text
caller
  ↓
Proxy
  ↓
abre transação
  ↓
PaymentService
  ↓
fecha transação
```

Esse conceito explica:

```text
@Transactional
@Async
@Cacheable
AOP
security
```

---

# 17. JDK Dynamic Proxy x CGLIB

O Spring AOP utiliza principalmente duas estratégias.

## JDK Dynamic Proxy

Funciona baseado em interfaces.

```text
PaymentService
    ↑
PaymentServiceImpl
```

O proxy implementa:

```text
PaymentService
```

Conceitualmente:

```text
PaymentService
      ↑
    Proxy
      ↓
PaymentServiceImpl
```

---

## CGLIB

CGLIB cria uma subclasse dinamicamente.

```text
PaymentService
      ↑
PaymentService$$SpringCGLIB
```

Isso permite proxy baseado diretamente na classe.

Mas existem limitações:

```text
final class
→ não pode ser subclassed

final method
→ não pode ser overridden

private method
→ não pode ser overridden
```

A documentação atual do Spring AOP descreve JDK Dynamic Proxy para targets baseados em interfaces e CGLIB para proxy baseado em classe, com essas limitações de herança. 

---

# 18. O problema da self-invocation

Esse é um dos pontos mais importantes de Spring em entrevista.

Considere:

```java
@Service
public class PaymentService {

    public void process() {
        save();
    }

    @Transactional
    public void save() {
    }
}
```

A chamada:

```java
save();
```

equivale conceitualmente a:

```java
this.save();
```

E acontece:

```text
Proxy
 ↓
process()
 ↓
PaymentService real
 ↓
this.save()
```

A chamada interna **não voltou para o proxy**.

Então o interceptor de `@Transactional` pode não ser executado.

O fluxo necessário seria:

```text
outro objeto
    ↓
Proxy
    ↓
@Transactional
    ↓
target
```

Isso acontece porque Spring AOP é fundamentalmente **proxy-based**. 

Essa é a origem de vários bugs envolvendo:

```text
@Transactional
@Async
@Cacheable
```

---

# 19. Mapa mental do Spring Core

Para memorizar:

```text
Application Start
      ↓
Configuration Metadata
      ↓
BeanDefinitions
      ↓
BeanFactoryPostProcessor
      ↓
Bean creation
      ↓
Dependency Injection
      ↓
BeanPostProcessor
      ↓
possível Proxy
      ↓
Bean disponível
      ↓
ApplicationContext
```

E:

```text
IoC
 ↓
Spring controla os objetos

DI
 ↓
Spring fornece dependências

ApplicationContext
 ↓
gerencia o container

BeanPostProcessor
 ↓
atua nos objetos

BeanFactoryPostProcessor
 ↓
atua nas definições

AOP
 ↓
adiciona comportamento transversal

Proxy
 ↓
intercepta chamadas

JDK Proxy
 ↓
interfaces

CGLIB
 ↓
subclasses
```

---

# 20. Resposta objetiva para entrevista

Se perguntarem **"Como funciona o Spring Core?"**, uma resposta consistente seria:

> O Spring Core é baseado principalmente em Inversion of Control e Dependency Injection. Em vez de cada classe criar diretamente suas dependências, ela declara aquilo de que precisa e o IoC Container cria, configura e conecta esses objetos.
>
> O principal container é o `ApplicationContext`, que estende `BeanFactory`. O `BeanFactory` fornece a infraestrutura básica de gerenciamento dos beans, enquanto o `ApplicationContext` adiciona recursos como eventos, environment, internacionalização e integração com AOP. 
>
> Os beans podem ser descobertos através de Component Scan, utilizando annotations como `@Service` e `@Repository`, ou registrados explicitamente através de `@Configuration` e `@Bean`. Também posso controlar seu registro com Profiles e `@Conditional`. 
>
> O container também controla o lifecycle dos beans e oferece extension points importantes. `BeanFactoryPostProcessor` atua nas definições antes da criação dos beans, enquanto `BeanPostProcessor` atua sobre as instâncias durante a inicialização e é utilizado internamente por várias funcionalidades do Spring. 
>
> Outro conceito fundamental é AOP. O Spring normalmente implementa AOP através de proxies, usando JDK Dynamic Proxy quando trabalha com interfaces ou CGLIB para proxy baseado em classe. Isso permite implementar recursos como transações, cache e segurança sem misturar essas responsabilidades com a regra de negócio. 
>
> Como Spring AOP é proxy-based, também é importante lembrar que self-invocation pode contornar o proxy. Por isso uma chamada interna para um método `@Transactional`, por exemplo, pode não receber a interceptação esperada.
>
> Então, para mim, entender Spring Core significa entender não apenas annotations, mas principalmente **como o container cria os beans, resolve dependências, aplica lifecycle processors e transforma determinados beans em proxies**.
