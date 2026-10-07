# Dataset Contract

## 1. Upstream source

Microsoft Azure Public Dataset V2:

https://github.com/Azure/AzurePublicDataset

Official V2 documentation:

https://github.com/Azure/AzurePublicDataset/blob/master/AzurePublicDatasetV2.md

Microsoft documents V2 as a 30-day trace with five-minute VM CPU utilization readings and VM information, with approximately 2.7 million VMs and 1.94 billion CPU readings in the full release. The official schema includes encrypted subscription/deployment/VM identifiers, VM creation/deletion timestamps, deployment size, CPU statistics, VM category, virtual-core bucket, memory bucket, and five-minute CPU statistics.

## 2. Challenge release

The candidate dataset is the fixed **`data-v1`** release of this repository.

The release is a real, curated subset of Azure Public Dataset V2. It is intentionally not committed to Git because the release asset is much larger than normal source files.

The release should be downloaded and extracted so that the repository looks like:

```text
challenge_data/
├── vm_metadata.csv
├── cpu_readings_*.csv
└── README.md
```

The exact asset/file names are documented in the `data-v1` release itself.

## 3. Row-level fields

The curated release is based on the original V2 fields. Important fields include:

| Field family | Meaning |
|---|---|
| subscription/deployment/VM ID | anonymized identifiers from the upstream dataset |
| VM created/deleted | VM lifecycle timestamps |
| deployment size | size of the deployment represented in the trace |
| CPU statistics | max/average/P95 and five-minute min/max/average CPU utilization |
| VM category | workload category |
| core bucket | virtual-core capacity bucket |
| memory bucket | VM memory capacity bucket |
| five-minute timestamp | workload observation time |

Candidates should rely on the release's bundled README/schema for exact column names.

## 4. Required derived targets

The candidate must construct the following aggregate time series.

### `active_vm_count`

For timestamp `t`:

```text
active_vm_count(t) = number of distinct VM IDs with a valid CPU observation at t
```

A VM must be counted at most once within a timestamp bucket.

### `aggregate_cpu_utilization`

For timestamp `t`:

```text
aggregate_cpu_utilization(t) = mean of VM-level five-minute average CPU utilization at t
```

The target is a percentage in `[0,100]` when expressed in percent units.

Candidates may propose another aggregate target as an extension, but the two targets above remain required.

## 5. Time handling

Treat the trace timestamp as a five-minute observation grid.

Candidates should:

- normalize timestamps to a consistent timezone representation;
- detect gaps and duplicates;
- document any resampling;
- avoid inventing target values for missing source observations without justification.

## 6. Release boundary

The candidate release contains only the data required for training/analysis. It does **not** contain future target labels outside the public training window.

Do not attempt to reconstruct omitted source records or infer private evaluation material.
