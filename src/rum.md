# The RUM Conjecture

The RUM Conjecture, introduced by Manos Athanassoulis, Michael Kester, Lukas Maas, Radu Stoica, Stratos Idreos, Anastasia Ailamaki, and Mark Callaghan in 2016, addresses a fundamental trade-off in the design of data structures for storage engines. It is not a proven theorem — it is a conjecture — but it captures a pattern so pervasive in practice that it has become a guiding principle for database engineers.

## The three dimensions

Every data structure that provides access to data must balance three costs:

### Read overhead (R)

The cost of reading data. How much extra work must the system do beyond the minimum necessary I/O to retrieve the requested data? A data structure with low read overhead can locate and return data quickly without scanning unnecessary entries.

An array indexed by key has optimal read overhead — O(1) lookup. A log-structured store may require scanning multiple files, yielding high read overhead.

### Update overhead (U)

The cost of writing (inserting, updating, or deleting) data. How much extra work beyond writing the new data itself? A data structure with low update overhead can absorb writes quickly without reorganising existing data.

An append-only log has optimal update overhead — O(1) append. A sorted array requires shifting elements on every insert, yielding high update overhead.

### Memory overhead (M)

The amount of space the data structure uses beyond the raw data itself. This includes indexes, pointers, metadata, bloom filters, internal fragmentation, and any auxiliary structures needed to support reads and updates.

A flat file with no index has minimal memory overhead. A fully indexed structure with multiple secondary indexes has high memory overhead.

## The conjecture

> **The RUM Conjecture.** An access method that is optimal in any two of {Read overhead, Update overhead, Memory overhead} must be suboptimal in the third. In other words, you can optimise for at most two of the three.

This is reminiscent of CAP, but for data structure design rather than distributed systems. The claim is that the three dimensions are fundamentally in tension, and no data structure can minimise all three simultaneously.

## Why the trade-off exists

The intuition is straightforward:

### Low R + Low U → High M

To read fast, you need indexes. To write fast, you need to avoid reorganising on every write. Doing both typically requires maintaining multiple representations of the data or auxiliary structures (bloom filters, hash indexes, cached summaries) that consume extra memory.

Example: a hash table with chaining gives O(1) reads and O(1) amortised updates, but requires storing pointers, maintaining a load factor, and potentially wasting space on empty buckets. Memory overhead grows.

### Low R + Low M → High U

To read fast without much extra memory, you need the data to be well-organised (sorted, indexed) using minimal auxiliary structures. But maintaining that organisation is expensive when data changes — every update may require resorting, rebalancing, or compacting.

Example: a sorted array supports O(log n) binary search reads with zero auxiliary memory. But inserting a new element costs O(n) due to shifting. Update overhead is high.

### Low U + Low M → High R

To write fast without much extra memory, you just append new data without maintaining any index. But then reads must scan everything, because there is no auxiliary structure to guide lookups.

Example: an unsorted log file supports O(1) appends with no extra memory, but reading requires O(n) scanning. Read overhead is high.

## The RUM spectrum in storage engines

Real storage engines embody specific RUM trade-off choices:

### B-trees: optimised for reads

B-trees (and their variants B+-trees, used in most relational databases) are the classic read-optimised structure:

- **Read**: O(log n) — excellent. The tree structure guides lookups directly to the target.
- **Update**: Moderate to high. Insertions may cause page splits; deletions may cause merges. Random I/O for each write.
- **Memory**: Moderate. The tree structure adds overhead, but it's bounded and predictable.

B-trees choose low R, moderate M, and pay in U. This is why they dominate OLTP workloads where reads are frequent and writes are smaller.

### LSM-trees: optimised for updates

Log-Structured Merge-trees (used in LevelDB, RocksDB, Cassandra, HBase) are the classic write-optimised structure:

- **Update**: Excellent. Writes go to an in-memory buffer and are periodically flushed as sorted runs. All I/O is sequential.
- **Read**: Higher than B-trees. A read may need to check multiple levels and use bloom filters to avoid false lookups.
- **Memory**: Moderate to high. Bloom filters, level metadata, and multiple sorted runs consume space.

LSM-trees choose low U, accept higher R, and manage M through tuning (compaction strategies, bloom filter sizing). This is why they dominate write-heavy workloads.

### Hash indexes: optimised for point reads

Hash-based structures (used in Bitcask, some in-memory databases):

- **Read**: O(1) for point lookups — optimal.
- **Update**: O(1) amortised — near-optimal.
- **Memory**: High. The hash table itself, chain pointers, and load factor overhead. Range queries are unsupported.

Hash indexes choose low R and low U, but pay in M and lose range query capability entirely.

### Column stores: optimised for analytical reads with low memory

Columnar storage (used in Parquet, ClickHouse, DuckDB):

- **Read**: Excellent for analytical queries (column scans, aggregations). Poor for point lookups.
- **Memory**: Low — columnar compression is highly effective.
- **Update**: Very high. Updating a single row touches many column files. Most column stores are append-only or batch-oriented.

Column stores choose low R (for analytical patterns) and low M, paying in U.

## Tuning the trade-offs

The RUM Conjecture does not say which trade-off to choose — it says you *must* choose. The art of storage engine design is tuning the knobs:

### Hybrid structures

Many modern storage engines blend approaches:

- **Adaptive indexing** (database cracking) starts with no index (low U, low M, high R) and incrementally builds an index as queries arrive, gradually trading M for R.
- **Tiered/leveled compaction** in LSM-trees lets you trade between read and write amplification.
- **Fractional cascading** and **fractional indexing** partially index the data, occupying a middle ground in the RUM space.

### Workload-aware design

The "right" position on the RUM spectrum depends entirely on the workload:

- Write-heavy, read-light (logging, event sourcing): prioritise low U.
- Read-heavy, write-light (analytics, reporting): prioritise low R.
- Memory-constrained (embedded systems, edge computing): prioritise low M.
- Mixed workloads: accept moderate overhead on all three, or use different structures for different access patterns (a common approach in systems like TiDB or CockroachDB).

## Is it really a conjecture?

The RUM Conjecture has not been formally proved in the way that CAP or Arrow's theorem have been. The original paper presents it as an empirical observation supported by a survey of existing data structures and a lower-bound argument for specific access patterns.

Formally proving the conjecture would require:

1. A precise model of what constitutes a "data structure" and what "optimal" means.
2. A proof that no data structure in that model can achieve the lower bound on all three dimensions simultaneously.

This formalisation is non-trivial. Different computation models, I/O models (internal memory vs. external memory), and workload assumptions lead to different lower bounds. The conjecture is compelling because it matches every known data structure, but the absence of a formal proof means it remains a conjecture.

Some researchers have made progress on formal results in restricted models. For example, lower bounds on the read-update trade-off for comparison-based data structures are well-established. But a fully general RUM impossibility theorem remains open.

## The design lesson

The RUM Conjecture teaches a practical lesson that every database engineer encounters: there is no universal data structure. Every choice of indexing strategy, storage layout, and auxiliary structure is a bet on the workload.

When someone presents a new data structure and claims it is "the best," the right question is: "Best for what?" The RUM Conjecture predicts that optimising one or two dimensions will degrade the third. Understanding this trade-off space is the difference between choosing a storage engine wisely and being seduced by benchmarks that highlight only the favourable dimension.

The conjecture is, in some ways, the most practical result in this book. It speaks directly to a decision that storage engineers make every day: where on the RUM spectrum should this system sit?
