# Amazon ML Challenge 2026 — Business Entity Resolution

A machine learning solution for identifying records that refer to the same real-world business across multiple noisy data sources.

**Repository:** [Ashwinchauhan89/Amazon-ML-Challenge-2026](https://github.com/Ashwinchauhan89/Amazon-ML-Challenge-2026)

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [Solution Workflow](#solution-workflow)
- [Methodology](#methodology)
- [Evaluation Metric](#evaluation-metric)
- [Repository Structure](#repository-structure)
- [Requirements](#requirements)
- [Setup](#setup)
- [Running the Solution](#running-the-solution)
- [Submission Validation](#submission-validation)
- [Data and Responsible Use](#data-and-responsible-use)
- [Limitations and Future Improvements](#limitations-and-future-improvements)
- [Author](#author)
- [Acknowledgements](#acknowledgements)

## Overview

Business information is often distributed across multiple databases and data providers. The same business can appear with spelling differences, abbreviations, inconsistent address formatting, missing fields, or alternate legal names. These inconsistencies make it difficult to determine whether two records describe the same real-world entity.

This project addresses **business entity resolution**: matching records from two noisy sources (Source 2 and Source 3) to a reference source (Source 1). The goal is to identify genuine matches while avoiding false-positive links.

The implementation and final submission artifacts are maintained in this repository. Exact model choices, performance scores, and execution commands should be documented from the code and verified evaluation output rather than assumed.

## Problem Statement

Given three sources of business records:

- **Source 1:** Reference businesses used as the anchor entities.
- **Source 2:** Records that may correspond to entities in Source 1.
- **Source 3:** Additional records that may correspond to entities in Source 1.

For every Source 1 entity, identify the matching records in Source 2 and Source 3. A record may have no match, and not every candidate pair should be linked.

Typical challenges include:

- Misspellings and typographical errors in business names.
- Differences in punctuation, spacing, abbreviations, and legal suffixes.
- Variations in street addresses and address components.
- Missing or incomplete fields.
- Similar names belonging to different businesses.
- Large numbers of possible record pairs.

## Objectives

1. Normalize business names and addresses to reduce superficial differences.
2. Generate plausible candidate pairs efficiently instead of comparing every possible pair.
3. Extract similarity signals from names and addresses.
4. Classify candidate pairs as matches or non-matches.
5. Select decision thresholds appropriate to the challenge's precision-oriented evaluation metric.
6. Produce output files in the official required format.
7. Validate the final artifacts before submission.

## Solution Workflow

```text
Business records: Source 1, Source 2, Source 3
                       |
                       v
             Data loading and checks
                       |
                       v
          Text and address normalization
                       |
                       v
           Candidate-pair generation
                 (blocking)
                       |
                       v
            Pairwise feature extraction
                       |
                       v
           Match / non-match prediction
                       |
                       v
             Threshold calibration
                       |
                       v
        Submission files and validation
```

### 1. Data preparation

Load the supplied training and test files, inspect columns and missing values, and verify identifiers and data types. Keep training labels separate from test inputs.

### 2. Text normalization

Where supported by the implementation, normalize text by applying consistent casing, whitespace handling, punctuation cleanup, and standardization of common abbreviations. Preserve original identifiers and source records so predictions can be mapped back to the required output format.

Normalization should improve comparability without erasing meaningful distinctions between businesses.

### 3. Candidate generation (blocking)

Comparing every record against every other record can be computationally expensive. Blocking narrows the search to plausible candidates using selected keys or similarity-based retrieval. Blocking improves efficiency, but a true match excluded at this stage cannot be recovered by the downstream classifier.

### 4. Feature engineering

Potential pairwise signals include:

- Exact agreement after normalization.
- Token overlap and Jaccard similarity.
- Character-level similarity, such as edit-distance-based scores.
- Fuzzy business-name similarity.
- Address similarity and token agreement.
- Agreement on available contextual fields, such as country.

Use only the features actually implemented by the solution when describing final experiments.

### 5. Match classification

A supervised classifier can estimate whether a candidate pair represents the same business. Tree-based models such as XGBoost or LightGBM are possible approaches for tabular similarity features; the final model should be named here only after confirming the implementation.

### 6. Decision thresholding

Convert model scores into match decisions using a threshold selected on validation data. Since false-positive matches can be costly under an F-score that emphasizes precision, threshold selection is an important part of the workflow.

### 7. Output generation

Generate the challenge-required prediction artifacts with the exact filenames, columns, identifiers, delimiters, and encoding specified in the official instructions. Do not assume a file schema: verify it against the challenge validator and template.

## Evaluation Metric

The challenge uses the **macro-averaged \(F_{0.5}\) score per Source 1 entity**. The \(F_{0.5}\) score weights precision more heavily than recall.

\[
F_{0.5} =
\frac{(1 + 0.5^2)\,P\,R}
{0.5^2 P + R}
=
\frac{1.25\,P\,R}{0.25P + R}
\]

Where:

- \(P\) = precision.
- \(R\) = recall.

**Precision** measures how many predicted matches are correct. **Recall** measures how many true matches are found. The macro average gives each evaluated Source 1 entity its prescribed contribution, so evaluation should follow the official challenge implementation, including its rules for entities with no matches.

Do not report a leaderboard score or local validation score unless it can be traced to a reproducible run or an official result.

## Repository Structure

The repository currently includes a `submission_FINAL/` directory. The exact files within that directory should be kept aligned with the committed implementation.

```text
Amazon-ML-Challenge-2026/
├── README.md
├── submission_FINAL/
│   └── <final submission files>
└── __MACOSX/                 # macOS archive metadata, if present
```

If `__MACOSX/` contains only archive metadata, it is generally not needed for execution and can be omitted from a cleaned repository. Review its contents before deleting it.

As the project evolves, consider documenting the roles of the actual scripts, notebooks, model files, dependency files, and output artifacts in this section.

## Requirements

Use the Python version and dependency versions supported by the submitted code. Typical requirements for a tabular entity-resolution pipeline may include:

- Python
- pandas and NumPy for data handling
- scikit-learn for preprocessing, model utilities, or evaluation
- A fuzzy matching library, if used by the implementation
- The specific ML library used by the final model

The list above is illustrative, not a verified inventory of this repository's dependencies. The committed dependency file and imports in the source code should be treated as authoritative.

## Setup

Clone the repository:

```bash
git clone https://github.com/Ashwinchauhan89/Amazon-ML-Challenge-2026.git
cd Amazon-ML-Challenge-2026
```

Create and activate a virtual environment.

**Windows (PowerShell):**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies using the dependency file provided with the submission. For example, if `requirements.txt` is present in the relevant directory:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If the dependency file is inside `submission_FINAL/`, run the installation command with that directory's path. Do not install an example dependency list blindly; use the file supplied by the project.

## Running the Solution

The precise entry point depends on the scripts included in `submission_FINAL/`. Inspect that directory and follow the submission's own run instructions.

A reproducible run should document:

1. Where the official training and test datasets must be placed.
2. The script or notebook used to preprocess records and build candidates.
3. The training and inference commands.
4. The output directory and required prediction filenames.
5. Any configuration values, random seeds, hardware requirements, and caches.
6. The exact command used to validate the generated files.

Do not run guessed script names. Replace this section with verified commands from the actual submission code before presenting the README as a complete reproduction guide.

## Submission Validation

Before submitting:

- [ ] Confirm the expected dataset files and paths.
- [ ] Confirm output filenames, columns, delimiters, and encoding against the official specification.
- [ ] Check that every output identifier comes from the supplied test data.
- [ ] Check for duplicate or malformed prediction rows.
- [ ] Confirm that the code does not use training labels to generate test predictions.
- [ ] Run the official validator, if supplied.
- [ ] Record the command, environment, and verified evaluation result.
- [ ] Ensure the submitted archive contains the required files and excludes unnecessary caches or secrets.

## Data and Responsible Use

- Use the challenge-provided datasets and permitted resources.
- Follow the official rules on external lookups, data access, and submission packaging.
- Do not publish private, restricted, or challenge-prohibited data in the repository.
- Keep API keys, credentials, personal information, and local environment files out of version control.
- Separate validation data from final test data to avoid leakage and misleading performance claims.

## Limitations and Future Improvements

Possible directions for further work, depending on the current implementation:

- Improve candidate blocking while measuring candidate recall.
- Evaluate normalization across languages, scripts, and country-specific address formats.
- Compare fuzzy similarity features and supervised classifiers.
- Tune thresholds using entity-level validation consistent with the official metric.
- Add error analysis for false positives, false negatives, and unmatched entities.
- Improve runtime and memory use for larger datasets.
- Add a reproducible end-to-end command and automated validation checks.

Any claimed improvement should be backed by controlled experiments on a clearly identified validation split.

## Author

**Ashwin Chauhan**

- GitHub: [@Ashwinchauhan89](https://github.com/Ashwinchauhan89)
- Project: [Amazon ML Challenge 2026](https://github.com/Ashwinchauhan89/Amazon-ML-Challenge-2026)

## Acknowledgements

Thanks to the Amazon ML Challenge organizers for defining the problem and providing the challenge resources. Refer to the official challenge documentation for the definitive task specification, dataset rules, evaluation procedure, and submission requirements.



##  The training files and datasets used in this project are not publicly displayed or shared in this repository to maintain data confidentiality and comply with the applicable rules, terms, and policies of the Amazon ML Challenge 2026.
