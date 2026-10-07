# AI/ML Take-Home Assignment
## Cloud Workload Forecasting & Analysis

### Time limit

**5–7 days maximum expected effort.**

We are not looking for a production-scale data platform. We are looking for a strong end-to-end ML approach to a realistic cloud workload problem.

---

## 1. Context

Cloud infrastructure teams need to understand and forecast workload demand to make decisions around capacity, scaling, resource allocation, and operational headroom.

You are given a curated subset of the **Microsoft Azure Public Dataset V2** VM trace. The upstream dataset contains anonymized VM telemetry sampled every five minutes together with VM metadata. The source contains 30 consecutive days of trace data from one geographical region.

Your task is to answer:

> **How predictable is this cloud workload, how accurately can near-term demand be forecast, and what should an infrastructure team do with the forecast?**

There is no single expected model.

---

# 2. Dataset

The dataset in the `data-v1` GitHub Release is a real subset of Azure Public Dataset V2. It is distributed separately from the source code because it is intentionally large enough to resemble a real data-science workload while remaining practical for a laptop/workstation.

The release contains a curated selection of Azure VM telemetry for the challenge population.

At the row level, the upstream V2 schema includes fields for encrypted subscription/deployment/VM identifiers, VM creation/deletion timestamps, deployment size, CPU statistics, VM category, virtual-core bucket, memory bucket, and five-minute CPU statistics. See [`DATASET.md`](DATASET.md) for the challenge contract and interpretation guidance.

---

# 3. Required work

## A. Understand the workload

Before modeling, establish what the data represents.

Investigate at least:

- timestamp cadence and coverage;
- missingness, duplicates, and irregularities;
- VM lifetimes and churn;
- CPU utilization distributions;
- temporal patterns and recurring behavior;
- workload differences across meaningful VM segments;
- peak and low-demand periods.

Do not stop at charts. Explain why important observations matter operationally.

## B. Construct forecasting targets

Build a time series at the required five-minute cadence, or aggregate to a coarser cadence with a clear justification.

You must produce forecasts for:

1. `active_vm_count` — number of active VMs represented by the challenge population at each timestamp.
2. `aggregate_cpu_utilization` — mean of the VM-level average CPU utilization readings represented at each timestamp, expressed as a percentage in `[0,100]`.

You may introduce additional targets if they improve the operational interpretation.

For `active_vm_count`, count each VM at most once per timestamp.

For `aggregate_cpu_utilization`, clearly document how you treat missing readings and any timestamp-level gaps.

## C. Forecast the next 24 hours

Produce a forecast for the **24-hour horizon immediately following the training window supplied by the challenge release**.

At five-minute cadence this corresponds to 288 forecast timestamps.

The supplied forecast template contains those timestamps. Your output must contain one prediction for every requested timestamp.

You may model at a coarser cadence, but your submission must be mapped back to every required timestamp and you must document how you performed that mapping.

## D. Build a baseline

Use at least one simple baseline, such as:

- persistence / last value;
- moving average;
- seasonal-naive.

The baseline must be implemented and evaluated using the same time-aware validation scheme as your main approach.

## E. Use time-aware validation

Do **not** randomly split time-series rows into train and test sets.

Use a temporal holdout, rolling-origin evaluation, or expanding-window evaluation.

Explain your split design and why it represents the production forecasting problem.

## F. Analyse forecast errors

Report more than one aggregate metric.

At minimum discuss:

- MAE;
- RMSE;
- a normalized metric or relative improvement over the baseline;
- performance during high-load/peak periods;
- where the model fails and why.

If you use a percentage metric, explain how you handle near-zero denominators.

## G. Make an operational recommendation

Assume an infrastructure team will actually use your forecast.

Recommend a simple policy for capacity planning, scaling, or headroom. Your policy might use:

- a safety margin;
- a forecast threshold;
- an uncertainty band;
- a peak-risk trigger;
- a workload-segment rule.

Explain the trade-off between under-provisioning and over-provisioning.

---

# 4. Modeling freedom

You may use:

- statistical forecasting;
- linear models;
- tree-based ML;
- gradient boosting;
- neural networks;
- hybrid approaches.

You may also use external public data if it is documented and legally usable.

**Model complexity is not a scoring category by itself.** A simple, well-validated approach is preferable to a complex model that leaks future information or cannot be reproduced.

---

# 5. Feature engineering

Reasonable features include:

- hour/day/week calendar features;
- lagged targets;
- rolling statistics;
- seasonal lags;
- recent growth or decay;
- workload-segment aggregates;
- VM churn/activity features.

For any feature derived from timestamps, make the information-availability assumption explicit.

A feature is valid only if it could have been computed at the forecast origin.

---

# 6. Optional extensions

These are optional. Do the core work first.

### Segment-level forecasting

Investigate categories, deployment sizes, core buckets, memory buckets, or another defensible segmentation.

### Uncertainty estimation

Produce prediction intervals or another uncertainty estimate and use them in the capacity recommendation.

### Anomaly detection

Identify unusual workload periods and assess whether they were predictable.

### Cross-cloud comparison

Compare selected Azure workload characteristics with another public cloud workload such as Google ClusterData2019. Focus on semantically comparable signals rather than forcing identical schemas.

### Generalization

Investigate whether your modeling approach changes materially across workload segments or environments.

---

# 7. Deliverables

Your submission should contain:

```text
README.md
pyproject.toml / requirements.txt
src/ or equivalent implementation
notebooks/ or equivalent analysis
reports/final_report.md or reports/final_report.pdf
tests/ (strongly recommended)
```

## README

Document:

- installation;
- dataset location;
- how to train/run the model;
- how to generate the required predictions;
- the main modeling decisions.

A reviewer should be able to follow your README from a clean checkout to a generated `predictions.csv`.

## Final report

Target length: **4–6 pages**, excluding appendices.

Include:

1. Executive summary;
2. data understanding and preprocessing;
3. workload analysis;
4. forecasting approach;
5. validation methodology;
6. results versus baseline;
7. error/failure analysis;
8. capacity-planning recommendation;
9. limitations and future work.

## Required prediction file

Generate:

```text
predictions.csv
```

using the schema in [`SUBMISSION.md`](SUBMISSION.md).

---

# 8. Evaluation

The evaluation emphasizes:

| Area | Weight |
|---|---:|
| Data understanding & preprocessing | 15% |
| Exploratory workload analysis | 20% |
| Forecasting methodology & validation | 20% |
| Model quality & feature engineering | 15% |
| Evaluation & error analysis | 15% |
| Engineering quality & reproducibility | 10% |
| Communication & operational recommendation | 5% |
| **Total** | **100%** |

We will not award more points simply because a candidate used LSTM/Transformer/etc.

A methodologically sound baseline-plus-ML solution can score very highly.

---

# 9. Important constraints

Do not:

- use future target values during feature construction;
- use the hidden future period during model development;
- randomly shuffle the time series for validation;
- rely on private/proprietary data;
- commit credentials or large generated artifacts to the repository;
- claim a model is better solely because of a single cherry-picked metric.

If you make a simplifying assumption, state it.

---

# 10. What a strong submission looks like

A strong submission usually does the following:

1. establishes a surprisingly useful simple baseline;
2. uses leakage-safe temporal validation;
3. explains workload behavior quantitatively;
4. chooses model/features for a reason;
5. analyses failure modes, not just average accuracy;
6. connects the forecast to an operational decision;
7. is reproducible from a clean checkout.
