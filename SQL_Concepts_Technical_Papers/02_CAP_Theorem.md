# CAP Theorem and Distributed Database Trade-offs

## Abstract

The CAP theorem describes a fundamental trade-off in distributed data
systems during a network partition. A distributed system cannot
simultaneously guarantee linearizable consistency and full availability
for every request when communication between nodes is partitioned. This
paper defines the three properties, clarifies the theorem's scope,
compares common design choices, and explains practical implications.

## 1. Introduction

Distributed databases place data across multiple machines or locations
to improve scale, resilience, and geographic reach. Those machines
communicate over a network, and networks can fail: messages may be
delayed, dropped, or blocked.

CAP is often summarized as "choose two of consistency, availability, and
partition tolerance." That phrase is misleading. A system operating
across an unreliable network must be prepared for partitions. The
meaningful choice appears when a partition occurs: should the system
reject or delay some requests to preserve consistency, or continue
serving requests while accepting that nodes may temporarily disagree?

## 2. The three properties

### 2.1 Consistency (C)

In CAP, consistency usually means **linearizability**: each operation
appears to take effect atomically at one point between its invocation
and response, respecting real-time ordering. A read after a completed
write must observe that write or a later one.

This is more specific than the broad "consistency" term used in ACID.

### 2.2 Availability (A)

Availability means every request sent to a non-failing node receives a
non-error response within a bounded time. The response may not contain
the newest value under a partition-tolerant, eventually consistent
design.

Availability does not simply mean that a website is reachable. It is a
formal property about request responses.

### 2.3 Partition tolerance (P)

Partition tolerance means the system continues to operate despite
communication failures that split nodes into groups unable to
communicate reliably.

A partition can be asymmetric or intermittent. It is not necessarily a
complete internet outage.

## 3. The core trade-off

Imagine replicas R1 and R2 hold the same account balance. A network
partition separates them. A client writes a new balance through R1 while
another client reads from R2.

The system has two broad choices:

-   **Consistency-first behavior:** R2 may reject or wait for operations
    because it cannot establish whether a newer write exists. This
    sacrifices availability for affected requests.
-   **Availability-first behavior:** R2 continues responding using its
    local state. It may return stale data or accept conflicting updates,
    sacrificing strong consistency during the partition.

The system cannot promise both linearizable consistency and availability
for all requests in the presence of a partition.

## 4. CP and AP designs

  -----------------------------------------------------------------------
  Design tendency         During a partition      Typical consequence
  ----------------------- ----------------------- -----------------------
  CP                      Rejects or blocks some  Stronger consistency,
                          operations that cannot  reduced availability
                          be made safe            for affected nodes

  AP                      Continues responding on Higher availability,
                          reachable sides         possible stale reads or
                                                  conflict resolution
  -----------------------------------------------------------------------

These are design tendencies, not permanent labels for every operation in
a product. Real systems may offer configurable consistency levels,
quorum settings, or different behavior for different data types.

A CA system is sometimes discussed in simplified diagrams. In a
genuinely distributed environment where partitions are possible, a
system cannot guarantee both C and A during the partition. A single-node
database avoids inter-node partitions but has other availability
limitations.

## 5. Quorums and replication

A replicated system may use N replicas and require a read quorum R and
write quorum W. A common rule of thumb for overlapping quorums is:

\[ R + W \> N \]

This overlap can help ensure reads intersect with recent writes, but it
does not by itself prove linearizability. The result depends on replica
protocol, version ordering, failure handling, and consistency model.

For example, with N=3, R=2, W=2, read and write quorums overlap. During
a partition, a side that cannot reach enough replicas may be unable to
complete the operation.

## 6. Consistency models beyond CAP

-   **Strong consistency / linearizability:** operations respect a
    single real-time order.
-   **Sequential consistency:** operations appear in one global order
    that preserves each process's program order, but not necessarily
    real-time order.
-   **Eventual consistency:** replicas converge if no new updates occur
    and communication resumes.
-   **Causal consistency:** causally related operations are observed in
    a compatible order.
-   **Read-your-writes:** a client sees its own completed writes.
-   **Monotonic reads:** a client does not move backward to older
    observed values.

These models provide a more useful vocabulary than simply calling a
database "consistent" or "inconsistent."

## 7. Choosing a design

Strong consistency is valuable for account balances, inventory
allocation, and unique reservations where conflicting decisions are
costly. Availability and eventual convergence may be acceptable for
likes, view counts, analytics, or offline-first collaboration.

A practical design may mix models: - Strongly coordinate money
movement. - Allow delayed convergence for activity feeds. - Cache data
with explicit freshness expectations. - Make writes idempotent so
clients can safely retry. - Define conflict-resolution rules before
conflicts occur.

## 8. CAP is not the whole story

CAP focuses on behavior during partitions. It does not quantify latency,
throughput, operational complexity, or consistency under normal
conditions. The PACELC framework adds another trade-off: even when there
is no partition, a distributed system often chooses between latency and
consistency.

## 9. Common misconceptions

1.  CAP does not say a system can only ever have two properties.
2.  Partition tolerance is not an optional feature for systems
    distributed over unreliable networks.
3.  Eventual consistency does not mean data is permanently inconsistent.
4.  A database product may expose multiple consistency levels.
5.  CAP does not replace application-level data modeling and conflict
    handling.

## 10. Conclusion

CAP explains why distributed systems must make deliberate choices when
replicas cannot communicate. The right choice depends on the cost of
stale data, rejected requests, and conflicting writes. Engineers should
define consistency requirements per operation rather than selecting a
database based on a simplistic CP/AP label.
