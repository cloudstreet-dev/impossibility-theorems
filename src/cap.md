# The CAP Theorem

In the year 2000, Eric Brewer stood before an audience at the ACM Symposium on Principles of Distributed Computing and made a conjecture: a distributed data store cannot simultaneously provide more than two of the following three guarantees — Consistency, Availability, and Partition tolerance. Two years later, Seth Gilbert and Nancy Lynch proved it.

The CAP Theorem has since become one of the most cited — and most misunderstood — results in distributed systems.

## The three properties

### Consistency (C)

Every read receives the most recent write or an error. In CAP terminology, this is **linearisability** — the strongest form of consistency. All nodes in the system agree on the current state at all times, as if there were a single copy of the data.

This is not eventual consistency, not read-your-writes consistency, not causal consistency. CAP's "C" means the gold standard: linearisability.

### Availability (A)

Every request received by a non-failing node in the system must result in a response. The system must not silently drop requests. Note that "a response" need not be the most recent data — just a valid, non-error response.

Availability in the CAP sense means every non-failed node can process operations. There is no requirement that the response be "correct" in the consistency sense.

### Partition tolerance (P)

The system continues to operate despite an arbitrary number of messages being dropped or delayed between nodes. Network partitions — where the network splits into groups that cannot communicate — are tolerated without the system shutting down entirely.

## The theorem

> **Theorem (Gilbert & Lynch, 2002).** In an asynchronous network model, it is impossible to implement a read/write data object that guarantees all three of availability, linearisable consistency, and partition tolerance.

### The proof intuition

The proof is surprisingly simple. Consider a distributed system with at least two nodes, N₁ and N₂, and a network partition that prevents them from communicating.

A client sends a write to N₁ (say, setting x = 1). Another client reads x from N₂.

- **For consistency**: N₂ must return x = 1 (the latest write). But N₂ hasn't received the write because of the partition. To return the correct value, N₂ would need to wait until the partition heals, which means...
- **For availability**: N₂ must return *some* response without waiting. If it returns the stale value (x = 0), consistency is violated. If it waits for the partition to heal, availability is violated.

You cannot have both. During a partition, you must choose: be consistent (refuse to respond or wait) or be available (respond with potentially stale data).

**And you must tolerate partitions** — in any real distributed system, network failures happen. They are not theoretical; they are Tuesday. This is why the practical choice is always between CP and AP.

## The CAP spectrum in practice

### CP systems: consistency over availability

When a partition occurs, a CP system sacrifices availability to maintain consistency. Nodes on the minority side of the partition may refuse to serve requests.

Examples:
- **ZooKeeper** uses a leader-based consensus protocol (ZAB). If a quorum is unreachable, the system stops serving writes.
- **etcd** and other Raft-based stores require a majority quorum. Minority partitions become read-only or unavailable.
- **Spanner** (Google) provides external consistency (even stronger than linearisability) using TrueTime, at the cost of higher write latency and reduced availability during partitions.
- **Traditional RDBMSs** in single-leader configurations (PostgreSQL with synchronous replication, MySQL Cluster) are typically CP.

### AP systems: availability over consistency

When a partition occurs, an AP system continues serving requests on all reachable nodes, accepting that responses may be inconsistent.

Examples:
- **Cassandra** (in its default configuration) allows reads and writes on any available node. Conflicting writes are resolved later using last-write-wins or application-defined merge functions.
- **DynamoDB** (in its default eventually consistent mode) is AP.
- **CouchDB** uses multi-version concurrency control and eventually resolves conflicts.
- **DNS** is the canonical AP system: it is always available, tolerates partitions gracefully, and sacrifices strong consistency (DNS records propagate slowly).

### The "CA" illusion

What about CA — consistent and available but not partition-tolerant? In theory, this is a single-node database. It provides both C and A because there is no network to partition. The moment you add a second node, you must tolerate the possibility of a partition, and you are back to choosing between C and A.

Some people describe traditional single-node relational databases as CA. This is technically correct but uninteresting: if you are not distributed, CAP does not apply. The whole point of CAP is that distribution forces a trade-off.

## Criticisms and refinements

### "Two out of three" is misleading

The framing "pick two out of three" suggests an equal trade-off, as if you're choosing items from a menu. In reality:

- Partition tolerance is not optional in a distributed system. Partitions happen. You must handle them.
- The real choice is: what does your system do *during* a partition? Does it sacrifice consistency or availability?
- *Outside* of partitions, you can (and should) have both consistency and availability.

This insight led to Brewer's later refinement: CAP is about the *behaviour during partitions*, not a permanent classification of the system.

### PACELC: the full picture

Daniel Abadi proposed the PACELC framework as a more nuanced alternative:

> If there is a **P**artition, choose between **A**vailability and **C**onsistency. **E**lse (when the system is running normally), choose between **L**atency and **C**onsistency.

This captures an important reality: even when there is no partition, there is a trade-off between consistency and latency. Strong consistency requires coordination (synchronous replication, consensus protocols), which adds latency. Weaker consistency models allow lower latency by reducing coordination.

Examples:
- **DynamoDB** is PA/EL — during partitions it chooses availability, and during normal operation it chooses low latency (eventual consistency by default).
- **Spanner** is PC/EC — during partitions it chooses consistency, and during normal operation it also chooses consistency (at the cost of higher latency via TrueTime).
- **Cassandra** can be configured as PA/EL (default) or PC/EC (with quorum reads/writes).

### Consistency is a spectrum

CAP's definition of consistency is linearisability — the strongest form. But there are many useful consistency models between linearisability and eventual consistency:

- **Sequential consistency**: operations appear to execute in some sequential order consistent with each client's ordering.
- **Causal consistency**: causally related operations are seen in the same order by all nodes.
- **Read-your-writes**: a client always sees its own writes.
- **Monotonic reads**: once a client reads a value, it never sees an older value.
- **Eventual consistency**: given sufficient time without new updates, all replicas converge.

Each of these relaxations buys you something in availability or latency. The engineering problem is choosing the consistency level that matches your application's requirements.

## The practical takeaway

The CAP Theorem does not tell you what to build. It tells you what questions to ask:

1. **What happens when the network partitions?** It will. Have you decided whether affected nodes should stop serving (CP) or serve potentially stale data (AP)?

2. **What consistency level does your application actually need?** Many applications are perfectly fine with eventual consistency. Banking ledgers need strong consistency. Social media feeds can tolerate stale reads.

3. **What is your latency budget?** Even without partitions, stronger consistency means more coordination, which means higher latency.

4. **Can your application handle conflicting writes?** If you choose AP, you need a conflict resolution strategy. Last-write-wins? Application-level merge? CRDTs?

The CAP Theorem did not invent these trade-offs. They existed long before Brewer named them. What the theorem did was prove that they are *inescapable* — that no amount of engineering cleverness can give you everything simultaneously in a distributed system.

That clarity is its lasting contribution: not a recipe for what to build, but a proof that certain wishful thinking is futile.
