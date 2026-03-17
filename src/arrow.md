# Arrow's Impossibility Theorem

We leave the domain of computation and enter the domain of collective choice. Arrow's Impossibility Theorem, proved by economist Kenneth Arrow in his 1951 doctoral dissertation, is one of the most celebrated results in social choice theory. It says, roughly, that no voting system can be fair.

More precisely: no method of aggregating individual preference rankings into a collective ranking can simultaneously satisfy a small set of conditions that most people would consider minimal requirements for fairness.

## The setup: social welfare functions

Suppose a group of voters must collectively rank a set of three or more alternatives (candidates, policies, options). Each voter has their own preference ranking — a complete, transitive ordering of the alternatives.

A **social welfare function** is a rule that takes the collection of all voters' preference rankings as input and produces a single collective ranking as output. It is a function from profiles of individual preferences to a social preference ordering.

Arrow asked: what properties should a reasonable social welfare function satisfy?

## Arrow's conditions

Arrow proposed five conditions. They are deliberately minimal — each seems almost too obvious to state:

### 1. Unrestricted domain (universality)

The social welfare function must produce a valid output for *every* possible combination of individual preference rankings. We cannot restrict which preferences voters are allowed to have. Democracy means people can prefer whatever they prefer.

### 2. Non-dictatorship

There is no single voter whose preference automatically becomes the group's preference regardless of what everyone else wants. The collective ranking is not simply one person's ranking imposed on everyone.

### 3. Pareto efficiency (unanimity)

If every single voter prefers alternative A over alternative B, then the collective ranking must also rank A above B. When everyone agrees, the group should reflect that agreement.

### 4. Independence of irrelevant alternatives (IIA)

The collective ranking of any two alternatives A and B should depend *only* on voters' rankings of A relative to B — not on how voters rank other alternatives C, D, etc.

This is subtle but crucial. It says that the relative ranking of A and B in the group outcome should not change if some voters re-order alternatives other than A and B. The presence or absence of a "spoiler" candidate should not affect whether A beats B.

### 5. Transitivity of the social ordering

The collective ranking must be transitive: if the group ranks A above B and B above C, then it must rank A above C. The group's preferences, like individual preferences, should be logically consistent.

## The theorem

> **Theorem (Arrow, 1951).** If there are three or more alternatives, there is no social welfare function that simultaneously satisfies unrestricted domain, non-dictatorship, Pareto efficiency, independence of irrelevant alternatives, and transitivity.

Any social welfare function that satisfies conditions 1, 3, 4, and 5 must be a dictatorship.

## Why this is surprising

Each of Arrow's conditions, taken individually, seems not just reasonable but *required*. Abandoning any of them feels like accepting a fundamental flaw:

- Drop universality? Then we're telling voters they're not allowed to have certain preferences.
- Drop non-dictatorship? Then it's not a collective decision.
- Drop Pareto efficiency? Then we might rank B above A even when literally everyone prefers A.
- Drop IIA? Then introducing or removing an irrelevant candidate can flip the outcome — the spoiler effect.
- Drop transitivity? Then the group's preferences are incoherent (A beats B, B beats C, but C beats A).

Arrow's Theorem says: pick your poison. At least one of these must go.

## A concrete illustration: the Condorcet paradox

The roots of Arrow's Theorem go back to the Marquis de Condorcet in the 18th century. Consider three voters and three candidates:

| Voter | 1st choice | 2nd choice | 3rd choice |
|-------|-----------|-----------|-----------|
| Alice | A | B | C |
| Bob   | B | C | A |
| Carol | C | A | B |

Using majority rule on pairs:
- A vs B: Alice and Carol prefer A. **A wins.**
- B vs C: Alice and Bob prefer B. **B wins.**
- C vs A: Bob and Carol prefer C. **C wins.**

So A beats B, B beats C, but C beats A. The collective preference is cyclic — it violates transitivity. This is the **Condorcet paradox**, and it shows that majority rule, the most natural aggregation method, can produce incoherent results.

Arrow's Theorem generalises this: the problem is not specific to majority rule. *No* rule can escape it without sacrificing one of the five conditions.

## Proof sketch

The full proof is involved but the strategy is illuminating:

1. **Identify a decisive set.** A set of voters is *decisive* for alternative A over B if, whenever everyone in the set prefers A to B, the social ranking places A above B (regardless of other voters' preferences). By Pareto efficiency, the set of all voters is decisive for every pair.

2. **Show that decisive sets shrink.** Using IIA and transitivity, Arrow shows that if a set of voters is decisive for some pair, it must also be decisive for *every* pair. Then, using a clever splitting argument, he shows that any decisive set can be split into two halves, one of which remains decisive.

3. **Arrive at a dictator.** By repeatedly splitting, the decisive set shrinks until it contains a single voter. That voter is decisive for all pairs — a dictator.

The proof reveals that the five conditions, taken together, create a logical ratchet that concentrates all decision-making power into a single individual.

## Responses and escape routes

Arrow's Theorem has not stopped people from designing voting systems. Instead, it has clarified the trade-offs:

### Relaxing IIA: most ranked-choice systems

Most practical voting systems (Borda count, instant-runoff voting, Condorcet methods with tiebreakers) violate IIA. They accept the spoiler effect as a trade-off for other desirable properties. The question becomes: *how often* and *how badly* does the spoiler effect manifest?

### Restricting the domain: single-peaked preferences

If voters' preferences can be arranged along a single ideological dimension (left–right, for example), and each voter has a single "peak" preference with preferences declining monotonically in both directions, then majority rule *does* produce a transitive ranking (Black's median voter theorem). Many real-world political preferences are approximately single-peaked, which is why majority rule works reasonably well in practice.

### Changing the input: cardinal utilities

Arrow's framework assumes *ordinal* preferences — voters rank alternatives but don't express intensity. If voters can express *how much* they prefer A over B (cardinal utilities), mechanisms like range voting and quadratic voting escape Arrow's constraints. The Gibbard–Satterthwaite theorem imposes different constraints on these systems (they are vulnerable to strategic manipulation), but Arrow's specific impossibility does not apply.

### Probabilistic and approximate methods

Randomised voting methods and methods that satisfy Arrow's conditions "most of the time" (for "most" preference profiles) offer another avenue. Arrow's Theorem is a worst-case result; the worst case may be rare in practice.

## Implications beyond voting

Arrow's Theorem resonates far beyond political elections:

- **Recommendation systems** that aggregate preferences from multiple signals (ratings, clicks, time-on-page) face Arrow-like trade-offs. There is no perfect aggregation of diverse preference signals.

- **Multi-objective optimisation** in engineering often involves ranking alternatives on multiple criteria. Arrow's Theorem implies there is no universally "fair" way to combine these criteria into a single ranking without accepting some undesirable property.

- **AI alignment** faces a version of Arrow's problem: if we want an AI system to reflect the values of many stakeholders, how do we aggregate their potentially conflicting preferences? Arrow says there is no perfect method.

- **Consensus protocols** in distributed systems involve nodes "voting" on the state of the system. While the analogy is imperfect (consensus protocols solve a different formal problem), the same tensions between fairness, efficiency, and coherence arise.

## The legacy

Arrow's Impossibility Theorem earned Kenneth Arrow the Nobel Prize in Economics in 1972. It is one of those results that, once understood, changes how you see collective decision-making forever.

The theorem does not say that democracy is hopeless or that all voting systems are equally bad. It says that *perfection* is impossible — that every system embodies trade-offs, and the choice of a voting system is itself a value judgement about which trade-offs are acceptable.

For the engineer and system designer, Arrow is a reminder that aggregation is hard. Whenever you combine multiple signals, preferences, or objectives into a single ranking, you are making choices that cannot satisfy everyone's definition of fairness. The honest response is not to pretend the trade-off doesn't exist but to make it explicit and let stakeholders decide which conditions they are willing to relax.
