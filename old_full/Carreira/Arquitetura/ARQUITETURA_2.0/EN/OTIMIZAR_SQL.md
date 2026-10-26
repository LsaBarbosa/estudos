# Resource Consumption.
> First, you need to discover **which queries are actually consuming resources**.
>
> Acts as SQL observability.

| Database                 | Equivalent to `pg_stat_statements`                       | Function                                                                                     |
| ------------------------ | -------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **PostgreSQL**           | `pg_stat_statements`                                     | Aggregate statistics on executed queries. |
| **Oracle Database**      | Automatic Workload Repository (AWR), Statspack, and `V$SQL` | Collects statistics on SQL, CPU usage, I/O, execution time, and execution plans. |

Without metrics, any optimization is just a guess.

---


# Proper Indexes
The main SQL optimization techniques, regardless of the database (PostgreSQL, MySQL, SQL Server, Oracle, etc.), are:

## 1. Create proper indexes

Speeds up searches, filters, JOINs, and sorting operations.

* Indexes: simple | composite | unique | covering

---

## Filter as early as possible

Reduce the number of records before performing JOINs.

| Situation    | Order            |
| ------------ | ---------------- |
| ❌ **Bad**    | `JOIN` → `WHERE` |
| ✅ **Better** | `WHERE` → `JOIN` |


(The optimizer usually handles this, but well-written queries make it easier.)

---

## 4. Optimize JOINs
| Practice                                                       | Why? | Trade-off                                                                                                                                         |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the correct columns                                        | Prevents incorrect relationships, duplicates, and unnecessary increases in the volume of processed data. | May require reviewing the data model and the relationship rules between tables. |
| Ensure indexes on join keys                                    | Reduces the cost of locating related records, especially in large tables. | Indexes increase storage consumption and the cost of write operations, such as `INSERT`, `UPDATE`, and `DELETE`. |
| Avoid unnecessary `JOIN`s                                      | Reduces the volume of data processed, memory usage, and query execution time. | Removing a `JOIN` might require another query or additional processing within the application. |
| Correctly choose between `INNER JOIN`, `LEFT JOIN`, `EXISTS`, etc. | Allows using the most suitable operation for the query's needs and helps the database generate a more efficient execution plan. | A more efficient choice might make the query less intuitive or alter the result set if the semantics are not fully understood. |


---

## 5. Avoid N+1 queries

| Solution                      | How it works                                                                                                                                | Advantage                                                                                | Trade-off                                                                                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`JOIN FETCH`**              | Loads the main entity and its relationships in a single SQL query using `JOIN FETCH`. | Customers and their orders are loaded in a single query. | Can generate multiple repeated rows for the same entity due to the `JOIN`. It is usually necessary to use `DISTINCT` and be careful with pagination. |
| **`EntityGraph`**| **Entity Graphs** | Allows defining which relationships should be loaded in a specific query, without altering the entity's default `fetch` strategy. | Avoids writing `JOIN FETCH` in multiple queries and keeps the repository more declarative. | Can load a large volume of data when many relationships are included simultaneously. |
| **Two-query approach** | Executes one query to fetch the main entities and another to fetch all relationships at once (usually using `IN`). | Avoids the N+1 problem without generating a result set with many duplicate rows. | Requires additional application-side processing to group and associate the returned data. |
---

## Use pagination

Avoid returning thousands of records.

Use:

```sql
LIMIT

OFFSET
```

or **Keyset Pagination (Seek Method)** techniques for large volumes.

---


## 13. Choose the right index type

Not every index solves every problem.

Examples:

* Simple index
* Composite index
* Partial index
* Expression-based index
* Full-text index
* Spatial index

---

## Analyze the execution plan

Tools such as:

* EXPLAIN
* EXPLAIN ANALYZE
* Execution Plan

allow you to check:

* scans
* joins
* sorting
* index usage
* expensive operations

---

## Partition large tables

Divides large volumes into smaller parts.

Examples:

* by date
* by region
* by customer

---

## Use batch operations

Avoid: 1,000 INSERTs

Prefer: 1 INSERT with 1,000 rows


or batch operations.

---

# Recommended optimization workflow

```text
Slow application
│
▼
Identify expensive queries (pg_stat_statements)
│
▼
Analyze execution plan (EXPLAIN ANALYZE)
│
▼
Check scans, joins, cardinality, memory, and I/O
│
▼
Fix root cause
(indexes, SQL, statistics, data model, partitioning)
│
▼
Analyze infrastructure
(pool, autovacuum, locks, bloat, checkpoints, memory)
│
▼
Validate in an environment with a representative workload
```

## Relationship with Spring Boot

In Spring Boot applications, PostgreSQL optimization is not limited to the database itself. It is also necessary to consider how the application accesses the data:

* Avoid the **N+1** problem in Hibernate.
* Use projections (DTOs) when loading full entities is unnecessary.
* Configure **HikariCP** correctly, avoiding connection pools that are excessively large or small.
* Define transactions (`@Transactional`) with the shortest possible duration to reduce contention and locking.
* Monitor slow queries using tools such as Micrometer, OpenTelemetry, and Hibernate logs.
* Use caching (e.g., Redis) only for truly frequent queries that can tolerate eventual data staleness.

Effective optimization results from a combination of **efficient queries, a suitable data model, correct PostgreSQL configuration, and a well-implemented data access layer within the Spring Boot application**.
