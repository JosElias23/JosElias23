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

All five repositories below do that. Every one of them reports a finding that
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
caught in the act — though once all ten pairwise comparisons carry a Holm
correction, no two transformers separate on the test set at all. Serving:
batching 32 documents gives 4.0× the throughput of one at a time on GPU, and
1.4× on CPU.

**[battery-dispatch-optimizer](https://github.com/JosElias23/battery-dispatch-optimizer)** — How much of a grid battery's theoretical value can you capture without knowing tomorrow's prices?

Mixed-integer optimisation (PuLP/HiGHS) over forecast day-ahead prices, scored
against the perfect-foresight bound.

**85.7% of the attainable optimum**, against 39.5% for a fixed charge/discharge
schedule. *The surprise:* a 12% better forecast by MAE bought only 2 points of
capture rate. Dispatch does not need accurate prices, it needs the *ordering* of
cheap and expensive hours — a conclusion two independent experiments reached
separately. Running the same rolling optimiser on the realised prices is what
made the remaining 14% gap attributable to forecasting rather than to the
horizon: −0.2% of it is the 48-hour window and 100.2% is forecast error.

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

*No.* TF-IDF + linear SVM reaches 0.5578 accuracy; the LLM zero-shot gets 0.2794
— **27.8 points worse and 624× slower**. Given eight *retrieved* examples it
closes most of the gap; eight *random* examples change nothing. But a majority
vote over the same eight retrieved examples, with no language model at all,
scores the same at a fifteenth of the cost — so the retrieval step carries that
result, not the generation step. A fine-tuned BETO did not beat TF-IDF either,
and loses significantly once its epoch count is chosen on a validation month
instead of the held-out one.

*The finding I am most attached to:* quantising to INT8 costs 0.53% accuracy,
which reads as free. It is not — **6.83% of individual predictions change** while
every aggregate metric says nothing happened, and nearly half of those flips go
from one wrong answer to a different wrong answer.

**[noaa-gsod-climate](https://github.com/JosElias23/noaa-gsod-climate)** — Does a results table I published in 2025 survive being recomputed?

Five years of NOAA weather data, 20,110,620 station-days, queried with SQL.

*It does not.* The table in that project's README reported fog as the most common
weather event at 10.5%; recomputing from a different source gives **rain at
25.02%, roughly 4.5× more frequent than fog**. The ranking was inverted and the
magnitudes were off by four orders of magnitude. The notebook's own output had
been right all along — only the write-up was wrong, which is the more
uncomfortable failure, because the code is the part people check.

Counting in SQL instead of pulling the year into pandas runs **38.7× faster**,
and a balanced panel of the 11,475 stations reporting in all five years shows
**13.9% of the apparent warming is the station network changing, not the
climate, 95% CI [8.2%, 18.9%]**.

*The part I did not expect:* the repository shipped a `sql/bigquery.sql` that
had never been executed, with a comment promising it returned the same numbers.
It could not have — the indicator columns are `STRING` there, not `BOOL`, so
`COUNTIF(fog)` is a type error and the file returned nothing. Fixed and run
against `bigquery-public-data.noaa_gsod`, it agrees with my own CSV parser on
**all six counts exactly**, and prices what the local experiment could only
time: `SELECT *` scans 729.6 MiB against 68.0 MiB for naming six columns,
**10.8× fewer bytes** for the same one-word change that was 10.1× faster
locally.

---

### What these repositories are evidence of

| | |
|---|---|
| **Statistics** | Paired bootstrap significance testing, Wilson intervals, block-bootstrap Monte Carlo, seed variance |
| **NLP** | HuggingFace fine-tuning, sub-word label alignment, CRF, retrieval few-shot, DSPy |
| **Optimisation** | MILP formulation, complementarity constraints, rolling horizon, perfect-foresight bounds |
| **RL** | Tabular Q-learning/SARSA from scratch, value iteration, finite-horizon backward induction |
| **Serving** | FastAPI, Docker, ONNX export, INT8 quantisation, latency/throughput/cost benchmarking |
| **Data** | DuckDB, SQL, Parquet warehousing at 20 M rows, public API and bulk-archive ingestion, leakage detection, temporal splits |
| **Cloud** | BigQuery against a public dataset, dry-run query planning, bytes-scanned and cost-per-query accounting, application-default credentials |
| **Engineering** | GitHub Actions CI, 344 tests across five repositories, fixed seeds, reproducible pipelines |

Every number published in these repositories is produced by a script and stored
as JSON in `reports/`. If a result is weak, it is written up as a limitation
rather than omitted — each repository has a `docs/DECISIONS.md` explaining what
was decided, why, and what was left undone.

---

### Contact

[LinkedIn](https://www.linkedin.com/in/jose-sanhueza-perez) · Santiago, Chile
