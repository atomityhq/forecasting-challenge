# Cloud Workload Forecasting Challenge

AI/ML take-home challenge built from the **Microsoft Azure Public Dataset V2** VM trace.

## Start here

1. Read [`ASSIGNMENT.md`](ASSIGNMENT.md).
2. Read [`DATASET.md`](DATASET.md).
3. Download the **`data-v1` GitHub Release** dataset and extract it as `challenge_data/`.
4. Read [`SUBMISSION.md`](SUBMISSION.md).
5. Implement your analysis and forecasting pipeline.

The challenge dataset is distributed as a GitHub Release rather than committed to the repository because it is intentionally large (targeting roughly 1.5 GB compressed).

## Dataset download

The hiring dataset is the fixed `data-v1` release of this repository. Use the exact version named in your assignment invitation; do not substitute a different upstream Azure release.

After downloading and extracting, the expected layout is:

```text
challenge_data/
├── vm_metadata.csv
├── cpu_readings_*.csv
└── README.md
```

The release is a **real, curated subset of Azure Public Dataset V2**. It is not synthetic data.

## What you are solving

The underlying data contains anonymized VM workload telemetry sampled every five minutes. Your job is to understand the workload and build a defensible forecasting pipeline for near-term aggregate demand.

The challenge is intentionally open-ended. We evaluate reasoning, time-series methodology, leakage prevention, error analysis, reproducibility, and the quality of your operational conclusions—not just model complexity.

## Expected environment

Python 3.11+ is recommended.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -e '.[dev]'
pytest -q
```

Model dependencies are intentionally not prescribed. Add only what you need and document them.

## Source

Microsoft Azure Public Dataset V2:
https://github.com/Azure/AzurePublicDataset

The official V2 documentation describes a 30-day VM trace with five-minute CPU readings, VM metadata, and encrypted subscription/deployment/VM identifiers. The full upstream trace is much larger than this challenge release.

## Submission

Submit your work as a repository or archive according to [`SUBMISSION.md`](SUBMISSION.md).
