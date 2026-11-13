# SQL Transactions and Transaction Management

## Abstract

Transactions group database operations into a logical unit that can be
committed or rolled back. They are essential for preserving business
invariants when multiple statements must succeed together. This paper
explains transaction boundaries, SQL commands, savepoints, autocommit,
error handling, isolation interactions, and application design.

## 1. Transaction fundamentals

A transaction is a sequence of operations treated as one unit of work.
For a bank transfer, the debit and credit must be coordinated. A
transaction provides a boundary around those changes.

``` sql
BEGIN;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

COMMIT;
```

If a failure occurs before commit, `ROLLBACK` cancels the transaction's
uncommitted changes.

## 2. Core transaction statements

-   `BEGIN` / `START TRANSACTION`: starts a transaction in engines that
    use explicit transaction blocks.
-   `COMMIT`: makes the transaction's changes committed.
-   `ROLLBACK`: abandons uncommitted changes.
-   `SAVEPOINT`: establishes a point to which a transaction can
    partially roll back.
-   `ROLLBACK TO SAVEPOINT`: undoes work after a savepoint while keeping
    the transaction active.

Syntax and behavior vary by engine. Some DDL statements cause implicit
commits in some systems.

## 3. Savepoints

``` sql
BEGIN;

UPDATE inventory
SET quantity = quantity - 1
WHERE product_id = 50;

SAVEPOINT after_inventory;

INSERT INTO order_items(order_id, product_id, quantity)
VALUES (9001, 50, 1);

-- If the item insertion needs to be undone:
ROLLBACK TO SAVEPOINT after_inventory;

COMMIT;
```

A savepoint is not an independent nested transaction. Releasing or
rolling back to a savepoint does not commit the outer transaction.

## 4. Autocommit

Many database clients operate in autocommit mode, where each statement
is committed automatically unless an explicit transaction is opened.
This is convenient for simple statements but dangerous when several
statements form one business operation.

Applications should understand their driver's transaction defaults and
explicitly manage transaction boundaries.

## 5. Error handling

A robust transaction workflow must: - Detect failed statements. - Roll
back when the transaction cannot safely continue. - Avoid reporting
success before commit succeeds. - Handle deadlocks and serialization
failures with bounded retries. - Ensure retries do not duplicate
external side effects.

Some engines mark the entire transaction as failed after a statement
error; others permit more recovery. Consult the target database's
semantics.

## 6. Conditional updates and concurrency safety

Avoid a read-then-write sequence when one atomic statement can enforce
the rule.

``` sql
UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1
  AND balance >= 100;
```

The application checks that exactly one row was affected. This reduces
the race window compared with reading the balance, deciding in
application code, and issuing an unconditional update.

## 7. Transactions in application code

A typical application pattern is: 1. Acquire a database connection. 2.
Begin a transaction. 3. Execute related parameterized statements. 4.
Validate affected-row counts and business conditions. 5. Commit on
success. 6. Roll back on failure. 7. Return the connection to the pool.

Keep transactions short. Do not hold locks while waiting for user input
or slow external HTTP calls.

## 8. Distributed transactions

A local database transaction cannot atomically include an unrelated
message broker or remote payment service unless a distributed
coordination protocol is used. Alternatives include: - **Transactional
outbox:** write business data and an outgoing event in one local
transaction; publish the event asynchronously. - **Saga:** coordinate
local transactions with compensating actions. - **Idempotency keys:**
make retries safe against duplicate requests.

## 9. Common transaction mistakes

-   Forgetting to commit or roll back.
-   Splitting one business operation across unrelated transactions.
-   Keeping transactions open too long.
-   Assuming a rollback can undo external actions.
-   Retrying every error without distinguishing transient and permanent
    failures.
-   Ignoring transaction boundaries in connection pools.

## 10. Conclusion

Transactions are the foundation of reliable multi-statement database
operations. Define business boundaries carefully, use atomic conditional
statements, handle failures explicitly, and keep transaction duration
short to reduce contention.
