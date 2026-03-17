# Open Conjectures and What These Limits Mean

We have surveyed eight impossibility results, ranging from mathematical logic to distributed systems to cybernetics. Some are proven theorems; others are conjectures with strong empirical support. All of them delineate boundaries — places where optimism must yield to structure.

In this final chapter, we look at the broader landscape: important open conjectures that may represent further impossibility results, the connections between the theorems we have covered, and what these limits mean for the practice of engineering and the pursuit of knowledge.

## Open conjectures and unresolved boundaries

### P ≠ NP

The most famous open problem in computer science. The conjecture states that there exist problems whose solutions can be *verified* efficiently (in polynomial time) but cannot be *found* efficiently. If true, it establishes a permanent computational boundary: a vast class of optimisation, scheduling, and search problems will never have efficient algorithms.

The implications are profound:
- **Cryptography** relies on P ≠ NP (or something like it). If P = NP, most public-key cryptography breaks — factoring, discrete logarithms, and lattice problems all become easy.
- **Optimisation** would be revolutionised if P = NP. Protein folding, circuit layout, logistics — all NP-hard problems — would become tractable.
- **Proof theory** would change: if P = NP, then finding proofs is as easy as verifying them, which would transform mathematics itself.

Despite decades of effort, neither P = NP nor P ≠ NP has been proved. The Clay Mathematics Institute has offered a $1 million prize. Most computer scientists believe P ≠ NP, but belief is not proof.

### The extended Church–Turing thesis

The standard Church–Turing thesis says that Turing machines capture all *computable* functions. The *extended* thesis makes a stronger claim: any physically realisable computation can be simulated by a probabilistic Turing machine with at most polynomial slowdown.

Quantum computing challenges this. If quantum computers can efficiently solve problems that classical computers cannot (as widely believed but not proven), then the extended thesis is false — there would be computations physically realisable (by quantum mechanics) that Turing machines cannot efficiently simulate.

The resolution depends on:
- Whether BQP (problems solvable by quantum computers) strictly contains BPP (problems solvable by classical probabilistic computers). This is believed but unproven.
- Whether large-scale, fault-tolerant quantum computers can be built. The engineering challenges are immense.

### The unique games conjecture

Proposed by Subhash Khot in 2002, the Unique Games Conjecture (UGC) states that a certain type of constraint satisfaction problem is NP-hard to approximate beyond a specific threshold. If true, it implies optimal inapproximability results for a wide range of problems — it would prove that many known approximation algorithms are the *best possible*.

The UGC remains open, but it has been enormously productive: many conditional results have been proved assuming it, creating a rich web of "if UGC then..." implications.

### One-way functions

The existence of one-way functions — functions that are easy to compute but hard to invert — is another foundational conjecture. If one-way functions do not exist, then:

- Pseudorandom generators do not exist.
- Zero-knowledge proofs are impossible.
- Essentially all of modern cryptography collapses.

The existence of one-way functions implies P ≠ NP (but the converse is not known to be true). Proving that one-way functions exist (or don't) would resolve fundamental questions about the nature of computation and secrecy.

### The Gödelian boundary for AI

Gödel's theorems tell us that no fixed formal system can capture all mathematical truth. Does this limit apply to artificial intelligence? If an AI system operates within a fixed formal system, Gödel implies it has blind spots. But humans may also operate within fixed (if unknown) formal systems — or they may not.

The question of whether there are *inherent* limitations to machine intelligence that do not apply to human intelligence remains open. Penrose argued yes; most AI researchers are skeptical. The resolution may depend less on Gödel's theorems and more on our understanding of what constitutes "intelligence" and "understanding" — questions that are philosophical as much as mathematical.

## Connections and patterns

Looking across the results in this book, several patterns emerge:

### The diagonal argument

Self-reference and diagonalisation are the engine behind multiple impossibility results:

- **Cantor** used diagonalisation to prove the reals are uncountable.
- **Gödel** used self-reference (via Gödel numbering) to construct undecidable sentences.
- **Turing** used diagonalisation to prove the Halting Problem undecidable.
- **Rice** used a reduction from the Halting Problem.

The common structure: if a system can represent and reason about itself, it can construct statements or computations that defy any fixed procedure for classifying them. Self-reference generates undecidability.

### The trilemma pattern

Several results take the form of a trilemma — three desirable properties of which only two can be simultaneously achieved:

- **CAP**: Consistency, Availability, Partition tolerance — pick two during partitions.
- **Zooko**: Human-meaningful, Secure, Decentralised — pick two.
- **RUM**: Read optimal, Update optimal, Memory optimal — pick two.

These trilemmas arise in different domains but share a structure: the three properties impose competing demands on a finite resource (network communication, namespace structure, data organisation), and the resource cannot stretch to satisfy all three.

### The completeness–soundness trade-off

Multiple results express a tension between completeness and soundness:

- **Gödel**: No consistent system can be complete.
- **Rice**: No programme analyser can be both sound and complete for non-trivial properties.
- **Arrow**: No voting system can satisfy all fairness axioms simultaneously.

The pattern: when a system tries to say everything correct (soundness) and miss nothing true (completeness), it is forced to either contradict itself, leave truths unstated, or sacrifice a fairness condition.

### Variety matching

Ashby's Law provides a meta-principle that connects to several other results:

- The CAP Theorem can be viewed through an Ashby lens: a distributed system's "regulator" (its consistency protocol) must have enough variety to handle the "disturbances" (network partitions, concurrent requests). When the disturbance variety exceeds the regulator's capacity, something breaks.
- Programme analysis tools (bounded by Rice's Theorem) must have enough variety (abstraction precision, annotation support) to handle the variety of programme behaviours they analyse.
- Voting systems (bounded by Arrow's Theorem) must have enough variety in their aggregation rules to handle the variety of voter preferences.

## What these limits mean for engineers

### 1. Trade-offs are not failures

The most important lesson: encountering a trade-off is not a sign of poor engineering. It is often a sign that you have hit a fundamental boundary. The CAP Theorem does not mean distributed systems are badly designed; it means they *must* make choices. Arrow's Theorem does not mean democracy is broken; it means perfect fairness is unachievable.

When you find yourself unable to achieve all desirable properties simultaneously, check whether you are facing a known impossibility. If so, the productive question is not "how do I get everything?" but "which trade-off best serves my context?"

### 2. Understand your constraints before choosing solutions

Impossibility results are constraints on the solution space. Understanding them early saves time:

- If you need strong consistency in a distributed system, budget for reduced availability during partitions.
- If you need a general programme analyser, accept that it will have false positives or false negatives.
- If you need a naming system, decide which of Zooko's three properties is least important.
- If you need a storage engine, decide where on the RUM spectrum your workload falls.

These are not decisions to agonise over — they are decisions to make *explicitly and early*, informed by the relevant impossibility result.

### 3. Beware of snake oil

Any product, tool, or technique that claims to transcend a proven impossibility result should be viewed with extreme skepticism. A distributed database that claims to be simultaneously consistent, available, and partition-tolerant is either using non-standard definitions of those terms or is wrong. A programme analyser that claims to find all bugs with no false positives is either restricted to a narrow domain or is wrong.

Impossibility results are your defence against hype. They do not tell you what is *possible* — but they draw firm lines around what is *not*.

### 4. Design for the trade-off, not against it

The best systems are designed *with* impossibility results in mind, not in defiance of them.

- CRDTs (Conflict-free Replicated Data Types) are designed for AP systems — they embrace eventual consistency and provide automatic conflict resolution, rather than fighting for linearisability.
- LSM-trees are designed for write-heavy workloads — they accept higher read overhead in exchange for near-optimal write performance.
- Approximation algorithms accept that they cannot achieve optimal solutions for NP-hard problems and instead provide guaranteed-quality approximate solutions.

These are examples of *designing with the grain* of impossibility: accepting the constraint and optimising within it.

### 5. The value of negative results

In engineering culture, positive results get the attention: features shipped, problems solved, systems launched. But negative results — proofs of what cannot be done — are often more valuable in the long run. They prevent wasted effort, clarify trade-offs, and guide design decisions.

Every hour *not* spent trying to build a universally fair voting system, or a distributed database with no trade-offs, or a programme analyser that catches everything, is an hour saved by understanding impossibility.

## The limits of limits

A final caveat: impossibility results have precise scopes. They prove that *specific combinations of properties* are unachievable *within specific formal frameworks*. They do not prove that the world is hopeless or that progress is impossible.

- Gödel's theorems apply to *formal systems of sufficient power*. Many useful systems are complete and decidable.
- The Halting Problem applies to *arbitrary programmes*. Many specific programmes can be shown to halt.
- CAP applies to *linearisable distributed systems during partitions*. Outside of partitions, you can have both consistency and availability.
- Arrow's theorem applies to *ranked voting with universal domain*. Restricted domains and cardinal utilities offer escape routes.

The limits are real, but they are also precise. Understanding their exact scope — what they forbid and what they leave open — is the key to using them productively.

## Closing thought

The impossibility theorems surveyed in this book are among the deepest insights produced by mathematics, computer science, economics, and systems theory. They are not barriers to progress but *maps of the terrain* — showing where the cliffs are so that you can navigate around them.

The engineer who understands these limits is not constrained by them. They are *freed* by them — freed from chasing impossible goals, freed to focus on the achievable, and freed to make explicit, informed trade-offs rather than stumbling into them by accident.

The walls are real. But knowing where the walls are is the beginning of navigating the maze.
