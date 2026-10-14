<a id="start"></a>

# Delivery Stories — Java Interview Preparation

Professional experience stories and technical follow-up answers covering **microservices, Kafka, Kubernetes, and legacy-system refactoring**.

## Table of Contents

1. [Microservices — Dell Technologies](#microservices-dell)
   - [Context and implementation](#micro-context)
   - [Key considerations and delivered value](#micro-value)
2. [Kafka and Asynchronous Communication — Banco PAN](#kafka-pan)
   - [Project experience](#kafka-experience)
   - [Quick concepts for interviews](#kafka-concepts)
   - [Frequently asked questions](#kafka-questions)
3. [Kubernetes — Microservice Deployment and Operations](#kubernetes)
   - [Experience and responsibilities at Dell](#k8s-experience)
   - [Technical interview answer](#k8s-answer)
   - [Essential technical concepts](#k8s-concepts)
   - [Follow-up questions](#k8s-questions)
4. [Biggest Professional Challenge — McDonald's](#mcdonalds)
   - [Interview story](#mcd-story)
   - [Technical follow-up questions](#mcd-questions)

---

<a id="microservices-dell"></a>

## 1. Microservices — Dell Technologies

<a id="micro-context"></a>

### Context and implementation

- One delivery I consider particularly relevant was my contribution to a **scheduling microservice at Dell**.
- We worked in a distributed-systems environment where I needed to enhance existing functionality while maintaining **loose coupling between services** and reliable integrations.
- To achieve this, I kept responsibilities clearly defined, isolated business rules within the domain, and separated them from infrastructure implementations. We used **hexagonal architecture**.
- With **Java 21 and Spring Boot**, I developed and enhanced REST endpoints, structured application responsibilities, and applied **SOLID principles**.
- I created automated tests with **JUnit and Mockito**, working in an environment with **CI/CD pipelines** and **AWS infrastructure**.

<a id="micro-value"></a>

### Key considerations and delivered value

**Main concern:** keeping business rules isolated and handling integration failures properly, so that issues in external components would not compromise the service.

I used resilience strategies such as **circuit breakers** and **backoff with jitter**.

**Value of this experience:** contributing to the evolution of a business feature in a microservices architecture while considering not just implementation, but also maintainability, quality, and application behavior in production.

[↑ Back to Top](#start)

---

<a id="kafka-pan"></a>

## 2. Kafka and Asynchronous Communication — Banco PAN

<a id="kafka-experience"></a>

### Project experience

- At **Banco PAN**, I worked on a **loan product backed by early access to Brazil's FGTS severance fund**, within a microservices architecture using **Java 21, Spring Boot, and Kafka**.
- One challenge was integrating different backend components while ensuring that changes to APIs and contracts did not disrupt processing flows.
- My responsibilities included developing and evolving **REST APIs**, defining and aligning integration contracts with the **BFF (Backend for Frontend)**, and working with asynchronous flows using Kafka.
- Because event processing is decoupled, we needed to pay close attention to **error handling, traceability, and data consistency**.
- I also supported production systems, using **Splunk and Log4j** to investigate issues, and maintained automated tests with a minimum of **80% code coverage**.
- This experience allowed me to contribute to a real financial product, gaining exposure to both integration development and the operational challenges of event-driven architecture.

<a id="kafka-concepts"></a>

### Quick concepts for interviews

| Concept | Concise technical explanation |
|---|---|
| **Partitioning** | A topic is split into partitions to support parallel processing and scalability. Kafka guarantees ordering only within each partition. |
| **Consumer groups** | Consumers in the same group share a topic's partitions. Each partition is assigned to only one consumer within a group, allowing workload distribution. |
| **Offsets** | Sequential positions of records within a partition. Consumers commit offsets to track progress and resume processing. |
| **Retries** | A strategy for reprocessing messages after transient failures. It may use backoff and a maximum number of attempts to avoid overload or endless retries. |
| **DLQ (Dead Letter Queue)** | A topic for messages that still fail after the configured processing attempts. It allows investigation and later reprocessing. |
| **Idempotency** | Ensures that processing the same message multiple times has the same business effect as processing it once. It can be implemented using unique identifiers and database-level controls. |
| **At-least-once** | Ensures delivery at least once under the configured durability and acknowledgment conditions. Duplicate processing is possible, so idempotency is important. |

<a id="kafka-questions"></a>

### Frequently asked questions

**1. How do you prevent message loss?**

By using replication, appropriate durability settings, a producer configured with `acks=all`, and committing the consumer offset only after successful processing.

**2. What happens when a consumer fails?**

Kafka rebalances the consumer group and reassigns partitions among the available consumers. Processing resumes from the committed offsets.

**3. How do you prevent duplicate processing?**

By implementing idempotency on the consumer side—for example, by storing the event ID with a unique constraint in the database.

**4. What is the difference between at-most-once, at-least-once, and exactly-once?**

- **At-most-once:** a message may be lost, but it is not redelivered.
- **At-least-once:** message loss is avoided under the configured guarantees, but duplicates are possible.
- **Exactly-once:** processing can happen exactly once within the scope of Kafka's transactional guarantees; effects on external systems require additional mechanisms.

**5. What happens if a topic has 3 partitions and 5 consumers in the same group?**

Only 3 consumers will be assigned partitions for that topic. The other 2 will remain idle for that topic.

**6. How do you preserve message ordering?**

By publishing related messages with the same **message key**, so they are routed to the same partition. Ordering is preserved within that partition.

> **Important point for senior-level interviews:** Kafka does not, by itself, guarantee the absence of duplicate business effects or transactional consistency with external databases. Those guarantees depend on the application's design.

[↑ Back to Top](#start)

---

<a id="kubernetes"></a>

## 3. Kubernetes — Microservice Deployment and Operations

<a id="k8s-experience"></a>

### Experience and responsibilities at Dell

- At Dell, in addition to development, I was involved in delivering and operating applications in **cloud-native environments**, using **AWS and GitLab pipelines**, although this is not my main area of expertise.
- An important part of this work was ensuring that backend changes could be delivered safely and were covered by automated tests.
- This environment also requires an understanding of container orchestration. In Kubernetes, I consider **readiness and liveness probes**, resource management, horizontal scaling, and **rolling updates** to be essential concepts.
- One concern is making sure an application not only starts successfully, but is actually ready to receive traffic and can shut down without unnecessarily disrupting requests in progress.

**My responsibility:** backend development with **Java and Spring Boot**, automated test coverage, and integrating changes into the **GitLab CI/CD process**. I was not responsible for Kubernetes cluster administration; my focus was delivering stable, testable applications suitable for distributed environments.

<a id="k8s-answer"></a>

### Technical interview answer

> "At Dell, I worked with Java 21 and Spring Boot applications in a cloud-native environment, contributing to development and delivery through GitLab pipelines.
>
> My responsibilities were primarily focused on backend development, ensuring code quality through automated tests and following the CI/CD process.
>
> I understand the fundamentals of Kubernetes, particularly how it manages containerized applications, availability, scaling, and updates.
>
> I understand the importance of health checks, such as readiness and liveness probes, to determine when an application can receive traffic and when a container needs to be restarted.
>
> I'm also familiar with rolling updates, resource limits, and horizontal scaling, although I wasn't the primary person responsible for configuring or administering the infrastructure."

<a id="k8s-concepts"></a>

### Essential technical concepts

| Concept | Concise explanation |
|---|---|
| **Pod** | The smallest deployable unit in Kubernetes. It can contain one or more containers that share networking and configured storage. |
| **Deployment** | Manages the desired application state, including replica count and Pod updates. |
| **Service** | Provides a stable endpoint for accessing a set of Pods, even when individual Pods are recreated. |
| **Readiness probe** | Checks whether an application is ready to handle requests. If it fails, the Pod is removed from the relevant Service endpoints until it becomes ready again. |
| **Liveness probe** | Checks whether the container is healthy. Repeated failures cause Kubernetes to restart the container. |
| **Startup probe** | Checks whether the application has started, allowing slow-starting applications to initialize before liveness and readiness checks take over. |
| **Requests and limits** | Requests specify resource needs used for scheduling; limits constrain CPU and memory consumption. |
| **HPA** | *Horizontal Pod Autoscaler*: automatically adjusts the number of replicas based on configured metrics. |
| **Rolling update** | Gradually replaces older Pods with new ones, aiming to maintain availability during an update. |
| **Rollback** | Allows a Deployment to return to a previous revision if an update causes problems. |

<a id="k8s-questions"></a>

### Follow-up questions

**1. How does a deployment work with GitLab CI/CD and Kubernetes?**

> "Typically, the pipeline runs tests, builds the application, creates a Docker image, and pushes it to a container registry. A deployment stage then updates the application version in Kubernetes, which manages the replacement of Pods."

**2. What is the difference between readiness and liveness probes?**

> "Readiness determines whether a Pod can receive traffic. Liveness identifies when a container needs to be restarted. In Spring Boot, we can expose these checks through Spring Boot Actuator endpoints."

Examples:

```text
/actuator/health/readiness
/actuator/health/liveness
```

**3. How does Kubernetes support high availability?**

> "By managing multiple replicas, replacing failed Pods, and distributing traffic among available Pods. Readiness probes and rolling updates can also help reduce downtime."

**4. What happens if a Pod uses too much memory?**

> "If a container exceeds its memory limit, it may be terminated with an OOMKilled status. That's why it's important to configure requests and limits appropriately and monitor resource consumption."

**5. How does a rolling update work?**

> "Kubernetes gradually replaces Pods running the previous version with Pods running the new version, based on settings such as maxSurge and maxUnavailable. Readiness probes help prevent traffic from being sent to instances that aren't ready yet."

**6. How do you prevent ongoing requests from being interrupted during a deployment?**

> "One approach is to combine readiness probes, rolling updates, and graceful shutdown. In Spring Boot, we can configure graceful shutdown so the application has time to complete in-flight requests before stopping."

**7. How would you troubleshoot a Pod that won't start?**

> "I'd first check the Pod status and events with kubectl describe, then inspect the logs using kubectl logs. I'd investigate startup errors, environment variables, probe configurations, and resource limits."

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

[↑ Back to Top](#start)

---

<a id="mcdonalds"></a>

## 4. Biggest Professional Challenge — McDonald's

**Focus:** legacy-system refactoring, **Oracle SQL/PLSQL query optimization**, and communication in English.

<a id="mcd-story"></a>

### Interview story (60–90 seconds)

> "One of the biggest challenges in my career was working on an international project for McDonald's.
>
> The project involved a legacy system built with Java 8. It had years of established business rules and relied heavily on an Oracle database, including complex SQL queries and PL/SQL stored procedures.
>
> My main challenge was to improve and refactor the system without breaking existing functionality, because even a small change could affect other parts of the application.
>
> I worked on refactoring legacy code to improve the separation of responsibilities, reduce complexity, and make the code easier to maintain. I also optimized SQL queries and PL/SQL routines by investigating performance bottlenecks and identifying opportunities to make database operations more efficient.
>
> Another challenge was communicating in English every day with an international team, especially when clarifying business rules and discussing changes to functionality that had been in place for years.
>
> To address this, I made sure I understood the existing behavior before changing the code, aligned technical decisions with the team, and delivered improvements incrementally to reduce risk.
>
> As a result, we improved the quality and maintainability of the legacy code and optimized database performance in the areas we worked on.
>
> This experience strengthened my ability to investigate complex systems, work safely with business-critical legacy code, and collaborate effectively in an international environment."

<a id="mcd-questions"></a>

### Technical follow-up questions

| Interview question | Suggested answer |
|---|---|
| **How did you refactor the Java 8 code?** | I reviewed the existing code to identify long methods, mixed responsibilities, and duplicated logic. I focused on separating responsibilities, improving readability, and preserving the original behavior. |
| **How did you identify slow SQL queries?** | I analyzed expensive queries, checking joins, filters, indexes, and execution plans to identify bottlenecks. |
| **How did you optimize PL/SQL?** | I reviewed stored-procedure processing to identify redundant operations, repeated queries, and opportunities to reduce database round trips. |
| **How did you make sure refactoring didn't break existing functionality?** | I preserved existing contracts and behavior, validated key business scenarios, and ran tests before delivery. |
| **What was the biggest risk?** | Accidentally changing a business rule embedded in legacy code or in a stored procedure used by other processes. |
| **Why didn't you rewrite everything?** | A full rewrite would have involved significant cost and risk. Incremental refactoring allowed us to improve code quality and performance without replacing the entire system. |

[↑ Back to Top](#start)
