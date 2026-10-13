<!-- solve-problem:17ac2287-e480-48e9-8a75-df4f6f07e7bc -->
# Online Matrix–Vector Multiplication conjecture

A [Tangle Solve problem](https://solve.tangle.tools/problems/17ac2287-e480-48e9-8a75-df4f6f07e7bc).

## What is the problem?

The **Online Matrix–Vector Multiplication (OMv) conjecture**. Fix an integer \(n\). The game proceeds in two phases:

1. **Preprocessing:** The algorithm is given a Boolean \(n\times n\) matrix \(M\), chosen by an adversary. It may run in time \(O(n^2 \,\mathrm{polylog}\, n)\) and build arbitrary data structures.
2. **Online phase:** For \(n\) rounds \(t=1,\dots,n\), the adversary adaptively presents a Boolean vector \(v_t\) (possibly depending on answers to earlier queries). The algorithm must output \(M v_t\) (over the Boolean semiring: AND is multiplication, OR is addition) before receiving \(v_{t+1}\).

**Conjecture (Henzinger–Krinninger–Nanongkai–Saranurak, 2015):** No algorithm with the above preprocessing budget can answer all \(n\) queries in total time \(O(n^{3-\varepsilon})\) for any \(\varepsilon > 0\). Equivalently, the total time is \(n^{3-o(1)}\).

The preprocessing convention matters: the conjecture is normally stated with near-linear (in the input size, \(n^2\)) preprocessing. Allowing polynomially more preprocessing, or non-adaptive (offline) vectors, trivializes the problem — you could compute all products offline in \(n^\omega\) time or preprocess for fast queries. The census record's "preprocessing convention explicitly fixed" refers to exactly this.

Note the amortized per-query bound is \(n^{2-o(1)}\); trivially each query can be answered in \(O(n^2)\) time, giving \(n^3\) total.

## Why does it matter?

OMv is one of the three pillars of fine-grained complexity (alongside SETH and 3SUM), and specifically the foundation of **dynamic algorithms**: it implies \(n^{3-o(1)}\) (or \(m^{2-o(1)}\)) lower bounds for dynamic reachability, shortest paths, maximum matching, subgraph connectivity, and maintaining 2-3 Clique and other problems under updates. Resolving it — either proving the lower bound or giving a truly subcubic algorithm — would either massively strengthen dynamic-complexity hardness or revolutionize dynamic graph algorithms. It also governs hardness for incremental query maintenance in databases.

## What's already known?

- The conjecture originates in the HKNS 2015 paper, which reduced OMv to ~15 dynamic problems, proving conditional lower bounds matching known upper bounds.
- A related weaker conjecture, **Mv** (a single matrix–vector query repeated, no adaptivity between queries within one batch), is often used; HKNS show OMv and a variant of Mv are interreducible up to logs.
- **Upper-bound attacks:** Matrix multiplication over the integers/ring gives \(n^\omega\) total time for the offline version, but adaptivity blocks batching. Attempts via sketching/preprocessing against adaptive adversaries have failed because the adversary can encode the previous answers into the next vector.
- **Lower-bound attacks:** No unconditional superquadratic-per-query lower bound exists in any realistic model (cell-probe lower bounds for the problem are far weaker; the problem relates to the long-standing weakness of our lower-bound techniques for non-network problems).
- Known sparsity results: for sparse \(M\) (say \(m\) ones), an \(O(m)\)-per-query algorithm exists, and matching conditional lower bounds are known in that regime (verify details in HKNS).
- The conjecture is believed partly because \(M v\) with adaptive \(v\) resembles \(n\) independent combinatorial matrix–vector products.

## What would count as solving it?

Three acceptable outcomes:

1. **Proof of the lower bound:** In the cell-probe model with word size \(O(\log n)\) and \(O(n^2 \mathrm{polylog}\, n)\) preprocessing, show total query time \(n^{3-o(1)}\). This would be a breakthrough in unconditional lower bounds (far beyond current techniques, which cap near \((n/\log n)^2\) per query via communication complexity).
2. **Refutation:** An algorithm with \(O(n^2\,\mathrm{polylog}\, n)\) preprocessing answering each adaptive query in \(O(n^{2-\varepsilon})\) time. This falsifies the conjecture and (via HKNS) gives faster algorithms for many dynamic problems.
3. **Partial/intermediate (also publishable):** (a) improved cell-probe lower bounds \(t_q \ge n^2 / f(n)\) for growing \(f\); (b) an equivalence with another established conjecture (SETH, 3SUM, combinatorial \(k\)-Clique) — currently OMv is *not* known to follow from SETH; (c) algorithms beating \(n^2\) per query for structured matrix classes.

## How to start

1. **Read HKNS 2015 first** (arXiv:1504.06148), Sections 2–3: the formal conjecture statement, the preprocessing convention, and the OMv↔Mv reduction. Everything downstream uses their exact formulation.
2. **Understand why the adversary is the crux.** Sketch for yourself why \(n^\omega\) matrix multiplication doesn't refute the conjecture: the online constraint forbids batching. Then examine whether any randomized "guess the future" or universal-circuit preprocessing could work — and where adaptivity defeats it.
3. **For a lower-bound attempt**, study cell-probe lower bounds for matrix-vector type problems (e.g., communication-complexity reductions à la Larsen's lower bounds for data structure problems), and try to formalize the intuition that each query requires \(n\) bits of independent information about \(M\)'s rows.
4. **Main obstacle:** on the algorithm side, adaptivity; on the lower-bound side, the sheer weakness of unconditional cell-probe techniques for problems without network-flow structure.

## Key references

- Henzinger, Krinninger, Nanongkai, Saranurak, "Unifying and Strengthening Hardness for Dynamic Problems via the Online Matrix-Vector Multiplication Conjecture" (arXiv:1504.06148). Defines the OMv conjecture and derives conditional lower bounds for numerous dynamic graph problems. https://arxiv.org/abs/1504.06148
- Williams, "Faster All-Pairs Shortest Paths via Circuit Complexity" (arXiv:1312.6680) — relevant context: subcubic-equivalence framework and hardness of combinatorial methods. https://arxiv.org/abs/1312.6680
- A survey of fine-grained complexity and OMv's place in it appears in Vassilevska Williams, "On Some Fine-Grained Questions in Algorithms and Complexity" (arXiv:1803.05123) — notes OMv is not known to follow from SETH. https://arxiv.org/abs/1803.05123

(Additional references on sparse-OMv and dynamic lower bounds building on HKNS exist post-2015; verify specific titles before citing.)

## Warnings

- **Preprocessing convention is the #1 source of errors.** An algorithm with superlinear preprocessing, or one that receives all vectors offline, does not refute the conjecture. Any claimed algorithm must be checked against the exact HKNS definition.
- **Adaptive vs. non-adaptive adversaries:** randomized algorithms must hold against adaptive adversaries; a Monte Carlo algorithm good against oblivious inputs may fail here.
- **Boolean semiring:** answers are over \(\{\vee,\wedge\}\), not \(\mathbb{F}_2\) or integers; reduction tricks between semirings are nontrivial.
- **Don't claim the trivial \(O(n^2)\)-per-query algorithm as progress** — it's the baseline.
- OMv is *independent* of SETH as far as anyone knows; do not assume deriving it from SETH is easy or known.

---
*`ALGO-000080` · flagged for review*

## Acceptance guidance

Three acceptable outcomes:

1. **Proof of the lower bound:** In the cell-probe model with word size \(O(\log n)\) and \(O(n^2 \mathrm{polylog}\, n)\) preprocessing, show total query time \(n^{3-o(1)}\). This would be a breakthrough in unconditional lower bounds (far beyond current techniques, which cap near \((n/\log n)^2\) per query via communication complexity).
2. **Refutation:** An algorithm with \(O(n^2\,\mathrm{polylog}\, n)\) preprocessing answering each adaptive query in \(O(n^{2-\varepsilon})\) time. This falsifies the conjecture and (via HKNS) gives faster algorithms for many dynamic problems.
3. **Partial/intermediate (also publishable):** (a) improved cell-probe lower bounds \(t_q \ge n^2 / f(n)\) for growing \(f\); (b) an equivalence with another established conjecture (SETH, 3SUM, combinatorial \(k\)-Clique) — currently OMv is *not* known to follow from SETH; (c) algorithms beating \(n^2\) per query for structured matrix classes.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/17ac2287-e480-48e9-8a75-df4f6f07e7bc?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000080.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000080)
