# GRL: discovering matrix formulas for graph counting

This six-week M2 A2IA project studies **Grammar Reinforcement Learning (GRL)** and applies a simplified search method to find matrix formulas that count paths in small graphs.

The implementation is based on the ICLR 2025 paper *Grammar Reinforcement Learning* and the course project specification (`projet_M2_A2IA.pdf`). It does not aim to reproduce the complete Gramformer/MCTS system.

## Objective

For an undirected graph with adjacency matrix `A`, the goal is to discover expressions that exactly count simple paths of length `ell`.

- `P2 = J ⊙ A²` is the first milestone.
- `P3` is the principal target.
- Vertices in a simple path are distinct; a path of length `ell` contains `ell + 1` vertices and `ell` edges.
- The coefficient of entry `(i, j)` in `Pell(A)` is the number of simple paths of length `ell` from vertex `i` to vertex `j`.

The project grammar is:

```text
M -> A | I | J | (MM) | (M ⊙ M)
```

where `A` is the adjacency matrix, `I` is the identity matrix, `J` is the all-ones matrix with diagonal zero, and `⊙` is the Hadamard product.

## GRL overview

GRL formulates formula discovery as generation by a context-free grammar. A pushdown automaton (PDA) represents the grammar:

- a variable such as `M` is expanded by a grammar production;
- terminal tokens such as `A`, `I`, `J`, `(`, `)`, and `⊙` are emitted;
- the stack keeps track of unfinished derivations.

In the paper, an agent learns to select grammar productions. It combines MCTS with a transformer model called Gramformer and uses a replay/buffer mechanism to improve the policy. The resulting expressions are evaluated for exactness and computational cost.

For this project, the same grammar and expression representation should be used, but an equivalent simplified agent may use random search, policy search, REINFORCE, or MCTS.

## Simplified milestones

1. **PDA generation**
   - Implement or reproduce the PDA derivation mechanism.
   - Generate only syntactically valid formulas.
   - Reproduce `P2 = J ⊙ A²` if possible.

2. **Graph data and exact references**
   - Generate small, simple, undirected graphs without loops.
   - Enumerate all simple paths directly, without relying on matrix powers as the reference.
   - Store or regenerate training, validation, and test graphs separately.

3. **Formula search**
   - Search bounded expressions from `M -> A | I | J | (MM) | (M ⊙ M)`.
   - Apply search limits on syntax size and evaluation budget.
   - Evaluate candidate formulas exactly and penalize excessive expression cost.

4. **P3 discovery**
   - Discover or approximate a formula for simple 3-path counts.
   - The original expression proposed in the paper is:

     ```text
     J ⊙ A³ - (I ⊙ A²)A - A(I ⊙ A²) + A
     ```

   - GRL also discovered a more compact expression:

     ```text
     J ⊙ (A(J ⊙ A²)) - A ⊙ (AJ)
     ```

   These expressions are targets for comparison, not grounds for copying the answer before running the search.

5. **Coefficients and validation**
   - Represent a discovered result as `F(A) = sum(c_k * T_k(A))`.
   - Fit coefficients only from training graphs.
   - Reuse the fitted coefficients on validation and test graphs.
   - Compare exact integer counts rather than relying only on numerical tolerance.

## Experimental protocol

- Separate **training**, **validation**, and **test** graph sets.
- Use at least three random seeds.
- Measure both:
  - formula discovery time;
  - formula usage/evaluation time.
- Compare the agent with at least a random-search baseline.
- Report syntax size, number of terms, cost, coefficient values, and exact-match rate.
- Use bounded formula size and term-count limits to prevent unbounded enumeration.
- Finish with an exhaustive or extensive final check on small graph sizes when practical.
- Treat finite validation as empirical evidence, not as a general proof.

The optional MCTS and Gramformer extensions may be added after P2/P3 has been recovered with a simpler agent.

## Repository layout

```text
data/       Generated graph datasets and reference counts
notebooks/  Exploratory notebooks for PDA generation and graph generators
scripts/    Intended location for generation, counting, search, and evaluation code
```

### Notebooks

- `notebooks/PDA.ipynb`: PDA/CFG derivation examples and the grammar reproduction.
- `notebooks/graph generator method exploration.ipynb`: examples of NetworkX Classic and Random graph generators, with each graph visualized using `nx.draw`.
- `scripts/graphe_embedding.ipynb`: work on node-level embeddings based on path and cycle counting, including the $l$-path matrix $P^{(l)}$ and the $l$-cycle matrix $C^{(l)}$.

## Running the notebooks

The notebooks can be opened with Jupyter:

```bash
jupyter notebook notebooks/PDA.ipynb
jupyter notebook "notebooks/graph generator method exploration.ipynb"
jupyter notebook scripts/graphe_embedding.ipynb
```

The PDA notebook demonstrates:

- a randomly generated formula;
- `(J ⊙ (AA))`, from the grammar example in the paper.

The graph generator notebook provides reproducible examples of NetworkX graph families and displays the generated graphs. The embedding notebook documents the distinction between walks counted by matrix powers and exact simple paths or cycles.

The repository does not yet require a shared package manifest or a fixed Python environment.

## Deliverables

- A clear implementation of PDA/CFG formula generation.
- Exact simple-path counting on generated graphs.
- P2 and P3 search experiments.
- A random-search baseline and, if implemented, policy learning or MCTS.
- Reproducible train/validation/test splits and seeds.
- Tables and plots comparing correctness, discovery time, and evaluation cost.
- Source code and a short written report describing findings and limitations.

## References

- *Grammar Reinforcement Learning*, ICLR 2025.
- Course project specification: `projet_M2_A2IA.pdf`.
