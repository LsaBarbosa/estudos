# Timeouts em sistemas distribuídos com Java e Spring

Lucas, **timeout não significa que a operação falhou**. Significa que uma camada deixou de esperar pela conclusão.

Esse é o ponto mais importante:

```text
O cliente desistiu de esperar ≠ o servidor parou de processar
```

Uma requisição pode atingir o timeout no cliente enquanto o servidor continua executando, grava no banco e confirma a transação. Se o cliente repetir a chamada, pode produzir efeitos duplicados.

---

# 1. Visão geral

Uma chamada HTTP pode passar por várias etapas:

```text
Cliente
  │
  ├── aquisição de conexão do pool
  ├── resolução DNS
  ├── conexão TCP
  ├── handshake TLS
  ├── envio da requisição
  │
  ▼
Servidor
  ├── fila de requisições
  ├── execução do controller
  ├── regra de negócio
  ├── chamada a outro serviço
  ├── aquisição de conexão com banco
  ├── espera por lock
  ├── execução da query
  └── envio da resposta
```

Cada etapa pode ter um timeout diferente.

| Timeout                  | O que limita                                        |
| ------------------------ | --------------------------------------------------- |
| Connect timeout          | Tempo para estabelecer conexão                      |
| Pool acquisition timeout | Tempo esperando uma conexão disponível no pool      |
| Read timeout             | Tempo sem receber dados pela conexão                |
| Request timeout          | Tempo permitido para uma requisição                 |
| Transaction timeout      | Tempo permitido para a transação                    |
| Query timeout            | Tempo de execução de uma consulta                   |
| Lock timeout             | Tempo esperando um lock                             |
| Deadline global          | Tempo máximo da operação completa                   |
| Cancelamento             | Tentativa de interromper trabalho ainda em execução |

---



# 10. RestClient com Java 21

Para código bloqueante e Spring MVC, pode-se utilizar `RestClient`.

Exemplo usando o `HttpClient` do Java:

```java
import java.net.http.HttpClient;
import java.time.Duration;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.client.JdkClientHttpRequestFactory;
import org.springframework.web.client.RestClient;

@Configuration
public class RestClientConfig {

    @Bean
    RestClient paymentRestClient() {
        HttpClient httpClient = HttpClient.newBuilder()
                .connectTimeout(Duration.ofMillis(500))
                .build();

        JdkClientHttpRequestFactory factory =
                new JdkClientHttpRequestFactory(httpClient);

        factory.setReadTimeout(Duration.ofSeconds(2));

        return RestClient.builder()
                .baseUrl("https://payment-service")
                .requestFactory(factory)
                .build();
    }
}
```

O `JdkClientHttpRequestFactory` é baseado no `HttpClient` do Java. Na implementação atual do Spring, seu `setReadTimeout` é aplicado por meio do timeout da requisição do `HttpRequest`. Isso demonstra por que é importante verificar a semântica da implementação subjacente: o nome “read timeout” não possui comportamento idêntico em todas as bibliotecas. ([Home][9])

---

# 11. Timeout do banco de dados

“Timeout do banco” não é um único timeout.

Existem pelo menos cinco limites diferentes.

---

## 11.1 Timeout para obter conexão do pool

No HikariCP:

```yaml
spring:
  datasource:
    hikari:
      connection-timeout: 500
```

Isso significa:

```text
A aplicação esperará até 500 ms para obter uma conexão disponível do pool.
```

Não é o tempo para:

* conectar fisicamente no PostgreSQL;
* executar uma query;
* esperar um lock;
* finalizar uma transação.

O HikariCP define `connectionTimeout` como o tempo máximo que o cliente espera por uma conexão disponível no pool. Quando o pool está cheio e não existem conexões ociosas, `getConnection()` aguarda até esse limite. ([GitHub][10])

---

## 11.2 Timeout da conexão física com o banco

É o tempo para abrir uma nova conexão TCP com o servidor PostgreSQL.

No PostgreSQL/libpq existe o parâmetro:

```text
connect_timeout
```

O driver JDBC possui suas próprias propriedades correspondentes. Esse timeout é diferente do tempo de aquisição da conexão no HikariCP. ([PostgreSQL][11])

---

## 11.3 Query timeout

Limita a duração da execução de uma consulta.

Com JPA:

```java
import jakarta.persistence.QueryHint;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.jpa.repository.QueryHints;

public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
        select o
        from Order o
        where o.customerId = :customerId
        """)
    @QueryHints(
        @QueryHint(
            name = "jakarta.persistence.query.timeout",
            value = "1500"
        )
    )
    List<Order> findByCustomerId(Long customerId);
}
```

O hint de timeout existe no Jakarta Persistence e é interpretado pelo provider, como Hibernate. O suporte efetivo também depende do driver e do banco. ([Hibernate Docs][12])

---

## 11.4 Transaction timeout

No Spring:

```java
@Transactional(timeout = 3)
public void confirmOrder(UUID orderId) {
    // Operação transacional
}
```

O valor de `timeout` é expresso em segundos.

Ele limita a duração da transação conforme o suporte oferecido pelo gerenciador de transações subjacente. Quando não configurado, o Spring usa o timeout padrão da infraestrutura ou nenhum timeout caso não exista suporte. ([Home][13])

Não trate isso como substituto universal para:

* timeout de query;
* timeout de lock;
* timeout de socket;
* deadline global.

---

## 11.5 Lock timeout

Uma query pode ser rápida, mas ficar bloqueada esperando outra transação.

Exemplo:

```text
T1 atualiza pedido 100 e mantém lock
T2 tenta atualizar pedido 100
T2 fica esperando
```

No PostgreSQL:

```sql
SET LOCAL lock_timeout = '300ms';
```

Se o lock não for obtido nesse período, a instrução é abortada.

Também é possível limitar a duração da instrução:

```sql
SET LOCAL statement_timeout = '1500ms';
```

O PostgreSQL diferencia claramente:

* `statement_timeout`: duração total da instrução;
* `lock_timeout`: tempo esperando a aquisição de um lock;
* `transaction_timeout`: duração da sessão dentro de uma transação;
* `idle_in_transaction_session_timeout`: tempo ocioso dentro de uma transação aberta. ([PostgreSQL][14])

Exemplo completo:

```sql
BEGIN;

SET LOCAL lock_timeout = '300ms';
SET LOCAL statement_timeout = '1500ms';

UPDATE orders
SET status = 'CONFIRMED'
WHERE id = 100;

COMMIT;
```

---

# 12. Cancelamento da operação

Cancelamento significa solicitar que um trabalho pare.

Não significa que ele necessariamente parará imediatamente.

## Formas de cancelamento

### Cancelamento reativo

```java
webClient.get()
        .uri("/resource")
        .retrieve()
        .bodyToMono(Resource.class)
        .timeout(Duration.ofSeconds(2));
```

Quando ocorre timeout, a assinatura reativa é cancelada. A implementação tenta interromper o trabalho de rede relacionado.

### Cancelamento com `Future`

```java
Future<Result> future = executor.submit(this::executeOperation);

boolean cancelled = future.cancel(true);
```

`cancel(true)` tenta interromper a thread caso a tarefa já tenha começado. A tarefa precisa responder corretamente à interrupção. O retorno de `cancel` não garante, por si só, que toda a computação e seus efeitos externos tenham sido interrompidos. ([Oracle Docs][15])

### `CompletableFuture.orTimeout`

```java
CompletableFuture<Result> future =
        CompletableFuture
                .supplyAsync(this::executeOperation)
                .orTimeout(2, TimeUnit.SECONDS);
```

Isso completa o `CompletableFuture` excepcionalmente com `TimeoutException`.

Entretanto, o `CompletableFuture` não controla diretamente a computação subjacente. Sua documentação informa que cancelamento é tratado como uma forma de conclusão excepcional, não como garantia de interrupção da tarefa que originou o resultado. ([Oracle Docs][16])

Portanto:

```text
orTimeout() pode parar a espera
sem necessariamente parar a execução real.
```

---

# 13. Cancelamento do cliente não garante cancelamento no banco

Considere:

```text
Cliente → Serviço A → PostgreSQL
```

1. Cliente chama o serviço.
2. Serviço inicia uma query.
3. Cliente atinge timeout.
4. A conexão HTTP é encerrada.
5. A query continua no PostgreSQL.
6. A transação realiza commit.

Isso pode acontecer porque o cancelamento precisa ser propagado entre todas as camadas:

```text
Cliente
  ↓ cancelamento
Servidor HTTP
  ↓ cancelamento
Thread ou pipeline reativo
  ↓ cancelamento
Driver JDBC
  ↓ cancelamento
Banco de dados
```

Se uma camada não coopera, o processamento pode continuar.

Por isso, para operações críticas, combine:

* deadline global;
* timeout HTTP;
* timeout transacional;
* query timeout;
* lock timeout;
* cancelamento cooperativo;
* idempotência.

---

# 14. Timeout e retry

Retry aumenta o tempo total.

Configuração:

```text
Timeout por tentativa: 1 segundo
Número de tentativas: 3
Backoff: 200 ms e 400 ms
```

Tempo potencial:

```text
1.000 ms
+ 200 ms
+ 1.000 ms
+ 400 ms
+ 1.000 ms
= 3.600 ms
```

Se o deadline global for `2 segundos`, essa política é inválida.

O retry deve respeitar:

```text
tempoRestanteNoDeadline
```

Pseudocódigo:

```java
Duration remaining = deadline.remaining();

if (remaining.compareTo(Duration.ofMillis(300)) < 0) {
    throw new DeadlineExceededException();
}

Duration attemptTimeout = min(
        Duration.ofMillis(700),
        remaining.minusMillis(100)
);
```

## Retry após timeout

Timeout produz resultado desconhecido.

Antes de repetir, avalie:

* a operação é idempotente?
* existe chave de idempotência?
* a tentativa anterior pode ter realizado commit?
* o servidor fornece consulta de status?
* o erro é transitório?
* ainda existe orçamento no deadline?

---

# 15. Configuração de referência

Considere uma API que deve responder em até três segundos.

```yaml
spring:
  mvc:
    async:
      request-timeout: 2800ms

  datasource:
    hikari:
      connection-timeout: 300

server:
  tomcat:
    connection-timeout: 5s
```

Cliente HTTP:

```text
Pool acquisition timeout:  200 ms
Connect timeout:            500 ms
Response/read timeout:    1.000 ms
Deadline da operação:     2.500 ms
```

Banco:

```text
Pool acquisition:         300 ms
Lock timeout:             200 ms
Query timeout:            800 ms
Transaction timeout:    2.000 ms
```

Esses valores são apenas uma referência estrutural. Os números reais devem ser definidos com base em:

* latência p95 e p99;
* SLO da API;
* distância de rede;
* complexidade da operação;
* comportamento em picos;
* capacidade do pool;
* taxa de erro;
* orçamento de retry.

---

# 16. Erros comuns

## Configurar apenas connect timeout

A conexão é criada rapidamente, mas a resposta pode demorar indefinidamente.

## Configurar apenas read timeout

Uma resposta lenta que envia pequenos blocos periodicamente pode permanecer ativa por muito tempo.

## Usar o mesmo timeout em todas as camadas

Todas as camadas expiram juntas, impedindo rollback organizado e resposta controlada.

## Acreditar que timeout cancela o servidor

O cliente pode desistir enquanto o servidor continua executando.

## Colocar retry sem deadline

O tempo total cresce de forma imprevisível.

## Usar retry em operação não idempotente

Pode duplicar pagamento, pedido, reserva ou envio.

## Confundir Hikari `connection-timeout` com query timeout

Ele controla a espera por uma conexão do pool.

## Manter transação aberta durante chamada HTTP

```java
@Transactional
public void confirmOrder() {
    orderRepository.save(order);

    paymentClient.process(); // chamada externa dentro da transação
}
```

Se a chamada externa demorar, a transação e possivelmente locks e conexão do banco permanecem ocupados.

---

# 17. Resposta para entrevista sênior

> Connect timeout limita o estabelecimento da conexão; read timeout limita a espera por dados; e request timeout limita uma requisição conforme a semântica da biblioteca. Para controlar a operação distribuída completa, eu utilizo um deadline global e propago o orçamento restante entre os serviços.
>
> Também diferencio timeout do cliente e do servidor. Quando o cliente atinge timeout, isso não garante que o servidor ou o banco interromperam o processamento. Portanto, timeout gera resultado desconhecido, e qualquer retry precisa considerar idempotência.
>
> No banco, separo timeout para obter conexão do pool, conexão física, execução de query, espera por lock e duração da transação. Em Spring, posso configurar os timeouts do WebClient ou RestClient, usar timeout transacional e aplicar query ou lock timeout no banco. A regra é que timeouts internos expirem antes do deadline externo, permitindo cancelamento, rollback e resposta controlada.

---

# 18. Mapa mental

```text
TIMEOUTS
│
├── Cliente HTTP
│   ├── Pool acquisition timeout
│   ├── DNS timeout
│   ├── Connect timeout
│   ├── TLS timeout
│   ├── Write timeout
│   ├── Read/response timeout
│   └── Request timeout
│
├── Servidor
│   ├── Connection timeout
│   ├── Async request timeout
│   ├── Business operation timeout
│   └── Deadline restante
│
├── Banco de dados
│   ├── Pool connection timeout
│   ├── Physical connection timeout
│   ├── Query/statement timeout
│   ├── Lock timeout
│   ├── Transaction timeout
│   └── Idle transaction timeout
│
├── Deadline global
│   ├── Limite fim a fim
│   ├── Propagado entre serviços
│   ├── Reduzido a cada etapa
│   └── Controla retries
│
└── Cancelamento
    ├── É cooperativo
    ├── Pode não interromper o servidor
    ├── Pode não interromper a query
    ├── Timeout gera resultado desconhecido
    └── Exige idempotência
```

[1]: https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.Builder.html?utm_source=chatgpt.com "HttpClient.Builder (Java SE 11 & JDK 11 )"
[2]: https://projectreactor.io/docs/netty/release/api/reactor/netty/http/client/HttpClient.html?utm_source=chatgpt.com "HttpClient (reactor-netty 1.3.6)"
[3]: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/time/class-use/Duration.html?utm_source=chatgpt.com "Uses of Class java.time.Duration (Java SE 11 & JDK 11 )"
[4]: https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html?utm_source=chatgpt.com "Asynchronous Requests"
[5]: https://docs.spring.io/spring-boot/appendix/application-properties/index.html?utm_source=chatgpt.com "Common Application Properties"
[6]: https://docs.spring.io/spring-framework/reference/web/webflux-webclient.html?utm_source=chatgpt.com "WebClient :: Spring Framework"
[7]: https://projectreactor.io/docs/netty/release/reference/http-client.html?utm_source=chatgpt.com "HTTP Client :: Reactor Netty Reference Guide"
[8]: https://projectreactor.io/docs/core/3.6.0-RC1/reference?utm_source=chatgpt.com "Reactor 3 Reference Guide"
[9]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/JdkClientHttpRequestFactory.html?utm_source=chatgpt.com "JdkClientHttpRequestFactory (Spring Framework 7.0.8 API)"
[10]: https://github.com/brettwooldridge/hikaricp?utm_source=chatgpt.com "GitHub - brettwooldridge/HikariCP: 光 HikariCP・A solid, high-performance, JDBC connection pool at last. · GitHub"
[11]: https://www.postgresql.org/docs/current/libpq-connect.html?utm_source=chatgpt.com "18: 32.1. Database Connection Control Functions"
[12]: https://docs.hibernate.org/orm/7.0/javadocs/org/hibernate/jpa/SpecHints.html?utm_source=chatgpt.com "SpecHints (Hibernate Javadocs) - Index of /"
[13]: https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html?utm_source=chatgpt.com "Using @Transactional"
[14]: https://www.postgresql.org/docs/current/runtime-config-client.html?utm_source=chatgpt.com "Documentation: 18: 19.11. Client Connection Defaults"
[15]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/FutureTask.html?utm_source=chatgpt.com "FutureTask (Java SE 21 & JDK 21)"
[16]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/CompletableFuture.html?utm_source=chatgpt.com "CompletableFuture (Java SE 21 & JDK 21)"
