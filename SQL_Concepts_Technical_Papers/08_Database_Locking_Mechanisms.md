# Database Locking Mechanisms and Concurrency Control

## Abstract

Locking coordinates concurrent database operations and protects data
from conflicting modifications. This paper explains shared and exclusive
locks, row/page/table granularity, intention locks, lock duration,
deadlocks, optimistic alternatives, and practical techniques for
reducing contention.

## 1. Why locking is needed

Suppose two transactions update the same inventory row at nearly the
same time. Without concurrency control, one update might overwrite
another or both might act on an outdated value. Locks are one mechanism
databases use to coordinate conflicting operations.

Not every database relies only on locks. MVCC, timestamp ordering, and
optimistic validation may also be used.

## 2. Shared and exclusive locks

-   **Shared (S) lock:** allows compatible readers to hold a lock
    concurrently, while preventing conflicting writes.
-   **Exclusive (X) lock:** protects a modification and generally
    conflicts with other locks on the same resource.

A simplified compatibility view:

  Requested/held         Shared   Exclusive
  ---------------- ------------ -----------
  Shared             Compatible   Conflicts
  Exclusive           Conflicts   Conflicts

Actual lock modes and compatibility matrices are more detailed in
production database engines.

## 3. Lock granularity

Locks may be acquired at different levels: - Row-level: protects
individual rows and allows more concurrency. - Page-level: protects a
storage page containing multiple rows. - Table-level: protects a whole
table. - Database-level: broad coordination, often for specialized
operations.

Fine-grained locks can improve concurrency but require more
lock-management overhead. Engines may escalate locks or use different
internal strategies.

## 4. Intention locks

In hierarchical locking systems, intention locks indicate that a
transaction intends to acquire finer-grained locks within a larger
object. They help the engine coordinate table-level and row-level
operations without checking every row lock individually.

## 5. Locking example

``` sql
BEGIN;

SELECT quantity
FROM inventory
WHERE product_id = 50
FOR UPDATE;

UPDATE inventory
SET quantity = quantity - 1
WHERE product_id = 50;

COMMIT;
```

`FOR UPDATE` is supported by several relational engines, but syntax and
exact behavior differ. It requests a lock suitable for protecting a row
that will be updated. The transaction should validate that quantity is
sufficient before decrementing it.

## 6. Deadlocks

A deadlock occurs when transactions wait on one another in a cycle.

Example: - T1 locks row A and requests row B. - T2 locks row B and
requests row A. - Neither can proceed.

Databases typically detect deadlocks and abort one transaction as a
victim. The application must be prepared to retry the whole transaction
when appropriate.

### Reducing deadlocks

-   Access rows and tables in a consistent order.
-   Keep transactions short.
-   Avoid user interaction inside transactions.
-   Create suitable indexes so updates locate rows efficiently.
-   Update only the rows required.
-   Handle deadlock errors with bounded retry and backoff.

## 7. Lock waits and timeouts

A lock wait occurs when a transaction requests an incompatible lock held
by another transaction. A lock timeout may abort a statement or
transaction depending on the engine. Monitor wait duration, blockers,
and the SQL statements holding locks.

## 8. Locking and MVCC

Multi-Version Concurrency Control maintains multiple row versions so
readers can often access a consistent snapshot without blocking writers.
Writers still need coordination, and some operations still acquire
locks. MVCC reduces certain read/write conflicts; it does not eliminate
all locking or deadlocks.

## 9. Optimistic concurrency control

Optimistic approaches allow work to proceed and check for conflicts at
update or commit time. A version column is a common application
technique:

``` sql
UPDATE products
SET price = 120.00,
    version = version + 1
WHERE product_id = 7
  AND version = 4;
```

If zero rows are updated, another transaction may have changed the row.
The application can reload and resolve the conflict.

## 10. Diagnosing contention

Inspect database lock views, blocked-session reports, deadlock logs, and
query plans. Record transaction duration and identify long-running
blockers. Do not solve contention by blindly increasing timeouts; that
may only make users wait longer.

## 11. Conclusion

Locks protect shared data but can reduce concurrency when transactions
conflict. Understand lock modes and granularity, keep transactions
short, use consistent access ordering, and monitor real blocking
patterns. MVCC and optimistic techniques complement rather than
universally replace locking.
