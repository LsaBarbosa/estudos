# 2. "Within a single database, I would use ACID transactions"

When the entire operation takes place within the same database, the database itself handles consistency.

*   Consider the example of transferring a balance from one account to another:
`Either both happen` or `neither happens`.

This is exactly what an ACID transaction guarantees.

In Spring:

```java
@Transactional
public void transfer(...) {

debit(); 

credit();

}
```

If any operation fails:

```
ROLLBACK
```

Everything reverts to the previous state.

---

# What does ACID mean?
| Property             | Description                                                                                                     |
| -------------------- | --------------------------------------------------------------------------------------------------------------- |
| **A — Atomicity**    | All or nothing. The transaction is either fully completed (**commit**) or completely undone (**rollback**). |
| **C — Consistency**  | Ensures that database rules and constraints remain valid. If an operation violates them, a **rollback** occurs. |
| **I — Isolation**    | Ensures that concurrent transactions do not incorrectly interfere with each other during execution. |
| **D — Durability**   | After a **commit**, persisted data cannot be lost, even in the event of a system failure. |

---

# 3. "Constraints"

Constraints are rules enforced by the database.

Example:


| Constraint      | Function                                                                        |
| --------------- | ------------------------------------------------------------------------------- |
| **PRIMARY KEY** | Uniquely identifies each record in the table and does not allow null values. |
| **UNIQUE**      | Prevents duplicate values ​​in one or more columns. |
| **CHECK**       | Ensures that values ​​meet a defined condition. |
| **FOREIGN KEY** | Ensures referential integrity by requiring the referenced record to exist. |
| **NOT NULL**    | Prevents a mandatory column from accepting null values. |

**These constraints prevent the database from entering invalid states.**

### Examples

| Situation                                               | Constraint                 | Result                                                               |
| ------------------------------------------------------ | -------------------------- | -------------------------------------------------------------------- |
| Prevent duplicate emails                               | `UNIQUE(email)`            | The database rejects records with the same email. |
| Ensure an order belongs to an existing customer        | `FOREIGN KEY (cliente_id)` | The database prevents the creation of orders with a non-existent `cliente_id`.

---

# 4. "Concurrency control"

In distributed systems, multiple users modify the same data simultaneously.


## Pessimistic locking

#### Locks the record.

> While a transaction is working, no one else modifies that record.

- _*It is safe, but reduces concurrency.*_

---

## Optimistic locking

More common in Spring.

> Does not lock the record while it is being edited.
>
> Instead, it checks at save time whether another transaction modified the record beforehand.

- This is done using the `@Version` annotation.

> When Hibernate sees that no row was updated, it concludes:
>
> "Someone changed this record after you read it."
>
> Then it throws the exception: `OptimisticLockException`

---


# How to answer in an interview

> The first step is to understand the level of consistency the business requires. If the entire operation takes place within a single database, I use ACID transactions, constraints, and concurrency control—such as optimistic locking with `@Version` or pessimistic locking where appropriate. In microservices, there is no single transaction spanning services, so I typically adopt eventual consistency. To achieve this, I publish business events using the Outbox Pattern to prevent message loss, implement idempotent consumers to handle retries, and—when an operation involves multiple services—use Sagas with compensating actions to maintain consistency across the entire process.
