# Apache Kafka — Resumo para Engenheiro Sênior

## 1. O que é Kafka

O **Apache Kafka** é uma plataforma distribuída de eventos usada para:

* mensageria assíncrona;
* integração entre serviços;
* processamento de eventos;
* streaming de dados;
* auditoria;
* construção de arquiteturas orientadas a eventos.

Diferente de uma fila tradicional, o Kafka mantém os eventos armazenados por um período configurável, mesmo depois de consumidos.

```text
Producer → Topic → Consumer
```

---

# 2. Componentes principais

## Producer

O **producer** publica eventos em um tópico.

```java
kafkaTemplate.send("payment-created", paymentId, event);
```

O producer pode controlar:

* tópico;
* chave da mensagem;
* partição;
* serialização;
* confirmação de escrita;
* retries.

## Consumer

O **consumer** lê os eventos publicados.

```java
@KafkaListener(
    topics = "payment-created",
    groupId = "notification-service"
)
public void consume(PaymentCreatedEvent event) {
    // processar evento
}
```

## Broker

Um **broker** é uma instância do Kafka.

Um cluster normalmente possui vários brokers:

```text
Kafka Cluster
├── Broker 1
├── Broker 2
└── Broker 3
```

## Topic

Um **topic** representa uma categoria de eventos.

Exemplos:

```text
order-created
payment-approved
customer-updated
```

Um tópico é dividido em partições.

---

# 3. Partições

As **partições** permitem paralelismo e escalabilidade.

```text
Topic: orders
├── Partition 0
├── Partition 1
└── Partition 2
```

Cada partição possui um log ordenado de eventos.

O Kafka garante ordenação apenas **dentro da mesma partição**, não em todo o tópico.

## Chave da mensagem

Mensagens com a mesma chave normalmente são enviadas para a mesma partição.

```java
kafkaTemplate.send(
    "orders",
    order.getCustomerId(),
    event
);
```

Isso é importante quando eventos relacionados precisam manter ordem.

```text
customerId=123
├── order-created
├── order-paid
└── order-shipped
```

Todos podem cair na mesma partição.

---

# 4. Offset

O **offset** identifica a posição de uma mensagem dentro da partição.

```text
Partition 0
Offset 0 → Evento A
Offset 1 → Evento B
Offset 2 → Evento C
```

O consumer mantém o controle de qual offset já processou.

## Commit de offset

O commit informa ao Kafka até onde o consumer processou.

Pode ser:

* automático;
* manual;
* síncrono;
* assíncrono.

Em aplicações críticas, o commit manual oferece maior controle.

```java
@KafkaListener(topics = "orders")
public void consume(
    OrderEvent event,
    Acknowledgment acknowledgment
) {
    process(event);
    acknowledgment.acknowledge();
}
```

O offset deve ser confirmado somente depois que o processamento for concluído corretamente.

---

# 5. Consumer Group

Consumers com o mesmo `group.id` pertencem ao mesmo grupo.

Dentro do grupo, cada partição é consumida por apenas um consumer por vez.

```text
Topic com 3 partições

Consumer Group A
├── Consumer 1 → Partition 0
├── Consumer 2 → Partition 1
└── Consumer 3 → Partition 2
```

Se existirem mais consumers do que partições, alguns ficarão ociosos.

```text
3 partições
5 consumers
→ apenas 3 consumers trabalharão
```

Grupos diferentes recebem os mesmos eventos de forma independente.

```text
payment-approved
├── Notification Service
├── Fraud Service
└── Accounting Service
```

Cada serviço deve possuir seu próprio `group.id`.

---

# 6. Replicação

Cada partição pode possuir réplicas em diferentes brokers.

```text
Partition 0
├── Leader: Broker 1
├── Replica: Broker 2
└── Replica: Broker 3
```

## Leader

O leader recebe leituras e escritas da partição.

## Followers

Os followers replicam os dados do leader.

Se o leader falhar, uma réplica elegível pode assumir sua função.

## Replication factor

```text
replication-factor = 3
```

Significa que existem três cópias da partição.

Maior replicação aumenta a disponibilidade, mas também aumenta:

* uso de disco;
* tráfego de rede;
* custo operacional.

---

# 7. Confirmação de escrita

O producer configura o nível de confirmação usando `acks`.

## `acks=0`

O producer não espera confirmação.

* menor latência;
* maior risco de perda.

## `acks=1`

Espera confirmação apenas do leader.

* equilíbrio entre desempenho e segurança;
* pode haver perda se o leader falhar antes da replicação.

## `acks=all`

Espera confirmação das réplicas necessárias.

* maior durabilidade;
* maior latência.

Para eventos críticos:

```properties
acks=all
enable.idempotence=true
```

---

# 8. Garantias de entrega

## At-most-once

A mensagem pode ser perdida, mas não será processada duas vezes.

```text
Commit do offset
↓
Processamento
```

Se o processamento falhar depois do commit, o evento é perdido.

## At-least-once

A mensagem não deve ser perdida, mas pode ser processada mais de uma vez.

```text
Processamento
↓
Commit do offset
```

É a abordagem mais comum.

Exige consumidores idempotentes.

## Exactly-once

Busca evitar perdas e duplicações dentro de um fluxo Kafka.

Pode envolver:

* producer idempotente;
* transações Kafka;
* Kafka Streams;
* controle de offsets.

Entretanto, exactly-once no Kafka não garante automaticamente exatamente uma alteração em sistemas externos, como bancos de dados ou APIs.

---

# 9. Idempotência

Como eventos podem ser reenviados, o consumer deve tolerar duplicações.

Exemplo com identificador único:

```java
@Transactional
public void process(PaymentEvent event) {
    if (processedEventRepository.existsById(event.eventId())) {
        return;
    }

    paymentService.process(event);
    processedEventRepository.save(
        new ProcessedEvent(event.eventId())
    );
}
```

Também é recomendável uma constraint única no banco:

```sql
CREATE UNIQUE INDEX uk_processed_event
ON processed_event(event_id);
```

A verificação apenas em memória não funciona adequadamente em ambientes distribuídos.

---

# 10. Retry e Dead Letter Topic

## Retry

Falhas temporárias podem ser tratadas com novas tentativas.

Exemplos:

* indisponibilidade momentânea;
* timeout;
* falha transitória de banco;
* dependência externa instável.

Use:

* limite de tentativas;
* backoff;
* jitter;
* métricas;
* idempotência.

## Dead Letter Topic

Eventos que continuam falhando podem ser enviados para um tópico separado.

```text
orders
   ↓ erro
orders-retry
   ↓ erro
orders-dlt
```

No Spring Kafka:

```java
@RetryableTopic(
    attempts = "4",
    backoff = @Backoff(delay = 1000, multiplier = 2),
    dltTopicSuffix = "-dlt"
)
@KafkaListener(topics = "orders")
public void consume(OrderEvent event) {
    process(event);
}
```

A DLT não deve ser apenas um depósito de erros. Deve possuir:

* monitoramento;
* alertas;
* processo de investigação;
* política de reprocessamento.

---

# 11. Rebalanceamento

O **rebalance** acontece quando as partições são redistribuídas entre os consumers.

Pode ocorrer quando:

* um consumer entra no grupo;
* um consumer sai;
* um consumer falha;
* o número de partições muda;
* o consumer demora demais para processar.

Durante o rebalance, o consumo pode ser temporariamente interrompido.

Configurações importantes:

```properties
max.poll.interval.ms
session.timeout.ms
heartbeat.interval.ms
max.poll.records
```

Processamentos longos dentro do listener podem causar rebalances desnecessários.

---

# 12. Retenção e compactação

## Retention

O Kafka pode manter eventos por tempo ou tamanho.

```properties
retention.ms=604800000
```

Exemplo: manter eventos por sete dias.

Depois do período de retenção, os segmentos antigos podem ser removidos.

## Log compaction

Na compactação, o Kafka mantém o registro mais recente para cada chave.

```text
customer-123 → nome antigo
customer-123 → nome atualizado
```

Depois da compactação:

```text
customer-123 → nome atualizado
```

É útil para:

* estado atual de entidades;
* caches distribuídos;
* reconstrução de estado;
* Change Data Capture.

---

# 13. Schema e evolução de eventos

Eventos são contratos entre produtores e consumidores.

Uma alteração incompatível pode quebrar vários serviços.

Formatos comuns:

* JSON;
* Avro;
* Protobuf;
* JSON Schema.

Em ambientes maduros, utiliza-se um **Schema Registry** para:

* versionar schemas;
* validar compatibilidade;
* evitar alterações incompatíveis;
* controlar evolução dos contratos.

Exemplo de evolução compatível:

```json
{
  "eventId": "abc",
  "customerId": "123",
  "email": "cliente@email.com"
}
```

Adicionar um campo opcional normalmente é mais seguro do que remover ou renomear campos existentes.

---

# 14. Kafka com Spring Boot

## Producer

```java
@Service
public class OrderEventProducer {

    private final KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;

    public OrderEventProducer(
        KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate
    ) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void publish(OrderCreatedEvent event) {
        kafkaTemplate.send(
            "order-created",
            event.orderId().toString(),
            event
        );
    }
}
```

## Consumer

```java
@Component
public class OrderEventConsumer {

    @KafkaListener(
        topics = "order-created",
        groupId = "payment-service"
    )
    public void consume(OrderCreatedEvent event) {
        // processar evento
    }
}
```

## Configuração básica

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092

    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer

    consumer:
      group-id: payment-service
      auto-offset-reset: earliest
      enable-auto-commit: false
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
```

---

# 15. Transactional Outbox

Um problema comum ocorre quando a aplicação precisa atualizar o banco e publicar um evento.

```java
repository.save(order);
kafkaTemplate.send("order-created", event);
```

Possíveis falhas:

```text
Banco confirmado
Kafka falha
→ pedido criado sem evento
```

Ou:

```text
Kafka publicado
Banco falha
→ evento publicado para operação inexistente
```

O padrão **Transactional Outbox** grava a alteração de negócio e o evento na mesma transação do banco.

```text
Transação
├── INSERT order
└── INSERT outbox_event
```

Depois, outro processo publica o evento no Kafka.

```text
Outbox Table
    ↓
Publisher ou CDC
    ↓
Kafka
```

Esse padrão evita a inconsistência entre banco e broker, mas ainda exige idempotência no consumer.

---

# 16. Observabilidade

Métricas importantes:

| Métrica                     | Significado                                       |
| --------------------------- | ------------------------------------------------- |
| Consumer lag                | Distância entre mensagens produzidas e consumidas |
| Throughput                  | Quantidade de eventos por período                 |
| Retry rate                  | Volume de reprocessamentos                        |
| DLT rate                    | Volume de mensagens enviadas à DLT                |
| Processing time             | Tempo de processamento do evento                  |
| Rebalance count             | Frequência de redistribuições                     |
| Under-replicated partitions | Partições sem todas as réplicas sincronizadas     |

O **consumer lag** é uma das métricas mais importantes.

```text
Latest offset: 10.000
Consumer offset: 8.000
Lag: 2.000 mensagens
```

Lag crescente pode indicar:

* consumer lento;
* falha no processamento;
* poucas partições;
* poucas instâncias;
* dependência externa degradada;
* volume acima da capacidade.

---

# 17. Erros comuns

## Usar Kafka como chamada HTTP

O producer não deve publicar um evento e ficar bloqueado aguardando resposta como se fosse uma API REST.

Kafka é mais adequado para desacoplamento assíncrono.

## Ignorar duplicações

Mesmo com configurações robustas, consumidores devem ser idempotentes.

## Criar partições sem planejamento

Mais partições aumentam paralelismo, mas também aumentam:

* arquivos;
* conexões;
* replicação;
* rebalances;
* custo operacional.

## Usar uma única chave

Se todas as mensagens possuem a mesma chave, todas vão para a mesma partição, eliminando o paralelismo.

## Não monitorar lag

Um serviço pode continuar ativo enquanto acumula milhões de eventos atrasados.

## Colocar dados sensíveis nos eventos

Eventos podem permanecer armazenados, replicados e acessíveis por múltiplos consumidores. Dados pessoais devem seguir critérios de segurança e LGPD.

---

# 18. O que dominar em nível sênior

Lucas, para entrevistas e decisões arquiteturais, domine:

* producers, consumers, brokers e topics;
* partições, offsets e consumer groups;
* ordenação por chave;
* replicação e eleição de leader;
* `acks`, retries e producer idempotente;
* at-most-once, at-least-once e exactly-once;
* commit manual de offsets;
* idempotência no consumer;
* retry topics e DLT;
* consumer lag;
* rebalances;
* retenção e log compaction;
* evolução de schemas;
* Transactional Outbox;
* consistência eventual;
* observabilidade;
* segurança e autorização;
* dimensionamento de partições.

---

# Mapa mental para revisão

```text
Kafka
├── Estrutura
│   ├── Broker
│   ├── Topic
│   ├── Partition
│   └── Offset
│
├── Produção
│   ├── Producer
│   ├── Key
│   ├── acks
│   ├── Retry
│   └── Idempotência
│
├── Consumo
│   ├── Consumer
│   ├── Consumer Group
│   ├── Commit
│   ├── Rebalance
│   └── Consumer Lag
│
├── Garantias
│   ├── At-most-once
│   ├── At-least-once
│   └── Exactly-once
│
├── Resiliência
│   ├── Retry Topic
│   ├── Backoff
│   ├── DLT
│   └── Idempotência
│
├── Arquitetura
│   ├── Event-driven
│   ├── Consistência eventual
│   ├── Transactional Outbox
│   └── Schema Registry
│
└── Operação
    ├── Replicação
    ├── Retenção
    ├── Compactação
    ├── Monitoramento
    └── Segurança
```

## Frase de entrevista

> Kafka é uma plataforma distribuída de eventos baseada em logs particionados. A escalabilidade ocorre por partições e consumer groups, enquanto confiabilidade exige replicação, controle de offsets, idempotência, monitoramento de lag e tratamento adequado de retries e mensagens não processáveis.
