[senior_java_interview_concepts_2026.md](https://github.com/user-attachments/files/34930366/senior_java_interview_concepts_2026.md)
# Senior Java Developer — Focused Concept Review

**Interview target:** November 19, 2026 · Senior Java Developer · English-only interview · Remote/LATAM

**Purpose:** a condensed concept reference selected from the provided study notes and prioritized for the job description and your reported backend experience. This is **not** a scripted Q&A bank. Read the definitions, understand the constraints, and then explain each idea in your own English.

**Source distinction:** Sections 01–07 are **selected/adapted from your original material** (`Texto colado(4).txt`). Section 08 is **additional role-specific material**, written to cover gaps in the source and explicitly labeled as such. The additions are concepts to study, **not claims about work you personally performed**.

## Study sequence

| Day | Topics | Priority |
|---|---|---|
| **Nov 16** | §01 Java 8→21 + §02 Spring Boot/JPA + §08A–B migration gaps | **P0** |
| **Nov 17** | §03 Microservices/messaging + §04 SQL/Oracle + §05 testing + §08C/E | **P0** |
| **Nov 18** | §06 Git/cloud + §07 engineering design + §08D/F–H, then timed oral explanations | **P1** |

**Legend:** P0 = must explain and defend technically; P1 = should understand well; P2 = breadth only if time allows.

## Experience-to-requirement map

| Experience you reported | Concepts to emphasize |
|---|---|
| **Itaú — account-opening systems** | API reliability, logs, metrics, incident investigation, production troubleshooting |
| **Banco PAN — loan platform** | Spring Boot, Kafka, integrations, event delivery, automated testing, idempotency |
| **McDonald’s — legacy modernization** | Java 8→21, service boundaries, compatibility, N+1, Hibernate fetching, incremental migration |
| **Dell — current project** | Java 21 microservices, RabbitMQ, AWS, distributed observability, English team collaboration |

> Do not present a general architectural practice as a personal accomplishment unless you can describe what you actually implemented, measured or verified.

## Table of contents

- [§01 — Java 8 → 21, OOP, collections and runtime](#section-01)
- [§02 — Spring Boot, HTTP and JPA/Hibernate](#section-02)
- [§03 — Microservices, legacy modernization, messaging and reliability](#section-03)
- [§04 — SQL, Oracle and database performance](#section-04)
- [§05 — Automated testing, code quality and delivery](#section-05)
- [§06 — Git, containers, AWS and operational troubleshooting](#section-06)
- [§07 — Design quality, architecture decisions and maintainability](#section-07)
- [§08 — Role-specific complements (not in the original selection)](#section-08)
- [§09 — Final review checklist](#section-09)
- [References](#references)

<a id="section-01"></a>
## §01 — Java 8 → 21, OOP, collections and runtime [P0]

**Focus:** contrast language features with runtime behavior. An experienced engineer explains why a feature helps, and what it does *not* guarantee.

### 01.01 == vs equals

For primitives, `==` compares values; for references, it compares object identity. `equals` can express logical equality, but the implementation inherited from `Object` also compares identity. The `equals` method does not need to call `hashCode`.

### 01.02 equals, hashCode, and toString

`equals` defines logical equality, `hashCode` must respect that equality for hash-based structures, and `toString` provides useful diagnostic text. With mutable entities, I also avoid changing fields used by `hashCode` while the object is inside a `HashMap` or `HashSet`.

### 01.03 Encapsulation and Invariants

Encapsulation is more than marking fields `private`: it controls how state can change and protects invariants. I prefer exposing business operations rather than generic setters because this reduces invalid states and coupling to internal implementation.

### 01.04 Composition and Delegation

Composition represents a HAS-A relationship and builds behavior through dependencies. I normally prefer composition when reusing behavior or swapping implementations because it reduces the structural coupling introduced by inheritance.

### 01.05 Records, Sealed Classes, and Enums

Records synthesize components, accessors, and methods such as `equals` and `hashCode`, but they are only shallowly immutable. Sealed classes and interfaces restrict permitted subtypes. Enums represent a closed set of constants that may also have state and behavior.

### 01.06 Immutability and Defensive Copying

An immutable object does not change its observable state after construction. I use final fields, validation, and control of mutable references; a list inside a record still needs copying and attention to its elements to prevent external mutation.

### 01.07 Generics and Type Safety

Generics allow code reuse with compiler-checked types, such as `List<Pedido>`. They eliminate unnecessary casts, but `List<Integer>` is not a subtype of `List<Number>`; invariance prevents incompatible insertions.

### 01.08 Checked and Unchecked Exceptions

Checked exceptions must be handled or declared with `throws`. Unchecked exceptions derive from `RuntimeException` and have no such requirement; the choice should reflect the API contract. `Error` generally represents serious problems and should not be caught indiscriminately.

### 01.09 BigDecimal and Monetary Values

`BigDecimal` supports decimal arithmetic with explicit scale and rounding. For money, I avoid constructing it from `double` and specify the rounding policy; `equals` takes scale into account, while `compareTo` compares numeric value.

### 01.10 ArrayList and Complexity

ArrayList is backed by a dynamic array. Index access is O(1), and appending is amortized O(1). Inserting or removing in the middle is O(n) because elements may need to shift.

### 01.11 HashMap

HashMap is a hash-based key-value structure. Under normal conditions, `get` and `put` are O(1) on average. Good `hashCode` implementations and compliance with the `equals` contract are essential for correct behavior and performance.

### 01.12 Mutable Keys in HashMap and HashSet

Changing fields used by `equals` or `hashCode` after insertion can prevent an object from being found in its expected bucket. I prefer stable, immutable keys; equality and hashing must remain consistent while objects are stored.

### 01.13 Stream vs Collection

A Collection stores data; a Stream describes a processing pipeline over a source. Streams are generally consumed only once and do not modify their source by themselves; side effects inside lambdas can compromise results.

### 01.14 map vs flatMap

`map` transforms each element into another value. `flatMap` transforms each element into a stream and flattens the results, such as converting a list of orders into a single stream of their items.

### 01.15 Optional

`Optional` represents possible absence, mainly in return values. I use `map`, `flatMap`, and `orElseThrow` to express control flow; `orElseGet` computes a fallback on demand, whereas an argument passed to `orElse` is evaluated even when a value exists.

### 01.16 Parallel Streams

Parallel streams divide work but add coordination overhead and may contend for a shared pool. I assess data volume, associativity, absence of shared state, and real measurements; parallelizing blocking calls without controls can saturate dependencies.

### 01.17 Heap

The heap is a shared area where objects and arrays reside. It is managed by the garbage collector. If space is insufficient for new allocations and GC cannot reclaim enough memory, `OutOfMemoryError: Java heap space` may occur.

### 01.18 Stack and Local Variables

Each thread has a logical execution stack with method frames, local variables, and operands. Local references can point to heap objects; they are not class metadata stored in Metaspace. Excessive call depth can cause `StackOverflowError`.

### 01.19 Garbage Collector

The garbage collector manages heap memory. It identifies objects no longer reachable from GC Roots and reclaims their memory. Collectors such as G1 and ZGC use different strategies to balance throughput, latency, and resource consumption.

### 01.20 Memory Leaks in Java

A leak can happen when references keep objects alive longer than needed, despite GC. Examples include unbounded caches, forgotten listeners, and improper use of ThreadLocal; I would investigate retention and paths to GC Roots.

### 01.21 G1 vs ZGC

G1 aims to balance throughput and pause-time goals. ZGC prioritizes low latency and performs more work concurrently. Trade-offs should be measured under real workloads, observing CPU, memory, throughput, and latency SLOs.

### 01.22 JFR, JMC, and GC Logs

Java Flight Recorder collects runtime events with diagnostic-appropriate configuration, and Java Mission Control helps analyze them. GC logs show collection frequency, duration, and memory occupancy; I combine this evidence before tuning.

### 01.23 Atomicity, Visibility, and Ordering

Atomicity prevents observing a partially completed operation; visibility ensures updates are observed; ordering constrains how operations may be observed. These are distinct properties: seeing a counter does not make incrementing it atomic.

### 01.24 Race Condition

A race condition occurs when the outcome depends on an incorrect interleaving of tasks, such as two threads reading the same balance and writing conflicting updates. I protect compound operations and their invariants with synchronization or a design that avoids shared state.

### 01.25 ExecutorService and Thread Pools

ExecutorService separates task submission from execution and manages results and shutdown. With platform-thread pools, I size capacity, queues, and rejection policies; unbounded queues can turn overload into steadily increasing memory consumption.

### 01.26 CompletableFuture

CompletableFuture composes asynchronous operations without explicitly blocking at each step. It is useful for combining independent tasks, but overly complex flows can impair readability and observability.

### 01.27 synchronized

`synchronized` provides mutual exclusion and visibility guarantees through a monitor. It is straightforward for protecting critical sections, but high contention on the same monitor may reduce parallelism and throughput.

### 01.28 volatile

`volatile` guarantees visibility and memory ordering for a variable but does not make compound operations atomic. It works well for flags and simple states, but does not replace locks or atomics for read-modify-write operations.

### 01.29 Deadlock

Deadlock occurs when tasks wait cyclically for resources and none can progress. I use consistent acquisition order, small critical sections, and, when appropriate, timed attempts; thread dumps help identify the cycle.

### 01.30 Virtual Threads: Benefits and Limitations

Virtual threads mainly improve scalability for I/O-bound workloads. They do not speed up CPU-bound work or remove external limits. If a database has 30 connections, creating thousands of virtual threads does not increase that limit.

### 01.31 Virtual Threads and Concurrency Control

Virtual threads suit many blocking I/O tasks and are typically created per task rather than pooled for reuse. I limit access to scarce resources with semaphores or dependency connection pools instead of assuming unlimited concurrency.

---

<a id="section-02"></a>
## §02 — Spring Boot, HTTP and JPA/Hibernate [P0]

**Focus:** be ready to walk through an HTTP request, Spring proxy interception, the persistence context and the SQL produced by Hibernate.

### 02.01 IoC and Dependency Injection

IoC transfers control over object creation and management to the container. Dependency Injection is how the container supplies those dependencies. I prefer constructor injection because it makes the contract explicit and simplifies testing.

### 02.02 Constructor vs Field Injection

Constructor injection makes mandatory dependencies explicit and supports final fields and straightforward tests. Field injection hides part of the contract and relies on infrastructure to populate fields; I use setters when a dependency is genuinely optional and the design calls for it.

### 02.03 Beans, Containers, and Lifecycle

A bean is an object managed by the Spring container. Its lifecycle may include instantiation, dependency injection, post-processing, initialization, and destruction. Singleton scope applies per bean definition and container; it does not guarantee thread safety or justify storing request state in services.

### 02.04 Proxies and Self-invocation

Spring uses proxies for features such as transactions, caching, and security. The proxy intercepts bean calls and adds behavior. A classic pitfall is self-invocation because internal calls may not pass through the proxy.

### 02.05 AOP, JDK Proxies, and Class-based Proxies

AOP separates cross-cutting concerns such as transactions and metrics. In Spring's usual model, interface-based or subclass-based proxies intercept calls; final methods and classes may limit subclass proxies, and internal calls do not go through another proxy.

### 02.06 @Async and Execution Context

`@Async` delegates execution to an executor when calls pass through the configured mechanism. The caller's transaction and ThreadLocals are not automatically propagated to the other thread; I configure error handling, capacity, and context explicitly.

### 02.07 Starters and Dependency Management

Starters group dependencies for capabilities such as web or persistence; Boot's version management maintains a compatible set. A starter does not implement the feature by itself, and overriding versions requires compatibility checks.

### 02.08 Auto-configuration and Back-off

Auto-configuration is conditional. Boot examines the classpath, properties, and existing beans and registers defaults when appropriate. When I supply custom configuration, many auto-configurations back off.

### 02.09 @ConfigurationProperties vs @Value

For external configuration, I prefer `@ConfigurationProperties` for groups of properties. It improves typing, validation, and maintainability compared with scattering `@Value` annotations.

### 02.10 Actuator and Endpoint Security

Actuator provides operational endpoints and metrics integration. In production, I expose only what is necessary, secure sensitive endpoints, and use health/readiness/liveness checks and metrics for observability.

### 02.11 DispatcherServlet and HttpMessageConverter

DispatcherServlet receives the request, uses HandlerMapping to find a controller, and HandlerAdapter to invoke it. HttpMessageConverters transform HTTP request and response bodies into and from Java objects.

### 02.12 @RestControllerAdvice and Error Handling

I centralize HTTP exception handling with `@RestControllerAdvice`, translating application exceptions into consistent responses without exposing internal details.

### 02.13 Resources, URIs, and Contracts

I model resources with stable identifiers and clear input, output, and error contracts. I avoid directly exposing persistence entities because that couples the API to the database schema and may reveal unintended data.

### 02.14 Safe and Idempotent Methods

Safe methods such as `GET` do not request business-state changes. `PUT` and `DELETE` are idempotent by semantics, although responses may vary; `POST` and `PATCH` can implement idempotency mechanisms, but the methods do not guarantee idempotency.

### 02.15 HTTP Status Codes

`200` indicates success, `201` creation, and `204` success without a body; `400` invalid input, `401` missing valid authentication, `403` forbidden access, and `404` resource not found. `409` denotes conflict, `429` too many requests, and `5xx` server errors.

### 02.16 Idempotency Keys in APIs

An idempotency key identifies retries of the same operation. I record the key, result, and associated request atomically, defining expiration and concurrent-call handling; reuse of a key with different content should be detected.

### 02.17 Spring Data vs JPA vs Hibernate

JPA defines the ORM contract, Hibernate implements that contract, and Spring Data JPA adds repositories and Spring integration.

### 02.18 Persistence Context and Dirty Checking

The persistence context manages entities. When an entity is managed, the provider can detect changes through dirty checking and synchronize them during flush or commit.

### 02.19 Entity States

An entity can be transient, managed, detached, or removed. Dirty checking tracks managed entities; changing a detached entity does not guarantee an automatic update. Understanding state avoids treating `save` or `merge` as a universal fix.

### 02.20 persist, merge, and save

`persist` makes a new entity managed; `merge` copies state into a managed instance and returns that instance. In Spring Data JPA, `save` chooses an operation based on whether the entity is new; the object passed to `merge` does not automatically become managed.

### 02.21 Flush vs Commit

Flush synchronizes persistence-context changes with the database and may execute SQL before commit. Commit completes the transaction; a successful flush does not mean data is committed or that a later rollback is impossible.

### 02.22 @Transactional and Transaction Boundaries

`@Transactional` defines transactional boundaries and usually works through proxies. I also consider propagation, isolation, and rollback. I avoid unnecessary `REQUIRES_NEW` because it creates an independent transaction and may increase connection pool usage.

### 02.23 REQUIRED, REQUIRES_NEW, and NESTED

REQUIRED joins an existing transaction or creates one; REQUIRES_NEW creates a separate physical transaction. NESTED may use savepoints within the same transaction where supported. Support depends on the transaction manager and database; independence also increases connection demand.

### 02.24 Rollback: RuntimeException, Error, and Checked Exceptions

By default, Spring rolls back on `RuntimeException` and `Error`, but not checked exceptions. I can configure `rollbackFor` or other rules; catching and ignoring a failure may prevent the interceptor from recognizing that rollback is required. [Reference](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)

### 02.25 Self-invocation and @Transactional

In the default proxy-based mode, an internal call through `this` does not pass through the transactional interceptor. Annotating the called method therefore does not automatically create the expected transaction; I reorganize collaboration among beans or use explicit transaction control where appropriate. [Reference](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)

### 02.26 readOnly and Asynchronous Transactions

`readOnly=true` is an optimization hint, not a universal prohibition on writes. In thread-bound transactions, creating an asynchronous task does not transfer the caller's transaction; I define the transactional boundary in the flow actually doing the work.

### 02.27 N+1 and Fetching Strategies

N+1 occurs when a main query triggers many additional queries for relationships. I detect it using logs/APM and address it with fetch joins, EntityGraphs, projections, or dedicated queries as appropriate.

### 02.28 LAZY, EAGER, and LazyInitializationException

LAZY defers loading where supported; EAGER requires data to be made available but does not guarantee a single query. Accessing an uninitialized association outside the context can fail; I select fetching based on the use case instead of making everything EAGER.

### 02.29 Optimistic vs Pessimistic Locking

Optimistic locking detects conflicts through versions and works well when conflicts are rare. Pessimistic locking locks data in the database and may be needed when conflict costs are high, but it increases contention.

### 02.30 Projections, Specifications, and Auditing

Projections reduce the data loaded, Specifications help with dynamic filters, and auditing automates creation and modification metadata.

### 02.31 Authentication vs Authorization

Authentication verifies identity. Authorization decides whether that identity may access a resource or perform an operation.

### 02.32 SecurityFilterChain

Spring Security is filter-based on the Servlet stack. `SecurityFilterChain` specifies the mechanisms and rules applied before a request reaches Spring MVC.

### 02.33 JWT: Validation and Limitations

Decoding a JWT does not prove its authenticity. I verify the signature, allowed algorithm, issuer, audience, and expiration; a signed JWT is not necessarily encrypted, and revocation needs more than signature verification.

### 02.34 CSRF vs CORS

CSRF protection addresses forged requests when credentials are sent automatically by the browser. CORS is a browser policy governing cross-origin access and does not replace authentication or authorization.

---

<a id="section-03"></a>
## §03 — Microservices, legacy modernization, messaging and reliability [P0]

**Focus:** describe service boundaries, failure modes, event delivery semantics and how to avoid losing or duplicating business actions.

### 03.01 Architectural Trade-offs

Every architectural decision trades one quality for another. Stronger consistency may reduce availability; more abstraction may reduce coupling but add complexity; more services improve autonomy but increase operational burden. In interviews, I make the prioritized requirement and accepted cost explicit.

### 03.02 DDD and Architecture

Domain-Driven Design helps when business complexity is significant. I emphasize ubiquitous language, bounded contexts, entities, value objects, aggregates, and domain services. DDD does not require microservices: it also works in modular monoliths and is more about organizing the domain than choosing infrastructure.

### 03.03 Bounded Context

In DDD, a bounded context is a boundary within which a domain model has consistent meaning. In microservice architecture, it helps identify more natural service boundaries. The same term can have different models across contexts. I avoid sharing one domain model across services because that increases coupling.

### 03.04 Hexagonal Architecture

Hexagonal Architecture centers the system on the domain and uses ports and adapters to isolate infrastructure. Ports define incoming and outgoing contracts; adapters connect HTTP, databases, messaging, or external APIs. The main advantages are decoupling and testability. More classes and abstractions are the cost, so I use it where infrastructure independence brings value.

### 03.05 Monolith vs Microservices

I do not treat microservices as the mandatory next step for monoliths. A monolith reduces operational complexity and supports local consistency. Microservices provide autonomy, scaling, and isolation but introduce network failures and distributed consistency. The choice depends on organizational scale, independent domains, deployment frequency, availability requirements, and team operational maturity.

### 03.06 When to Extract a Microservice

I consider extraction when a clear domain boundary meets a concrete need, such as independent scaling, different release cadence, failure isolation, distinct security requirements, or ownership by another team. I do not extract solely because a class grew large. I first seek a stable boundary and measure the operational cost.

### 03.07 Synchronous vs Asynchronous Communication

Synchronous communication is simpler when the caller needs an immediate response, but creates temporal coupling and propagates latency and failures. Asynchronous communication reduces that coupling and absorbs spikes but requires eventual consistency, idempotency, and observability. I decide based on response needs and expected failure behavior.

### 03.08 Event vs Command

A command expresses an intention: `CriarPedido`. An event expresses an accomplished fact: `PedidoCriado`. A command normally has an intended recipient and may fail; an event may have zero or many consumers. Mixing these concepts often produces confusing messaging contracts.

### 03.09 Strong vs Eventual Consistency

Strong consistency needs a defined guarantee, such as linearizability: a read that starts after a completed write observes that write or a later one. Eventual consistency allows temporary divergence while promising convergence when no new updates occur. These concepts are not synonyms for database transaction isolation levels.

### 03.10 Saga

A Saga coordinates a distributed business transaction across local transactions, using compensating actions when steps fail. It can use choreography through events or orchestration through an explicit coordinator. It does not provide a global ACID rollback; it provides business consistency across services.

### 03.11 Outbox Pattern

The Outbox Pattern addresses updating a database and publishing an event without relying on a distributed transaction. The application stores the business change and an outbox record in one local transaction. A separate process later publishes the event. Trade-offs include asynchronous publication, deduplication, and outbox cleanup.

### 03.12 Idempotency

An idempotent operation can be retried without unintended additional effects. This matters in distributed systems because retries and redelivery occur. I use idempotency keys, unique constraints, processed-message records, or naturally idempotent business logic depending on the case.

### 03.13 Backward Compatibility

Backward compatibility means older consumers keep working after the producer evolves. For APIs and events, I prefer adding optional fields, avoiding abrupt removal or renaming, and coordinating changes in stages. This reduces synchronized deployments.

### 03.14 Strangler Fig Pattern

Strangler Fig gradually replaces a legacy system by routing features to new components while the legacy system continues running. Incremental migration reduces big-bang risk. The challenge is preserving integration and consistency during coexistence.

### 03.15 Anti-Corruption Layer

An Anti-Corruption Layer protects a domain from legacy or external models. Instead of spreading external types and rules throughout the application, I translate contracts into the internal model at a dedicated boundary. This reduces coupling and eases future replacement.

### 03.16 Service Boundaries

I define boundaries based on business capabilities and data ownership, not tables or technical layers. A well-defined service encapsulates its own model, rules, and persistence, reducing excessive cross-service calls.

### 03.17 Database per Service

Each microservice should ideally own its data. Directly sharing a schema introduces strong coupling and allows one service to depend on another's internal structure. Integration should happen through APIs or events.

### 03.18 Inbox and Idempotent Consumers

I store the received event ID and business change in the same local transaction, enforcing uniqueness to prevent duplicate processing. I acknowledge consumption only after commit; external effects need their own approach, such as another outbox or idempotency key.

### 03.19 Timeout

A timeout limits how long a service waits for a dependency. Without one, threads, connections, and other resources may remain blocked indefinitely. It must respect the request's overall time budget and be consistent with retries.

### 03.20 Unknown Outcome After Timeout

A timeout means the response missed its deadline, not that the server stopped executing the operation. For effects such as charging a payment, I check the state or retry using the same idempotency key before starting a new operation.

### 03.21 Retry

Retries are appropriate for transient failures when operations support re-execution. I limit attempts, use backoff and jitter, and check idempotency. Poorly configured retries can amplify outages.

### 03.22 Circuit Breaker

A circuit breaker temporarily stops calls to a dependency with a high failure rate, preventing wasted resources and cascading failures. It typically transitions through closed, open, and half-open states.

### 03.23 Bulkhead

Bulkheads isolate resources so one failing dependency cannot consume all application capacity. They can use separate pools, semaphores, or concurrency limits.

### 03.24 Cascading Failure

Cascading failure occurs when degradation in one service consumes dependent services' resources and spreads the problem. Timeouts, circuit breakers, bulkheads, rate limits, and graceful degradation can limit the impact.

### 03.25 Distributed Tracing

Distributed tracing shows a request's path across multiple services and the time spent in each step. It is especially useful for identifying dependencies that increase latency or cause failures.

### 03.26 P95 and P99

P95 and P99 are thresholds at or below which approximately 95% and 99% of observed latencies fall. I track them per operation and dependency alongside volume and errors; averaging percentiles or summing P99s does not produce end-to-end P99.

### 03.27 SLO and Error Budget

An SLO sets a reliability or latency target. An error budget represents how much error or downtime remains acceptable under that target. I use it to balance release velocity against reliability investment.

### 03.28 Independent Deployment

Independent deployment is a key microservices goal, but is real only when contracts, databases, configuration, and dependencies are also decoupled. If every deployment requires coordination, the architecture remains strongly coupled.

### 03.29 Topic

A topic is a logical category of events. It is divided into partitions, and each record belongs to one partition. Partition count influences parallelism, throughput, and ordering.

### 03.30 Partition

A partition is an ordered, append-only log within a topic. Ordering is guaranteed within a partition, not globally across all topic partitions.

### 03.31 Consumer Group

A consumer group allows parallel consumption of a topic. Within a group, each partition is assigned to at most one active consumer at a time. Different groups consume the same event stream independently.

### 03.32 Ordering

Kafka guarantees ordering only within a partition. To preserve order per customer or order, I use a stable key so related events land in the same partition. This reduces parallelism for that key but preserves sequence.

### 03.33 At-least-once

At-least-once prioritizes preventing loss but permits duplicates. The offset is commonly committed after processing. If an application processes a message and crashes before committing, delivery may repeat. Hence idempotency matters.

### 03.34 Exactly-once

Exactly-once in Kafka depends on scope. Kafka offers idempotent producers and transactions for Kafka-to-Kafka workflows. That does not automatically guarantee end-to-end exactly-once when external effects such as databases or HTTP calls are involved.

### 03.35 Offset Commit

A committed offset specifies where a group resumes; it normally stores the next offset to consume. Committing before completion can lose work; committing afterward can cause redelivery. With parallel execution, I do not advance past earlier incomplete records.

### 03.36 Consumer Lag

Consumer lag describes the distance between a reference log position and the consumer position, depending on the metric. Committed-offset lag may include processed but uncommitted work; I compare trends, event age, throughput, and errors when interpreting delays.

### 03.37 DLQ

A dead-letter queue stores messages that cannot be processed after retry policy exhaustion. It prevents indefinite blocking but requires inspection, fixes, and reprocessing processes. A DLQ should not become a permanent dumping ground for ignored failures.

### 03.38 Schema Evolution

Events are contracts. To evolve schemas, I favor backward-compatible changes such as adding optional fields. Formats such as Avro, Protobuf, and JSON Schema can be paired with registries to govern compatibility.

### 03.39 Kafka vs RabbitMQ

Kafka is centered on distributed logs, retention, high event throughput, and natural replay. RabbitMQ is traditionally queue- and message-routing-oriented. The decision follows workload: event streaming and history favor Kafka, while work queues and complex routing may favor RabbitMQ.

### 03.40 Kafka and Outbox

Outbox is a common strategy for publishing Kafka events consistently with a database transaction. The application writes business data and the outbox record together, then a publisher sends the event to Kafka.

---

<a id="section-04"></a>
## §04 — SQL, Oracle and database performance [P0]

**Focus:** diagnose a slow Oracle query using evidence, not guesses. Separate JPA configuration problems from database execution problems.

### 04.01 Primary Key, Unique, and Foreign Key

A primary key uniquely identifies each row and does not allow `NULL`. A `UNIQUE` constraint also enforces uniqueness, but its `NULL` semantics may vary by database. A foreign key enforces referential integrity between tables. I use database constraints because consistency must not rely solely on the application.

### 04.02 Normalization vs Denormalization

Normalization reduces redundancy and update anomalies, usually separating entities and relationships into coherent tables. Denormalization duplicates or precomputes data to reduce joins or speed up reads. I start with a normalized design and denormalize only when a proven performance requirement exists, accepting the additional consistency cost.

### 04.03 INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL JOIN

`INNER JOIN` returns only rows that match on both sides. `LEFT JOIN` preserves all rows from the left table and returns `NULL` for missing matches on the right. `RIGHT JOIN` does the reverse, while `FULL JOIN` preserves unmatched rows from either side. I choose joins based on relationship semantics, not assumed performance.

### 04.04 CTE vs Subquery

CTEs improve readability and help structure complex queries, including recursion where recursive CTEs are supported. Subqueries may suffice for small cases. I do not assume a CTE is automatically faster or slower: optimizers may treat them differently, so I validate performance through the execution plan.

### 04.05 Window Functions

Window functions calculate values over related rows without collapsing the result as `GROUP BY` does. Examples include `ROW_NUMBER`, `RANK`, `LAG`, `LEAD`, and aggregates using `OVER`. They are useful for rankings, comparisons with prior rows, and running totals without multiple subqueries.

### 04.06 EXISTS vs IN vs JOIN

`EXISTS`, `IN`, and `JOIN` express different needs, although modern optimizers may generate equivalent plans in some cases. I use `EXISTS` to test existence, `JOIN` to combine data, and `IN` where membership semantics are clear. For performance, I compare plans rather than follow a universal rule.

### 04.07 Indexes

An index is an auxiliary structure that reduces data lookup cost, often avoiding a full table scan. Its trade-off is disk space and additional `INSERT`, `UPDATE`, and `DELETE` work to maintain the index. More indexes are not automatically better.

### 04.08 Composite Indexes and Column Order

Column order matters in a composite index. I choose it based on actual filter, join, and ordering patterns. In B-tree indexes, queries tend to make better use of leading prefixes; an index on `(cliente_id, status, data)` is not equivalent to `(status, cliente_id, data)`.

### 04.09 Selectivity and Cardinality

Cardinality describes the number of distinct values in a column, while selectivity describes how much a predicate reduces the row set. Indexes tend to help when the query eliminates a significant fraction of rows. For low-cardinality columns, the optimizer may prefer a scan depending on distribution and volume.

### 04.10 Sargability

A sargable condition lets the database use an index efficiently to locate a data range. Applying a function directly to an indexed column, for example, can prevent or reduce index usage. I aim to keep indexed columns searchable in predicates and confirm results in the execution plan.

### 04.11 Execution Plan

The execution plan shows how the optimizer intends to execute a query: scans, seeks, joins, sorts, row estimates, and relative costs. For a slow query, I compare estimates against actual row counts and check unexpected scans, expensive joins, sorts, missing indexes, and statistics. The goal is finding the cause, not merely forcing an index.

### 04.12 Optimizing Queries in Production

I begin with evidence: latency, frequency, returned data volume, and execution plans. I then inspect filters, joins, indexes, cardinality, statistics, rows read versus returned, and unnecessary O(n) work. Only then do I consider rewriting queries, changing indexes, pagination, or remodeling access.

### 04.13 N+1 Queries

N+1 occurs when an initial query loads a collection of records and additional queries run for each item. This increases round trips and latency. I detect it through logs or APM and address it using joins, batching, or specialized queries and loading strategies as appropriate.

### 04.14 ACID

ACID stands for Atomicity, Consistency, Isolation, and Durability. Atomicity ensures the entire transaction applies or rolls back; consistency preserves rules and constraints; isolation controls interference among concurrent transactions; durability ensures a confirmed commit survives failures according to the database guarantees.

### 04.15 Isolation Levels

The classic levels are `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, and `SERIALIZABLE`. Higher isolation reduces the possibility of dirty reads, non-repeatable reads, and phantom reads, potentially at greater concurrency cost. Exact behavior depends on the database and its locking or MVCC implementation.

### 04.16 Lost Update and Write Skew

A lost update happens when one write improperly overwrites another. Write skew can occur when transactions update different rows after reading a shared condition, violating a joint rule; versioning one row does not solve every cross-row invariant.

### 04.17 MVCC

MVCC, or Multi-Version Concurrency Control, retains row versions so reads and writes can proceed with less blocking at certain isolation levels. It improves concurrency but does not eliminate conflicts, locks, or the need to understand database-specific isolation.

### 04.18 PostgreSQL vs Oracle: Isolation

Both commonly default to READ COMMITTED, but their guarantees are not interchangeable. PostgreSQL treats READ UNCOMMITTED as READ COMMITTED and supports REPEATABLE READ; Oracle does not offer exactly the same set of levels. I check snapshots, conflicts, and retry requirements in each database's documentation. [PostgreSQL](https://www.postgresql.org/docs/current/transaction-iso.html) · [Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/26/cncpt/data-concurrency-and-consistency.html)

### 04.19 Optimistic vs Pessimistic Concurrency

Optimistic concurrency assumes conflicts are rare and detects concurrent changes, for example with a version column. Pessimistic concurrency locks resources before modifying them. I favor optimistic control when conflicts are rare and pessimistic control when conflict cost is high and expected contention justifies locking.

### 04.20 Database Deadlocks

Deadlocks occur when transactions form a circular wait for locks. Databases generally detect the cycle and abort one transaction. To reduce them, I keep transactions short, acquire resources consistently, use appropriate indexes to avoid locking unnecessary rows, and prepare to retry a victim transaction when appropriate.

### 04.21 Connection Pools and Saturation

Connection pools reuse connections and limit concurrent database operations. I monitor waits for connections, long transactions, and database capacity; increasing pool size may simply shift the overload to the server.

---

<a id="section-05"></a>
## §05 — Automated testing, code quality and delivery [P0]

**Focus:** the vacancy values **behavioral confidence**, not coverage percentages alone. Explain what each kind of test is designed to catch.

### 05.01 Unit Tests

Validate a unit of behavior quickly with controlled dependencies. For pure business rules, I instantiate objects directly; starting all of Spring to test a simple function adds cost without improving the evidence.

### 05.02 JUnit and Assertions

JUnit organizes test execution, lifecycle, and parameters. Assertions should verify observable outcomes and relevant cases rather than mirror implementation or merely confirm that no exception was thrown.

### 05.03 Mock, Stub, Fake, and Spy

A stub provides controlled responses; a mock verifies interactions; a fake supplies a simplified working implementation; a spy observes or partially overrides a real object. I choose based on what the test must demonstrate and avoid simulating every internal detail.

### 05.04 Mockito

Mockito creates test doubles and configures dependency responses. I verify interactions when an external call is the important effect; excessive `verify` usage can couple tests to internal structure and make refactoring fragile.

### 05.05 Parameterized Tests and Edge Cases

Parameterized tests exercise the same rule with different inputs. I cover boundaries, absence, duplicates, and invalid data where the contract requires them rather than testing only the happy path.

### 05.06 Integration Tests

Verify interactions between real components, such as application code and a database. They matter for transactions, mappings, SQL, and serialization, which may pass with mocks and fail in real environments.

### 05.07 Testcontainers

Testcontainers runs real dependencies in containers during tests. It makes databases and brokers more like production; I choose suitable versions, isolate test data, and account for startup time and container-runtime availability.

### 05.08 @SpringBootTest

Loads the application context to test interactions among Spring components. It suits flows that need that context, but its cost calls for selective use; the configured web environment determines how HTTP is exercised.

### 05.09 @WebMvcTest and MockMvc

`@WebMvcTest` loads a slice focused on MVC. MockMvc tests Spring HTTP processing without starting a real network server; I validate routes, serialization, errors, and applicable security while controlling controller dependencies.

### 05.10 @DataJpaTest

Loads a JPA persistence slice to test entities, repositories, and queries. An in-memory database may hide differences in dialects, locks, and constraints; for those cases, I use the same database type as production.

### 05.11 Contract Tests

Verify expectations between consumers and providers, such as fields, statuses, and schemas. They help detect incompatibilities without starting the entire system but do not guarantee availability or a complete journey's behavior.

### 05.12 Test Pyramid

Distributes verification across many fast tests and fewer broad, expensive tests. Proportions depend on system risks; the goal is reliable feedback at sustainable cost, not a fixed percentage.

### 05.13 Coverage and Mutation Testing

Coverage shows which code ran but does not guarantee meaningful assertions. Mutation testing makes small code changes to check whether tests detect them; it improves suite evaluation at the cost of longer execution.

### 05.14 Load and Performance Testing

Load tests measure behavior under defined volume; stress tests seek limits; soak tests assess extended operation. I evaluate tail latency, errors, throughput, and saturation using representative data, without confusing user counts with requests per second.

### 05.15 Pull Requests and Code Review

Pull requests are technical and collaboration gates. I combine human review with automated validation rather than rely on review alone. Branch protection should prevent merges when builds, tests, or required checks fail.

### 05.16 Quality Gate

A quality gate defines minimum promotion conditions such as passing tests, acceptable static analysis, and absence of critical vulnerabilities. Gates should prevent real problems instead of accumulating arbitrary metrics.

### 05.17 CI/CD Pipeline

A typical Java pipeline includes checkout, build, unit tests, static analysis, integration tests, packaging, artifact or image publication, deployment, and post-deployment validation. I separate stages to fail early and save time and resources.

### 05.18 Database Migrations

Database migration should be part of delivery using tools such as Flyway or Liquibase. I version scripts, execute them in a controlled way, and avoid destructive changes incompatible with still-running versions.

### 05.19 Zero-Downtime Database Migration

For zero downtime, I make backward-compatible changes in stages. First I add new columns or structures without removing old ones, then update the application to use the new format, migrate data, and remove legacy structures only after no version depends on them.

### 05.20 Expand and Contract

Expand and Contract is a two-phase schema-evolution strategy. Expand adds compatible structures; Contract removes old structures only after all consumers have migrated. This is essential for rolling deployments.

### 05.21 Rolling vs Blue-Green vs Canary

Rolling is simple and economical but mixes versions. Blue-Green enables quick rollback at the cost of duplicate capacity. Canary reduces risk through progressive exposure but demands mature automation and metrics. I choose based on criticality, cost, and operational capability.

---

<a id="section-06"></a>
## §06 — Git, containers, AWS and operational troubleshooting [P1]

**Focus:** understand operational mechanics even if you did not own infrastructure. Git conflict handling is a firm requirement, not a bonus.

### 06.01 Git Strategy

A Git strategy should reduce long-lived branches and late integration. Trunk-based development favors short branches and frequent integration. Git Flow can suit some contexts but adds branches and coordination. I select based on release frequency and team maturity.

### 06.02 Trunk-Based Development

Trunk-based development encourages small, short-lived branches that merge quickly into the trunk. It reduces divergence, conflicts, and time to integration. Feature flags help when functionality is not ready to be exposed.

### 06.03 Dockerfile, Layers, and Build Context

A Dockerfile describes image construction. Step order affects cache reuse; I keep build contexts small and never copy secrets into image layers because deleting them later may leave them in image history.

### 06.04 Multi-stage Builds and Java Runtime

I separate compilation from execution so the final image contains only what is needed. I use a runtime compatible with the JAR and run as a nonprivileged user where possible to reduce image size and process permissions.

### 06.05 Deployment and ReplicaSet

A Deployment declares versions and desired replica counts and manages rollouts through ReplicaSets. Multiple replicas do not guarantee high availability if they share a failure point or are distributed poorly.

### 06.06 Service, DNS, and Inbound Traffic

A Service supplies a stable destination for selected Pods and can be discovered through DNS. External access depends on appropriate resources and controllers, such as LoadBalancer, Ingress, or Gateway API.

### 06.07 Requests, Limits, and OOMKilled

Requests inform scheduling; limits constrain resource use. CPU may be throttled, while exceeding memory can terminate the container; an OS-level `OOMKilled` is not the same as a JVM-thrown `OutOfMemoryError`.

### 06.08 Startup, Readiness, and Liveness Probes

A startup probe allows initialization before other checks apply. Readiness determines whether a Pod receives traffic; liveness may restart a container. Temporary dependency unavailability should not, by default, trigger mass restarts through liveness probes. [Reference](https://kubernetes.io/docs/concepts/workloads/pods/probes/)

### 06.09 Graceful Shutdown

During shutdown, an application should stop accepting new work, safely complete or interrupt active tasks, and release resources. I align Kubernetes termination deadlines with HTTP, consumers, and running tasks to reduce loss during rollouts.

### 06.10 Pod Diagnostics

I start with events, status, and termination reason, then investigate logs, probes, resources, and connectivity. `CrashLoopBackOff` indicates repeated restarts, not a root cause; the cause may be application code, configuration, permissions, or dependencies.

### 06.11 Shared Responsibility Model

Under the shared responsibility model, AWS secures the cloud, including data centers, hardware, and physical infrastructure; customers secure what they run in the cloud, including IAM, network settings, data, service-dependent patches, and application controls.

### 06.12 IAM

IAM controls authentication and authorization within AWS. I work with users, roles, and policies under least privilege: granting only necessary permissions for the smallest practical duration and scope.

### 06.13 IAM User vs Role

An IAM User is a persistent identity. An IAM Role is an assumable identity, usually temporary, used by applications, AWS services, or federated users. For EC2, ECS, and Lambda workloads, I prefer roles over static access keys.

### 06.14 VPC

A VPC is an isolated AWS virtual network. Within it, I configure CIDR ranges, subnets, route tables, gateways, and security groups. I normally separate public and private resources based on exposure requirements.

### 06.15 RDS

RDS is a managed relational database service. AWS handles provisioning, backups, and some operations depending on configuration. I am still responsible for schemas, indexes, queries, capacity, and access architecture.

### 06.16 SQS

SQS is a managed queue for asynchronous decoupling. Producers send messages and consumers process them independently. It helps absorb spikes, support retries, and reduce temporal coupling among services.

### 06.17 CloudWatch

CloudWatch centralizes metrics, logs, alarms, and dashboards for AWS services. In production, I monitor latency, error rates, saturation, throttling, queues, CPU, memory when available, and business metrics.

### 06.18 Secrets Manager vs Parameter Store

Secrets Manager focuses on secrets and provides features such as integrated rotation in many scenarios. Parameter Store handles configuration and can also store secure values. I choose based on rotation, integration, and cost.

### 06.19 ECS vs EKS

ECS is simpler and tightly integrated with AWS. EKS offers the Kubernetes ecosystem and portability but increases operational complexity. I would choose EKS for a genuine Kubernetes requirement; otherwise, ECS may suffice.

### 06.20 Jenkins

Jenkins is a flexible, extensible automation server. Jenkinsfiles support complex pipelines, but the platform requires operations, plugin updates, security controls, and agent maintenance.

### 06.21 GitLab CI

GitLab CI defines pipelines in a versioned file and integrates build, testing, and deployment with the platform. Core concepts match other tools: stages, jobs, runners, artifacts, and environments.

### 06.22 Post-deployment Observability

Deployment does not end when a container starts. I monitor errors, latency, p95/p99, CPU, memory, GC, connection pools, and business metrics to confirm the new version is healthy.

---

<a id="section-07"></a>
## §07 — Design quality, architecture decisions and maintainability [P1]

**Focus:** justify a design through maintainability, testability, performance and actual requirements. Avoid pattern-for-pattern’s-sake.

### 07.01 SOLID in Practice

In a checkout, I separate calculation, persistence, and payment by responsibility, use strategies for variations, and define coherent contracts for integrations. I evaluate the design by how easily rules can be tested and changed, not by the number of classes created.

### 07.02 DIP vs Dependency Injection vs IoC

DIP governs the direction of dependencies in code. Dependency Injection supplies dependencies from outside an object. IoC is the broader inversion of control; injecting a concrete class does not, by itself, satisfy DIP.

### 07.03 DRY, KISS, and YAGNI

DRY avoids duplicating business knowledge; KISS favors understandable solutions; YAGNI avoids building speculative features. Similar-looking code does not always encode the same rule; premature abstraction can also increase coupling.

### 07.04 Strategy — Behavioral

Encapsulates interchangeable algorithms under a common contract, such as shipping or discount calculations. It favors composition and OCP; the client or a policy selects the strategy.

### 07.05 Adapter — Structural

Translates an existing interface into the contract expected by a client. For example, I use it to integrate an external provider with an application port, keeping translation details out of business rules.

### 07.06 Proxy — Structural

Represents another object and controls access to it, potentially applying security, lazy loading, or interception. Spring AOP uses proxies for various features; calls that bypass the proxy may also bypass that behavior.

### 07.07 Repository, DTO, and Mapper

Repository provides domain-oriented persistence access; DTO transfers data across boundaries; Mapper converts representations. These are common patterns in Java applications but are not in the GoF catalog and should not duplicate layers without benefit.

### 07.08 When Not to Apply a Pattern

I avoid a pattern when it creates more indirection than value or anticipates variations without evidence. The solution must remain easy to explain, test, and modify; knowing a pattern's name is not a substitute for assessing its cost.

### 07.09 Functional and Nonfunctional Requirements

Functional requirements describe behavior; nonfunctional requirements define qualities such as latency, availability, security, and cost. I translate vague terms such as "fast" into measurable targets and load conditions to compare options.

### 07.10 ADRs and the C4 Model

An ADR records a decision, its context, alternatives, and consequences. C4 structures views of context, containers, and components, adding code-level detail when useful; I document enough to guide decisions and evolution without diagramming everything.

### 07.11 Observability

Observability helps explain internal system behavior through external signals. I consider logs, metrics, and traces its three main pillars. In distributed systems, correlation IDs, trace IDs, p95/p99, error rates, and saturation are essential for identifying bottlenecks and cross-service failures.

### 07.12 Logs, Metrics, and Traces

Logs explain specific events; metrics show aggregate behavior over time; traces show a request's path through components. I use all three together. A trace can identify a slow service, a metric show frequency, and a log explain the error in that execution.

### 07.13 Cache-Aside

With cache-aside, the application checks the cache; on a miss, it reads the source and populates the cache. This is simple and common but may cause stampedes when many requests miss the same key. TTL, per-key locking, or proactive refresh can help.

### 07.14 Rate Limiting

Rate limiting controls how many operations are accepted over a period. It protects capacity, reduces abuse, and prevents dependency overload. Limits may apply per user, API key, IP, or tenant. I also define an appropriate response, usually `429`, and client retry policies.

### 07.15 Backpressure

Backpressure prevents producers from creating work faster than consumers or dependencies can process it. Bounded queues, concurrency controls, rate limiting, or reactive mechanisms can enforce it. Without backpressure, memory, threads, or connections accumulate until failure.

---

<a id="section-08"></a>
## §08 — Role-specific complements (additional material; not copied from original notes) [P0/P1]

The job description explicitly requests the following areas. They receive supplemental explanations here because the original notes either did not cover them directly or treated them only indirectly.

### 08A. Java 8 vs Java 21 — feature timeline [P0]

| Java release | Relevant capability | What it means in backend work |
|---|---|---|
| **8** | Lambdas, Streams, `Optional`, `java.time`, default interface methods, `CompletableFuture` | Baseline for modern functional-style Java and asynchronous composition. |
| **9** | Module system, factory methods such as `List.of` | Stronger module boundaries; concise read-only collection construction. |
| **10–11** | `var` (10), standard HTTP Client (11) | Local type inference and a modern client for HTTP calls. |
| **14–17** | Switch expressions (14), text blocks (15), records and `instanceof` patterns (16), sealed classes (17) | Less boilerplate and clearer, safer modeling of values and controlled hierarchies. |
| **21** | Virtual threads, record patterns, pattern matching for `switch`, sequenced collections | Better scalability for blocking I/O and additional expressive language features. |

**Key distinction:** Java 21 does not automatically make old applications faster or reactive. Migration must account for dependency compatibility, runtime flags, removed/encapsulated APIs, performance baselines, library support, regression testing and deployment configuration. Not every Java 21 feature was first introduced in Java 21.

**Virtual threads:** they are most valuable when requests spend time blocked on I/O. They do not multiply database connections or improve CPU-intensive algorithms. In Java 21, blocking while holding a `synchronized` monitor may pin a virtual thread to its carrier. Limits on DB pools, remote APIs and queues still matter.

### 08B. Spring Boot 2.x → 3.x migration — conceptual checklist [P0]

1. **Runtime baseline:** Spring Boot 3.0 requires Java **17+** and Spring Framework **6**; a Java 8 application cannot simply update its Boot dependency and start unchanged.
2. **Jakarta namespace:** Java EE API packages such as `javax.persistence.*` and `javax.validation.*` move to `jakarta.persistence.*` and `jakarta.validation.*`. This requires dependency and source compatibility checks; not every `javax.*` class changes.
3. **Hibernate 6:** validate ORM dependencies, JPQL/HQL syntax, custom types, SQL generation, dialect settings, mappings and query plans. A compiling application can still regress in data access.
4. **Spring Security 6:** review configured `SecurityFilterChain`, request authorization rules, deprecated APIs and security integration tests. A migration can accidentally alter access permissions.
5. **Observability and configuration:** review Actuator/Micrometer instrumentation, configuration property changes, startup behavior and logs; validate dashboards and alerts after upgrading.
6. **Migration strategy:** inventory dependencies → establish regression baseline → migrate incrementally where possible → run unit/integration/contract tests → load test critical flows → deploy gradually with rollback readiness.

**Do not conflate two operations:** upgrading the **JDK** from 8 to 21 and upgrading **Spring Boot** from an older generation to 3.x are related but different migrations. The specific framework migration steps only apply if that version change happened in the system.

### 08C. Oracle SQL, ERDs and query plans [P0]

- **ERD (Entity–Relationship Diagram):** describes entities/tables, primary keys, foreign keys and cardinalities (one-to-one, one-to-many, many-to-many). Review missing constraints, unnecessary joins, duplicated data, nullable foreign keys, cycles and relationship ownership. Many-to-many relationships normally need a junction table.
- **Oracle SQL specifics:** know analytic functions (`ROW_NUMBER`, `DENSE_RANK`, `LAG`), `MERGE` for conditional insert/update, CTEs with `WITH`, pagination with `FETCH FIRST ... ROWS ONLY`, and bind variables for safe, reusable SQL.
- **Execution plans:** Oracle can show predicted plans with `EXPLAIN PLAN` and `DBMS_XPLAN.DISPLAY`. For an already executed SQL statement, `DBMS_XPLAN.DISPLAY_CURSOR` can reveal execution information where access and statistics permit. Examine access paths, join methods, estimated versus actual rows, and expensive operations; an index is not automatically the best plan.
- **Oracle read consistency:** Oracle `READ COMMITTED` uses statement-level consistent reads; transaction behavior is database-specific. Understand locks, `SELECT ... FOR UPDATE`, deadlocks and lost-update protection through application logic, locking or conditional updates.
- **SQL tuning workflow:** identify high-latency/high-frequency queries → capture SQL and execution statistics → check predicate selectivity and row estimates → check indexes, joins and cardinalities → change one relevant factor → measure under representative data and concurrency.

### 08D. Advanced Git operations [P0]

- **Merge:** combines histories with an explicit merge commit where applicable, preserving branch ancestry. Useful when a team values an audit trail of parallel work.
- **Rebase:** replays commits onto a new base, creating new commit identities. Useful for keeping a feature branch current or simplifying local history. Avoid rewriting a shared branch without agreement.
- **Interactive rebase:** `git rebase -i` can reorder, edit, squash or drop local commits. `squash` combines commits while allowing message editing; `fixup` combines while normally discarding the fixup message.
- **Squash merge:** merges the overall changes of a branch into one new commit; the original feature-branch commits do not become individual commits on the target branch.
- **Conflict resolution:** inspect affected files and intended behavior → resolve conflicts → run tests → stage resolved files → use `git rebase --continue` (or finish the merge). `git rebase --abort` returns to the pre-rebase state when appropriate.
- **`revert` vs `reset`:** `revert` records a new commit that undoes a previous change; `reset` moves a branch reference and may change index/worktree. Revert is generally safer for commits already shared with a team.

### 08E. RabbitMQ vs Kafka — make your Dell/BANCO PAN background count [P0]

- **RabbitMQ:** message broker centered on exchanges, queues, bindings and routing keys. Consumers acknowledge messages; manual acknowledgments and dead-letter handling require careful configuration. Prefetch controls in-flight deliveries to a consumer and can help prevent overload.
- **Kafka:** partitioned, durable event log with consumer offsets and consumer groups. Events can be retained and replayed; ordering is generally per partition rather than global. Partition count affects scaling in a consumer group.
- **Decision criteria:** RabbitMQ often suits flexible routing and task processing; Kafka often suits high-volume event streams, retention/replay and event fan-out. Both can support asynchronous integration, retries, monitoring and at-least-once delivery patterns, but their abstractions and operational behavior differ.
- **Idempotent processing:** whenever messages may be redelivered, ensure business effects are not duplicated (e.g., using a stable business-event ID with a uniqueness check/transactional inbox). `ACK` only after safely completing the intended operation according to the design.

### 08F. Estimates, mentoring, English collaboration and cross-stack troubleshooting [P0/P1]

- **Work estimates:** break down work by API, rules, persistence, integration, tests, deployment and unknowns. State assumptions, dependencies and risk. Express uncertainty as a range or confidence level when appropriate; revisit estimates when requirements change.
- **Technical communication:** distinguish symptom, evidence, hypothesis, mitigation, root cause and prevention. Share concise updates during incidents; never confuse a plausible theory with confirmed findings.
- **Mentoring:** use focused code reviews, pairing, design discussions and actionable feedback. Explain the reasoning behind changes, not only the final code. Promote ownership and avoid becoming a bottleneck.
- **Code review:** prioritize correctness, security, failure handling, data consistency, performance, tests, naming and long-term maintainability. Discuss observable behavior and requirements rather than personal preferences.
- **C# / React comprehension:** when reading unfamiliar code, trace input → controller/handler → service/domain logic → persistence/network calls → returned state. In React, identify component state, props, effects and API calls; in C#, identify async tasks, dependency injection, controllers and DTOs. Do not claim implementation expertise if your actual strength is integration and flow analysis.
- **Working with Pacific Time:** clarify expected overlap, meeting times, asynchronous handoffs and communication norms; do not assume the interviewer’s local clock is your local clock.

### 08G. JUnit Jupiter and performance evidence [P0]

- **JUnit 5 ecosystem:** JUnit **Platform** launches test engines; **Jupiter** supplies the modern programming model (`@Test`, `@ParameterizedTest`, `@BeforeEach`, `@Nested`, assertions and extensions); **Vintage** allows running older JUnit 3/4 tests. Writing a Jupiter test is not a separate alternative to using JUnit—it is part of the JUnit 5 ecosystem.
- **Mocking strategy:** use Mockito for collaborators whose behavior is not under test. Prefer real domain objects for business rules and actual containerized dependencies for database/broker integration. Avoid asserting only implementation details.
- **Load test:** measure behavior under expected concurrent requests, realistic data and known SLAs/SLOs. Track throughput (requests/s), p50/p95/p99 latency, error rate and resource saturation.
- **Stress test:** increase traffic beyond normal capacity to find bottlenecks and failure modes. A useful report identifies breaking points, queue behavior, recovery characteristics and the changes made after measurement.

### 08H. Security tokens and secure microservices [P1]

- **Bearer tokens:** possession grants access, so protect them in transit (TLS), storage, logs and monitoring. Apply short lifetimes and scope-based least privilege.
- **JWT verification:** validate signature, expected signing algorithm, issuer, audience, expiration and relevant scope/roles; decoding alone does not establish trust.
- **Service-to-service authorization:** authenticate the calling service and independently authorize its intended operation. An API gateway can centralize some controls but does not eliminate checks in downstream services.
- **Secrets:** use managed secret storage and scoped identity-based access, not hardcoded repository configuration. Rotation and auditability matter.

---

<a id="section-09"></a>
## §09 — Final concept checklist

The goal is to be able to explain each item in **45–90 seconds**, give one technical example and state a realistic limitation, without memorizing a script.

- [ ] **Java/JVM:** Java 8 vs 21 timeline; `equals/hashCode`; records; Streams; concurrency; virtual threads; JVM/GC diagnostics.
- [ ] **Spring:** IoC; proxies; self-invocation; Boot 3 migration; `@Transactional`; rollback; persistence context.
- [ ] **JPA:** N+1; fetch joins vs entity graphs vs projections; LAZY/EAGER; locking; query performance.
- [ ] **Microservices:** boundaries; modular monolith; outbox; idempotency; Kafka/RabbitMQ; timeouts/retries/circuit breakers.
- [ ] **Oracle SQL:** joins; CTE/window functions; ERDs; indexes; explain plans; locks and isolation.
- [ ] **Testing:** JUnit Jupiter; Mockito; integration/Testcontainers; coverage limits; load vs stress testing.
- [ ] **Git/DevOps:** rebase vs merge vs squash; conflict resolution; CI/CD; Docker/Kubernetes; AWS fundamentals.
- [ ] **Senior behaviors:** work estimates; mentoring; peer review; business requirements; incident communication.

**Final self-test:** for each P0 concept, say in English: *(1) definition, (2) implementation/diagnosis, (3) benefit, (4) trade-off, (5) one real example if you have one.* If a project detail is forgotten, distinguish what you **did** from what you **would do today**.

---

<a id="references"></a>
## References

- **Primary source:** your supplied `Texto colado(4).txt`, 25 chapters on Java, Spring, databases, architecture and operations. The short definitions in §§01–07 are selected/adapted from this file.
- **Official Java 21 language changes:** https://docs.oracle.com/en/java/javase/21/language/java-language-changes-summary.html
- **Spring Boot 3.0 migration guide:** https://github.com/spring-projects/spring-boot/wiki/spring-boot-3.0-migration-guide
- **Oracle execution plan reference:** https://docs.oracle.com/en/database/oracle/oracle-database/26/tgsql/generating-and-displaying-execution-plans.html
- **Git interactive rebase reference:** https://git-scm.com/docs/git-rebase

**Scope note:** this reference is intentionally focused on the vacancy. Lower-relevance material from the original notes (large GoF catalogs, deep AWS service inventories and exhaustive CI/CD metrics) was omitted. This is not an exhaustive language or cloud handbook.
