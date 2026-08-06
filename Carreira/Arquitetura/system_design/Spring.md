# Spring

# IoC e Dependency Injection
> Injeção de Dependência é um princípio no qual um objeto recebe suas dependências de um componente externo, em vez de criá-las diretamente.
>
> No Spring, isso é feito pelo container de IoC, que instancia os objetos, resolve suas dependências e gerencia seu ciclo de vida.
>
> A forma recomendada é a injeção via construtor, porque torna as dependências explícitas, facilita testes e garante que o objeto seja criado em um estado válido.
>
> Esse mecanismo reduz o acoplamento entre as classes e melhora a manutenção e a testabilidade da aplicação.

### Beans e ApplicationContext

O `ApplicationContext` mantém o ciclo de vida dos beans e resolve suas dependências.

Estereótipos comuns:

| Anotação          | Uso                       |
| ----------------- | ------------------------- |
| `@Component`      | Componente genérico       |
| `@Service`        | Regra de negócio          |
| `@Repository`     | Acesso a dados            |
| `@Controller`     | MVC                       |
| `@RestController` | API REST                  |
| `@Configuration`  | Configuração de beans     |
| `@Bean`           | Criação manual de um bean |

### Escopos de beans

* `singleton`: uma instância por contexto Spring.
* `prototype`: nova instância a cada solicitação.
* `request`: uma instância por requisição HTTP.
* `session`: uma instância por sessão HTTP.

**Atenção:** beans singleton devem ser, preferencialmente, **stateless** e thread-safe.

### Ciclo de vida

Fluxo simplificado:

```text
Instanciação
   ↓
Injeção de dependências
   ↓
BeanPostProcessor
   ↓
@PostConstruct
   ↓
Bean disponível
   ↓
@PreDestroy
```

---

# 2. Spring Data JPA

O **Spring Data JPA** abstrai o acesso ao banco de dados, enquanto o **Hibernate** normalmente atua como implementação da especificação JPA.

```text
Aplicação
   ↓
Spring Data JPA
   ↓
JPA
   ↓
Hibernate
   ↓
JDBC
   ↓
Banco de dados
```

## Fetch Type

* `LAZY`: relacionamento carregado somente quando acessado.
* `EAGER`: relacionamento carregado imediatamente.

Em coleções, prefira normalmente `LAZY`.

## Problema N+1

Ocorre quando uma consulta carrega uma lista e depois executa uma consulta adicional para cada elemento.

```text
1 consulta para clientes
100 consultas para pedidos
Total: 101 consultas
```

Soluções:

* `JOIN FETCH`;
* `@EntityGraph`;
* projeções;
* consultas específicas;
* batch fetching.

```java
@Query("""
    select distinct c
    from Customer c
    left join fetch c.orders
""")
List<Customer> findAllWithOrders();
```

## Concorrência

### Optimistic Locking

```java
@Version
private Long version;
```

Detecta se outra transação modificou o registro antes da atualização.

Adequado quando:

* conflitos são pouco frequentes;
* alta concorrência de leitura;
* não se deseja manter locks no banco.

### Pessimistic Locking

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("select c from Customer c where c.id = :id")
Optional<Customer> findForUpdate(UUID id);
```

Adequado quando:

* conflitos são frequentes;
* a operação exige exclusividade;
* o custo de repetir a operação é alto.

## Paginação

```java
Page<Customer> findByActiveTrue(Pageable pageable);
```

```java
PageRequest.of(0, 20, Sort.by("name"));
```

Em grandes volumes, paginação baseada em offset pode ficar cara. Em determinados cenários, considere **keyset pagination**.

---

# 3. Spring Cloud

O **Spring Cloud** oferece componentes para construção de sistemas distribuídos e microsserviços.

## Service Discovery

Permite que serviços sejam localizados dinamicamente.

Exemplo tradicional:

* Eureka Server;
* Eureka Client.

```text
Order Service
     ↓ consulta
Service Registry
     ↓ endereço disponível
Payment Service
```

Em Kubernetes, o próprio cluster normalmente fornece discovery por DNS e Services.

## API Gateway

O Spring Cloud Gateway centraliza a entrada das requisições.

Responsabilidades comuns:

* roteamento;
* autenticação;
* rate limiting;
* filtros;
* logs;
* correlação de requisições;
* circuit breaker.

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: payment-service
          uri: lb://PAYMENT-SERVICE
          predicates:
            - Path=/payments/**
```

O gateway não deve concentrar regras de negócio.

## Configuração centralizada

O Spring Cloud Config permite armazenar configurações externamente.

```text
Git Repository
      ↓
Config Server
      ↓
Microsserviços
```

Benefícios:

* configuração por ambiente;
* versionamento;
* centralização;
* separação entre código e configuração.

Segredos devem ficar em ferramentas próprias, como:

* AWS Secrets Manager;
* HashiCorp Vault;
* Kubernetes Secrets;
* Parameter Store.

## Comunicação entre serviços

### OpenFeign

```java
@FeignClient(name = "payment-service")
public interface PaymentClient {

    @PostMapping("/payments")
    PaymentResponse create(@RequestBody PaymentRequest request);
}
```

O Feign simplifica chamadas HTTP, mas continua sendo uma comunicação remota sujeita a:

* timeout;
* indisponibilidade;
* latência;
* respostas parciais;
* falhas de rede.

### Load Balancing

O Spring Cloud LoadBalancer distribui chamadas entre diferentes instâncias.

```text
Order Service
     ↓
Load Balancer
 ┌──────┼──────┐
 ↓      ↓      ↓
P1     P2     P3
```

## Resiliência

O ecossistema atual utiliza frequentemente o Resilience4j.

### Circuit Breaker

Interrompe temporariamente chamadas para uma dependência com falhas.

Estados:

```text
CLOSED → OPEN → HALF_OPEN
```

### Retry

Repete operações que falharam temporariamente.

Deve ser usado com:

* limite de tentativas;
* backoff;
* jitter;
* operações idempotentes.

### Bulkhead

Limita quantas requisições simultâneas podem acessar uma dependência, impedindo que ela consuma todos os recursos da aplicação.

### Rate Limiter

Controla quantas requisições são aceitas dentro de um período.

## Observabilidade distribuída

Cada requisição deve possuir identificadores que permitam acompanhar seu caminho pelos serviços.

Elementos:

* logs estruturados;
* métricas;
* traces distribuídos;
* correlation ID;
* OpenTelemetry;
* Micrometer;
* Prometheus;
* Grafana.

```text
Gateway
  traceId=abc
     ↓
Order Service
  traceId=abc
     ↓
Payment Service
  traceId=abc
```

## Comunicação assíncrona

Microsserviços também podem se comunicar por eventos usando:

* Kafka;
* RabbitMQ;
* AWS SQS;
* Spring Cloud Stream.

Pontos essenciais:

* mensagens duplicadas;
* idempotência;
* DLQ;
* retry;
* ordenação;
* evolução de contratos;
* consistência eventual;
* Transactional Outbox.

## O que dominar em nível sênior

* Service Discovery.
* API Gateway.
* Configuração externa.
* Load balancing.
* OpenFeign e clientes HTTP.
* Timeout, retry, circuit breaker e bulkhead.
* Observabilidade distribuída.
* Comunicação síncrona versus assíncrona.
* Idempotência e consistência eventual.
* Falhas parciais.
* Segurança entre serviços.
* Kubernetes versus componentes tradicionais do Spring Cloud.
* Evitar cadeias síncronas longas entre microsserviços.

---

# Comparação rápida

| Área            | Responsabilidade principal                            |
| --------------- | ----------------------------------------------------- |
| Spring Core     | Criação, configuração e gerenciamento dos componentes |
| Spring Data JPA | Persistência, transações e acesso ao banco            |
| Spring Cloud    | Comunicação, configuração e resiliência distribuída   |

# Visão de fluxo

```text
Spring Cloud Gateway
        ↓
Controller
        ↓
Spring Core / Service
        ↓
@Transactional
        ↓
Spring Data JPA
        ↓
Hibernate
        ↓
PostgreSQL
```

# Prioridade de estudo

```text
1. Spring Core
   IoC → DI → Beans → Ciclo de vida → AOP → Proxies

2. Spring Data JPA
   Entidades → Contexto de persistência → Transações
   → Fetching → N+1 → Locks → Performance

3. Spring Cloud
   Gateway → Clientes HTTP → Discovery → Config
   → Resiliência → Observabilidade → Mensageria
```

Para nível sênior, Lucas, não basta conhecer as anotações. É necessário explicar **como o Spring funciona internamente, quais problemas cada recurso resolve e quais trade-offs sua utilização introduz**.
