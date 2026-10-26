Lucas, em **Spring Boot** o ponto principal é entender que ele não substitui o Spring Core. Ele constrói uma camada sobre o Spring Framework para reduzir configuração manual, aplicar convenções e facilitar aplicações standalone e production-ready. Na documentação atual, a linha estável mais recente é o **Spring Boot 4.1.0**. 

# 1. Spring Boot — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Spring Boot** | Camada sobre o Spring Framework que fornece convenções, auto-configuração, gerenciamento de dependências, execução standalone e recursos operacionais. | Reduz muito configuração manual, mas abstrai decisões que o desenvolvedor precisa saber diagnosticar. | APIs REST, microsserviços, batch e aplicações standalone. |
| **`@SpringBootApplication`** | Annotation principal que combina auto-configuração, component scanning e configuração Spring Boot. | Muito conveniente, mas posicioná-la incorretamente pode afetar o scanning da aplicação. | Classe principal da aplicação. |
| **AutoConfiguration** | Spring Boot analisa classpath, beans existentes e propriedades para configurar automaticamente a infraestrutura necessária. | Facilita desenvolvimento, mas pode parecer "mágico" se você não entende as condições aplicadas. | Configuração automática de MVC, Jackson, DataSource, JPA, Kafka etc. |
| **Starter** | Dependência agregadora que traz um conjunto coerente de bibliotecas necessárias para determinada funcionalidade. | Facilita dependências, mas pode trazer transitivamente componentes que não serão utilizados. | `spring-boot-starter-web`, `data-jpa`, `actuator`. |
| **Conditional Beans** | Beans ou auto-configurações são registrados somente quando determinadas condições são satisfeitas. | Muito flexível, mas configurações condicionais complexas podem ser difíceis de diagnosticar. | Auto-configuração de DataSource somente quando determinada classe/propriedade existe. |
| **`@ConditionalOnClass`** | Ativa configuração quando determinada classe está no classpath. | O comportamento depende das dependências presentes. | Habilitar configuração de uma biblioteca automaticamente. |
| **`@ConditionalOnMissingBean`** | Cria um bean apenas quando o usuário ainda não registrou outro equivalente. | Pode causar comportamento inesperado se existir outro bean que faça a condição recuar. | Permitir sobrescrever defaults do Boot. |
| **`@ConditionalOnProperty`** | Ativa configuração dependendo de uma propriedade. | Aumenta flexibilidade, mas muitas flags tornam configuração difícil de manter. | `feature.payment.enabled=true`. |
| **ConfigurationProperties** | Faz binding de propriedades externas para objetos Java estruturados e tipados. | Exige uma classe de configuração adicional, mas escala muito melhor que dezenas de `@Value`. | Configurações de APIs externas, timeout, URLs, credenciais e limites. |
| **Actuator** | Conjunto de funcionalidades para monitoramento e gerenciamento da aplicação. | Expor endpoints indiscriminadamente pode gerar risco operacional e de segurança. | Health checks, métricas, Prometheus, thread dump e diagnóstico. |
| **Profiles** | Permitem ativar diferentes conjuntos de configuração ou beans conforme o ambiente. | Excesso de profiles pode gerar muitas combinações difíceis de testar. | `dev`, `test`, `prod`. |
| **External Configuration** | Permite retirar configuração do código e fornecê-la externamente via properties, YAML, environment variables, argumentos etc. | Existe uma ordem de precedência que precisa ser conhecida para troubleshooting. | Configuração diferente por ambiente sem recompilar a aplicação. |

Os starters são descritores de dependências convenientes e mantêm conjuntos compatíveis de dependências transitivas. A auto-configuração, por sua vez, configura componentes de acordo com o classpath e com aquilo que o desenvolvedor já declarou. 

---

# 2. `@SpringBootApplication`

Uma aplicação Boot normalmente começa assim:

```java
@SpringBootApplication
public class PaymentApplication {

    public static void main(String[] args) {
        SpringApplication.run(PaymentApplication.class, args);
    }
}
```

Essa annotation reúne, conceitualmente:

```text
@SpringBootApplication
        │
        ├── @SpringBootConfiguration
        │
        ├── @EnableAutoConfiguration
        │
        └── @ComponentScan
```

Portanto ela faz três coisas fundamentais:

```text
configuração Spring
       +
component scanning
       +
auto-configuration
``` 


---

# 3. AutoConfiguration

Esse é provavelmente o conceito mais importante de Spring Boot.

Imagine que você adicionou:

```text
Spring MVC
Jackson
Tomcat
```

ao classpath.

O Spring Boot percebe essas dependências e configura automaticamente parte da infraestrutura necessária.

Por exemplo:

```text
classpath
   ↓
Spring MVC encontrado
   ↓
condições avaliadas
   ↓
configuração MVC aplicada
```

Outro exemplo:

```text
DataSource library existe?
          ↓
configurações existem?
          ↓
usuário já criou DataSource?
          ↓
       não
          ↓
Boot configura um DataSource
```

A ideia não é:

> "Spring Boot configura tudo."

A ideia correta é:

> **Spring Boot possui configurações pré-definidas que são ativadas quando determinadas condições são satisfeitas.**

A própria documentação descreve a auto-configuração como **non-invasive**: se você fornecer sua própria configuração, muitas auto-configurações recuam. 

---

# 4. O famoso "Spring Boot magic"

Considere:

```java
@SpringBootApplication
public class Application {
}
```

Você adiciona um starter web e consegue criar:

```java
@RestController
public class CustomerController {
}
```

sem configurar manualmente:

```text
DispatcherServlet
Jackson
servidor web
MVC infrastructure
converters
error handling básico
```

Isso acontece porque:

```text
Starter
   ↓
dependencies entram no classpath
   ↓
AutoConfiguration
   ↓
conditions são avaliadas
   ↓
BeanDefinitions são registradas
   ↓
Spring Core cria os beans
```

Essa relação é fundamental:

> **Spring Core gerencia os beans. Spring Boot decide e facilita quais configurações devem ser registradas.**

---

# 5. Starter

Um starter não é uma funcionalidade misteriosa do runtime.

É principalmente um **conjunto de dependências**.

Por exemplo:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

Em vez de você selecionar manualmente diversas dependências relacionadas a JPA, o starter oferece uma entrada coerente.

Mentalmente:

```text
spring-boot-starter-data-jpa
            ↓
       dependências
            ↓
          JPA
        Hibernate
         Spring
          JDBC
          etc.
```

O starter facilita **dependency management**.

A AutoConfiguration utiliza o resultado dessas dependências no classpath para decidir o que configurar. 

Então:

```text
Starter
   ↓
traz dependências


AutoConfiguration
   ↓
configura recursos
```

Essa diferença é muito perguntável em entrevista.

---

# 6. Conditional Beans

As condições são o coração da AutoConfiguration.

Exemplos importantes:

```text
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnBean
@ConditionalOnProperty
```

Imagine:

```java
@Bean
@ConditionalOnMissingBean
public PaymentClient paymentClient() {
    return new DefaultPaymentClient();
}
```

Significa aproximadamente:

```text
já existe PaymentClient?
        │
   ┌────┴────┐
   │         │
  sim       não
   │         │
não cria    cria default
```

Isso permite um comportamento muito importante do Spring Boot:

> **convention over configuration, sem impedir customização.**

A auto-configuração normalmente utiliza condições como `@ConditionalOnClass` e `@ConditionalOnMissingBean`. 

---

# 7. Exemplo de AutoConfiguration

Considere que uma biblioteca possui:

```java
@Configuration
@ConditionalOnClass(PaymentClient.class)
public class PaymentAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    PaymentClient paymentClient() {
        return new DefaultPaymentClient();
    }
}
```

Agora existem dois cenários.

### Usuário não configura nada

```text
PaymentClient no classpath
       ↓
condition match
       ↓
nenhum PaymentClient existente
       ↓
DefaultPaymentClient
```

### Usuário cria seu próprio bean

```java
@Bean
PaymentClient paymentClient() {
    return new CustomPaymentClient();
}
```

Então:

```text
@ConditionalOnMissingBean
         ↓
não corresponde
         ↓
Boot recua
```

Esse comportamento é chamado frequentemente de:

**back-off.**

---

# 8. Como descobrir o que o Boot configurou

Essa é uma boa pergunta de entrevista.

Se algo está sendo criado automaticamente e você quer saber:

> "Por que esse Bean existe?"

Você pode iniciar com:

```bash
--debug
```

O Spring Boot gera um **conditions evaluation report** mostrando auto-configurações que deram match ou não.

Também existe suporte no Actuator através do endpoint:

```text
/actuator/conditions
``` 


Isso é melhor do que responder:

> "O Boot criou por mágica."

---

# 9. `@ConfigurationProperties`

Imagine:

```yaml
payment:
  url: https://payment.example.com
  timeout: 5s
  retries: 3
```

Uma forma ruim de crescer isso seria:

```java
@Value("${payment.url}")
String url;

@Value("${payment.timeout}")
Duration timeout;

@Value("${payment.retries}")
int retries;
```

Uma abordagem mais estruturada:

```java
@ConfigurationProperties(prefix = "payment")
public record PaymentProperties(
        URI url,
        Duration timeout,
        int retries
) {}
```

Agora temos:

```text
application.yaml
      ↓
binding
      ↓
PaymentProperties
      ↓
objeto tipado
```

Isso oferece:

- organização;
- type safety;
- relaxed binding;
- metadata;
- validação integrada;
- facilidade de teste.

A documentação recomenda `@ConfigurationProperties` quando você possui um conjunto de propriedades próprio, em vez de espalhar configurações relacionadas em diversos `@Value`. 

---

# 10. `@ConfigurationProperties` x `@Value`

Regra prática:

```text
Uma propriedade isolada
        ↓
@Value pode resolver


Grupo de configuração
        ↓
@ConfigurationProperties
```

Por exemplo:

```text
payment.url
payment.timeout
payment.retries
payment.connection-pool-size
payment.enabled
```

Nesse cenário:

```java
@ConfigurationProperties
```

é geralmente superior.

A documentação atual destaca que `@ConfigurationProperties` possui relaxed binding e metadata, enquanto `@Value` possui suporte a SpEL que `ConfigurationProperties` não possui. 

---

# 11. External Configuration

Uma aplicação não deveria precisar ser recompilada somente porque mudou:

```text
database URL
API endpoint
timeout
feature flag
port
log level
```

Por isso o Spring Boot suporta **externalized configuration**.

Uma mesma aplicação:

```text
application.jar
```

pode executar em:

```text
DEV
QA
PROD
```

com configurações diferentes.

Fontes possíveis incluem:

```text
application.properties
application.yaml
environment variables
system properties
command-line arguments
external files
``` 


---

# 12. Precedência de configuração

Spring Boot possui uma ordem de precedência.

Isso significa que configurações podem sobrescrever outras.

Conceitualmente:

```text
valor padrão
      ↓
application.yaml
      ↓
configuração específica
      ↓
variável de ambiente
      ↓
argumento com maior precedência
```

Não memorize toda a lista para entrevista.

Memorize o conceito:

> **Property Sources possuem precedência e uma fonte com prioridade maior pode sobrescrever outra.**

Isso explica problemas clássicos como:

> "Meu `application.yaml` diz uma coisa, mas em produção aparece outra."

Possivelmente uma variável de ambiente ou outra fonte está sobrescrevendo o valor.

---

# 13. Profiles

Profiles permitem variar partes da configuração.

Por exemplo:

```text
application.yaml
application-dev.yaml
application-prod.yaml
```

Ativando:

```properties
spring.profiles.active=prod
```

podemos carregar configuração específica de produção.

Profiles também podem controlar beans:

```java
@Configuration
@Profile("prod")
public class ProductionConfig {
}
``` 


Mas existe um cuidado importante.

Evite transformar profiles em:

```text
dev
qa
prod
aws
gcp
oracle
postgres
kafka
client-a
client-b
feature-x
```

criando centenas de combinações.

Normalmente configuração externa e condições explícitas escalam melhor para muitos desses casos.

---

# 14. Actuator

Actuator é a principal infraestrutura production-ready do Spring Boot.

Ao adicionar o starter apropriado, podemos disponibilizar informações operacionais sobre a aplicação.

Exemplos:

```text
/actuator/health
/actuator/info
/actuator/metrics
/actuator/prometheus
/actuator/loggers
/actuator/threaddump
/actuator/heapdump
/actuator/conditions
```

Dependendo do endpoint, configuração e exposição.

O Actuator também integra métricas através do **Micrometer**, permitindo exportação para sistemas como Prometheus, Datadog, Dynatrace e OTLP. 

---

# 15. Health checks

Um dos usos mais importantes do Actuator é:

```text
/actuator/health
```

Em ambientes Kubernetes, health information pode contribuir para verificações como:

```text
liveness
readiness
```

Conceitualmente:

```text
Liveness
   ↓
a aplicação ainda está viva?


Readiness
   ↓
a aplicação está pronta
para receber tráfego?
```

Isso é extremamente importante em microsserviços.

Uma aplicação pode estar:

```text
processo vivo
```

mas não estar:

```text
pronta para receber requests
```

por exemplo porque alguma infraestrutura necessária ainda não está disponível.

---

# 16. Métricas

O Actuator integra-se ao Micrometer.

Podemos observar métricas de:

```text
JVM
Heap
GC
CPU
threads
HTTP
connection pools
Kafka
banco
custom business metrics
```

Por exemplo:

```text
http.server.requests
jvm.memory.used
jvm.gc...
process.cpu...
```

A documentação atual também inclui métricas relacionadas a Virtual Threads quando o módulo apropriado está presente. 

Esse ponto conecta Spring Boot diretamente com observabilidade.

---

# 17. Actuator não significa expor tudo

Uma consideração importante de produção:

Não devemos simplesmente disponibilizar todos os endpoints publicamente.

Alguns endpoints podem revelar:

```text
configuração
estrutura da aplicação
mappings
threads
logs
informações operacionais
```

Por isso é necessário controlar:

```text
exposição
autorização
rede
segurança
```

Especialmente para endpoints sensíveis como heap dump e alteração de configuração operacional.

---

# 18. Spring Core x Spring Boot

Essa diferença precisa estar muito clara.

```text
Spring Framework
│
├── IoC
├── DI
├── ApplicationContext
├── Beans
├── AOP
├── Transactions
└── infraestrutura


Spring Boot
│
├── AutoConfiguration
├── Starters
├── Conditional Configuration
├── External Configuration
├── ConfigurationProperties
├── Actuator
└── convenções
```

Portanto:

> **Spring cria e gerencia os objetos.**

> **Spring Boot automatiza e padroniza grande parte da configuração necessária para montar a aplicação.**

---

# 19. Mapa mental do Spring Boot

Para memorizar:

```text
@SpringBootApplication
        ↓
Component Scan
        +
AutoConfiguration
        ↓
analisa classpath
        ↓
analisa propriedades
        ↓
analisa beans existentes
        ↓
Conditional
        ↓
registra configurações necessárias
        ↓
Spring Container
        ↓
Application
```

E no lado operacional:

```text
External Configuration
        ↓
@ConfigurationProperties
        ↓
Application


Actuator
   ↓
Health
Metrics
Prometheus
Diagnostics
```Lucas, em **Spring Security** o ponto principal não é decorar annotations. É entender o fluxo: **a requisição entra na cadeia de filtros, a identidade é autenticada, armazenada no contexto de segurança e depois as regras de autorização decidem se o acesso será permitido**. Spring Security é baseado fundamentalmente em Servlet Filters no stack MVC. 

# 1. Spring Security — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Authentication** | Processo de verificar **quem é o usuário ou cliente**. O resultado normalmente é representado por um objeto `Authentication`. | Requer estratégia segura de credenciais/tokens e pode envolver IdP externo. | Login, Basic Auth, JWT Bearer, OAuth2/OIDC. |
| **Authorization** | Processo de decidir **o que o usuário autenticado pode fazer**. | Regras muito granulares aumentam segurança, mas também complexidade. | Permitir `/admin/**` somente para administradores. |
| **SecurityFilterChain** | Define os filtros e regras de segurança aplicáveis às requisições HTTP. | Ordem e configuração incorretas podem abrir ou bloquear endpoints indevidamente. | Configurar autenticação, autorização, CORS, CSRF e OAuth2. |
| **SecurityContextHolder** | Mantém o `SecurityContext`, que contém o `Authentication` corrente. | Código muito acoplado ao contexto dificulta testes e arquitetura. | Recuperar o usuário autenticado durante a requisição. |
| **AuthenticationManager** | Contrato responsável por processar uma tentativa de autenticação. | Pode exigir múltiplos providers quando existem mecanismos diferentes. | Username/password, LDAP, autenticação customizada. |
| **AuthenticationProvider** | Implementa uma estratégia concreta para autenticar determinado tipo de `Authentication`. | Vários providers tornam o fluxo mais sofisticado. | Banco de usuários, LDAP, autenticação personalizada. |
| **GrantedAuthority** | Representa uma permissão atribuída ao usuário autenticado, como role ou scope. | Autorizações excessivamente específicas podem ficar difíceis de administrar. | `ROLE_ADMIN`, `SCOPE_orders.read`. |
| **JWT** | Formato de token que transporta claims e pode ser assinado digitalmente. Não é, por si só, um protocolo de autenticação. | Facilita APIs stateless, mas revogação imediata e exposição de claims exigem cuidado. | Access Token em APIs REST. |
| **OAuth 2.0** | Framework de **autorização** para conceder acesso limitado a recursos através de access tokens. | É mais complexo que autenticação própria simples, mas padroniza delegação de acesso. | API protegida, integração entre sistemas, login delegado em conjunto com OIDC. |
| **OIDC** | Camada de identidade construída sobre OAuth 2.0, adicionando autenticação e `ID Token`. | Adiciona conceitos e endpoints, mas padroniza identidade federada. | Login com Google, Microsoft, Keycloak, Auth0 etc. |
| **Access Token** | Credencial usada para acessar um Resource Server. | Precisa de validade, escopo e proteção adequados. | `Authorization: Bearer <token>`. |
| **ID Token** | Token do OIDC que representa informações sobre a autenticação do usuário para o Client. | Não deve ser confundido com Access Token. | Identificar o usuário depois de um login OIDC. |
| **RBAC** | Role-Based Access Control. Permissões são associadas a papéis, e usuários recebem esses papéis. | Simples para muitos sistemas, mas pode ficar limitado em regras contextuais muito complexas. | `ADMIN`, `USER`, `MANAGER`. |
| **CORS** | Política de navegador que controla requisições entre origens diferentes. | Configuração permissiva demais aumenta superfície de exposição; restritiva demais quebra frontends legítimos. | Angular em `frontend.com` chamando API em `api.com`. |
| **CSRF** | Ataque em que o navegador da vítima envia uma requisição autenticada indesejada a um sistema. | Proteção adiciona gerenciamento de token; desabilitá-la indiscriminadamente é perigoso. | Aplicações autenticadas por sessão/cookie. |
| **Resource Server** | Aplicação que recebe Access Tokens e protege recursos/API com base nesses tokens. | Depende de validação correta do token e do Authorization Server. | Microsserviço Spring Boot protegido por JWT. |
| **Authorization Server** | Sistema responsável por autenticar/autorizar clientes e emitir tokens OAuth2/OIDC. | Centraliza identidade, mas vira componente crítico da arquitetura. | Keycloak, Spring Authorization Server, Entra ID, Okta. |

Spring Security mantém o usuário autenticado em um `SecurityContext`, e o `Authentication` contém, entre outras informações, as authorities utilizadas posteriormente nas decisões de autorização. 

---

# 2. Authentication x Authorization

Essa diferença precisa estar automática na entrevista.

```text
Authentication
      ↓
Quem é você?


Authorization
      ↓
O que você pode fazer?
```

Exemplo:

```text
Lucas
  ↓
token válido
  ↓
AUTHENTICATED
```

Depois:

```text
Lucas
  ↓
ROLE_USER
  ↓
GET /orders
  ↓
permitido
```

Mas:

```text
Lucas
  ↓
ROLE_USER
  ↓
DELETE /admin/users/10
  ↓
ROLE_ADMIN necessária
  ↓
negado
```

Portanto:

> **Authentication estabelece identidade. Authorization decide acesso.**

---

# 3. Arquitetura real de uma requisição

O modelo simplificado que você apresentou está correto:

```text
Request
   ↓
Security Filter Chain
   ↓
Authentication
   ↓
Authorization
   ↓
Controller
```

Para uma entrevista Senior, eu expandiria mentalmente para:

```text
HTTP Request
      ↓
Servlet Filter Chain
      ↓
Spring Security
      ↓
SecurityFilterChain
      ↓
Authentication Filter
      ↓
AuthenticationManager
      ↓
AuthenticationProvider
      ↓
Authentication
      ↓
SecurityContextHolder
      ↓
AuthorizationFilter
      ↓
AuthorizationManager
      ↓
Controller
```

O `AuthorizationFilter` consulta o `Authentication` e delega a decisão para um `AuthorizationManager`; se o acesso for permitido, a cadeia continua até a aplicação. 

---

# 4. SecurityFilterChain

Hoje a configuração típica é baseada em um Bean:

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http)
        throws Exception {

    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        );

    return http.build();
}
```

Mentalmente:

```text
/public/**
     ↓
permitAll


/admin/**
     ↓
ROLE_ADMIN


qualquer outra
     ↓
authenticated
```

As regras são avaliadas de acordo com os matchers configurados, e `hasRole("ADMIN")` trabalha sobre authorities usando a convenção de role do Spring. 

---

# 5. Authentication internamente

Imagine autenticação com usuário e senha.

```text
username
password
   ↓
Authentication Filter
   ↓
AuthenticationManager
   ↓
AuthenticationProvider
   ↓
valida credenciais
   ↓
Authentication autenticado
   ↓
SecurityContext
```

Um objeto `Authentication` pode representar tanto:

```text
credenciais ainda não autenticadas
```

quanto:

```text
usuário autenticado
+
principal
+
authorities
```

Esse modelo permite ao Spring Security suportar vários mecanismos diferentes através da mesma arquitetura. 

---

# 6. JWT

JWT precisa ser explicado corretamente.

Um JWT normalmente possui:

```text
HEADER
.
PAYLOAD
.
SIGNATURE
```

Por exemplo:

```text
header
 ↓
algoritmo e metadata

payload
 ↓
claims

signature
 ↓
integridade/autenticidade
```

Claims podem incluir:

```text
sub
iss
aud
exp
nbf
scope
roles
```

Um erro comum é dizer:

> "JWT criptografa os dados."

Não necessariamente.

Um JWT assinado garante principalmente **integridade e autenticidade**, mas seu payload normalmente pode ser decodificado.

Portanto:

> não coloque informação secreta no payload simplesmente porque está usando JWT.

---

# 7. JWT em um Resource Server

Uma arquitetura muito comum é:

```text
Client
   ↓
Authorization Server
   ↓
Access Token JWT
   ↓
Client
   ↓
API / Resource Server
```

Na API:

```text
Authorization: Bearer eyJ...
```

O Spring Security pode:

```text
extrair token
    ↓
validar assinatura
    ↓
validar expiração
    ↓
validar issuer
    ↓
converter claims/scopes
    ↓
Authentication
    ↓
SecurityContext
```

O Resource Server do Spring Security valida, por padrão no cenário configurado com issuer, assinatura e claims temporais como `exp` e `nbf`, além do `iss`, e converte scopes para authorities com prefixo `SCOPE_`. 

---

# 8. JWT não é OAuth2

Outra pergunta clássica.

```text
JWT
=
formato de token
```

Enquanto:

```text
OAuth2
=
framework/protocolo de autorização
```

Você pode:

```text
usar OAuth2 com JWT
```

mas também:

```text
usar OAuth2 com opaque token
```

O Spring Security suporta Resource Server tanto com JWT quanto com opaque tokens. 

Então não diga:

> "JWT e OAuth2 são duas formas concorrentes de autenticação."

São conceitos de níveis diferentes.

---

# 9. OAuth2

OAuth2 resolve principalmente:

> **Como uma aplicação recebe autorização limitada para acessar determinado recurso?**

Existem quatro papéis conceituais importantes:

```text
Resource Owner
      ↓
normalmente usuário


Client
      ↓
aplicação que quer acesso


Authorization Server
      ↓
emite tokens


Resource Server
      ↓
API protegida
```

Esses papéis fazem parte do modelo definido pelo OAuth 2.0. 

Exemplo:

```text
Frontend
   ↓
Authorization Server
   ↓
Access Token
   ↓
Orders API
```

---

# 10. OAuth2 não é originalmente protocolo de autenticação

Esse ponto vale muito em entrevista.

OAuth2 é um framework de:

> **autorização.**

Ele permite acesso limitado a recursos. 

Para identidade padronizada, usamos:

```text
OpenID Connect
```

---

# 11. OIDC

OIDC significa:

**OpenID Connect.**

Ele adiciona uma camada de identidade sobre OAuth2 e permite ao Client verificar a identidade do usuário. 

Mentalmente:

```text
OAuth2
   ↓
Authorization


OIDC
   ↓
OAuth2
+
Authentication / Identity
```

OIDC introduz um conceito muito importante:

```text
ID Token
```

O Spring Security identifica um login OIDC, por exemplo, pela presença do scope `openid` e utiliza componentes específicos de OIDC. 

---

# 12. Access Token x ID Token

Memorize:

```text
Access Token
     ↓
serve para acessar uma API
```

e:

```text
ID Token
     ↓
serve para informar ao Client
sobre a autenticação do usuário
```

Exemplo:

```text
User
 ↓
Login com Google
 ↓
OIDC
 ↓
ID Token
 ↓
Frontend/BFF conhece identidade
```

Depois:

```text
Access Token
 ↓
Orders API
```

Não trate o ID Token automaticamente como credencial para chamar qualquer Resource Server.

---

# 13. RBAC

RBAC significa:

**Role-Based Access Control.**

Em vez de:

```text
Lucas
pode endpoint A
pode endpoint B
pode endpoint C
```

criamos:

```text
ROLE_ADMIN
   ↓
permissões administrativas


ROLE_USER
   ↓
permissões comuns
```

E associamos:

```text
User
 ↓
Roles
 ↓
Permissions
```

No Spring:

```java
.requestMatchers("/admin/**")
.hasRole("ADMIN")
```

ou:

```java
@PreAuthorize("hasRole('ADMIN')")
```

Por baixo, roles e outras permissões são representadas como `GrantedAuthority`. 

---

# 14. Role x Authority

Spring Security trabalha essencialmente com:

```text
GrantedAuthority
```

Por exemplo:

```text
ROLE_ADMIN
SCOPE_orders.read
orders:write
payment:approve
```

`hasRole("ADMIN")` é uma conveniência sobre authority e normalmente adiciona o prefixo:

```text
ROLE_
```

Então:

```java
hasRole("ADMIN")
```

corresponde conceitualmente a:

```text
ROLE_ADMIN
``` 


---

# 15. CORS

CORS significa:

**Cross-Origin Resource Sharing.**

Imagine:

```text
Angular
https://app.example.com

        ↓

Spring API
https://api.example.com
```

As origens são diferentes.

O navegador aplica restrições de same-origin e utiliza CORS para decidir se o frontend pode realizar aquela comunicação.

Você pode controlar:

```text
allowed origins
allowed methods
allowed headers
credentials
```

Um detalhe importante no Spring Security:

> CORS deve ser tratado antes da segurança da requisição, especialmente porque uma requisição de preflight pode não possuir cookies de autenticação.

A documentação do Spring destaca explicitamente que CORS deve ser processado antes do Spring Security nesse fluxo. 

---

# 16. CORS não é Authentication

Outra confusão comum:

```text
CORS
≠
Authentication
```

CORS não pergunta:

> "Quem é esse usuário?"

Ele pergunta algo mais próximo de:

> "Esse navegador pode fazer essa requisição a partir dessa origem?"

Além disso, CORS é principalmente uma política aplicada pelo **browser**.

Não é um mecanismo para impedir que outro backend faça uma requisição HTTP para sua API. 

---

# 17. CSRF

CSRF significa:

**Cross-Site Request Forgery.**

Imagine que o usuário está autenticado:

```text
bank.com
 ↓
cookie de sessão
```

Depois acessa:

```text
evil.com
```

O site malicioso tenta provocar:

```text
POST bank.com/transfer
```

Se o navegador automaticamente anexar a credencial, como um cookie de sessão, a API pode acreditar que aquela requisição foi legítima.

Uma proteção tradicional utiliza:

```text
CSRF Token
```

que o atacante não consegue simplesmente provocar o navegador a enviar da mesma maneira que uma credencial automática. Spring Security fornece proteção CSRF por padrão para métodos HTTP inseguros em aplicações Servlet. 

---

# 18. Posso desabilitar CSRF em uma API JWT?

Essa pergunta aparece bastante.

A resposta correta não é simplesmente:

> "API REST sempre desabilita CSRF."

Depende de **como a autenticação é transportada**.

Se sua API usa:

```text
Authorization: Bearer <token>
```

e o token precisa ser explicitamente inserido nesse header pelo cliente, uma API totalmente stateless normalmente não depende da proteção CSRF da mesma maneira que uma aplicação baseada em cookies.

Nesse cenário é comum configurar:

```java
.csrf(csrf -> csrf.disable())
```

Por outro lado, se sua autenticação utiliza:

```text
cookie
session
JWT armazenado em cookie
```

e o navegador envia essa credencial automaticamente, CSRF volta a ser relevante.

A documentação recomenda considerar proteção CSRF para requisições processadas por browsers e observa que serviços usados somente por clientes não-browser podem optar por desabilitá-la. 

---

# 19. Stateless com JWT

Uma arquitetura típica de microsserviço pode ser:

```text
Client
   ↓
Bearer JWT
   ↓
SecurityFilterChain
   ↓
validação do token
   ↓
Authentication
   ↓
SecurityContext
   ↓
Authorization
   ↓
Controller
```

Nesse modelo, normalmente evitamos uma sessão HTTP tradicional para armazenar autenticação entre requests.

Cada requisição carrega sua credencial:

```text
Request 1
Authorization: Bearer JWT


Request 2
Authorization: Bearer JWT


Request 3
Authorization: Bearer JWT
```

O Resource Server valida cada requisição conforme sua configuração.

---

# 20. 401 x 403

Memorize isso para entrevista:

```text
401 Unauthorized
       ↓
não autenticado
ou credencial inválida
```

Exemplo:

```text
JWT ausente
JWT inválido
JWT expirado
```

Enquanto:

```text
403 Forbidden
      ↓
autenticado
mas sem autorização
```

Exemplo:

```text
usuário autenticado

ROLE_USER

endpoint exige ROLE_ADMIN
        ↓
403
```

Essa distinção é fundamental ao diagnosticar problemas de segurança.

---

# 21. Mapa mental

Uma arquitetura moderna com OAuth2 e JWT pode ser lembrada assim:

```text
             Authorization Server
                    │
                    │ autentica / autoriza
                    ↓
                 JWT Token
                    │
                    ↓
Client ─────────── Request
                    │
                    ↓
           SecurityFilterChain
                    │
                    ↓
           valida Authentication
                    │
                    ↓
           SecurityContext
                    │
                    ↓
             Authorization
                    │
           ┌────────┴────────┐
           │                 │
        permitido          negado
           │                 │
           ↓                 ↓
      Controller          401 / 403
```

E conceitualmente:

```text
Authentication
     ↓
quem é você?


Authorization
     ↓
o que pode fazer?


OAuth2
     ↓
autorização delegada


OIDC
     ↓
identidade sobre OAuth2


JWT
     ↓
formato de token


RBAC
     ↓
controle por roles


CORS
     ↓
controle entre origens no browser


CSRF
     ↓
proteção contra requisição
forjada usando credencial
enviada automaticamente
```

# 22. Resposta objetiva para entrevista

Se perguntarem **"Como funciona o Spring Security em uma API moderna?"**, uma resposta consistente seria:

> Spring Security é baseado em uma cadeia de filtros que intercepta a requisição antes de ela chegar ao controller. Primeiro ocorre a autenticação, que determina a identidade do usuário ou cliente e cria um `Authentication`, normalmente armazenado no `SecurityContext`. Depois ocorre a autorização, que utiliza roles, authorities ou scopes para decidir se aquele principal pode acessar determinado recurso. 
>
> Em uma API moderna, é comum configurar o serviço como OAuth2 Resource Server e receber um JWT no header `Authorization: Bearer`. O Spring valida o token e transforma seus claims ou scopes em authorities usadas nas regras de autorização. 
>
> Também separo bem os conceitos: JWT é um formato de token; OAuth2 é um framework de autorização; e OIDC adiciona identidade e autenticação sobre OAuth2, incluindo o conceito de ID Token. 
>
> Para controle de acesso posso utilizar RBAC com roles e authorities, tanto em regras HTTP quanto em segurança de métodos.
>
> CORS controla comunicação entre diferentes origens no browser e deve ser configurado adequadamente antes da autenticação da requisição. CSRF protege principalmente cenários em que o browser envia credenciais automaticamente, como cookies e sessões; em APIs stateless usando Bearer Token via header, a decisão de desabilitá-lo deve considerar exatamente como a credencial é transportada. 
>
> Então o fluxo que mantenho mentalmente é: **request, SecurityFilterChain, authentication, SecurityContext, authorization e, somente se permitido, controller**.

---

# 20. Resposta objetiva para entrevista

Se perguntarem **"Como funciona o Spring Boot?"**, uma resposta curta e consistente seria:

> Spring Boot é uma camada sobre o Spring Framework que reduz configuração manual através principalmente de starters, auto-configuração e convention over configuration.
>
> Os starters agrupam dependências compatíveis para determinada funcionalidade, enquanto a AutoConfiguration analisa classpath, propriedades e beans existentes para decidir quais configurações registrar. Isso é feito principalmente através de condições como `ConditionalOnClass`, `ConditionalOnProperty` e `ConditionalOnMissingBean`. 
>
> Um ponto importante é que a AutoConfiguration faz back-off quando fornecemos nossa própria configuração, então ela fornece defaults sem impedir customização.
>
> Para configuração da aplicação, prefiro `ConfigurationProperties` quando existe um grupo de propriedades, porque permite binding tipado e estruturado. O Boot também suporta configuração externa por YAML, properties, variáveis de ambiente e outros Property Sources, respeitando uma ordem de precedência. 
>
> Profiles permitem variar configurações ou Beans entre ambientes, mas devem ser usados com cuidado para evitar muitas combinações.
>
> Para produção, o Actuator fornece health checks, métricas e endpoints de diagnóstico, com integração ao Micrometer para observabilidade. 
>
> Então eu vejo o Spring Framework como responsável pelo container e gerenciamento dos Beans, enquanto o Spring Boot fornece **convenções, auto-configuração, gerenciamento de dependências, configuração externa e recursos production-ready** para tornar esse container mais simples de usar em aplicações reais.
