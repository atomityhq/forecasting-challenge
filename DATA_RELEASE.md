# Challenge Dataset Release

The hiring dataset is distributed as a GitHub Release asset because it is intentionally large for a take-home exercise.

## Required release

Use the fixed release:

```text
data-v1
```

The release asset should be named:

```text
azure-challenge-data-v1.zip
```

After extraction:

```text
challenge_data/
├── vm_metadata.csv
├── cpu_readings_*.csv
└── README.md
```

## Expected size

The intended release size is approximately **1.5 GB compressed**. The exact byte size is not part of the assignment contract.

## Integrity

The release should include a SHA-256 checksum file. Verify it before starting work:

```bash
sha256sum -c azure-challenge-data-v1.sha256
```

## Source attribution

The dataset is a curated subset of Microsoft's Azure Public Dataset V2. See `SOURCES.md` and the upstream repository for attribution and current license terms.
