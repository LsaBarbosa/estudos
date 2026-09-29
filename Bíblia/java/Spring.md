# Guia de Revisão Spring — Entrevistas

Material consolidado para estudo e suporte em entrevistas sobre o ecossistema Spring.

<a id="sumario-geral"></a>

## Sumário Geral

1. [Spring Core → abrir sumário do módulo](#core-indice)
2. [Spring Boot → abrir sumário do módulo](#boot-indice)
3. [Spring MVC → abrir sumário do módulo](#mvc-indice)
4. [Spring Data → abrir sumário do módulo](#data-indice)
5. [Spring Security → abrir sumário do módulo](#security-indice)
6. [Spring Cloud → abrir sumário do módulo](#cloud-indice)

## Ordem recomendada de estudo

```text
Spring Core
    ↓
Spring Boot
    ↓
Spring MVC
    ↓
Spring Data
    ↓
Spring Security
    ↓
Spring Cloud
```

> Core explica como o container funciona. Boot explica como a aplicação é montada. MVC, Data e Security cobrem os principais stacks de aplicação. Cloud entra depois, quando surgem problemas de sistemas distribuídos.

---

# Spring Core

<a id="core-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#core-visao-geral)
- [2. IoC e Dependency Injection](#core-ioc-di)
  - [2.1 IoC Container](#core-ioc-container)
  - [2.2 Dependency Injection](#core-dependency-injection)
  - [2.3 Constructor Injection](#core-constructor-injection)
  - [2.4 Field vs Setter vs Constructor](#core-tipos-injecao)
- [3. Beans](#core-beans)
  - [3.1 Registro de Beans](#core-registro-beans)
  - [3.2 @Component e Estereótipos](#core-stereotypes)
  - [3.3 @Bean e @Configuration](#core-bean-configuration)
  - [3.4 Bean Scopes](#core-bean-scopes)
  - [3.5 Ciclo de Vida](#core-bean-lifecycle)
- [4. Resolução de Dependências](#core-resolucao-dependencias)
  - [4.1 @Primary](#core-primary)
  - [4.2 @Qualifier](#core-qualifier)
  - [4.3 @Lazy](#core-lazy)
- [5. AOP e Proxies](#core-aop)
  - [5.1 Proxy](#core-proxy)
  - [5.2 Self-invocation](#core-self-invocation)
  - [5.3 Casos de Uso](#core-aop-casos)
- [6. Configuração](#core-configuracao)
  - [6.1 Environment](#core-environment)
  - [6.2 Profiles](#core-profiles)
  - [6.3 Eventos](#core-eventos)
- [7. Armadilhas de Entrevista](#core-armadilhas)
- [8. Cenários de Produção](#core-producao)
- [9. Resposta Completa de Entrevista](#core-resposta-completa)
- [10. Mapa Mental](#core-mapa-mental)

---

<a id="core-visao-geral"></a>
# 1. Visão Geral

Spring Core é a base do ecossistema Spring.

```text
Spring Core
│
├── IoC
├── Dependency Injection
├── Beans
├── Scopes
├── Lifecycle
├── Configuration
├── Events
└── AOP / Proxies
```

O ponto central é o **IoC Container**, responsável por criar, configurar, relacionar e gerenciar objetos da aplicação.

### Resposta de entrevista

> Spring Core fornece o container de IoC e o mecanismo de Dependency Injection. Em vez de as classes criarem diretamente suas dependências, o container gerencia esses objetos como beans e injeta as dependências necessárias. Isso reduz acoplamento e facilita testes, configuração e substituição de implementações.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-ioc-di"></a>
# 2. IoC e Dependency Injection

<a id="core-ioc-container"></a>
## 2.1 IoC Container

**Inversion of Control** significa transferir para o framework o controle sobre criação e gerenciamento dos objetos.

Sem IoC:

```java
public class PedidoService {
    private final PagamentoGateway gateway = new StripeGateway();
}
```

Com IoC:

```java
@Service
public class PedidoService {

    private final PagamentoGateway gateway;

    public PedidoService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

Principais abstrações:

```java
BeanFactory
ApplicationContext
```

Na prática, aplicações Spring usam principalmente `ApplicationContext`.

<a id="core-dependency-injection"></a>
## 2.2 Dependency Injection

Dependency Injection é o mecanismo pelo qual uma dependência é fornecida de fora.

```text
PedidoService
     │
     └── PagamentoGateway
```

Benefícios:

- baixo acoplamento;
- testabilidade;
- substituição de implementação;
- configuração centralizada;
- separação de responsabilidades.

<a id="core-constructor-injection"></a>
## 2.3 Constructor Injection

Forma preferida para dependências obrigatórias.

```java
@Service
public class PedidoService {

    private final PedidoRepository repository;

    public PedidoService(PedidoRepository repository) {
        this.repository = repository;
    }
}
```

Vantagens:

- dependência explícita;
- objeto nasce em estado válido;
- permite campos `final`;
- facilita testes;
- evita dependência oculta.

<a id="core-tipos-injecao"></a>
## 2.4 Field vs Setter vs Constructor

| Tipo | Vantagem | Problema |
|---|---|---|
| Constructor | Dependências explícitas e obrigatórias | Construtor enorme pode indicar excesso de responsabilidades |
| Setter | Útil para dependência opcional | Pode permitir objeto parcialmente configurado |
| Field | Menos código | Oculta dependências e dificulta teste unitário puro |

### Regra prática

> Dependência obrigatória → constructor injection.

### Resposta de entrevista

> IoC é a inversão do controle de criação e gerenciamento dos objetos para o container. Dependency Injection é a forma como o container fornece essas dependências. Prefiro constructor injection porque deixa o contrato explícito e facilita testes.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-beans"></a>
# 3. Beans

Um **bean** é um objeto gerenciado pelo Spring Container.

<a id="core-registro-beans"></a>
## 3.1 Registro de Beans

Pode ocorrer por:

- component scanning;
- métodos `@Bean`;
- auto-configuration;
- configuração programática.

<a id="core-stereotypes"></a>
## 3.2 @Component e Estereótipos

```java
@Component
@Service
@Repository
@Controller
@RestController
```

Intenção semântica:

```text
@Component  → componente genérico
@Service    → serviço / regra de aplicação
@Repository → persistência
@Controller → MVC
@RestController → API REST
```

`@Repository` também participa da tradução de determinadas exceptions de persistência para a hierarquia do Spring.

<a id="core-bean-configuration"></a>
## 3.3 @Bean e @Configuration

Útil quando:

- a classe não é sua;
- criação exige lógica específica;
- configuração explícita é desejável.

```java
@Configuration
public class PaymentConfig {

    @Bean
    public PaymentClient paymentClient() {
        return new PaymentClient();
    }
}
```

<a id="core-bean-scopes"></a>
## 3.4 Bean Scopes

Principais:

```text
singleton
prototype
request
session
application
websocket
```

### Singleton

É o padrão.

```text
1 bean por ApplicationContext
```

> Não significa singleton global da JVM.

### Prototype

Nova instância quando o container resolve o bean.

### Cuidado

Bean singleton não é automaticamente thread-safe. Evite estado mutável compartilhado em services singleton.

<a id="core-bean-lifecycle"></a>
## 3.5 Ciclo de Vida

Fluxo simplificado:

```text
instanciação
   ↓
injeção
   ↓
post-processors
   ↓
@PostConstruct
   ↓
bean disponível
   ↓
@PreDestroy
```

Pontos importantes:

```java
@PostConstruct
@PreDestroy
BeanPostProcessor
```

### Resposta de entrevista

> Bean é um objeto gerenciado pelo Spring. O container cuida de criação, injeção, lifecycle e scope. Por padrão os beans são singleton por ApplicationContext, então evito guardar estado mutável de requisição em services.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-resolucao-dependencias"></a>
# 4. Resolução de Dependências

Quando há múltiplas implementações:

```java
interface Notificador {}

@Component
class EmailNotificador implements Notificador {}

@Component
class SmsNotificador implements Notificador {}
```

<a id="core-primary"></a>
## 4.1 @Primary

```java
@Primary
@Component
public class EmailNotificador implements Notificador {
}
```

Define uma implementação preferencial.

<a id="core-qualifier"></a>
## 4.2 @Qualifier

```java
public PedidoService(
    @Qualifier("smsNotificador") Notificador notificador
) {
    this.notificador = notificador;
}
```

Seleciona explicitamente um bean.

<a id="core-lazy"></a>
## 4.3 @Lazy

Posterga a criação até o bean ser necessário.

### Trade-off

Pode reduzir trabalho no startup, mas também deslocar erros de configuração para o primeiro uso.

### Resposta de entrevista

> Quando existem múltiplos beans do mesmo tipo, uso `@Primary` para definir o padrão ou `@Qualifier` para selecionar explicitamente. `@Lazy` posterga inicialização, mas deve ser usado com critério.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-aop"></a>
# 5. AOP e Proxies

AOP permite aplicar comportamento transversal.

Exemplos:

```text
transação
segurança
logging
métricas
cache
```

<a id="core-proxy"></a>
## 5.1 Proxy

Vários recursos Spring funcionam por proxy.

```text
caller
   ↓
Spring Proxy
   ↓
bean real
```

Exemplos:

```java
@Transactional
@Cacheable
@Async
@PreAuthorize
```

<a id="core-self-invocation"></a>
## 5.2 Self-invocation

```java
public void executar() {
    salvar();
}

@Transactional
public void salvar() {
}
```

A chamada interna pode não atravessar o proxy.

```text
self-call
   ↓
objeto real
   X
proxy
```

Consequência:

> Anotações baseadas em proxy podem não surtir efeito como esperado.

<a id="core-aop-casos"></a>
## 5.3 Casos de Uso

AOP é adequado para cross-cutting concerns.

### Trade-off

- reduz repetição;
- centraliza comportamento transversal;
- adiciona comportamento implícito;
- pode dificultar debugging se usado excessivamente.

### Resposta de entrevista

> Spring usa proxies em recursos como transações, cache e segurança. O proxy intercepta chamadas ao bean e aplica comportamento adicional. Um cuidado clássico é self-invocation, porque chamadas internas podem não passar pelo proxy.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-configuracao"></a>
# 6. Configuração

<a id="core-environment"></a>
## 6.1 Environment

Fontes comuns:

```text
application.properties
application.yml
variáveis de ambiente
system properties
command line
config server
```

Use configuração externa para valores que variam entre ambientes.

<a id="core-profiles"></a>
## 6.2 Profiles

```java
@Profile("dev")
@Bean
DataSource devDataSource() {
    ...
}
```

Exemplos:

```text
dev
test
staging
prod
```

Evite profiles demais, porque combinações complexas dificultam previsibilidade.

<a id="core-eventos"></a>
## 6.3 Eventos

```java
ApplicationEventPublisher
@EventListener
```

Exemplo:

```java
publisher.publishEvent(new PedidoCriadoEvent(id));
```

### Atenção

Evento Spring local:

```text
mesmo processo
```

não substitui Kafka/RabbitMQ/SQS em integração distribuída.

### Resposta de entrevista

> Spring permite externalizar configuração, ativar configurações por profiles e publicar eventos locais. Eu diferencio eventos dentro do processo de mensageria distribuída.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-armadilhas"></a>
# 7. Armadilhas de Entrevista

- Construtor único normalmente não exige `@Autowired`.
- Bean singleton **não** é automaticamente thread-safe.
- `@Service` e `@Component` registram componentes; a principal diferença é semântica.
- `@Transactional` pode falhar em self-invocation.
- O Spring injeta objetos que fazem parte do contexto, isto é, beans conhecidos pelo container.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-producao"></a>
# 8. Cenários de Produção

## Estado mutável em singleton

Evite:

```java
@Service
public class CheckoutService {
    private String currentUser;
}
```

Dados de request não devem ficar em campos mutáveis compartilhados de um singleton.

## Dependência circular

```text
ServiceA → ServiceB → ServiceA
```

Geralmente indica acoplamento excessivo.

## Anotação não funciona

Verifique:

- bean realmente está no container?
- chamada passa pelo proxy?
- self-invocation?
- configuração condicional?
- bean correto foi injetado?

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-resposta-completa"></a>
# 9. Resposta Completa de Entrevista

> Spring Core é a base do ecossistema e fornece IoC, Dependency Injection e gerenciamento de beans. O container cria componentes, resolve dependências e controla lifecycle e scopes.
>
> Prefiro constructor injection para dependências obrigatórias porque deixa o contrato explícito, facilita testes e permite objetos consistentes. Beans são singleton por ApplicationContext por padrão, então evito estado mutável compartilhado.
>
> Também é importante entender proxies, porque vários recursos como `@Transactional`, cache e segurança dependem de interceptação. Isso explica problemas clássicos de self-invocation.
>
> Para configuração, uso propriedades externas e profiles com moderação, mantendo a aplicação previsível.

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="core-mapa-mental"></a>
# 10. Mapa Mental

```text
SPRING CORE
│
├── IoC
├── DI
│   └── constructor injection
├── Beans
│   ├── @Component
│   ├── @Service
│   ├── @Repository
│   └── @Bean
├── Scopes
├── Lifecycle
├── @Primary / @Qualifier
├── AOP / Proxy
│   └── self-invocation
└── Config
    ├── Environment
    ├── Profiles
    └── Events
```

[↑ Sumário do módulo](#core-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Spring Boot

<a id="boot-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#boot-visao-geral)
- [2. Auto-configuration](#boot-auto-configuration)
  - [2.1 Conditions](#boot-conditions)
  - [2.2 Back-off](#boot-backoff)
- [3. Starters e Dependency Management](#boot-starters)
- [4. @SpringBootApplication](#boot-springbootapplication)
- [5. Configuração Externa](#boot-external-config)
  - [5.1 application.yml/properties](#boot-application-config)
  - [5.2 @ConfigurationProperties](#boot-configuration-properties)
  - [5.3 Profiles](#boot-profiles)
- [6. Embedded Server](#boot-embedded-server)
- [7. Actuator](#boot-actuator)
  - [7.1 Health](#boot-health)
  - [7.2 Metrics](#boot-metrics)
  - [7.3 Segurança dos Endpoints](#boot-actuator-security)
- [8. Startup e Diagnóstico](#boot-startup)
- [9. Armadilhas de Entrevista](#boot-armadilhas)
- [10. Produção](#boot-producao)
- [11. Resposta Completa de Entrevista](#boot-resposta-completa)
- [12. Mapa Mental](#boot-mapa-mental)

---

<a id="boot-visao-geral"></a>
# 1. Visão Geral

Spring Boot simplifica criação e operação de aplicações Spring.

```text
Spring Boot
│
├── auto-configuration
├── starters
├── dependency management
├── embedded server
├── externalized configuration
├── actuator
└── conventions
```

```text
Spring Framework
→ fornece mecanismos

Spring Boot
→ automatiza e padroniza configuração desses mecanismos
```

### Resposta de entrevista

> Spring Boot não substitui o Spring Framework. Ele reduz configuração manual através de auto-configuration, starters, servidor embarcado, configuração externa e ferramentas operacionais como Actuator.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-auto-configuration"></a>
# 2. Auto-configuration

Auto-configuration cria configurações padrão com base em classpath, propriedades e beans existentes.

```text
dependência JDBC presente
      +
propriedades presentes
      +
DataSource customizado ausente
      ↓
Boot pode configurar DataSource
```

<a id="boot-conditions"></a>
## 2.1 Conditions

Condições comuns:

```java
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnProperty
```

<a id="boot-backoff"></a>
## 2.2 Back-off

Quando o desenvolvedor fornece configuração própria, várias auto-configurações recuam.

```text
default Boot
    ↓
bean customizado encontrado
    ↓
back-off
```

### Resposta de entrevista

> Auto-configuration é condicional. O Boot observa classpath, propriedades e beans existentes e registra defaults quando fazem sentido. Se eu forneço uma configuração própria, muitas auto-configurações fazem back-off.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-starters"></a>
# 3. Starters e Dependency Management

Exemplos:

```text
spring-boot-starter-web
spring-boot-starter-data-jpa
spring-boot-starter-security
spring-boot-starter-actuator
```

Benefícios:

- reduz seleção manual;
- combina dependências compatíveis;
- acelera bootstrap.

### Trade-off

Starter pode incluir dependências que não são usadas.

Para footprint e segurança:

> revise a árvore de dependências.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-springbootapplication"></a>
# 4. @SpringBootApplication

Conceitualmente combina:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

A posição da classe influencia component scanning.

### Regra prática

Coloque a classe principal em um package raiz da aplicação.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-external-config"></a>
# 5. Configuração Externa

<a id="boot-application-config"></a>
## 5.1 application.yml/properties

```yaml
server:
  port: 8080

app:
  payment:
    timeout: 2s
```

Não armazene secrets diretamente no repositório.

<a id="boot-configuration-properties"></a>
## 5.2 @ConfigurationProperties

Bom para grupos de propriedades:

```java
@ConfigurationProperties(prefix = "app.payment")
public record PaymentProperties(
    Duration timeout,
    URI baseUrl
) {}
```

Vantagens:

- tipagem;
- validação;
- organização;
- menos strings espalhadas.

<a id="boot-profiles"></a>
## 5.3 Profiles

```text
application.yml
application-dev.yml
application-prod.yml
```

Use profiles para diferenças ambientais, não para regras de negócio.

### Resposta de entrevista

> Para configuração externa, prefiro `@ConfigurationProperties` quando existe um grupo de propriedades. Isso melhora tipagem, validação e manutenção em comparação com espalhar `@Value`.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-embedded-server"></a>
# 6. Embedded Server

Aplicações web Boot normalmente usam servidor embarcado.

```text
jar
├── aplicação
├── dependências
└── servidor HTTP
```

Execução:

```bash
java -jar app.jar
```

Benefícios:

- deploy simples;
- configuração consistente;
- bom encaixe em containers.

### Resposta de entrevista

> O servidor embarcado permite empacotar aplicação e servidor juntos, simplificando deploy e operação.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-actuator"></a>
# 7. Actuator

Endpoints operacionais possíveis:

```text
health
metrics
info
loggers
prometheus
env
beans
```

<a id="boot-health"></a>
## 7.1 Health

Em orquestração:

```text
liveness  → processo deve ser reiniciado?
readiness → pode receber tráfego?
```

<a id="boot-metrics"></a>
## 7.2 Metrics

Integração com Micrometer.

Métricas relevantes:

- HTTP latency;
- request count;
- JVM memory;
- GC;
- threads;
- connection pools;
- métricas de negócio.

<a id="boot-actuator-security"></a>
## 7.3 Segurança dos Endpoints

Não exponha indiscriminadamente:

```text
/env
/beans
/configprops
```

### Resposta de entrevista

> Actuator fornece endpoints operacionais e integração com métricas. Em produção eu exponho somente o necessário, protejo endpoints sensíveis e uso health/readiness/liveness e métricas para observabilidade.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-startup"></a>
# 8. Startup e Diagnóstico

Quando auto-configuração surpreende, investigue:

- condition evaluation report;
- logs de startup;
- beans registrados;
- propriedades efetivas;
- classpath.

Perguntas:

```text
Por que esse bean existe?
Por que essa auto-configuração não entrou?
```

### Resposta de entrevista

> Para diagnosticar auto-configuration eu verifico condições, classpath, propriedades e beans existentes. O comportamento normalmente deriva de uma condition satisfeita ou não satisfeita.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-armadilhas"></a>
# 9. Armadilhas de Entrevista

- Spring Boot não é um servidor; ele pode configurar um servidor embarcado.
- Auto-configuration é customizável e condicional.
- `@ConfigurationProperties` costuma ser melhor que `@Value` para propriedades agrupadas.
- Actuator não deve ficar todo exposto publicamente.
- Starter não significa "zero configuração".

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-producao"></a>
# 10. Produção

Considere:

```text
configuração externa
secrets
graceful shutdown
readiness
liveness
métricas
tracing
logs estruturados
pool de conexões
limites de CPU/memória
```

Evite trabalho pesado em `@PostConstruct`.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-resposta-completa"></a>
# 11. Resposta Completa de Entrevista

> Spring Boot simplifica aplicações Spring através de auto-configuration, starters, dependency management e convenções. A auto-configuração é condicional e leva em conta classpath, propriedades e beans existentes, fazendo back-off quando forneço configuração própria.
>
> Para propriedades estruturadas, prefiro `@ConfigurationProperties`. Em aplicações web, o Boot também simplifica o servidor embarcado.
>
> Em produção, Actuator e Micrometer são fundamentais para health, métricas e observabilidade. Também protejo endpoints sensíveis e configuro readiness e liveness corretamente.

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="boot-mapa-mental"></a>
# 12. Mapa Mental

```text
SPRING BOOT
│
├── Auto-config
│   ├── conditions
│   └── back-off
├── Starters
├── Dependency Management
├── @SpringBootApplication
├── Config
│   ├── yml/properties
│   ├── @ConfigurationProperties
│   └── profiles
├── Embedded Server
└── Actuator
    ├── health
    ├── metrics
    └── security
```

[↑ Sumário do módulo](#boot-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Spring MVC

<a id="mvc-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#mvc-visao-geral)
- [2. Fluxo da Requisição](#mvc-fluxo)
  - [2.1 DispatcherServlet](#mvc-dispatcher-servlet)
  - [2.2 HandlerMapping](#mvc-handler-mapping)
  - [2.3 HandlerAdapter](#mvc-handler-adapter)
  - [2.4 HttpMessageConverter](#mvc-message-converter)
- [3. Controllers REST](#mvc-controllers)
  - [3.1 @RestController](#mvc-restcontroller)
  - [3.2 Mapping](#mvc-mapping)
  - [3.3 Request Body e Response](#mvc-body-response)
- [4. Validação](#mvc-validacao)
- [5. Tratamento de Erros](#mvc-erros)
  - [5.1 @ExceptionHandler](#mvc-exception-handler)
  - [5.2 @ControllerAdvice](#mvc-controller-advice)
- [6. Filter vs Interceptor](#mvc-filter-interceptor)
- [7. Async e Streaming](#mvc-async)
- [8. MVC vs WebFlux](#mvc-mvc-vs-webflux)
- [9. Armadilhas de Entrevista](#mvc-armadilhas)
- [10. Produção](#mvc-producao)
- [11. Resposta Completa de Entrevista](#mvc-resposta-completa)
- [12. Mapa Mental](#mvc-mapa-mental)

---

<a id="mvc-visao-geral"></a>
# 1. Visão Geral

Spring MVC é o stack web tradicional baseado em Servlet.

```text
HTTP Request
    ↓
DispatcherServlet
    ↓
Controller
    ↓
Service
    ↓
Response
```

### Resposta de entrevista

> Spring MVC é o framework web baseado em Servlet do Spring. Ele usa o DispatcherServlet como front controller, resolve o handler, executa o controller e converte request e response através de message converters.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-fluxo"></a>
# 2. Fluxo da Requisição

<a id="mvc-dispatcher-servlet"></a>
## 2.1 DispatcherServlet

É o **Front Controller** do MVC.

```text
Client
  ↓
DispatcherServlet
  ↓
infraestrutura MVC
```

<a id="mvc-handler-mapping"></a>
## 2.2 HandlerMapping

Identifica qual controller/método atende a rota.

```java
@GetMapping("/clientes/{id}")
```

<a id="mvc-handler-adapter"></a>
## 2.3 HandlerAdapter

Adapta e executa o handler encontrado.

<a id="mvc-message-converter"></a>
## 2.4 HttpMessageConverter

Converte:

```text
JSON → Java Object
Java Object → JSON
```

Exemplo típico com Jackson.

### Resposta de entrevista

> O DispatcherServlet recebe a requisição, usa HandlerMapping para encontrar o controller e HandlerAdapter para executá-lo. Os HttpMessageConverters fazem a conversão entre o corpo HTTP e objetos Java.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-controllers"></a>
# 3. Controllers REST

<a id="mvc-restcontroller"></a>
## 3.1 @RestController

Conceitualmente:

```text
@Controller + @ResponseBody
```

```java
@RestController
@RequestMapping("/pedidos")
public class PedidoController {
}
```

<a id="mvc-mapping"></a>
## 3.2 Mapping

```java
@GetMapping
@PostMapping
@PutMapping
@PatchMapping
@DeleteMapping
```

Bindings:

```java
@PathVariable
@RequestParam
@RequestHeader
```

<a id="mvc-body-response"></a>
## 3.3 Request Body e Response

```java
@PostMapping
public ResponseEntity<PedidoResponse> criar(
    @Valid @RequestBody CriarPedidoRequest request
) {
    ...
}
```

### Regra prática

Controller fino:

```text
HTTP
 ↓
Controller
 ↓
Application/Domain Service
```

### Resposta de entrevista

> Controller deve lidar principalmente com rota, input, validação, status HTTP e output. Regra de negócio relevante fica em serviços ou no domínio.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-validacao"></a>
# 4. Validação

```java
public record CriarClienteRequest(
    @NotBlank String nome,
    @Email String email
) {}
```

Uso:

```java
@Valid @RequestBody CriarClienteRequest request
```

Diferencie:

```text
validação estrutural
→ null, formato, tamanho

validação de negócio
→ invariantes do domínio
```

### Resposta de entrevista

> Uso Bean Validation para regras estruturais do input. Regras de negócio permanecem na camada responsável pelo domínio.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-erros"></a>
# 5. Tratamento de Erros

<a id="mvc-exception-handler"></a>
## 5.1 @ExceptionHandler

Trata exception específica.

<a id="mvc-controller-advice"></a>
## 5.2 @ControllerAdvice

Centraliza mapeamento de exceptions.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
}
```

Benefícios:

- contrato de erro consistente;
- controllers mais simples;
- centralização.

Não exponha stack traces ou detalhes internos.

### Resposta de entrevista

> Centralizo tratamento HTTP de exceptions com `@RestControllerAdvice`, transformando exceptions de aplicação em respostas consistentes sem vazar detalhes internos.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-filter-interceptor"></a>
# 6. Filter vs Interceptor

| Filter | Interceptor |
|---|---|
| Servlet level | Spring MVC level |
| Atua antes/durante pipeline HTTP | Atua ao redor do handler |
| Não precisa conhecer controller | Conhece handler selecionado |
| Bom para concerns gerais | Bom para concerns MVC |

### Filter

Bom para:

- headers;
- correlation id;
- wrappers;
- logging HTTP.

### Interceptor

Métodos:

```java
preHandle()
postHandle()
afterCompletion()
```

### Resposta de entrevista

> Filter atua no nível Servlet e é adequado para preocupações HTTP gerais. Interceptor atua dentro do Spring MVC e consegue trabalhar com o handler selecionado.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-async"></a>
# 7. Async e Streaming

Spring MVC suporta:

```java
Callable
DeferredResult
StreamingResponseBody
```

Mas continua baseado em Servlet.

`@Async` não transforma automaticamente a arquitetura em reativa.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-mvc-vs-webflux"></a>
# 8. MVC vs WebFlux

| MVC | WebFlux |
|---|---|
| Servlet | Reactive |
| Modelo imperativo | Modelo reativo |
| Ótimo com JDBC/JPA | Melhor com stack não bloqueante |
| Mais simples para stack bloqueante | Mais complexo |

### Regra prática

Se a aplicação usa JPA/JDBC bloqueante de ponta a ponta, MVC costuma ser escolha natural.

### Resposta de entrevista

> Escolho MVC ou WebFlux com base no stack inteiro. Se minhas dependências são bloqueantes, MVC costuma ser mais simples e coerente. WebFlux faz mais sentido quando o fluxo é realmente não bloqueante de ponta a ponta.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-armadilhas"></a>
# 9. Armadilhas de Entrevista

- `@RestController` não é exatamente igual a `@Controller`.
- Filter e Interceptor atuam em níveis diferentes.
- `@Valid` não substitui regras de negócio.
- MVC pode ter async, mas não vira WebFlux por isso.
- Controller não deveria concentrar regra de negócio.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-producao"></a>
# 10. Produção

Observe:

- latência por endpoint;
- p95/p99;
- taxa de erro;
- payload limits;
- timeouts;
- correlation id;
- status HTTP;
- paginação;
- contrato de erro.

Evite vazar detalhes internos.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-resposta-completa"></a>
# 11. Resposta Completa de Entrevista

> Spring MVC é o stack web baseado em Servlet. O DispatcherServlet funciona como front controller, resolve o handler, executa o controller e usa message converters para transformar JSON em objetos Java e vice-versa.
>
> Nos controllers, concentro preocupações HTTP e delego regra de negócio. Uso Bean Validation para validações estruturais e `@RestControllerAdvice` para padronizar erros.
>
> Também diferencio Filter, que atua no nível Servlet, de Interceptor, que atua no pipeline MVC. Para escolher MVC ou WebFlux, considero se o stack é bloqueante ou realmente reativo de ponta a ponta.

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="mvc-mapa-mental"></a>
# 12. Mapa Mental

```text
SPRING MVC
│
├── DispatcherServlet
│   ├── HandlerMapping
│   ├── HandlerAdapter
│   └── MessageConverter
├── Controller
│   ├── mappings
│   └── ResponseEntity
├── Validation
├── Error Handling
│   └── Advice
├── Filter
├── Interceptor
└── MVC vs WebFlux
```

[↑ Sumário do módulo](#mvc-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Spring Data

<a id="data-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#data-visao-geral)
- [2. Spring Data vs JPA vs Hibernate](#data-spring-data-jpa-hibernate)
- [3. Repositories](#data-repositories)
  - [3.1 CrudRepository](#data-crudrepository)
  - [3.2 JpaRepository](#data-jparepository)
  - [3.3 Derived Queries](#data-derived-queries)
  - [3.4 @Query](#data-query)
- [4. Persistence Context](#data-persistence-context)
  - [4.1 EntityManager](#data-entity-manager)
  - [4.2 Estados da Entidade](#data-entity-states)
  - [4.3 Dirty Checking](#data-dirty-checking)
- [5. Transações](#data-transactions)
  - [5.1 @Transactional](#data-transactional)
  - [5.2 Propagation](#data-propagation)
  - [5.3 Isolation](#data-isolation)
  - [5.4 Rollback](#data-rollback)
- [6. Fetching e N+1](#data-fetching)
  - [6.1 Lazy vs Eager](#data-lazy-eager)
  - [6.2 N+1](#data-n-plus-one)
  - [6.3 EntityGraph e Fetch Join](#data-fetch-solutions)
- [7. Paginação e Ordenação](#data-pagination)
- [8. Locking](#data-locking)
  - [8.1 Optimistic Lock](#data-optimistic)
  - [8.2 Pessimistic Lock](#data-pessimistic)
- [9. Projections, Specifications e Auditing](#data-advanced)
- [10. Armadilhas de Entrevista](#data-armadilhas)
- [11. Produção](#data-producao)
- [12. Resposta Completa de Entrevista](#data-resposta-completa)
- [13. Mapa Mental](#data-mapa-mental)

---

<a id="data-visao-geral"></a>
# 1. Visão Geral

No contexto relacional:

```text
Spring Data JPA
      ↓
JPA
      ↓
Hibernate / outro provider
      ↓
JDBC
      ↓
Database
```

### Resposta de entrevista

> Spring Data JPA fornece abstrações de repository sobre JPA, reduzindo boilerplate. JPA é a especificação de persistência e Hibernate é uma implementação comum dessa especificação.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-spring-data-jpa-hibernate"></a>
# 2. Spring Data vs JPA vs Hibernate

```text
Spring Data JPA
→ repository abstraction

JPA
→ especificação / API

Hibernate
→ provider ORM / implementação
```

### Resposta de entrevista

> JPA define o contrato de ORM, Hibernate implementa esse contrato e Spring Data JPA adiciona repositories e integração com Spring.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-repositories"></a>
# 3. Repositories

<a id="data-crudrepository"></a>
## 3.1 CrudRepository

Operações básicas:

```java
save()
findById()
findAll()
delete()
existsById()
```

<a id="data-jparepository"></a>
## 3.2 JpaRepository

```java
public interface ClienteRepository
        extends JpaRepository<Cliente, Long> {
}
```

Adiciona capacidades úteis no contexto JPA.

<a id="data-derived-queries"></a>
## 3.3 Derived Queries

```java
findByEmail(String email)
findByStatusAndCreatedAtAfter(...)
existsByCpf(...)
```

### Trade-off

Bom para consultas simples. Nomes enormes indicam que outra abordagem pode ser melhor.

<a id="data-query"></a>
## 3.4 @Query

```java
@Query("select c from Cliente c where c.status = :status")
List<Cliente> buscarAtivos(Status status);
```

Pode usar JPQL ou native query conforme necessidade.

### Resposta de entrevista

> Repositories reduzem boilerplate. Derived queries funcionam bem para casos simples; para consultas mais complexas uso `@Query`, Specifications ou queries dedicadas.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-persistence-context"></a>
# 4. Persistence Context

<a id="data-entity-manager"></a>
## 4.1 EntityManager

Operações:

```java
persist()
find()
merge()
remove()
flush()
```

Mesmo usando repository, entender `EntityManager` é importante para compreender JPA.

<a id="data-entity-states"></a>
## 4.2 Estados da Entidade

```text
Transient
Managed
Detached
Removed
```

<a id="data-dirty-checking"></a>
## 4.3 Dirty Checking

```java
@Transactional
public void alterarNome(Long id, String nome) {
    Cliente cliente = repository.findById(id).orElseThrow();
    cliente.alterarNome(nome);
}
```

Se a entidade está managed, mudanças podem ser detectadas e sincronizadas no flush/commit sem `save()` explícito.

### Resposta de entrevista

> O persistence context gerencia entidades. Quando uma entidade está managed, o provider pode detectar alterações por dirty checking e sincronizá-las no flush ou commit.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-transactions"></a>
# 5. Transações

<a id="data-transactional"></a>
## 5.1 @Transactional

```java
@Transactional
public void confirmarPedido(Long id) {
    ...
}
```

Normalmente aplicada via proxy.

Self-invocation é uma armadilha importante.

<a id="data-propagation"></a>
## 5.2 Propagation

Principais:

```text
REQUIRED
REQUIRES_NEW
SUPPORTS
MANDATORY
NOT_SUPPORTED
NEVER
NESTED
```

### REQUIRED

Usa existente ou cria nova.

### REQUIRES_NEW

Suspende a atual e cria transação independente.

Trade-off:

- mais conexões;
- consistência mais complexa;
- commit independente.

<a id="data-isolation"></a>
## 5.3 Isolation

Fenômenos:

```text
dirty read
non-repeatable read
phantom read
```

Níveis:

```text
READ_UNCOMMITTED
READ_COMMITTED
REPEATABLE_READ
SERIALIZABLE
```

Comportamento real depende do banco.

<a id="data-rollback"></a>
## 5.4 Rollback

Por padrão, rollback automático é associado principalmente a unchecked exceptions.

Quando necessário:

```java
@Transactional(rollbackFor = MinhaCheckedException.class)
```

### Resposta de entrevista

> `@Transactional` define o boundary transacional e normalmente funciona via proxy. Também considero propagation, isolation e rollback. Evito `REQUIRES_NEW` sem necessidade porque cria transação independente e pode aumentar uso do pool.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-fetching"></a>
# 6. Fetching e N+1

<a id="data-lazy-eager"></a>
## 6.1 Lazy vs Eager

```text
LAZY  → carregar quando necessário
EAGER → carregar automaticamente
```

EAGER não garante "uma query apenas".

<a id="data-n-plus-one"></a>
## 6.2 N+1

```text
1 query → pedidos
N queries → itens de cada pedido
```

Sintomas:

- muitas queries;
- latência;
- carga no banco.

<a id="data-fetch-solutions"></a>
## 6.3 EntityGraph e Fetch Join

```java
@EntityGraph(attributePaths = "itens")
```

ou:

```text
join fetch
```

Outras estratégias:

- projection;
- query específica;
- batch fetching.

### Cuidado

Fetch join de coleção com paginação exige atenção.

### Resposta de entrevista

> N+1 ocorre quando uma query principal dispara várias queries adicionais para relacionamentos. Detecto por logs/APM e resolvo conforme o caso com fetch join, EntityGraph, projection ou query dedicada.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-pagination"></a>
# 7. Paginação e Ordenação

```java
Pageable
Page<T>
Slice<T>
```

### Page

Normalmente inclui total, o que pode exigir `COUNT`.

### Slice

Indica próxima página sem necessariamente calcular total.

### Resposta de entrevista

> Uso `Page` quando preciso do total e `Slice` quando basta saber se existe próxima página, evitando count caro em alguns cenários.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-locking"></a>
# 8. Locking

<a id="data-optimistic"></a>
## 8.1 Optimistic Lock

```java
@Version
private Long version;
```

```text
Tx A lê v1
Tx B lê v1
Tx A atualiza → v2
Tx B tenta atualizar v1 → conflito
```

Bom quando conflitos são raros.

<a id="data-pessimistic"></a>
## 8.2 Pessimistic Lock

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
```

Trade-offs:

- bloqueio;
- espera;
- risco de deadlock;
- menor throughput sob contenção.

### Resposta de entrevista

> Optimistic locking detecta conflito por versão e funciona bem quando conflitos são raros. Pessimistic locking bloqueia no banco e pode ser necessário quando o custo do conflito é alto, mas aumenta contenção.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-advanced"></a>
# 9. Projections, Specifications e Auditing

## Projection

Carrega apenas dados necessários.

## Specification

Ajuda a compor filtros dinâmicos.

## Auditing

```java
@CreatedDate
@LastModifiedDate
@CreatedBy
@LastModifiedBy
```

### Resposta de entrevista

> Projections reduzem dados carregados, Specifications ajudam em filtros dinâmicos e auditing automatiza metadados de criação e alteração.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-armadilhas"></a>
# 10. Armadilhas de Entrevista

- `save()` não significa necessariamente INSERT.
- EAGER não resolve N+1 automaticamente.
- `@Transactional` pode não funcionar em self-call.
- ORM não elimina necessidade de SQL, índices, locks e planos de execução.
- Entidade JPA não precisa ser o contrato HTTP da API.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-producao"></a>
# 11. Produção

Monitore:

```text
query latency
slow queries
pool de conexões
N+1
locks
deadlocks
transaction time
rows retornadas
p95/p99
```

Evite:

- transações longas;
- `findAll()` em tabelas grandes;
- graphs gigantes;
- EAGER por conveniência;
- queries sem índices adequados.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-resposta-completa"></a>
# 12. Resposta Completa de Entrevista

> Spring Data JPA reduz boilerplate de acesso a dados através de repositories. Ele fica sobre JPA, que é a especificação, enquanto Hibernate é uma implementação comum.
>
> Para trabalhar bem com JPA, é importante entender persistence context, estados da entidade, dirty checking e transações. Também presto atenção ao fetching, porque N+1 pode degradar fortemente a aplicação.
>
> Em concorrência de dados, escolho optimistic ou pessimistic locking conforme frequência e custo do conflito. Em produção, acompanho queries, índices, pool de conexões e tempo de transação, porque ORM não elimina a necessidade de entender o banco.

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="data-mapa-mental"></a>
# 13. Mapa Mental

```text
SPRING DATA
│
├── Repositories
├── JPA / Hibernate
│   ├── EntityManager
│   ├── Persistence Context
│   └── Dirty Checking
├── Transactions
│   ├── Propagation
│   ├── Isolation
│   └── Rollback
├── Fetching
│   ├── LAZY / EAGER
│   └── N+1
├── Pagination
├── Projection / Specification
└── Locking
    ├── Optimistic
    └── Pessimistic
```

[↑ Sumário do módulo](#data-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Spring Security

<a id="security-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#security-visao-geral)
- [2. Authentication vs Authorization](#security-authn-authz)
- [3. Security Filter Chain](#security-filter-chain)
- [4. Authentication Flow](#security-authentication-flow)
  - [4.1 SecurityContext](#security-security-context)
  - [4.2 AuthenticationManager](#security-authentication-manager)
  - [4.3 AuthenticationProvider](#security-authentication-provider)
  - [4.4 UserDetailsService](#security-userdetails-service)
- [5. Passwords](#security-passwords)
- [6. Authorization](#security-authorization)
  - [6.1 URL Rules](#security-url-rules)
  - [6.2 Method Security](#security-method-security)
- [7. CSRF e CORS](#security-csrf-cors)
  - [7.1 CSRF](#security-csrf)
  - [7.2 CORS](#security-cors)
- [8. Session vs Stateless](#security-session-stateless)
- [9. JWT e OAuth2 Resource Server](#security-jwt-oauth2)
- [10. Armadilhas de Entrevista](#security-armadilhas)
- [11. Produção](#security-producao)
- [12. Resposta Completa de Entrevista](#security-resposta-completa)
- [13. Mapa Mental](#security-mapa-mental)

---

<a id="security-visao-geral"></a>
# 1. Visão Geral

Fluxo simplificado:

```text
HTTP Request
    ↓
Security Filter Chain
    ↓
Authentication
    ↓
SecurityContext
    ↓
Authorization
    ↓
Controller
```

### Resposta de entrevista

> Spring Security atua principalmente através de uma cadeia de filtros antes de a requisição chegar ao controller. Ele estabelece a identidade autenticada no SecurityContext e depois aplica regras de autorização.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-authn-authz"></a>
# 2. Authentication vs Authorization

```text
Authentication
→ Quem é você?

Authorization
→ O que você pode fazer?
```

Exemplos de autorização:

```text
ROLE_ADMIN
SCOPE_orders.read
permissão específica
```

### Resposta de entrevista

> Authentication valida identidade. Authorization decide se essa identidade pode acessar determinado recurso ou executar determinada operação.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-filter-chain"></a>
# 3. Security Filter Chain

Configuração moderna:

```java
@Bean
SecurityFilterChain security(HttpSecurity http) throws Exception {
    return http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
            .anyRequest().authenticated()
        )
        .build();
}
```

A cadeia pode envolver:

```text
CORS
CSRF
authentication
authorization
exception handling
security context
session
```

### Resposta de entrevista

> Spring Security é filter-based no stack Servlet. A `SecurityFilterChain` define mecanismos e regras aplicados antes de a requisição alcançar o MVC.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-authentication-flow"></a>
# 4. Authentication Flow

<a id="security-security-context"></a>
## 4.1 SecurityContext

Contém a `Authentication` corrente.

Normalmente:

- principal;
- authorities;
- estado de autenticação;
- credentials quando aplicável.

<a id="security-authentication-manager"></a>
## 4.2 AuthenticationManager

Coordena autenticação.

```text
Authentication
     ↓
AuthenticationManager
     ↓
AuthenticationProvider
```

<a id="security-authentication-provider"></a>
## 4.3 AuthenticationProvider

Implementa uma estratégia específica.

Exemplos:

- username/password;
- LDAP;
- mecanismo customizado.

<a id="security-userdetails-service"></a>
## 4.4 UserDetailsService

Em fluxo tradicional:

```java
UserDetails loadUserByUsername(String username)
```

### Resposta de entrevista

> O AuthenticationManager coordena o processo e delega a AuthenticationProviders. Em autenticação tradicional, o provider pode usar UserDetailsService para carregar o usuário e PasswordEncoder para validar a senha.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-passwords"></a>
# 5. Passwords

Nunca armazene senha em texto puro.

Use:

```java
PasswordEncoder
```

Exemplo:

```java
BCryptPasswordEncoder
```

Validação:

```java
passwordEncoder.matches(raw, encoded)
```

### Resposta de entrevista

> Senhas devem ser armazenadas com password hashing apropriado, nunca reversível ou em texto puro. Spring abstrai isso com PasswordEncoder.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-authorization"></a>
# 6. Authorization

<a id="security-url-rules"></a>
## 6.1 URL Rules

```java
.requestMatchers("/admin/**").hasRole("ADMIN")
.requestMatchers("/orders/**").hasAuthority("SCOPE_orders.read")
```

<a id="security-method-security"></a>
## 6.2 Method Security

```java
@PreAuthorize("hasRole('ADMIN')")
```

Pode complementar regras HTTP.

### Regra

Segurança apenas no frontend:

```text
não é segurança
```

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-csrf-cors"></a>
# 7. CSRF e CORS

<a id="security-csrf"></a>
## 7.1 CSRF

Explora credenciais enviadas automaticamente pelo browser, como cookie de sessão.

Pergunte:

```text
como a credencial é transportada?
o browser a envia automaticamente?
```

Não desabilite CSRF mecanicamente apenas porque a aplicação é REST.

<a id="security-cors"></a>
## 7.2 CORS

Controla requisições cross-origin no navegador.

Configura:

- origins;
- methods;
- headers;
- credentials.

CORS não é autenticação.

### Resposta de entrevista

> CSRF protege contra requisições forjadas quando credenciais são enviadas automaticamente pelo browser. CORS é uma política de browser para acesso entre origens e não substitui autenticação ou autorização.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-session-stateless"></a>
# 8. Session vs Stateless

## Stateful

```text
sessão no servidor
+
session id no cliente
```

## Stateless

Cada request carrega credencial suficiente.

```text
Bearer Token
```

Em API stateless:

```java
SessionCreationPolicy.STATELESS
```

pode ser apropriado.

### Trade-off

Stateless facilita escala, mas revogação e controle de tokens podem ficar mais complexos.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-jwt-oauth2"></a>
# 9. JWT e OAuth2 Resource Server

```text
JWT
→ formato de token

OAuth2
→ framework/protocolo de autorização

OpenID Connect
→ identidade sobre OAuth2
```

No Resource Server:

```text
Bearer Token
    ↓
validar assinatura + claims
    ↓
Authentication
    ↓
authorities/scopes
```

Valide conforme contexto:

- issuer;
- audience;
- expiration;
- not-before;
- scopes/roles.

### Resposta de entrevista

> JWT é um formato de token, enquanto OAuth2 define fluxos de autorização. Em um Resource Server, Spring Security valida o bearer token, cria a Authentication e usa claims ou scopes para autorização.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-armadilhas"></a>
# 10. Armadilhas de Entrevista

- JWT não é sinônimo de OAuth2.
- CORS não protege a API contra qualquer cliente malicioso.
- Desabilitar CSRF não é sempre correto.
- Role e authority não são exatamente o mesmo conceito.
- Segurança no gateway não elimina automaticamente autorização interna nos serviços.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-producao"></a>
# 11. Produção

Proteja:

- signing keys;
- client secrets;
- admin endpoints;
- Actuator;
- refresh/access tokens.

Monitore:

- 401;
- 403;
- falhas de autenticação;
- brute force;
- tokens inválidos.

Nunca logue:

- senha;
- access token;
- refresh token;
- secrets.

Princípios:

```text
least privilege
HTTPS
key rotation
expiração adequada
validação de claims
```

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-resposta-completa"></a>
# 12. Resposta Completa de Entrevista

> Spring Security usa uma cadeia de filtros para autenticar e autorizar requisições antes de elas chegarem ao controller. Authentication identifica o usuário e Authorization decide o que ele pode fazer.
>
> Em autenticação tradicional, AuthenticationManager delega a AuthenticationProviders, que podem usar UserDetailsService e PasswordEncoder. Em OAuth2 Resource Server, o Spring valida bearer tokens e transforma claims/scopes em authorities.
>
> Também diferencio CSRF de CORS: CSRF protege contra requisições forjadas quando credenciais são enviadas automaticamente, enquanto CORS controla acesso entre origens no browser.
>
> Em produção, aplico least privilege, protejo segredos, não registro tokens em log e valido corretamente issuer, audience, expiração e permissões.

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="security-mapa-mental"></a>
# 13. Mapa Mental

```text
SPRING SECURITY
│
├── Filter Chain
├── Authentication
│   ├── SecurityContext
│   ├── AuthenticationManager
│   ├── AuthenticationProvider
│   └── UserDetailsService
├── PasswordEncoder
├── Authorization
│   ├── URL
│   └── Method
├── CSRF
├── CORS
├── Session / Stateless
└── OAuth2 / JWT
```

[↑ Sumário do módulo](#security-indice) · [↑ Sumário geral](#sumario-geral)


---

[↑ Voltar ao sumário geral](#sumario-geral)

---

# Spring Cloud

<a id="cloud-indice"></a>

## Índice

[↑ Voltar ao sumário geral](#sumario-geral)

- [1. Visão Geral](#cloud-visao-geral)
- [2. Configuração Distribuída](#cloud-config)
- [3. Service Discovery](#cloud-discovery)
- [4. API Gateway](#cloud-gateway)
- [5. Client-side Load Balancing](#cloud-load-balancer)
- [6. OpenFeign](#cloud-openfeign)
- [7. Resiliência](#cloud-resilience)
  - [7.1 Circuit Breaker](#cloud-circuit-breaker)
  - [7.2 Retry](#cloud-retry)
  - [7.3 Timeout](#cloud-timeout)
  - [7.4 Bulkhead](#cloud-bulkhead)
- [8. Spring Cloud Stream](#cloud-stream)
- [9. Observabilidade Distribuída](#cloud-observability)
- [10. Armadilhas de Entrevista](#cloud-armadilhas)
- [11. Produção](#cloud-producao)
- [12. Resposta Completa de Entrevista](#cloud-resposta-completa)
- [13. Mapa Mental](#cloud-mapa-mental)

---

<a id="cloud-visao-geral"></a>
# 1. Visão Geral

Spring Cloud oferece integrações para problemas distribuídos.

```text
Spring Cloud
│
├── Config
├── Discovery
├── Gateway
├── Load Balancing
├── HTTP Clients
├── Resilience
├── Messaging
└── Observability
```

### Importante

Spring Cloud não cria microserviços automaticamente. Ele fornece ferramentas para problemas comuns de distribuição.

### Resposta de entrevista

> Spring Cloud complementa Spring Boot com padrões e integrações para sistemas distribuídos, como configuração centralizada, discovery, gateway, load balancing, clients declarativos, resiliência e mensageria.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-config"></a>
# 2. Configuração Distribuída

Spring Cloud Config:

```text
Config Repository
      ↓
Config Server
      ↓
Applications
```

Benefícios:

- centralização;
- versionamento;
- separação por ambiente.

Trade-offs:

- dependência operacional;
- disponibilidade;
- bootstrap;
- secrets;
- refresh;
- rollback.

### Resposta de entrevista

> Config Server centraliza propriedades de múltiplos serviços. É útil para consistência e versionamento, mas passa a ser parte crítica da infraestrutura e precisa de alta disponibilidade e segurança.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-discovery"></a>
# 3. Service Discovery

```text
nome lógico
    ↓
instâncias disponíveis
```

Exemplo:

```text
order-service
├── 10.0.1.10:8080
├── 10.0.1.11:8080
└── 10.0.1.12:8080
```

Em Kubernetes, DNS e Services já fornecem discovery em muitos casos.

### Resposta de entrevista

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, avalio primeiro os mecanismos nativos antes de adicionar registry dedicado.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-gateway"></a>
# 4. API Gateway

Spring Cloud Gateway pode centralizar:

- routing;
- filtros;
- autenticação na borda;
- rate limiting;
- headers;
- observabilidade.

```text
Client
  ↓
Gateway
  ├── Service A
  ├── Service B
  └── Service C
```

### Cuidado

Não transforme gateway em monólito de regra de negócio.

### Resposta de entrevista

> Gateway centraliza preocupações de borda, como roteamento, filtros e políticas transversais. Evito colocar regra de negócio nele.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-load-balancer"></a>
# 5. Client-side Load Balancing

```text
cliente
   ↓
instâncias
   ↓
seleção
   ↓
destino
```

Spring Cloud LoadBalancer fornece integração para esse padrão.

### Trade-off

Em plataformas com load balancing nativo, avalie se a camada adicional é necessária.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-openfeign"></a>
# 6. OpenFeign

```java
@FeignClient(name = "customer-service")
public interface CustomerClient {

    @GetMapping("/customers/{id}")
    CustomerResponse findById(@PathVariable Long id);
}
```

Vantagens:

- client declarativo;
- menos boilerplate;
- integração Spring.

Mas a chamada continua remota:

```text
timeout
falha
latência
retry
observabilidade
idempotência
```

### Resposta de entrevista

> OpenFeign simplifica o client HTTP, mas não remove propriedades de rede. Continuo configurando timeout, tratamento de erro, resiliência e observabilidade.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-resilience"></a>
# 7. Resiliência

<a id="cloud-circuit-breaker"></a>
## 7.1 Circuit Breaker

Estados conceituais:

```text
CLOSED
 ↓ falhas
OPEN
 ↓ espera
HALF_OPEN
 ↓ teste
CLOSED / OPEN
```

Evita insistir em dependência degradada.

<a id="cloud-retry"></a>
## 7.2 Retry

Use quando:

- erro é transitório;
- operação é idempotente ou protegida contra duplicidade;
- número de tentativas é limitado;
- há backoff;
- preferencialmente jitter.

Retry mal configurado pode amplificar incidente.

<a id="cloud-timeout"></a>
## 7.3 Timeout

Limita quanto tempo esperar.

Sem timeout:

```text
threads/conexões ficam ocupadas
```

Timeout deve respeitar o budget total da request.

<a id="cloud-bulkhead"></a>
## 7.4 Bulkhead

Isola recursos.

Exemplos:

- semaphore;
- limite de concorrência;
- pools separados.

### Resposta de entrevista

> Em chamadas remotas combino timeout, circuit breaker e, quando faz sentido, retry com backoff e jitter. Retry precisa respeitar idempotência. Bulkhead impede que uma dependência degradada consuma todos os recursos locais.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-stream"></a>
# 8. Spring Cloud Stream

Conceitos:

```text
producer
consumer
binding
binder
destination
```

Pode integrar com brokers como Kafka e RabbitMQ via binders.

### Trade-off

A abstração não elimina fundamentos do broker.

Para Kafka, continue entendendo:

- partitions;
- consumer groups;
- offsets;
- delivery semantics;
- ordering;
- DLQ;
- throughput.

### Resposta de entrevista

> Spring Cloud Stream facilita integração com mensageria, mas não elimina a necessidade de conhecer o broker. Com Kafka, ainda preciso entender partições, consumer groups, offsets e semântica de entrega.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-observability"></a>
# 9. Observabilidade Distribuída

Correlacione:

```text
logs
metrics
traces
```

Conceitos:

```text
trace id
span id
latency
error rate
dependency timing
```

No ecossistema Spring moderno, observabilidade se integra ao Micrometer e tracing.

### Resposta de entrevista

> Em sistemas distribuídos, métricas isoladas não bastam. Correlaciono logs, métricas e traces para localizar onde latência ou erro foi introduzido na cadeia.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-armadilhas"></a>
# 10. Armadilhas de Entrevista

- Gateway não elimina automaticamente segurança interna.
- Retry nem sempre melhora disponibilidade.
- Feign não torna chamada remota local.
- Service discovery não exige necessariamente Eureka.
- Circuit Breaker não substitui timeout.
- Spring Cloud não substitui entendimento de redes distribuídas.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-producao"></a>
# 11. Produção

Para cada dependência remota, defina:

```text
timeout
retry policy
idempotência
circuit breaker
limite de concorrência
observabilidade
```

Monitore:

- p95/p99;
- error rate;
- saturation;
- circuit state;
- retry count;
- timeout count;
- downstream latency.

Evite:

```text
retry em cascata
timeout acima do budget total
fallback silencioso
dependência síncrona desnecessária
```

### Regra

> Distribuição aumenta modos de falha. Abstração não elimina latência nem partial failure.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-resposta-completa"></a>
# 12. Resposta Completa de Entrevista

> Spring Cloud fornece integrações para problemas comuns de sistemas distribuídos. Eu destacaria configuração centralizada, service discovery, gateway, load balancing, clients declarativos, resiliência, mensageria e observabilidade.
>
> OpenFeign simplifica o client HTTP, mas uma chamada remota continua sujeita a timeout, erro e latência. Por isso configuro resiliência por dependência, usando timeout, circuit breaker e retry apenas quando a operação suporta reexecução.
>
> Também considero o ambiente. Em Kubernetes, discovery e load balancing podem ser fornecidos pela própria plataforma, então evito adicionar componentes redundantes.
>
> Em produção, acompanho traces, métricas e logs distribuídos para entender a cadeia inteira.

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)

---

<a id="cloud-mapa-mental"></a>
# 13. Mapa Mental

```text
SPRING CLOUD
│
├── Config
├── Discovery
├── Gateway
├── Load Balancer
├── OpenFeign
├── Resilience
│   ├── Timeout
│   ├── Retry
│   ├── Circuit Breaker
│   └── Bulkhead
├── Stream
└── Observability
    ├── logs
    ├── metrics
    └── traces
```

[↑ Sumário do módulo](#cloud-indice) · [↑ Sumário geral](#sumario-geral)


---

<a id="resumo-geral"></a>
# Resumo Geral do Ecossistema Spring

| Módulo | Responsabilidade principal | Conceitos fundamentais |
|---|---|---|
| Spring Core | Container e composição | IoC, DI, beans, scopes, lifecycle, proxies |
| Spring Boot | Bootstrap e automação | auto-configuration, starters, config externa, Actuator |
| Spring MVC | HTTP / REST | DispatcherServlet, controllers, validation, advice, filters |
| Spring Data | Persistência | repositories, JPA, transactions, N+1, locking |
| Spring Security | Segurança | filter chain, authentication, authorization, CSRF, CORS, JWT |
| Spring Cloud | Sistemas distribuídos | config, gateway, discovery, Feign, resilience, messaging |

## Resposta de 60 segundos sobre o ecossistema

> Eu vejo o ecossistema Spring em camadas. Spring Core fornece IoC, Dependency Injection e gerenciamento de beans. Spring Boot automatiza a configuração e simplifica bootstrap e operação. Spring MVC cobre o stack HTTP baseado em Servlet, enquanto Spring Data abstrai parte do acesso a dados sobre tecnologias como JPA. Spring Security fornece a cadeia de autenticação e autorização. Quando a aplicação entra em um cenário distribuído, Spring Cloud adiciona integrações para configuração, gateway, clients, resiliência, mensageria e observabilidade.
>
> O ponto importante é entender que essas abstrações não eliminam os fundamentos. Mesmo usando Spring Data, eu preciso entender banco e transações; usando Feign, preciso entender rede, timeout e resiliência; usando Security, preciso entender autenticação e autorização.

## Mapa Mental Geral

```text
SPRING
│
├── CORE
│   ├── IoC / DI
│   ├── Beans
│   └── AOP / Proxy
│
├── BOOT
│   ├── Auto-config
│   ├── Starters
│   └── Actuator
│
├── MVC
│   ├── DispatcherServlet
│   ├── Controllers
│   └── Error Handling
│
├── DATA
│   ├── Repository
│   ├── JPA / Hibernate
│   ├── Transactions
│   └── Locking
│
├── SECURITY
│   ├── Filter Chain
│   ├── Authentication
│   ├── Authorization
│   └── OAuth2 / JWT
│
└── CLOUD
    ├── Config
    ├── Gateway
    ├── Discovery
    ├── Feign
    ├── Resilience
    ├── Messaging
    └── Observability
```

[↑ Voltar ao sumário geral](#sumario-geral)
