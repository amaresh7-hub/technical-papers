# Database Transaction Isolation Levels

## Abstract

Isolation levels define how much one transaction can observe the
intermediate or concurrent work of other transactions. This paper
explains common anomalies---dirty reads, non-repeatable reads, phantom
reads, and serialization anomalies---and compares the standard isolation
levels with practical database behavior.

## 1. Why isolation levels exist

Concurrent transactions improve throughput, but they can interfere. A
transaction may read data while another transaction changes it.
Isolation levels specify which forms of interference are allowed.

The SQL standard defines four familiar levels. Real database
implementations differ, especially because MVCC and locking strategies
can provide stronger behavior than the minimum standard requirements.

## 2. Common anomalies

### Dirty read

A transaction reads data written by another transaction that has not
committed. If the writer rolls back, the reader has observed a value
that never became committed.

### Non-repeatable read

A transaction reads the same row twice and gets different values because
another transaction committed an update between the reads.

### Phantom read

A transaction repeats a predicate query and sees a different set of rows
because another transaction inserted, deleted, or changed rows so they
now match the predicate.

### Serialization anomaly

The combined outcome of concurrent transactions cannot be explained by
any serial ordering of those transactions. This is broader than the
three classic anomalies.

## 3. Standard isolation levels

  -----------------------------------------------------------------------
  Level             Dirty reads       Non-repeatable    Phantom reads
                                      reads             
  ----------------- ----------------- ----------------- -----------------
  Read Uncommitted  Possible          Possible          Possible

  Read Committed    Prevented         Possible          Possible

  Repeatable Read   Prevented         Prevented         Standard permits
                                                        phantoms

  Serializable      Prevented         Prevented         Prevented;
                                                        serializable
                                                        outcomes required
  -----------------------------------------------------------------------

This table summarizes the SQL-standard model. Vendor implementations may
be stronger or use different mechanisms.

## 4. Read Uncommitted

This is the weakest level. It may allow dirty reads. Some database
engines do not implement true dirty reads even when this level is
requested.

Use it only when approximate, potentially inconsistent reads are
acceptable and the engine's behavior is understood.

## 5. Read Committed

Each statement sees committed data as of a statement-specific point or
snapshot, depending on the engine.

A second read in the same transaction may see a newly committed update
from another transaction. This is a common default isolation level.

## 6. Repeatable Read

The goal is to ensure repeated reads of previously read rows remain
stable. The SQL standard still permits phantom rows at this level.

Some engines implement repeatable read using snapshot isolation, which
prevents many classic anomalies but can still permit write skew. Do not
assume that "repeatable read" means fully serializable.

## 7. Serializable

Serializable isolation requires concurrent transactions to produce an
outcome equivalent to some serial execution. It provides the strongest
standard isolation guarantee, but may reduce concurrency or cause
transactions to abort and require retries.

Implementations may use strict locking, predicate/range locks, or
Serializable Snapshot Isolation.

## 8. Write skew example

Two doctors are on call. The rule says at least one doctor must remain
on call. Two concurrent transactions each read that both doctors are on
call; one turns doctor A off, the other turns doctor B off. If both
commit under an isolation model that permits write skew, no doctor
remains on call.

A serializable transaction model prevents this non-serializable outcome.
Alternative solutions include locking a shared coordination row or
enforcing the invariant through a suitable schema design.

## 9. Choosing an isolation level

Consider: - What anomalies would violate the business rule? - Is stale
data acceptable? - Can the application retry aborted transactions? -
What are the workload's contention and latency requirements? - What
isolation implementation does the chosen database actually provide?

Examples: - Reporting may tolerate a snapshot that is not the latest. -
Inventory allocation may require conditional updates or stronger
coordination. - Financial invariants may need serializable execution or
explicit locking.

## 10. Setting isolation

Syntax varies by engine. A common pattern is:

``` sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN;

-- Transaction statements

COMMIT;
```

Some engines require the isolation setting before `BEGIN`; others use
different syntax or scope. Verify the correct order for the database in
use.

## 11. Practical rules

-   Keep transactions short.
-   Make invariants explicit.
-   Use constraints and atomic updates where possible.
-   Handle deadlock and serialization failures.
-   Test concurrency with multiple sessions, not only sequential unit
    tests.
-   Do not assume the same isolation name means identical implementation
    across products.

## 12. Conclusion

Isolation levels balance correctness and concurrency. Learn the
anomalies first, then choose the weakest level that safely preserves the
application's invariants---or use stronger isolation when the cost of an
incorrect concurrent outcome is unacceptable.
