# Microsserviços com Spring — Resumo para Engenheiro Sênior

## 1. O que são microsserviços

Microsserviços são uma abordagem arquitetural em que o sistema é dividido em serviços pequenos, independentes e alinhados a capacidades de negócio.

```text
Sistema de e-commerce
├── Customer Service
├── Order Service
├── Payment Service
├── Inventory Service
└── Notification Service
```

Cada serviço deve possuir:

* responsabilidade de negócio bem definida;
* implantação independente;
* banco ou modelo de dados sob seu controle;
* comunicação por APIs ou eventos;
* ciclo de desenvolvimento próprio.

Microsserviço não significa apenas dividir uma aplicação Spring Boot em vários projetos.

---

## 2. Quando utilizar

Microsserviços são adequados quando existem:

* domínios de negócio bem definidos;
* necessidade de escalar partes isoladamente;
* times independentes;
* diferentes ritmos de evolução;
* alta necessidade de disponibilidade;
* sistema suficientemente complexo.

Não são indicados automaticamente para aplicações pequenas, pois introduzem:

* complexidade operacional;
* falhas de rede;
* consistência eventual;
* observabilidade distribuída;
* maior custo de infraestrutura;
* dificuldade de testes integrados.

---

# 3. Spring Boot como base

Cada microsserviço geralmente é uma aplicação Spring Boot independente.

```java
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```

Estrutura comum:

```text
order-service
├── controller
├── application
├── domain
├── infrastructure
├── repository
├── messaging
└── configuration
```

O Spring Boot oferece:

* configuração automática;
* servidor embutido;
* injeção de dependência;
* integração com banco;
* APIs REST;
* mensageria;
* observabilidade;
* segurança;
* health checks.

---

# 4. Decomposição por domínio

A divisão deve seguir capacidades de negócio, e não apenas entidades ou tabelas.

Divisão inadequada:

```text
Customer Service
Address Service
Phone Service
Document Service
```

Divisão mais adequada:

```text
Customer Service
Order Service
Payment Service
Delivery Service
```

Uma referência importante é o **Bounded Context**, do Domain-Driven Design.

Cada serviço deve possuir seu próprio modelo e vocabulário.

```text
Payment Service
- pagamento
- autorização
- estorno
- liquidação
```

```text
Order Service
- pedido
- item
- status
- entrega
```

---

# 5. Banco de dados por serviço

Cada microsserviço deve controlar seus próprios dados.

```text
Order Service   → Order Database
Payment Service → Payment Database
Customer Service → Customer Database
```

Um serviço não deve consultar diretamente as tabelas de outro serviço.

Evite:

```sql
SELECT *
FROM payment.payments
JOIN orders.orders;
```

Prefira:

* API REST;
* eventos;
* projeções locais;
* replicação controlada;
* consultas agregadas em serviços próprios.

## Benefícios

* menor acoplamento;
* autonomia;
* evolução independente;
* isolamento de falhas;
* possibilidade de tecnologias diferentes.

## Consequência

Não existe transação ACID única entre vários serviços.

Isso leva a:

* consistência eventual;
* compensações;
* eventos;
* Saga;
* Transactional Outbox.

---

# 6. Comunicação síncrona

A comunicação síncrona normalmente ocorre via HTTP ou gRPC.

```text
Order Service
    ↓ HTTP
Payment Service
```

## RestClient

```java
@Service
public class PaymentClient {

    private final RestClient restClient;

    public PaymentClient(RestClient.Builder builder) {
        this.restClient = builder
            .baseUrl("http://payment-service")
            .build();
    }

    public PaymentResponse createPayment(
        PaymentRequest request
    ) {
        return restClient.post()
            .uri("/payments")
            .body(request)
            .retrieve()
            .body(PaymentResponse.class);
    }
}
```

Outras opções:

* `WebClient`;
* OpenFeign;
* gRPC.

## Riscos

Toda chamada remota pode apresentar:

* timeout;
* indisponibilidade;
* lentidão;
* resposta parcial;
* falha de rede;
* duplicação;
* incompatibilidade de contrato.

Por isso, configure sempre:

* timeout de conexão;
* timeout de resposta;
* retry controlado;
* circuit breaker;
* observabilidade.

---

# 7. Comunicação assíncrona

Na comunicação assíncrona, um serviço publica eventos e outros serviços os consomem.

```text
Order Service
    ↓ order-created
Kafka
 ├── Payment Service
 ├── Inventory Service
 └── Notification Service
```

Exemplo:

```java
public record OrderCreatedEvent(
    UUID eventId,
    UUID orderId,
    UUID customerId,
    BigDecimal total
) {
}
```

## Benefícios

* menor acoplamento temporal;
* maior resiliência;
* escalabilidade;
* processamento independente;
* melhor absorção de picos.

## Desafios

* consistência eventual;
* duplicação;
* eventos fora de ordem;
* reprocessamento;
* versionamento de contratos;
* monitoramento de lag;
* tratamento de falhas.

---

# 8. API Gateway

O API Gateway centraliza a entrada das requisições externas.

```text
Cliente
   ↓
API Gateway
├── Order Service
├── Customer Service
└── Payment Service
```

Responsabilidades adequadas:

* roteamento;
* autenticação;
* rate limiting;
* correlação de requisições;
* logs;
* filtros;
* TLS;
* políticas de acesso.

Exemplo com Spring Cloud Gateway:

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/orders/**
```

O gateway não deve concentrar regras de negócio.

---

# 9. Service Discovery

Service Discovery permite localizar instâncias dinamicamente.

```text
Order Service
      ↓
Service Registry
      ↓
Payment Service instances
```

Soluções tradicionais:

* Eureka;
* Consul;
* Zookeeper.

Em Kubernetes, isso normalmente é resolvido por:

* Services;
* DNS interno;
* balanceamento do cluster.

Em ambientes Kubernetes, Eureka pode ser desnecessário.

---

# 10. Load balancing

Quando existem múltiplas instâncias, as chamadas devem ser distribuídas.

```text
Payment Service
├── Instance 1
├── Instance 2
└── Instance 3
```

O balanceamento pode ocorrer:

* no cliente;
* no gateway;
* no Kubernetes;
* em um load balancer externo.

No ecossistema Spring, o Spring Cloud LoadBalancer pode realizar balanceamento no cliente.

---

# 11. Resiliência

## Timeout

Define quanto tempo esperar por uma dependência.

```text
Sem timeout:
Thread fica bloqueada indefinidamente
```

Timeouts devem ser definidos de acordo com o SLA da operação.

## Retry

Repete falhas temporárias.

Use apenas quando:

* a falha for transitória;
* a operação for idempotente;
* houver limite de tentativas;
* existir backoff e jitter.

Não aplique retry indiscriminadamente em toda exceção.

## Circuit Breaker

Interrompe chamadas para uma dependência degradada.

```text
CLOSED → OPEN → HALF_OPEN
```

## Bulkhead

Limita recursos utilizados por uma dependência.

Exemplo:

```text
Payment Client: máximo 20 chamadas simultâneas
Notification Client: máximo 10 chamadas simultâneas
```

Isso evita que uma dependência lenta esgote todas as threads ou conexões.

## Rate Limiter

Controla a quantidade de requisições por período.

## Fallback

Entrega uma resposta alternativa quando possível.

Fallback não deve ocultar falhas críticas nem retornar dados incorretos.

---

# 12. Resilience4j

Exemplo de circuit breaker:

```java
@CircuitBreaker(
    name = "paymentService",
    fallbackMethod = "fallback"
)
public PaymentResponse createPayment(
    PaymentRequest request
) {
    return paymentClient.createPayment(request);
}

private PaymentResponse fallback(
    PaymentRequest request,
    Throwable error
) {
    return PaymentResponse.unavailable();
}
```

Configuração:

```yaml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        sliding-window-size: 20
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10s
```

A configuração deve ser baseada em métricas reais, e não em valores arbitrários.

---

# 13. Consistência distribuída

Uma operação distribuída pode envolver vários serviços.

Exemplo:

```text
1. Criar pedido
2. Reservar estoque
3. Processar pagamento
4. Confirmar pedido
```

Uma transação local não cobre todos esses passos.

```java
@Transactional
public void createOrder() {
    repository.save(order);
    paymentClient.pay();
}
```

O `@Transactional` protege apenas o banco local. Ele não desfaz automaticamente uma chamada HTTP ou evento Kafka.

---

# 14. Saga

Saga coordena uma sequência de transações locais.

## Coreografia

Cada serviço reage a eventos.

```text
Order Created
    ↓
Inventory Reserved
    ↓
Payment Approved
    ↓
Order Confirmed
```

Vantagens:

* baixo acoplamento;
* boa escalabilidade;
* arquitetura orientada a eventos.

Desvantagens:

* fluxo difícil de visualizar;
* maior complexidade de observabilidade;
* risco de dependências implícitas entre eventos.

## Orquestração

Um orquestrador controla o fluxo.

```text
Order Saga
├── reservar estoque
├── processar pagamento
└── confirmar pedido
```

Vantagens:

* fluxo centralizado;
* melhor visibilidade;
* compensações explícitas.

Desvantagens:

* risco de centralização excessiva;
* orquestrador mais complexo.

---

# 15. Transações compensatórias

Quando uma etapa falha, operações anteriores podem precisar ser compensadas.

```text
Estoque reservado
Pagamento rejeitado
→ liberar estoque
```

Compensação não é necessariamente rollback.

Exemplos:

* cancelar reserva;
* emitir estorno;
* cancelar pedido;
* liberar saldo;
* marcar operação para análise.

A compensação pode falhar e também precisa de retry, idempotência e monitoramento.

---

# 16. Transactional Outbox

Problema:

```java
orderRepository.save(order);
kafkaTemplate.send("order-created", event);
```

Cenário de falha:

```text
Banco confirma
Kafka falha
→ pedido salvo sem evento
```

Com Outbox:

```text
Mesma transação
├── INSERT order
└── INSERT outbox_event
```

Depois:

```text
Outbox
   ↓
Publisher ou CDC
   ↓
Kafka
```

Isso garante que a alteração e o registro do evento sejam persistidos atomicamente no banco local.

O consumidor ainda deve ser idempotente.

---

# 17. Idempotência

Uma operação idempotente pode ser executada várias vezes sem produzir efeitos adicionais.

Exemplo:

```java
@Transactional
public void process(OrderCreatedEvent event) {
    if (processedEventRepository.existsById(event.eventId())) {
        return;
    }

    createPayment(event);
    processedEventRepository.save(
        new ProcessedEvent(event.eventId())
    );
}
```

Use também uma constraint única:

```sql
CREATE UNIQUE INDEX uk_processed_event
ON processed_event(event_id);
```

Apenas verificar antes de inserir pode sofrer condição de corrida.

A constraint deve ser a garantia final.

---

# 18. Contratos de API

APIs são contratos entre serviços.

Boas práticas:

* versionamento controlado;
* compatibilidade retroativa;
* DTOs específicos;
* validação;
* documentação OpenAPI;
* códigos HTTP consistentes;
* tratamento padronizado de erros.

Exemplo de erro:

```json
{
  "code": "PAYMENT_NOT_AUTHORIZED",
  "message": "Pagamento não autorizado",
  "traceId": "c74e2d9f"
}
```

Evite expor diretamente:

* entidades JPA;
* stack traces;
* detalhes internos;
* mensagens do banco;
* estrutura interna do domínio.

---

# 19. Evolução de contratos

Mudanças incompatíveis podem quebrar consumidores.

Mudanças mais seguras:

* adicionar campo opcional;
* manter campos antigos durante migração;
* suportar duas versões temporariamente;
* usar contract testing;
* versionar eventos.

Mudanças perigosas:

* renomear campos;
* alterar tipo;
* remover atributos;
* mudar significado de valores;
* alterar enum sem considerar consumidores.

---

# 20. Observabilidade distribuída

Em microsserviços, logs isolados não são suficientes.

São necessários:

## Logs

Logs estruturados com:

* `traceId`;
* `spanId`;
* nome do serviço;
* ambiente;
* identificador da operação;
* código de erro.

## Métricas

Principais métricas:

* throughput;
* taxa de erro;
* latência p50, p95 e p99;
* uso de CPU e memória;
* pool de conexões;
* circuit breaker aberto;
* retries;
* consumer lag;
* tamanho de filas.

## Traces

Permitem acompanhar uma requisição entre serviços.

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

Ferramentas comuns:

* Micrometer;
* OpenTelemetry;
* Prometheus;
* Grafana;
* Jaeger;
* Zipkin;
* Elastic Stack.

---

# 21. Spring Boot Actuator

O Actuator disponibiliza informações operacionais.

Endpoints comuns:

```text
/actuator/health
/actuator/metrics
/actuator/prometheus
/actuator/info
```

Configuração:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
```

## Liveness

Indica se o processo está funcional.

Uma falha de dependência externa normalmente não deve derrubar a liveness.

## Readiness

Indica se a aplicação está pronta para receber tráfego.

Pode considerar:

* conexão com banco;
* configuração carregada;
* dependências essenciais;
* inicialização concluída.

---

# 22. Segurança

Cada serviço deve validar identidade e autorização conforme sua responsabilidade.

Estratégias comuns:

* OAuth 2.0;
* OpenID Connect;
* JWT;
* mTLS;
* API Gateway;
* service accounts;
* autorização por escopo ou papel.

Fluxo comum:

```text
Cliente
   ↓ JWT
Gateway
   ↓ JWT validado
Microsserviço
   ↓ valida escopos e permissões
```

Não confie apenas na rede interna.

Também é necessário:

* criptografia em trânsito;
* rotação de credenciais;
* gerenciamento de segredos;
* menor privilégio;
* auditoria;
* proteção de dados sensíveis.

---

# 23. Configuração centralizada

Configurações não devem ficar fixadas no código.

Exemplo:

```yaml
payment:
  endpoint: ${PAYMENT_ENDPOINT}
  timeout: ${PAYMENT_TIMEOUT:3s}
```

Opções:

* Spring Cloud Config;
* Kubernetes ConfigMap;
* Kubernetes Secret;
* AWS Parameter Store;
* AWS Secrets Manager;
* HashiCorp Vault.

Segredos não devem ser armazenados em Git.

---

# 24. Deployment e escalabilidade

Cada serviço deve ser implantado de forma independente.

```text
Order Service
├── pod 1
├── pod 2
└── pod 3

Payment Service
├── pod 1
└── pod 2
```

Escalabilidade horizontal depende de serviços preferencialmente stateless.

Estado de sessão deve ficar em:

* banco;
* Redis;
* token;
* armazenamento compartilhado.

Evite manter estado relevante apenas na memória da instância.

---

# 25. Testes

## Testes unitários

Validam regras de negócio isoladamente.

## Testes de integração

Validam:

* banco;
* Kafka;
* Redis;
* integrações;
* configurações Spring.

Ferramentas:

* Spring Boot Test;
* Testcontainers;
* WireMock;
* Awaitility.

## Contract testing

Valida se produtor e consumidor continuam compatíveis.

Ferramentas:

* Spring Cloud Contract;
* Pact.

## Testes end-to-end

Devem existir em quantidade controlada, pois são:

* mais lentos;
* mais frágeis;
* mais caros;
* difíceis de diagnosticar.

---

# 26. Anti-patterns

## Monólito distribuído

Serviços separados fisicamente, mas altamente dependentes.

```text
Order → Customer → Payment → Inventory → Delivery
```

Qualquer falha interrompe toda a cadeia.

## Banco compartilhado

Vários serviços acessando as mesmas tabelas.

Isso elimina autonomia e aumenta acoplamento.

## Cadeias síncronas longas

Cada chamada adicional aumenta:

* latência;
* probabilidade de falha;
* dificuldade de rastreamento;
* consumo de recursos.

## Entidade compartilhada

Compartilhar um mesmo modelo de domínio entre serviços cria acoplamento de código e negócio.

## Biblioteca comum excessiva

Bibliotecas compartilhadas devem conter apenas elementos realmente transversais.

Evite compartilhar:

* entidades JPA;
* regras de domínio;
* contratos internos mutáveis;
* serviços de negócio.

## Microsserviço muito pequeno

Serviços excessivamente fragmentados aumentam custo sem entregar autonomia real.

---

# 27. O que dominar em nível sênior

Lucas, para nível sênior, domine:

* decomposição por domínio;
* Bounded Context;
* banco por serviço;
* comunicação síncrona e assíncrona;
* API Gateway;
* Service Discovery;
* load balancing;
* timeout, retry, circuit breaker e bulkhead;
* idempotência;
* consistência eventual;
* Saga;
* Transactional Outbox;
* contratos e versionamento;
* observabilidade distribuída;
* segurança entre serviços;
* health checks;
* escalabilidade horizontal;
* testes de contrato;
* anti-patterns;
* trade-offs entre monólito e microsserviços.

---

# Comparação rápida

| Tema            | Responsabilidade              |
| --------------- | ----------------------------- |
| Spring Boot     | Construção do serviço         |
| Spring Cloud    | Infraestrutura distribuída    |
| Kafka           | Comunicação assíncrona        |
| Resilience4j    | Resiliência                   |
| Actuator        | Saúde e métricas              |
| OpenTelemetry   | Rastreamento distribuído      |
| Spring Security | Autenticação e autorização    |
| JPA             | Persistência local            |
| Kubernetes      | Orquestração e escalabilidade |

---

# Mapa mental

```text
Microsserviços + Spring
├── Decomposição
│   ├── Domínio
│   ├── Bounded Context
│   └── Banco por serviço
│
├── Comunicação
│   ├── REST
│   ├── gRPC
│   ├── Kafka
│   └── Contratos
│
├── Spring
│   ├── Spring Boot
│   ├── Spring Cloud
│   ├── Spring Security
│   ├── Actuator
│   └── Data JPA
│
├── Resiliência
│   ├── Timeout
│   ├── Retry
│   ├── Circuit Breaker
│   ├── Bulkhead
│   └── Rate Limiter
│
├── Consistência
│   ├── Saga
│   ├── Compensação
│   ├── Outbox
│   └── Idempotência
│
├── Observabilidade
│   ├── Logs
│   ├── Métricas
│   ├── Traces
│   └── Alertas
│
└── Operação
    ├── Kubernetes
    ├── Escalabilidade
    ├── Configuração
    ├── Segurança
    └── Deployment
```

> Um microsserviço maduro não é apenas uma API Spring Boot isolada. Ele precisa possuir autonomia de domínio e dados, comunicação resiliente, contratos evolutivos, observabilidade, segurança e capacidade de operar corretamente diante de falhas parciais.
