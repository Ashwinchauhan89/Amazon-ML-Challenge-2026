# Amazon ML Challenge 2026 — Output Files and Training Data

This directory documents the training data, model workflow, and prediction artifacts for the **Amazon ML Challenge 2026: Business Entity Resolution** project.

> **Data access note:** The official challenge dataset must be obtained through the authorized challenge resources. This file documents the expected file roles and formatS.

## 1. Challenge Overview

The task is to resolve business records from three independent data sources. Source 1 is the reference source. For each Source 1 record, the system predicts the matching record IDs from Source 2 and Source 3. A Source 1 entity may have no matches, one match, or multiple matches.

Business names and addresses may vary because of typos, abbreviations, punctuation, transliteration, missing address components, and formatting differences.

## 2. Training Dataset

Place the authorized training files in the expected training directory:

```text
dataset/
└── train/
    ├── train_source1.tsv
    ├── train_source2.tsv
    ├── train_source3.tsv
    └── train_ground_truth.tsv
```

| File | Purpose |
|---|---|
| `train_source1.tsv` | Reference business records |
| `train_source2.tsv` | Candidate business records from Source 2 |
| `train_source3.tsv` | Candidate business records from Source 3 |
| `train_ground_truth.tsv` | Known matches used to train and validate the matching pipeline |

Each source file is expected to contain these fields:

- `entity_id` — unique record identifier, usually prefixed with `S1-`, `S2-`, or `S3-`
- `business_name` — business name
- `business_address` — business address
- `country` — country label

The ground-truth file is expected to contain:

- `source1_entity_id` — Source 1 record identifier
- `matched_entity_ids` — comma-separated IDs of matching Source 2 and/or Source 3 records; empty when there are no matches

Use the actual challenge files and official instructions as the authority for schemas and filenames.

## 3. Test Dataset

The authorized test files should be placed in the test directory:

```text
dataset/
└── test/
    ├── test_source1.tsv
    ├── test_source2.tsv
    └── test_source3.tsv
```

The test set does not include ground-truth match labels. Do not invent labels for the test data or use test records to tune against unavailable answers. Ensure the pipeline supports all country labels found in the test data, including labels that were not present in training.

## 4. TSV Format

All `.tsv` files are **tab-separated**, not comma-separated. Read them with an explicit tab delimiter:

```python
import pandas as pd

source1 = pd.read_csv("dataset/train/train_source1.tsv", sep="\t")
ground_truth = pd.read_csv(
    "dataset/train/train_ground_truth.tsv",
    sep="\t"
)
```

Do not open a TSV as a CSV with the default comma delimiter: address values and matched-ID lists can contain commas.

## 5. Matching Pipeline

The end-to-end workflow is:

1. Load the source records and verify required columns.
2. Normalize business names and addresses without changing record IDs.
3. Generate candidate pairs using a blocking strategy.
4. Calculate pairwise name/address similarity features.
5. Train and validate a matching model using training labels.
6. Select a decision threshold using a validation split and the official evaluation procedure.
7. Run inference on the test data.
8. Write and validate the output TSV files.

Keep the validation split separate from model fitting to reduce overfitting and avoid data leakage.

## 6. Output Artifacts

The final output directory is expected to contain:

```text
output/
├── matching_results.tsv
└── candidate_pairs.tsv
```

### `matching_results.tsv`

This is the final matching prediction file.

Required logical columns:

| Column | Description |
|---|---|
| `source1_entity_id` | Source 1 reference record ID |
| `matched_entity_ids` | Comma-separated matching IDs from Source 2 and/or Source 3; empty when there is no match |

Example only — these IDs are illustrative, not actual challenge predictions:

```tsv
source1_entity_id	matched_entity_ids
S1-EXAMPLE-001	S2-EXAMPLE-009,S3-EXAMPLE-021
S1-EXAMPLE-002	S3-EXAMPLE-014
S1-EXAMPLE-003	
```

Every Source 1 test entity must be represented according to the official submission specification. Confirm the exact output schema and row-count requirements with the official validator before submission.

### `candidate_pairs.tsv`

This file records candidates produced by the blocking/candidate-generation stage. Its exact columns must match the challenge instructions or the project's validator. A typical design records the Source 1 ID and the candidate Source 2/Source 3 IDs considered for matching.

This file is an intermediate/audit artifact; do not assume it replaces `matching_results.tsv` as the scored submission.

## 7. Evaluation

The challenge specification describes a precision-weighted F-score, \(F_{0.5}\), with macro-averaging per Source 1 entity. Precision is weighted more heavily than recall.

\[
F_{0.5} =
\frac{1.25 \times P \times R}{0.25 \times P + R}
\]

Where `P` is precision and `R` is recall. Follow the official evaluation code for the exact treatment of empty match lists and macro-averaging.

Do not include a leaderboard result or validation score in this file unless it is backed by a reproducible run or an official result.

## 8. Validation Checklist

Before submission:

- [ ] All required output files exist in `output/`.
- [ ] Files use tab delimiters and the required column names.
- [ ] Every required Source 1 test ID is represented exactly as specified.
- [ ] Matched IDs refer to valid records in Source 2 or Source 3.
- [ ] Empty-match cases are encoded according to the official format.
- [ ] No duplicate or malformed rows are present.
- [ ] No training labels or private data are accidentally included in test predictions.
- [ ] The official validator passes.
- [ ] The submission package contains only permitted files and no credentials or secrets.

## 9. Data Security and Challenge Compliance

- Obtain datasets only from authorized challenge resources.
- Do not commit restricted datasets, hidden labels, credentials, API keys, or personal information to a public repository.
- Follow the challenge rules for external data, network access, model usage, and submission packaging.
- Keep large datasets and generated artifacts out of Git unless the challenge explicitly permits publishing them.


# NOTE : I AM NOT GOING SHOW THE TRAINING FILE AND DATASETS BECAUSE OF THE DATA SHOULD TAKEN SECERT AS UNDER THE RULE AND TERMS OF AMAZON POLICS IN THIS CHALLENGE.
