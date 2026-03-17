# Rice's Theorem

If the Halting Problem tells us that we cannot determine whether a programme halts, Rice's Theorem delivers the full blow: we cannot determine *anything* non-trivial about what a programme computes. It is the most sweeping undecidability result in computer science.

## The theorem

> **Theorem (Rice, 1953).** For any non-trivial property of partial functions, there is no general algorithm that decides whether the function computed by a given programme has that property.

Let's unpack the terminology:

- A **partial function** is the function computed by a programme — it maps inputs to outputs, and may be undefined for inputs on which the programme does not halt.
- A **property** of partial functions is any set of partial functions. For example, "the set of all functions that return 0 on input 0" or "the set of all functions that are total (defined on all inputs)."
- A property is **trivial** if it is satisfied by *all* partial functions or by *no* partial functions. Every other property is **non-trivial**.
- **Deciding** a property means having an algorithm that, given any programme P, correctly answers "yes" or "no" as to whether the function computed by P has the property.

Rice's Theorem says: if the property is non-trivial — if there is at least one function that has it and at least one that doesn't — then no algorithm can decide it.

## Examples of what Rice's Theorem forbids

The scope is breathtaking. Here are concrete examples of undecidable questions, all immediate corollaries of Rice's Theorem:

- **Does this programme compute the factorial function?** Undecidable.
- **Does this programme always return a positive number?** Undecidable.
- **Does this programme produce the same output as this other programme?** (Programme equivalence.) Undecidable.
- **Is the function computed by this programme total?** (Does it halt on all inputs?) Undecidable.
- **Does this programme ever output the string "hello"?** Undecidable.
- **Does this programme implement a sorting algorithm correctly?** Undecidable.

Any question of the form "does this programme compute a function with property X?" — where X is non-trivial — falls to Rice's Theorem.

## The proof

The proof is a clean reduction from the Halting Problem.

**Setup.** Let S be a non-trivial set of partial functions (the "property" we want to decide). Since S is non-trivial:

- There exists some programme Q₁ whose function is in S.
- There exists some programme Q₀ whose function is not in S.

Without loss of generality, assume the totally undefined function (the programme that loops on all inputs) is not in S. (If it is, we can work with the complement of S instead, and an algorithm for one gives an algorithm for the other.)

**Reduction.** Given a programme P and input I (an instance of the Halting Problem), construct a new programme R(x):

```
programme R(x):
    run P on I         // simulate P(I)
    return Q₁(x)      // if P(I) halts, compute Q₁(x)
```

Now consider what function R computes:

- **If P halts on I**, then R first finishes running P(I), then computes Q₁(x). So R computes the same function as Q₁, which is in S.
- **If P loops on I**, then R never gets past the first line, so R loops on all inputs. R computes the totally undefined function, which is not in S (by our assumption).

Therefore, R's function is in S if and only if P halts on I.

If we had an algorithm to decide membership in S (i.e., whether a given programme's function is in S), we could use it to decide the Halting Problem: given P and I, construct R, then ask the S-decider about R. But the Halting Problem is undecidable. Contradiction. ∎

## What Rice's Theorem does NOT say

Rice's Theorem is often misunderstood as saying "nothing can be determined about programmes." This is too strong. The theorem has precise boundaries:

**It applies to semantic properties, not syntactic properties.** A semantic property is about what the programme *computes* (its input-output behaviour). A syntactic property is about the programme's *text*. For example:

- "Does this programme contain more than 100 lines?" — syntactic, decidable, unaffected by Rice.
- "Does this programme use a variable named `x`?" — syntactic, decidable.
- "Does this programme compute a total function?" — semantic, undecidable by Rice.

**It applies to extensional properties, not intensional ones.** Two programmes that compute the same function have the same extensional (semantic) properties. But they may differ intensionally — one might use more memory, or take different code paths. Some intensional properties are decidable, others are not, but Rice's Theorem specifically governs extensional ones.

**It does not prevent analysis of specific programmes.** Rice says no algorithm works for *all* programmes. For any particular programme, you might be able to determine its properties through manual analysis, domain-specific reasoning, or restricted tools. The impossibility is about *general* algorithms, not about *specific* instances.

**It does not apply to finite domains.** If the set of possible inputs is finite and bounded, you can (in principle) test all of them. Rice applies to programmes operating on unbounded domains like the natural numbers.

## The spectrum of approximation

Rice's Theorem defines the boundary, but practical software engineering lives in the space of useful approximations:

### Sound over-approximation

A tool that says "this programme MIGHT violate property X" is sound if every real violation is flagged. The trade-off is false positives — safe programmes that get flagged anyway. Abstract interpretation (used in tools like Astrée, Infer, and Polyspace) takes this approach.

### Complete under-approximation

A tool that says "this programme DOES violate property X" is complete if every flagged programme is a genuine violator. The trade-off is false negatives — real violations that slip through. Testing and fuzzing work this way: they find real bugs but cannot guarantee absence of bugs.

### The unsound, incomplete middle ground

Many practical tools are neither fully sound nor fully complete. They use heuristics, type systems, and pattern matching to catch common issues while accepting that edge cases will be missed. This is the pragmatic approach that powers most of the software industry.

### Type systems as restricted decidability

Type systems work by restricting the *language* rather than the *analysis*. A well-typed programme in Haskell or Rust has certain properties guaranteed by construction — memory safety, absence of null pointer dereferences, etc. The type system is decidable because it limits what programmes can express, not because it has solved an undecidable problem.

## Rice's Theorem and security

The impossibility of general semantic analysis has direct security implications:

- **Malware detection** cannot be perfect. Any purely semantic analysis of whether a programme is malicious is undecidable. Antivirus tools use signatures (syntactic), heuristics (incomplete semantic), and sandboxing (testing) — all approximations.

- **Vulnerability detection** is undecidable in general. No tool can examine arbitrary code and guarantee it finds all security vulnerabilities. This is why defence in depth — combining static analysis, dynamic testing, code review, and runtime protections — is the standard practice.

- **Obfuscation** exploits Rice's Theorem implicitly. Code obfuscation works because determining what an obfuscated programme computes is undecidable in general. An obfuscator transforms the syntax while preserving the semantics, and Rice says no general algorithm can see through the transformation to the underlying function.

## The philosophical weight

Rice's Theorem is, in a sense, the computer science version of the problem of other minds. You can observe a programme's *behaviour* on specific inputs (testing), and you can inspect its *structure* (code review), but you cannot, in general, determine what it *is* — what function it computes.

Programmes are opaque in a deep, mathematical sense. This opacity is not a limitation of our current tools. It is an intrinsic property of computation itself.

For the working engineer, Rice's Theorem is a permanent reminder: any claim about general-purpose programme analysis comes with caveats. The question is never "does this tool catch all bugs?" (it provably cannot) but rather "what class of bugs does this tool catch, and what does it miss?"

Knowing the limits of your tools is the first step toward using them well.
