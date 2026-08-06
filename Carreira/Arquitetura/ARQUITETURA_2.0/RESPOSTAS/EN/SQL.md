# What are indexes in PostgreSQL?

> Indexes are data structures used to speed up the search for records in a table, preventing the database from needing to traverse all the rows during a query.
>
> They improve reading performance, but increase the time for writing operations, as INSERT, UPDATE and DELETE also need to be updated.
>
> Therefore, I would only create indexes for columns frequently used in filters, JOINs, sorting or constraints.

The search goes from a **Table Scan** to an **Index Scan**, significantly reducing the number of pages read.

---

# How do you use EXPLAIN ANALYZE?

> Used to understand how Database executes a query and identify possible performance bottlenecks.
>
> Reporting operations such as Sequential Scan, Index Scan, Nested Loop, Hash Join and Sort, in addition to the time spent in each step and the number of lines processed.
>
> Instead of optimizing queries through trial and error, I would use EXPLAIN ANALYZE to base decisions on evidence.

### Example

```sql
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE client_id = 10;
```

You can return:

```text
Seq Scan or Index Scan
```

If it appears:

```text
SeqScan

in a huge table, it may indicate a lack of index.
```
---

# What is JOIN?

> JOIN is the operation used to combine records from two or more tables based on a relationship condition.
>
> The most common types are INNER JOIN, which returns only records that match in both tables, and LEFT JOIN, which returns all records from the left table, even if there is no match in the right table.
>
> To obtain good performance, it is important that the columns used in the JOIN are indexed when appropriate, reducing the number of necessary readings.

### INNER JOIN

```sql
SELECT *
FROM client c
JOIN request p
ON c.id = p.cliente_id;
```

Only customers with orders.

---

### LEFT JOIN

```sql
SELECT *
FROM client c
LEFT JOIN request p
ON c.id = p.cliente_id;
```

All customers.

Even those who have never bought it.

---

# What are Locks?

> Locks control concurrent access to data and ensure transaction consistency.
>
> While a transaction changes a record, other transactions are prevented from modifying it until commit or rollback.
>
> PostgreSQL uses different types of locks, such as row locks, table locks and internal locks for administrative operations.
>
> In concurrent environments, I would monitor for prolonged locking and deadlocks, as they can increase latency or block other transactions.

---

# What are Isolation Levels?

> Isolation Level defines the degree of isolation between concurrent transactions, balancing data consistency and performance.
>
> The PostgreSQL default is Read Committed, in which each query only sees data already committed by other transactions.
>
> In scenarios that require greater consistency, levels such as Repeatable Read or Serializable can be used, which reduce concurrency anomalies, but increase the possibility of blocking or conflicts.
>
> The choice of isolation level depends on business requirements and there is no ideal level for all cases.

### Main levels

| Level | Avoid |
| ---------------- | ------------------------------------ |
| Read Uncommitted | practically not used in PostgreSQL |
| Read Committed | DirtyRead |
| Repeatable Read | Non Repeatable Read |
| Serializable | PhantomRead |

---


# What is the N+1 problem?

> When a query searches for a list of records and then performs another search for each item in that list, increasing the number of queries in the database.
>
> Search for customer orders. it searches for customers then a search for each customer's order list
>
> In Spring Data JPA applications, this problem often occurs due to lazy loading of relationships.
>
> To avoid it, I use strategies like `JOIN FETCH`, `EntityGraph` or specific queries designed to load only the necessary data.


## How to solve N+1?

### 1. JOIN FETCH

```java
@Query("""
SELECT c
FROM Customer c
JOIN FETCH w.orders
""")
```

Load customers and orders in a single query.

**Advantage:** eliminates N+1.

**Trade-off:** can return duplicate rows for the same customer due to JOIN, and it is common to use `DISTINCT`.

---

### 2. EntityGraph

```java
@EntityGraph(attributePaths = "orders")
List<Customer> findAll();
```

Allows you to inform which relationships should be loaded only in that query, maintaining the `LAZY` relationship in the rest of the application.

---

# Interview tip

A very common question is:

> **"How would you investigate a slow query in PostgreSQL?"**

A solid answer would be:

> I would start by running **EXPLAIN ANALYZE** to understand the query execution plan.I would determine whether the database is using an **Index Scan** or performing a **Sequential Scan**, and analyze the **JOIN** types, the number of rows processed, and the time spent at each stage. I would also evaluate the presence of appropriate indexes and outdated statistics, as well as issues such as the **N+1** problem, **locks**, or long-running transactions. Based on this evidence, I would decide whether the best solution is to create or adjust indexes, rewrite the query, revise the data access model, or change the loading strategy used by the application.
