# ACID Properties in Relational Database Systems

## Abstract

ACID is a set of properties that make database transactions dependable.
Atomicity, Consistency, Isolation, and Durability define how a database
should behave when operations succeed, fail, overlap, or encounter a
system crash. This paper explains each property, the mechanisms commonly
used to implement it, practical SQL examples, failure scenarios, and
design considerations.

## 1. Introduction

A transaction is a logical unit of database work. Consider transferring
₹500 from account A to account B. The operation requires debiting A and
crediting B. If only the debit succeeds, the database contains an
invalid business result. ACID provides the guarantees needed to prevent
such outcomes.

ACID is a behavioral contract, not one specific algorithm. Database
engines combine logging, constraints, concurrency control, recovery, and
storage guarantees to implement it.

## 2. Atomicity

**Atomicity means all operations in a transaction happen, or none of
them do.** A transaction must not leave a partially completed business
operation.

``` sql
BEGIN;

UPDATE accounts
SET balance = balance - 500
WHERE account_id = 101;

UPDATE accounts
SET balance = balance + 500
WHERE account_id = 202;

COMMIT;
```

If an error occurs before `COMMIT`, the application or database can
issue `ROLLBACK`. The database restores the changes made by the
transaction.

### How it is implemented

Many relational databases use a transaction log or write-ahead log
(WAL). Before modified data pages are safely written, the log records
enough information to recover or undo the operation. Recovery uses log
records to determine which transactions committed and which must be
rolled back.

### Important limitation

Atomicity does not automatically protect a workflow spanning independent
systems. A database transaction cannot, by itself, roll back an email
already sent or a payment processed by a separate provider. Distributed
workflows may need sagas, compensating actions, or an outbox pattern.

## 3. Consistency

**Consistency means a committed transaction preserves all database rules
and invariants.** It moves the database from one valid state to another
valid state.

Examples of rules: - An account balance cannot be negative, if the
business rule forbids it. - An employee must reference an existing
department. - An order total must satisfy the application's defined
calculation rules. - A primary key must be unique and non-null.

``` sql
CREATE TABLE accounts (
    account_id BIGINT PRIMARY KEY,
    balance DECIMAL(12, 2) NOT NULL,
    CONSTRAINT chk_nonnegative_balance CHECK (balance >= 0)
);
```

The database can enforce structural rules through `PRIMARY KEY`,
`FOREIGN KEY`, `UNIQUE`, `NOT NULL`, and `CHECK` constraints. Other
business invariants may require carefully designed transactions or
application logic.

Consistency is only as strong as the rules actually declared and
enforced. A database cannot infer every business rule.

## 4. Isolation

**Isolation controls how concurrent transactions interact.** Ideally,
each transaction behaves as though it ran without interference from
other transactions. Real databases offer isolation levels that trade
concurrency against the anomalies they permit.

For example, two users might attempt to reserve the last available seat.
Without suitable concurrency control, both could read "1 seat available"
and both insert a reservation. Constraints, row locks, serializable
execution, or atomic conditional updates can prevent overselling.

Isolation is explored in detail in the separate paper on database
isolation levels.

## 5. Durability

**Durability means that once a transaction is committed, its result
survives failures covered by the database's durability guarantees.**

Databases commonly use: - Write-ahead logging. - Checkpoints to limit
recovery work. - Flush operations to persist commit records. -
Replication and backups for broader failure protection.

A successful commit generally means the database has made the commit
durable according to its configuration. Some systems allow relaxed
settings that acknowledge commits before data is fully persisted to
stable storage. Such settings change the failure guarantees.

Durability is not the same as backup. Replication can copy accidental
deletion or corruption; backups and point-in-time recovery address
different failure modes.

## 6. Complete transaction example

``` sql
BEGIN;

UPDATE accounts
SET balance = balance - 500
WHERE account_id = 101
  AND balance >= 500;

-- The application must verify exactly one row was updated.

UPDATE accounts
SET balance = balance + 500
WHERE account_id = 202;

COMMIT;
```

If the debit updates zero rows, the application must not continue as if
the transfer succeeded. It should roll back. Production code should also
handle missing destination accounts and retryable concurrency errors.

## 7. ACID and distributed systems

A single database can provide strong ACID transactions. Across multiple
services or databases, guaranteeing one atomic transaction is harder.
Two-phase commit is one option but introduces coordination and
availability costs. Sagas use a sequence of local transactions with
compensating actions when later steps fail.

## 8. Common misconceptions

1.  ACID does not mean a database can never fail; it defines behavior
    during failures.
2.  Consistency is not the same as replication consistency.
3.  Isolation does not always mean transactions literally execute one at
    a time.
4.  A successful commit does not replace backups or disaster recovery.
5.  Application code must still validate business outcomes and handle
    errors.

## 9. Practical checklist

-   Put related database changes in one transaction.
-   Enforce data invariants with constraints where possible.
-   Choose an isolation level based on the anomalies the business can
    tolerate.
-   Check affected-row counts for conditional updates.
-   Keep transactions short and avoid network calls while holding locks.
-   Test rollback, deadlock, process-crash, and retry paths.
-   Understand the durability settings of the deployed database.

## 10. Conclusion

ACID is foundational to reliable relational data management. Atomicity
prevents partial transactions, consistency preserves declared rules,
isolation controls concurrency anomalies, and durability protects
committed results. Correct database design requires understanding both
the guarantees and their boundaries.
