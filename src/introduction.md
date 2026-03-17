# Introduction

There are things that cannot be done. Not because we lack the talent, the budget, or the time — but because the universe of logic, computation, and systems has hard walls built into its structure. These walls are not engineering failures. They are mathematical certainties.

This book is a tour of those walls.

## Why impossibility matters

Engineers and builders are optimists by nature. We see problems and reach for solutions. But some of the most important insights in computer science, mathematics, economics, and systems design are *negative* results — proofs that certain goals are provably unachievable, no matter how clever the approach.

Understanding these limits is not defeatist. It is clarifying. When you know what *cannot* be done, you stop wasting effort on impossible goals and start making informed trade-offs instead. You stop searching for a distributed database that is simultaneously consistent, available, and partition-tolerant in all cases — and start choosing which two properties matter most for your use case. You stop looking for a perfect voting system and start understanding which fairness criteria you're willing to relax.

## What this book covers

We begin with the deepest result: **Gödel's Incompleteness Theorems**, which showed in 1931 that any sufficiently powerful formal system contains true statements it cannot prove. This shattered Hilbert's dream of a complete, consistent foundation for all of mathematics.

From there, we move to **the Halting Problem**, Turing's 1936 proof that no general algorithm can determine whether an arbitrary program will terminate. This is the foundational impossibility result in computer science, and its consequences ripple through everything from compiler design to software verification.

**Rice's Theorem** generalises the Halting Problem dramatically: *any* non-trivial semantic property of programs is undecidable. You cannot build a general tool that reliably answers questions about what programs do.

We then cross from logic and computation into social choice with **Arrow's Impossibility Theorem**, which proves that no ranked voting system with three or more candidates can simultaneously satisfy a small set of reasonable fairness axioms. Democracy, it turns out, is harder than it looks.

In distributed systems, **the CAP Theorem** formalises the trade-off every distributed database must face: you cannot have consistency, availability, and partition tolerance all at once. This result, proved rigorously by Gilbert and Lynch in 2002, shapes every conversation about distributed architecture.

**Zooko's Triangle** addresses naming systems: a name cannot simultaneously be human-meaningful, secure, and decentralised. Although modern systems like Namecoin and ENS have found creative workarounds, the underlying tension remains instructive.

The **RUM Conjecture** (Read, Update, Memory) posits that no data structure can be optimal across all three dimensions — read performance, update performance, and memory footprint. You must always sacrifice at least one. This is a conjecture rather than a theorem, but it captures a widely observed reality in storage engine design.

**Ashby's Law of Requisite Variety** comes from cybernetics: a controller must have at least as much variety (complexity, degrees of freedom) as the system it seeks to regulate. Simple rules cannot govern complex systems. This has profound implications for management, automation, and system design.

Finally, we close with a chapter on **open conjectures and the broader meaning of these limits** — what they tell us about the nature of knowledge, the practice of engineering, and the questions that remain unanswered.

## Who this book is for

This book is written for engineers, computer scientists, and curious thinkers who want to understand the proven boundaries of what is possible. You do not need a PhD in mathematics to follow along, but we will not shy away from the key ideas and proof sketches that give these results their force. The goal is understanding, not just awareness.

Each chapter is self-contained. You can read them in order for a narrative arc from pure logic to applied systems, or dip into whichever result is most relevant to your current work.

Let's begin.
