# 1. Kafka is a distributed event log organized into topics

> Kafka is a distributed event log organized into topics.

# What is a topic?

##### A topic is simply a category of events.

#### Spring Example

Producer

```java
kafkaTemplate.send("pedidos", pedido);
```

Consumer

```java
@KafkaListener(topics = "pedidos")
public void consumir(Pedido pedido){
}
```

---

# Each topic is divided into partitions
- Each partition has its own log.
- They enable parallelism, increasing throughput.
- The partition key determines which partition the message is written to (establishing ordering).
- Each partition has a leader broker.

#### The offset identifies the event's position **within the partition**. 
- Each partition has its own sequence. 
- Ordering is guaranteed only within the same partition. 
- Consumers control the offset (committing messages that have been processed).
- In Spring Kafka, this can be automatic (`enable-auto-commit`) or manual via `Acknowledgment`, allowing greater control over when an event is considered processed.


Visually:
#### Partition
```
+-----------+ +-----------+
|Partition0 | |Partition0 |
+-----------+ +-----------+
```
#### Key
```java
kafkaTemplate.send("pedidos", pedidoId, pedido);
```
pedidoId is the key.

---

# 4. The leader broker writes the event

> The leader broker for that partition writes the event sequentially to the log.

- Each partition has a leader broker.

- Every producer communicates only with the leader—never directly with the replicas.

- The leader replicates data to other brokers; if it fails, a new leader is promoted.

#### ISR (In-Sync Replicas)

- Depending on the configuration (`acks` and `min.insync.replicas`), the producer may wait for confirmation from all in-sync replicas before considering the write successful. ---

# Consumer Group
### Important rule
#### Within the same group:
- **A partition can only be processed by one consumer at a time.**

- This prevents duplicate processing within the same group.
#### Different groups
- Each group maintains its own offsets.
- Consumers from distinct groups can consume from the same partition.
---

## Message remains stored after consumption


This enables:

* replay
* auditing
* reprocessing
* recovery after failures
* new consumers reading old events

> Kafka removes messages only when the retention policy is met.

---

# Complete Kafka flow

```text
Producer (Spring Boot)
│
kafkaTemplate.send("orders", key, order)
│
▼
Partition selection (key hash)
│
▼
Partition Leader Broker
│
Writes sequentially to the log
│
▼
Replicates to Follower Brokers
│
▼
Event receives an Offset in the partition
│
▼
Consumer Group receives the partition
│
▼
@KafkaListener processes the event in Java
│
▼
Consumer confirms (commits) the Offset
│
▼
Event remains stored until retention expires
```

# Relationship with Spring Boot and Java

In Spring Boot development:

* **Producer (`KafkaTemplate`)**: publishes events to topics.
* **Kafka Broker**: receives, persists, and replicates events.
* **Topic**: organizes events by business domain.
* **Partition**: enables parallelism and scalability.
* **Offset**: identifies the position of each event in the partition.
* **Consumer (`@KafkaListener`)**: processes the events.
* **Consumer Group**: distributes partitions among consumers to scale processing.
* **Offset Commit**: records how far the consumer has processed events.
* **Retention**: keeps events available for replay and auditing even after consumption. These concepts form the foundation for understanding more advanced Kafka features, such as **delivery guarantees (at-most-once, at-least-once, and exactly-once), producer idempotence, transactions, rebalancing, log compaction, key-based partitioning, Dead Letter Topics (DLT), and integration with Spring Kafka for error handling and retries**.
