# FASE 15 — Testes

Lucas, para nível Senior/Tech Lead, o ponto principal não é saber escrever `@Test`. É saber montar uma estratégia que dê **feedback rápido, confiança sobre integrações reais e proteção contra regressões**.

A ideia central é:

```text
Unit Test
    ↓
regra isolada e rápida

Integration Test
    ↓
integração real entre componentes

E2E
    ↓
fluxo completo do usuário
```

Testcontainers é especialmente relevante porque permite validar seu código contra tecnologias reais, como PostgreSQL, Kafka e Redis, em ambientes descartáveis e reproduzíveis. 

## 1. Conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Test Pyramid** | Estratégia que privilegia muitos testes rápidos nas camadas inferiores e poucos testes caros no topo. | Se aplicada rigidamente pode ignorar que alguns sistemas precisam de mais testes de integração. | Organizar estratégia de testes de uma aplicação backend. |
| **Unit Test** | Testa uma unidade de comportamento isoladamente, normalmente sem banco, rede ou infraestrutura externa. | Muito rápido, mas pode dar falsa confiança se todas as dependências importantes estiverem mockadas. | Testar regras de cálculo, validações, services e domínio. |
| **Integration Test** | Testa a integração real entre componentes, frameworks ou infraestrutura. | Mais lento e mais caro que unit test. | Repository + PostgreSQL, producer + Kafka. |
| **E2E Test** | Testa o sistema pelo fluxo mais próximo possível do uso real. | Mais lento, mais frágil e difícil de diagnosticar. | Criar pedido → pagar → consultar resultado. |
| **JUnit 5** | Framework/plataforma principal para estruturar e executar testes Java modernos. | Fácil de usar, mas sozinho não resolve mocks ou infraestrutura. | `@Test`, lifecycle, parametrização e extensões. |
| **Mockito** | Biblioteca para criar mocks, stubs e verificar interações. | Mock excessivo acopla o teste à implementação e pode esconder problemas reais. | Simular `PaymentGateway` em um unit test. |
| **Mock** | Objeto falso controlado pelo teste para substituir uma dependência. | Não valida contrato/integridade da implementação real. | Simular serviço externo ou repository no teste unitário. |
| **Stub** | Define respostas preestabelecidas de uma dependência. | Pode criar cenários irreais quando configurado excessivamente. | `when(repository.findById()).thenReturn(...)`. |
| **Spy** | Envolve um objeto real permitindo observar ou sobrescrever parte de seu comportamento. | Pode deixar o teste confuso e fortemente acoplado à implementação. | Casos específicos em código legado. |
| **AssertJ** | Biblioteca de assertions fluentes e expressivas para Java. | Adiciona outra dependência, mas melhora bastante legibilidade. | Validar objetos, collections, exceptions e campos. |
| **Testcontainers** | Cria containers descartáveis durante testes para executar dependências reais. | Mais lento que mocks e requer runtime compatível com containers. | PostgreSQL, Kafka, Redis, RabbitMQ. |
| **PostgreSQL Testcontainer** | Executa PostgreSQL real para testes. | Startup é mais caro que banco em memória. | Testar JPA, migrations, SQL, constraints e locks. |
| **Kafka Testcontainer** | Executa um broker Kafka real nos testes. | Testes ficam mais pesados e assíncronos. | Producer, consumer, serializers e tópicos. |
| **Redis Testcontainer** | Executa Redis real através de container. | Mais lento que fake/in-memory implementation. | Cache, TTL, serialization, distributed locks. |
| **RabbitMQ Testcontainer** | Executa broker RabbitMQ real para integração. | Mais infraestrutura durante o teste. | Exchanges, queues, routing e acknowledgements. |
| **WireMock** | Simula servidores HTTP controlando requests e responses. | Não garante que o serviço remoto real continue obedecendo ao contrato. | Simular API de pagamento, timeout, 500 e respostas específicas. |
| **Test Fixture** | Conjunto conhecido de dados e estado utilizado pelo teste. | Fixtures gigantes ficam difíceis de entender e manter. | Criar Customer e Order para cenário específico. |
| **Parameterized Test** | Executa o mesmo teste com diferentes entradas. | Casos demais podem dificultar diagnóstico. | Validar várias combinações de regras de negócio. |
| **Flaky Test** | Teste que passa e falha sem mudança funcional no código. | Destrói confiança na suíte e no pipeline. | Race conditions, sleeps, dependência de horário/rede. |

JUnit 5 é composto por Platform, Jupiter e Vintage; Mockito fornece criação de mocks, stubbing e verificação de interações; e AssertJ fornece assertions fluentes com mensagens de erro mais expressivas. 

---

# 2. Pirâmide de testes

O modelo clássico é:

```text
             E2E
            /   \
       Integration
       /         \
      Unit Tests
```

A ideia não é decorar uma proporção exata.

É entender o custo:

```text
Unit
 ↓
muito rápido
baixo custo
diagnóstico fácil


Integration
 ↓
mais realista
mais lento


E2E
 ↓
maior confiança no fluxo completo
maior custo
mais fragilidade
```

Por isso não queremos validar cada regra de negócio exclusivamente por E2E.

Se uma regra simples pode ser comprovada por um unit test em milissegundos, não existe motivo para subir a aplicação inteira apenas para validá-la.

---

# 3. Unit Tests

Considere:

```java
class DiscountCalculator {

    BigDecimal calculate(Customer customer) {
        // regra
    }
}
```

O teste pode executar:

```text
input
 ↓
regra
 ↓
output
```

sem:

```text
Spring Context
PostgreSQL
Kafka
HTTP
Docker
```

Esse é um bom unit test.

Características:

```text
rápido
determinístico
isolado
fácil de diagnosticar
```

---

# 4. Mockito

Imagine:

```java
class OrderService {

    private final PaymentGateway paymentGateway;
}
```

Em um unit test não queremos necessariamente chamar o sistema de pagamento real.

Podemos usar:

```java
PaymentGateway gateway =
        mock(PaymentGateway.class);
```

e definir:

```java
when(gateway.authorize(any()))
        .thenReturn(APPROVED);
```

Depois verificamos comportamento.

Mockito foi projetado justamente para criação de mocks, stubbing e verificação de interações. 

---

# 5. Mock demais é um problema

Considere um teste:

```text
OrderController mock

OrderService mock

OrderRepository mock

Kafka mock

Database mock

PaymentClient mock
```

O teste passa.

Mas:

```text
query SQL está errada

migration está errada

serialização Kafka está errada

constraint está errada
```

e você não descobre nada disso.

Por isso:

> **Mocks aumentam isolamento, mas diminuem fidelidade.**

A própria documentação do Mockito recomenda não mockar tudo nem tipos que você não controla indiscriminadamente. 

---

# 6. O que eu costumo mockar

Em unit tests, normalmente:

```text
externo à unidade
        ↓
mock
```

Por exemplo:

```text
OrderService

├── OrderRepository → mock
├── PaymentGateway → mock
└── EventPublisher → mock
```

Assim testo especificamente:

```text
regra do OrderService
```

Mas quando quero validar:

```text
Repository
+
Hibernate
+
Migration
+
PostgreSQL
```

mock não resolve.

Aí entra integração real.

---

# 7. AssertJ

Em vez de assertions pouco expressivas:

```java
assertEquals("APPROVED", result.status());
```

podemos escrever:

```java
assertThat(result.status())
        .isEqualTo(APPROVED);
```

Collections:

```java
assertThat(orders)
        .hasSize(2)
        .extracting(Order::status)
        .containsExactly(APPROVED, PENDING);
```

Exceptions:

```java
assertThatThrownBy(() -> service.process())
        .isInstanceOf(BusinessException.class)
        .hasMessageContaining("payment");
```

AssertJ é focado em assertions fluentes, legibilidade e mensagens de falha úteis. 

---

# 8. Integration Tests

Integration Test verifica se componentes reais funcionam juntos.

Por exemplo:

```text
Spring Data JPA
      ↓
Hibernate
      ↓
PostgreSQL
```

ou:

```text
Kafka Producer
      ↓
Kafka Broker
      ↓
Kafka Consumer
```

Aqui não quero testar apenas:

```text
minha lógica Java
```

Quero testar:

```text
minha lógica
+
framework
+
configuração
+
infraestrutura
```

---

# 9. Por que Testcontainers é tão importante

Historicamente alguém poderia utilizar:

```text
H2
```

para testar código que em produção utiliza:

```text
PostgreSQL
```

O problema é:

```text
H2
≠
PostgreSQL
```

Podem existir diferenças em:

```text
SQL
tipos
constraints
indexes
locking
functions
JSONB
dialect
transactions
```

Testcontainers permite executar:

```text
PostgreSQL real
```

dentro de um container descartável.

A documentação do projeto destaca exatamente esse benefício: banco real oferece compatibilidade maior do que uma substituição em memória, embora tenha custo maior de execução. 

---

# 10. PostgreSQL com Testcontainers

Conceitualmente:

```java
@Container
static PostgreSQLContainer<?> postgres =
        new PostgreSQLContainer<>("postgres:17");
```

Fluxo:

```text
JUnit
  ↓
Testcontainers
  ↓
PostgreSQL container
  ↓
Spring Boot
  ↓
Repository
  ↓
teste
```

Podemos validar:

```text
Flyway/Liquibase
JPA mappings
constraints
native queries
transactions
locks
indexes funcionais
```

Testcontainers possui módulo específico para PostgreSQL. 

---

# 11. Exemplo importante: constraint real

Imagine:

```sql
UNIQUE (idempotency_key)
```

Você quer garantir que:

```text
payment-order-123
```

não seja inserido duas vezes.

Um mock de repository:

```java
when(repository.save(...))
```

não prova essa constraint.

Um PostgreSQL real prova.

Por isso, para sistemas distribuídos e idempotência, testes de integração com banco real têm bastante valor.

---

# 12. Kafka com Testcontainers

Podemos levantar Kafka real:

```text
Test
 ↓
KafkaContainer
 ↓
Broker real
```

Depois testar:

```text
Producer
   ↓
Kafka
   ↓
Consumer
```

Isso permite validar:

```text
serializer
deserializer
topic
consumer
producer
headers
partition key
event contract
```

Testcontainers possui suporte específico para Kafka e consegue iniciar e gerenciar o broker durante o teste. 

---

# 13. Redis com Testcontainers

O quickstart oficial do Testcontainers usa justamente Redis como exemplo.

Em vez de depender de:

```text
Redis instalado no localhost
```

o teste cria:

```text
Redis container
```

com porta dinâmica.

Isso evita:

```text
dependência do ambiente do desenvolvedor
conflito de portas
estado compartilhado entre testes
```

e fornece uma instância conhecida para cada suíte. 

---

# 14. Por que não depender de infraestrutura local

Evite:

```text
"Para executar os testes,
instale PostgreSQL,
Kafka e Redis
na sua máquina."
```

Isso cria:

```text
Developer A
PostgreSQL 16

Developer B
PostgreSQL 17

CI
PostgreSQL 15
```

e diferentes configurações.

Testcontainers aproxima o cenário de:

```text
Infrastructure as Code
```

também para testes.

A imagem define a infraestrutura necessária.

---

# 15. WireMock

Testcontainers não substitui WireMock.

Eles resolvem problemas diferentes.

Imagine sua aplicação chamando:

```text
Payment Provider
```

Você não controla o provider externo.

Nos testes você não quer:

```text
fazer pagamentos reais
depender da internet
depender da disponibilidade do terceiro
```

Então WireMock cria um servidor HTTP controlado:

```text
Application
     ↓
WireMock
```

Podemos simular:

```text
200 OK

400 Bad Request

500 Internal Server Error

timeout

response lenta

headers

payloads específicos
```

WireMock trabalha através de stubs que associam critérios de request a responses controladas. 

---

# 16. Testcontainers x WireMock x Mockito

Essa diferença deve ficar automática:

```text
Mockito
   ↓
simula objeto Java


WireMock
   ↓
simula servidor HTTP


Testcontainers
   ↓
executa infraestrutura real
```

Por exemplo:

### Unit test

```text
PaymentGateway
→ Mockito
```

### Integration test do HTTP client

```text
PaymentClient
→ WireMock
```

### Integration test do banco

```text
Repository
→ PostgreSQL Testcontainer
```

### Integration test de mensageria

```text
Producer + Consumer
→ Kafka Testcontainer
```

---

# 17. E2E

Um teste E2E poderia validar:

```text
POST /orders
      ↓
Order Service
      ↓
Database
      ↓
Kafka
      ↓
Payment
      ↓
GET /orders/{id}
```

Ele verifica um fluxo próximo ao comportamento real.

Isso dá muita confiança.

Mas custa mais:

```text
startup
infraestrutura
rede
sincronização
dados
diagnóstico
```

Por isso E2E deve normalmente ficar concentrado nos **fluxos críticos**, e não em todas as combinações possíveis.

---

# 18. O perigo dos testes frágeis

Um teste assim:

```java
Thread.sleep(5000);
```

e depois:

```java
assertThat(messageWasConsumed).isTrue();
```

é candidato a flaky test.

Talvez:

```text
na minha máquina
5 segundos é suficiente
```

mas:

```text
CI sobrecarregado
→ não é
```

Prefira esperar por uma **condição**, com timeout controlado, em vez de esperar um número arbitrário de segundos.

---

# 19. Teste deve verificar comportamento, não implementação

Imagine:

```java
verify(repository).save(order);
verify(mapper).map(order);
verify(logger).info(...);
verify(eventFactory).create(...);
```

Seu teste está extremamente acoplado ao fluxo interno.

Uma refatoração que preserve perfeitamente o comportamento pode quebrar o teste.

Prefira verificar:

```text
entrada
   ↓
comportamento
   ↓
resultado observável
```

Verificação de interação é útil quando **a interação é parte do comportamento**, por exemplo:

```text
pagamento não deve
ser chamado duas vezes
```

---

# 20. O que precisa ser testado com infraestrutura real

Para um backend Java moderno, eu priorizaria integração real para:

```text
JPA / Hibernate
      ↓
PostgreSQL

Flyway / Liquibase
      ↓
PostgreSQL

Kafka Producer / Consumer
      ↓
Kafka

Redis integration
      ↓
Redis

RabbitMQ Producer / Consumer
      ↓
RabbitMQ
```

Porque boa parte dos bugs está justamente na fronteira:

```text
Java
↔
infraestrutura
```

---

# 21. Teste de Repository

Uma estratégia útil:

```text
Repository
   ↓
Hibernate
   ↓
PostgreSQL Testcontainer
```

Testar:

```text
query funciona?

mapping funciona?

constraint funciona?

fetch funciona?

migration é válida?

transaction funciona?
```

Isso oferece muito mais confiança do que:

```text
mock(repository)
```

para testar o próprio repository.

---

# 22. Testes e CI/CD

A suíte também deve considerar custo.

Um pipeline pode ser:

```text
Commit
   ↓
Unit Tests
   ↓
Integration Tests
   ↓
Build
   ↓
E2E / Smoke Tests
```

Os testes mais rápidos dão feedback primeiro.

Se:

```text
unit test falhou
```

não faz sentido gastar tempo executando toda a infraestrutura E2E.

---

# 23. O mapa mental mais importante

Para memorizar:

```text
UNIT TEST
   ↓
A regra funciona isoladamente?


INTEGRATION TEST
   ↓
Meus componentes funcionam juntos?


E2E
   ↓
O fluxo completo funciona?
```

E:

```text
JUnit
   ↓
estrutura os testes


Mockito
   ↓
mocka objetos Java


AssertJ
   ↓
faz assertions


WireMock
   ↓
simula HTTP


Testcontainers
   ↓
executa dependências reais
```

---

# 24. Estratégia que eu usaria

Para uma aplicação Spring Boot:

```text
Regra de negócio
      ↓
JUnit + AssertJ
      ↓
Mockito quando necessário


Repository
      ↓
PostgreSQL Testcontainers


Kafka
      ↓
Kafka Testcontainers


Redis
      ↓
Redis Testcontainers


HTTP externo
      ↓
WireMock


Fluxos críticos
      ↓
E2E
```

O objetivo é combinar:

**velocidade nos testes pequenos + confiança nos testes de integração.**

---

# Resposta objetiva para entrevista

> Eu trabalho com testes em diferentes níveis. Unit tests validam regras de negócio de forma rápida e isolada, normalmente usando JUnit, AssertJ e Mockito quando preciso substituir dependências. Integration tests validam a integração real entre aplicação, frameworks e infraestrutura, enquanto E2E fica mais concentrado nos fluxos críticos do sistema.
>
> Eu uso Mockito com cuidado porque mockar tudo pode gerar testes rápidos, mas com baixa fidelidade. Um repository mockado, por exemplo, não valida SQL, mappings Hibernate, migrations ou constraints do banco.
>
> Por isso considero Testcontainers especialmente importante. Ele permite executar tecnologias reais durante os testes, como PostgreSQL, Kafka e Redis, em containers descartáveis e reproduzíveis. Isso aproxima bastante o ambiente de teste daquilo que realmente existe em produção. 
>
> Para integrações HTTP externas, utilizo WireMock para simular respostas, erros e latência sem depender do serviço real.
>
> Também evito testes excessivamente acoplados à implementação e flaky tests baseados em `sleep`. Prefiro testar comportamento observável e manter os testes determinísticos.
>
> Então, para mim, uma boa estratégia de testes combina **unit tests rápidos para regras, integration tests reais nas fronteiras críticas e poucos E2E para validar jornadas importantes**, buscando confiança sem tornar o pipeline lento e instável.
