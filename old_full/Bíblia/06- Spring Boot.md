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
```

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
