# Database Indexes: Structures, Query Performance, and Trade-offs

## Abstract

Indexes are auxiliary data structures that help database engines locate
rows without scanning an entire table. They can dramatically improve
selective reads and joins, but they consume storage and add write
overhead. This paper covers index fundamentals, common index types,
composite and covering indexes, query plans, and practical tuning.

## 1. What is an index?

An index stores search-key values and references to corresponding rows
or data pages. It is conceptually similar to a book's index: it helps
locate relevant content without reading every page.

Without a useful index, a query may perform a full table scan. With an
appropriate index, the engine may perform an index seek or range scan
and fetch only relevant rows.

## 2. B-tree indexes

B-tree and B+ tree variants are common general-purpose index structures.
They maintain sorted keys and support equality lookups, range
predicates, and ordered retrieval efficiently.

``` sql
CREATE INDEX idx_employees_department
ON employees(department_id);
```

A query filtering by department may benefit:

``` sql
SELECT employee_id, first_name
FROM employees
WHERE department_id = 10;
```

Exact implementation details differ between database engines.

## 3. Hash indexes

Hash indexes use a hash function to locate entries and are typically
suited to equality comparisons. They generally do not support ordered
range scans. Availability and behavior vary by database product and
storage engine.

## 4. Unique indexes

A unique index enforces uniqueness as well as supporting lookup.

``` sql
CREATE UNIQUE INDEX uq_users_email
ON users(email);
```

This is stronger than checking uniqueness only in application code,
because concurrent requests can otherwise race.

NULL uniqueness behavior differs across database systems, so verify the
target engine's rules.

## 5. Composite indexes

A composite index contains multiple columns.

``` sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, order_date);
```

This commonly supports predicates beginning with `customer_id`, such as:

``` sql
SELECT *
FROM orders
WHERE customer_id = 42
  AND order_date >= DATE '2026-01-01';
```

The column order matters. An index on `(customer_id, order_date)` is not
generally equivalent to one on `(order_date, customer_id)`. The
leftmost-prefix behavior is common in B-tree implementations, though
exact optimizer rules vary.

## 6. Covering indexes

An index is covering for a query when it contains all columns needed to
satisfy that query, allowing the engine to avoid fetching base-table
rows in some cases.

Some engines support included columns:

``` sql
-- Syntax example for engines that support INCLUDE.
CREATE INDEX idx_orders_customer
ON orders(customer_id)
INCLUDE (order_date, total_amount);
```

Do not assume this syntax is portable across database products.

## 7. Other index families

-   **Bitmap indexes:** useful for some analytical workloads and
    low-cardinality columns in certain engines.
-   **Full-text indexes:** support token and text-search operations.
-   **Spatial indexes:** accelerate geographic and geometric queries.
-   **Partial/filtered indexes:** index only rows satisfying a
    predicate.
-   **Expression/function-based indexes:** index a computed expression,
    such as a normalized email value.

Choose an index type based on the database engine and query pattern.

## 8. Indexes and query plans

Use the database's plan tools, such as `EXPLAIN` or `EXPLAIN ANALYZE`,
to inspect access paths, estimated/actual row counts, join algorithms,
and expensive operations.

An index may not be used when: - A large fraction of the table must be
read. - The predicate applies a function to the indexed column and no
matching expression index exists. - Data types cause implicit
conversions. - Statistics are stale. - The optimizer estimates a scan is
cheaper.

A sequential scan is not automatically bad. Reading most of a table
sequentially can be cheaper than many random lookups.

## 9. Costs of indexes

Every additional index can: - Consume disk and memory. - Increase
insert, update, and delete work. - Cause page splits or fragmentation in
some structures. - Increase backup and maintenance time. - Complicate
bulk loading.

Indexes should be selected, not accumulated indiscriminately.

## 10. Practical tuning method

1.  Identify slow queries using workload metrics.
2.  Inspect actual execution plans.
3.  Check row estimates versus actual rows.
4.  Identify selective filters, join keys, and sort/group requirements.
5.  Design an index aligned with the query's predicates and ordering.
6.  Test representative read and write workloads.
7.  Keep or remove the index based on measured benefit.

## 11. Common misconceptions

-   Indexing every column is not good design.
-   A foreign key does not guarantee that every database automatically
    creates an index on it.
-   Indexes do not fix incorrect joins or poor query logic.
-   More indexes can make writes slower.
-   The optimizer's choice depends on statistics and data distribution.

## 12. Conclusion

Indexes are performance tools with real storage and write costs.
Effective indexing starts with workload measurement and execution-plan
analysis, not guesswork. Build indexes around actual access patterns and
regularly review whether they still provide value.
