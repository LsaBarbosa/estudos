## 1. Start with the domain and business responsibility

> “I would start with the domain and business responsibility, not the infrastructure.”
>
> First, it is necessary to understand what the module solves, what integrations it performs, and what operations it needs to handle.
>
> This is directly linked to DDD.

---

## 3. Define service boundaries

Each microservice must have clear boundaries regarding:
>which data and rules belong to the service;
>
>which operations it offers and events it publishes;
>
>what information it receives from other services.

In DDD, this boundary can be associated with the concept of a **Bounded Context**.

---

# 7. Define non-functional requirements

> “Next, I would define non-functional requirements, such as availability, volume, latency, security, and consistency.”

## 7.1 Availability

Availability represents the amount of time the service remains operational.

To improve availability, the following can be used:

* multiple instances;
* load balancer;
* health checks;

---

## 7.2 Volume and throughput

##### It is necessary to estimate:

* messages and requests per second;

##### Volume influences:

* Number of instances and database indexes;
* Partitioning, caching, and queues;
* Connection limits and pool sizes;

---

## 7.3 Latency

Latency is the time between the request and the response.

It is necessary to define percentiles, not just the average.

```text
P95 less than 300 milliseconds.
P95 indicates that 95% of requests must respond within that time.
```

The average can mask very slow requests.

### Common sources of latency

* unindexed queries, chained calls between services;
* misconfigured connections, connection pool bottlenecks;
* blocking processing;
---
## 7.5 Consistency

> an ACID transaction can guarantee local consistency. >
>`@Transactional` typically does not create a single transaction spanning multiple microservices.

##### Distributed consistency must be handled.

* **Eventual consistency:** A consistency model where, after a propagation period, all replicas converge to the same state.

* **Saga:** A pattern for coordinating distributed transactions through a sequence of local transactions and compensating actions.

* **Events:** Immutable records representing something significant that occurred in the business domain, which can be consumed by other systems.

* **Compensations:** Operations executed to undo or neutralize the effects of a previous step when a Saga fails.

* **Outbox Pattern:** A pattern that ensures reliable event publication by writing the data change and the event within the same local transaction.

* **Reconciliation:** The process of verifying and correcting discrepancies between systems to ensure the final state is consistent.

* **Idempotency:** A property of an operation that produces the same result when executed one or more times with the same input.

---

# 8. Designing for failure handling

> “I would also design for failure handling from the very beginning.”

In distributed systems, failures are not rare exceptions.

## 8.3 Circuit Breaker

The Circuit Breaker prevents continued calls to a dependency that is repeatedly failing.

Main states:

```text
Closed:
calls proceed normally.

Open:
calls are immediately blocked.

Half-open:
some test calls are allowed.
```
It helps prevent:

* cascading failures;
* thread saturation;
* connection exhaustion;
* progressive latency increase.

---

## 8.4 Bulkhead

The Bulkhead pattern isolates resources to prevent a dependency from consuming the service's entire capacity.

For example, calls to the reporting service should not consume all the threads used by the payment service. The idea is similar to a ship's compartments: a failure in one compartment shouldn't sink the entire vessel.

---

# 10. Design for observability

> “I would also design for observability from the start.”

Observability allows you to understand the system's internal state through the signals it produces.

The best-known pillars are:

* logs;
* metrics;
* distributed traces.

---

## 10.1 Logs

Logs should be structured and contain context.

It is also necessary to avoid including sensitive data in logs.

---

## 10.2 Metrics

Metrics allow you to track the service's aggregate behavior.

With Spring Boot Actuator and Micrometer, metrics can be exported to Prometheus.

---

## 10.3 Distributed tracing

Distributed tracing tracks a request across multiple services.

All spans share a `traceId`.

This allows you to discover:

* where the failure occurred;
* which service was slow;
* how much time each step took;
* which dependency caused the problem.

Common technologies include:

* OpenTelemetry;
* Jaeger;
* Zipkin;
* Grafana Tempo.

---

# 13. Plan for testing from the start

> “I would also design for testing from the start.”

Microservices require different levels of testing.

---

## 13.1 Tests
Unit tests

Validate isolated rules.

They are fast and do not depend on infrastructure.

---

## 13.2 Integration tests

Validate integration with real or near-real components.

Testcontainers is very useful in the Java ecosystem.

This reduces differences between the test environment and the production environment.

---

## 13.3 Contract tests

Validate whether the producer and consumer agree on the contract.

These tests help detect incompatible changes before deployment.

---

## 13.4 End-to-end tests

Validate the complete flow.

They should exist, but in smaller numbers, because they are:

* slower;
* more fragile;
* more expensive;
* harder to diagnose.

---

## 13.5 Failure tests

It is also necessary to test negative scenarios:

A distributed system is not adequately tested if only the happy path has been validated.

---

# 14. Planning the deployment strategy

> “I would also design the deployment strategy from the start.”
---

## 14.1 Rolling deployment

Instances are replaced gradually.

```text
Version 1
Version 1
Version 1

Afterwards:

Version 1
Version 1
Version 2

Afterwards:

Version 1
Version 2
Version 2
```

For a time, two versions coexist.

Therefore, contracts and the database must be compatible across versions.

---

## 14.2 Blue-green deployment

There are two environments:

```text
Blue: current version.
Green: new version.
```

After validating Green, traffic is directed to it.

If a failure occurs, traffic can be switched back to Blue.

---

## 14.3 Canary deployment

The new version receives a small percentage of traffic.

Example:

```text
95% of traffic → current version.
5% of traffic → new version.
```

If metrics and error rates remain healthy, the percentage is increased.

---


---

# Summary example: order microservice

## Responsibility

Manage the order lifecycle.

## Owned data

* order;
* items;
* status;
* history;
* total value.

## API

```text
POST /orders
GET /orders/{id}
POST /orders/{id}/cancellation
```

## Published events

```text
OrderCreated
OrderConfirmed
OrderCancelled
```

## Consumed events

```text
PaymentApproved
PaymentDeclined
StockReserved
StockReservationFailed
```

## Non-functional requirements

```text
Availability: 99.9%.
P95: up to 300 milliseconds.
Peak: 1,000 requests per second.
Consistency: eventual consistency across order, stock, and payment.
```

## Resilience

```text
Timeouts on remote calls.
Retries only for transient failures.
Circuit breaker for dependencies.
Idempotent consumers.
Dead-letter queue for unprocessable messages.
```

## Observability

```text
Logs with orderId, eventId, and traceId.
Metrics for created and cancelled orders.
Error rate metric.
Tracing for integrations.
Latency and failure alerts.
```

---

# Appropriate interview response

> I would start by identifying a cohesive business capability and clearly defining the domain boundary. The microservice should control its own rules and data, exposing well-defined contracts via APIs or events.
>
> Then, I would gather non-functional requirements, such as availability, volume, latency, security, and consistency levels. This information would drive decisions regarding persistence, synchronous vs. asynchronous communication, scalability, and resilience mechanisms.
>
> From the outset, I would design for timeouts, controlled retries, circuit breakers, idempotency, duplicate message handling, and a distributed consistency strategy. I would also include structured logging, metrics, tracing, contract testing, integration testing, and a safe strategy for deployment and database evolution.
>
> Finally, I would validate whether there is a genuine need for separation. Microservices offer autonomy and independent scalability but increase operational and distributed system complexity. When these benefits do not justify the cost, I would prefer to start with a well-structured modular monolith.
