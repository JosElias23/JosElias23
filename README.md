## José Elías Sanhueza Pérez

**English** · [Español](README.es.md)

Physics Engineer (Universidad Andrés Bello, mathematical modelling), working in
machine learning in Santiago, Chile. Previously ML Engineer at Banco BCI and
Product Analyst at AFP Cuprum.

Physics taught me that a number without an error bar is not a measurement. Most
of what I build is an attempt to apply that to machine learning: compute the
ceiling before claiming to approach it, put a confidence interval on every
comparison, and report the result even when it contradicts what I set out to
prove.

All four repositories below do that. Every one of them reports a finding that
went against my own hypothesis.

---

### Projects

**[spanish-ner-benchmark](https://github.com/JosElias23/spanish-ner-benchmark)** — Does a Spanish-specific encoder beat a multilingual one at Spanish NER?

Five models on CoNLL-2002, from a gazetteer to XLM-R, plus a FastAPI service with
measured latency and cost.

*The hypothesis failed.* mBERT scored 0.8720 F1 and BETO 0.8705 — a difference of
0.0015, 95% CI [−0.0086, +0.0113], p = 0.75. Statistically indistinguishable.
Writing "mBERT wins" would have been a claim about sampling noise. The best model
on the dev set was also the worst on test, which is model-selection overfitting
caught in the act. Serving: batching 64 documents gives 6.7× the throughput of
one at a time.

**[battery-dispatch-optimizer](https://github.com/JosElias23/battery-dispatch-optimizer)** — How much of a grid battery's theoretical value can you capture without knowing tomorrow's prices?

Mixed-integer optimisation (PuLP/HiGHS) over forecast day-ahead prices, scored
against the perfect-foresight bound.

**85.7% of the attainable optimum**, against 39.5% for a fixed charge/discharge
schedule. *The surprise:* a 12% better forecast by MAE bought only 2 points of
capture rate. Dispatch does not need accurate prices, it needs the *ordering* of
cheap and expensive hours — a conclusion two independent experiments reached
separately. Computing the upper bound is what made the remaining 14% gap
attributable to forecasting rather than to the solver.

**[rl-from-scratch](https://github.com/JosElias23/rl-from-scratch)** — Q-learning and SARSA implemented from first principles, measured against an exactly computed optimum.

*The optimum turned out to be three different numbers.* The figure everyone
quotes for FrozenLake, 0.8235, assumes unlimited time; under the 100-step limit
the environment actually enforces, the ceiling is 0.7442. The agents reach 0.7378
— **99.1% of the attainable ceiling, with a policy matching the optimal one in
all 16 states**.

*And a result I had to correct.* On one seed, SARSA hit CartPole's 500-step cap
with zero variance and won cleanly. Across eight seeds the ordering reverses
(Q-learning 432 vs SARSA 302) and is *still* not significant. Both tables are in
the repository. In tabular RL the variance that matters is between seeds, not
within an evaluation.

**[licitaciones-unspsc](https://github.com/JosElias23/licitaciones-unspsc)** — Can a local LLM classify Chilean public tenders better than a linear model?

Real data from the ChileCompra OCDS API (CC0), warehoused in DuckDB.

*No.* TF-IDF + linear SVM reaches 0.5578 accuracy; the LLM zero-shot gets 0.2767
— **32 points worse and 9,345× slower**. Given eight *retrieved* examples it
draws level; eight *random* examples change nothing, which is exactly what the
control arm exists to prove. A fine-tuned BETO did not beat TF-IDF either.

*The finding I am most attached to:* quantising to INT8 costs 0.53% accuracy,
which reads as free. It is not — **8.19% of individual predictions change**, 15.6×
more churn than the accuracy delta implies, and half of those flips are from one
wrong answer to a different wrong answer, which no aggregate metric can see.

---

### What these repositories are evidence of

| | |
|---|---|
| **Statistics** | Paired bootstrap significance testing, Wilson intervals, block-bootstrap Monte Carlo, seed variance |
| **NLP** | HuggingFace fine-tuning, sub-word label alignment, CRF, retrieval few-shot, DSPy |
| **Optimisation** | MILP formulation, complementarity constraints, rolling horizon, perfect-foresight bounds |
| **RL** | Tabular Q-learning/SARSA from scratch, value iteration, finite-horizon backward induction |
| **Serving** | FastAPI, Docker, ONNX export, INT8 quantisation, latency/throughput/cost benchmarking |
| **Data** | DuckDB, SQL, public API ingestion, leakage detection, temporal splits |
| **Engineering** | GitHub Actions CI, 198 tests across four repositories, fixed seeds, reproducible pipelines |

Every number published in these repositories is produced by a script and stored
as JSON in `reports/`. If a result is weak, it is written up as a limitation
rather than omitted — each repository has a `docs/DECISIONS.md` explaining what
was decided, why, and what was left undone.

---

### Contact

[LinkedIn](https://www.linkedin.com/in/jose-sanhueza-perez) · Santiago, Chile
