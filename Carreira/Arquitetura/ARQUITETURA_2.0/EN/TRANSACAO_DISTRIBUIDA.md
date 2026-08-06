## What is a distributed transaction?

A **distributed transaction** happens when a single business operation needs to change **more than one independent resource**, but all of these resources need to remain consistent.


# The problem

Within a single bank it is simple:

```text
BEGIN

Update account A
Update account B

COMMIT
```

If error occurs:

```text
ROLLBACK
```

Everything returns to its previous state.

But imagine:

```text
Order Service
↓

Bank A

Stock Service
↓

Bank B

Payment Service
↓

Bank C
```

Each bank has its own transaction.

There is no single `COMMIT` that encompasses them all.

This is why distributed transactions arise.

---


# How to solve?

There are two classic approaches.

---

# 1. Two-Phase Commit (2PC)

>She tries to get everyone to confirm or everyone to cancel.
>
> There is a component that controls the entire operation called: `Coordinator`

---

## Phase 1 — Prepare

The coordinator asks everyone:`"Can you confirm this transaction?"`

Each participant runs everything internally, but still **does not commit**. `Everyone is waiting.`

---

## Phase 2 — Commit

If everyone responds: `OK`

The `coordinator` sends: `COMMIT` to everyone.

Now everyone definitely records.


### And if one of them answers "no" it's ROLLBACK for everyone.

So no one confirms.

---

### TRADE-OFF

> "Tight coupling, locks, and coordinator dependency."
* Everyone needs to participate in the same transaction, which reduces the independence of microservices.
* While waiting for the final decision, participants keep resources locked.
* If the coordinator goes down, services are left waiting
---


Therefore, it is usually avoided in modern architectures.

---

# The alternative: Saga

> "In microservices, I generally prefer a Saga."

* Saga is a standard for maintaining consistency **without a global transaction**.

* Each service executes only its **local transaction**.



#### What if something goes wrong?

The solution is to take **compensatory actions**.
* It logically undoes the previous effect

Note that it is not a `ROLLBACK` from the bank. It is a **new transaction**, which changes the state to reflect the cancellation.

---

# How to implement Saga in Spring?

There are two main ways.

## 1. Choreography


| Category | Items |
| ---------------- | --------------------------------------------------------------------------------------------- |
| **Definition** | • There is no central coordinator.<br>• Each service publishes events and reacts to received events.<br>• Each service knows only the events relevant to it. |
| **Advantages** | • Low coupling.<br>• Easy to scale.<br>• No single point of failure. |
| **Disadvantages** | • Complex flows are difficult to follow.<br>• Debug and observability require attention. |


---

## 2. Orchestration
| Appearance | Description |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| **Definition** | There is an **orchestrator** who decides the sequence of steps and coordinates the execution of the process. |
| **Advantages** | • Centralized flow.<br>• Simpler to monitor.<br>• Best for long and complex processes. |
| **Disadvantages** | • Introduces a central component.<br>• Can increase coupling to the orchestrator. |

---

# Relationship with Spring Boot

In Spring Boot applications it is common to use:

* **`@Transactional`** to ensure atomicity **within a single database**.
* **Spring for Apache Kafka** for exchanging events between microservices.
* **Transactional Outbox Pattern** to reliably publish events after a local transaction.
* **Idempotent consumers** to handle message redelivery.
* **Saga** (by choreography or orchestration) to coordinate distributed transactions.
* Tools such as **Temporal**, **Camunda**, **Orkes Conductor** or **Axon Framework** when there is a need to orchestrate complex flows.

---

# Summary for interviews

* A distributed transaction involves changes to multiple independent resources, such as different databases or microservices.
* **Two-Phase Commit (2PC)** seeks to ensure that everyone commits or everyone cancels a transaction, but introduces tight coupling, locks and dependence on a coordinator.
* In microservices architectures, the most common approach is the **Saga** pattern, in which each service runs its own local transaction.
* When a failure occurs, **there is no global rollback**; instead, **compensatory transactions** are performed that logically undo the effects of the previous steps.
* In the Spring ecosystem, it is common to combine **`@Transactional`**, **Kafka**, **Outbox Pattern**, **idempotence** and **Saga** for consistency eventual consistency between distributed services.
