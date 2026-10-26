# Virtual Thread

> A virtual thread is lightweight and runs on top of a platform thread.
>
> When performing blocking I/O-bound operations, the JVM suspends the virtual thread and releases the platform thread.
>
> It enables support for a larger number of concurrent operations.
>
> They do not make CPU-bound operations faster and do not eliminate external limits, such as connection pools.

# Kafka


> It is a distributed event log organized into topics, and each topic is divided into partitions.
>
> The producer sends an event to a partition, typically based on a key.
>
> The leader broker for that partition writes the event sequentially to the log and replicates it to other brokers.
>
> Consumers read the events and track the offset up to which they have processed.

---
# Idempotency

> Idempotency means executing an operation repeatedly using the same identifier without producing additional effects.
>
> An idempotent producer prevents retries from generating duplicate events.
>
> An idempotent consumer produces the same effect whether the event is consumed once or multiple times.

---

# Consistencia

> First, I would define the consistency level that the business actually requires.
>
> Within a single database, I would use ACID transactions, constraints, and concurrency control. 
>
> For distributed workflows, I would use eventual consistency, business events, idempotent consumers, the Transactional Outbox Pattern, and, when necessary, Sagas with compensating actions.
---

# Distributed transaction

> An operation that spans different systems.
>
> A classic approach is the Two-Phase Commit.
>
> First, a coordinator asks if all participants are ready to commit. Then, if everyone responds positively, it sends the commit command. However, it creates coupling; I prefer the Saga pattern.
>
> In the Saga pattern, each service executes its own local transaction. If a later step fails, compensating actions are executed to logically undo the previous steps.
---

# Design a Microservice 

> I would start with the domain and business responsibility, not the infrastructure.
>
> The service should own its own data and have a well-defined API or event contract.
>
> Next, I would define non-functional requirements, such as availability, volume, latency, security, and consistency.
>
> I would also design for fault handling, idempotency, observability, contract versioning, testing, and deployment strategy right from the start.
>
> Before creating a new microservice, I would validate whether the autonomy and scalability justify the costs associated with a distributed system. In many scenarios, a modular monolith is a better solution.
