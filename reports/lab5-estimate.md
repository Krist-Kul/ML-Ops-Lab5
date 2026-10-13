# Lab 5 — cost estimate, made before running

Written 2026-10-13 16:15 +07, before any Lab 5 resource existed: no pipeline compiled or
submitted, no service account created, no Gemini call made. `reports/lab5-cost.md` compares
the bill against this file; the point of writing it first is that it cannot be tuned to match.

Rates are the verified ones already in `src/costs.py` (asia-southeast1, 33.355 THB/USD).
Vertex Pipelines adds a flat **USD 0.03 per run** on top of the compute its steps use.

| component | assumption | THB |
|---|---|---:|
| Pipeline — step compute | 6 runs (pass, gate-fail, validate-abort, one scheduled, two lost to debugging) × 6 steps × ~2 billed min on `e2-standard-4` (6.34 THB/h); train on spot `n1-standard-4` (3.06 THB/h) | 7.6 |
| Pipeline — run fee | 6 × 0.03 USD | 6.0 |
| Training | inside the pipeline line above; no separate jobs planned | 0 |
| Storage | images already in Artifact Registry, plus ~3 MB per run in GCS, a few days | 0.5 |
| Serving | staging is a registry alias, as in Lab 4: no replica. The optimisation (Task 5.6) moves the service to a scale-to-zero platform and load-tests it for ~10 min | 1.5 |
| Monitoring | custom metrics are inside the free allotment | 0 |
| LLM step (Part B) | ~40 cases × 4 recordings × ~700 tokens on a Flash-class model | 0.5 |
| **Total, GCP, labelled `lab=5`** | | **≈ 16 THB** |

The second provider (Task 4) bills in a separate account in USD; expected to be inside its
free tier and is reported separately rather than folded into this number.

**What I expect to be wrong.** The step minutes. A pipeline step is a custom job, and the
provisioning before the container starts is the part I have never measured for a pipeline;
Lab 2's single jobs spent about as long provisioning as running. If the gap is large it will
be there, not in the per-run fee.
