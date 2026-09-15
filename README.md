# Evaluating Representations of Judicial Decisions Using Sentencing Similarity

This repository contains the reproducibility package for the experiments reported in **“Evaluating Representations of Judicial Decisions Using Sentencing Similarity.”**

The experiments compare one sparse lexical representation (TF-IDF) and six dense text representations on Brazilian judicial decisions. The evaluation asks whether decisions that are close in a representation space also exhibit similar sentence lengths.

## Repository structure

```text
.
├── README.md
├── requirements.txt
├── .gitignore
├── SHA256SUMS.txt
├── artifact_manifest.json
│
├── data/
│   ├── decision_metadata.csv
│   └── representations/
│       ├── bert_large_pt.npz
│       ├── bge_m3.npz
│       ├── e5_base.npz
│       ├── legalbr.npz
│       ├── minilm.npz
│       ├── qwen3.npz
│       └── tfidf_matrix.npz
│
├── notebooks/
│   ├── 01_build_public_representations.ipynb
│   └── 02_reproduce_experiments.ipynb
│
└── results/
    ├── figures/
    ├── metrics/
    ├── statistics/
    └── tables/
```

## Public data and privacy

The original judicial decision texts are **not distributed in this repository**.

The public package contains only:

- opaque decision identifiers;
- the sentence length used in the quantitative evaluation;
- de-identified/pseudonymized vector representations;
- aggregate and statistical outputs derived from those representations.

Original process identifiers and the mapping between public IDs and source decisions are not included.

The representation files should therefore be understood as **de-identified representation artifacts**, not as a claim of mathematically irreversible anonymization.

## Evaluated representations

The following dense representations are evaluated:

| Repository name | Model/checkpoint |
|---|---|
| `legalbr` | `rufimelo/Legal-BERTimbau-sts-large-ma-v3` |
| `e5_base` | `intfloat/multilingual-e5-base` |
| `minilm` | `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` |
| `bert_large_pt` | `rufimelo/bert-large-portuguese-cased-sts` |
| `qwen3` | `Qwen/Qwen3-Embedding-0.6B` |
| `bge_m3` | `BAAI/bge-m3` |

TF-IDF is included as the sparse lexical baseline.

## Experimental criterion

For each decision, the evaluation retrieves its \(k\) nearest neighbors using cosine similarity in the corresponding representation space.

The main metric is the neighborhood variance ratio \(R(k)\), which compares the average sentence-length variance inside the induced neighborhoods with the sentence-length variance of the complete evaluation set:

- \(R(k) < 1\): neighborhoods are more homogeneous in sentence length than the complete set;
- \(R(k) = 1\): neighborhood variance is equivalent to the global variance;
- \(R(k) > 1\): neighborhoods are more heterogeneous than the global reference.

Therefore, **lower \(R(k)\) values are preferred under this criterion**.

The package also reports neighbor-based mean absolute error (MAE), Friedman omnibus tests, Kendall's \(W\), pairwise Wilcoxon signed-rank tests, Holm-adjusted p-values, and average ranks.

## Quick reproduction

The public experiments can be reproduced **without access to the original judicial texts**.

### 1. Create an environment

Python 3.10 or newer is recommended.

```bash
python -m venv .venv
```

Linux/macOS:

```bash
source .venv/bin/activate
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Start Jupyter

From the repository root:

```bash
jupyter lab
```

Open:

```text
notebooks/02_reproduce_experiments.ipynb
```

and run all cells.

The notebook automatically creates/recreates the `results/` directory.

## Expected sanity-check results

The public reproduction should use **1,942 decisions** and seven representations.

The main table should contain approximately the following values:

| Representation | R@5 | R@10 | R@20 | MAE@5 | MAE@10 | MAE@20 |
|---|---:|---:|---:|---:|---:|---:|
| LegalBR | 0.7003 | 0.8234 | 0.8872 | 117.16 | 113.25 | 111.04 |
| E5 Base | 0.8233 | 0.9795 | 1.0785 | 128.85 | 121.09 | 119.79 |
| MiniLM | 0.8300 | 0.9386 | 1.0337 | 125.78 | 119.44 | 117.30 |
| BERT Large PT | 0.6763 | 0.7880 | 0.8609 | 118.58 | 115.35 | 113.76 |
| Qwen3 | 0.7925 | 0.9102 | 0.9942 | 119.36 | 115.03 | 113.06 |
| BGE-M3 | 0.6956 | 0.8100 | 0.8761 | 115.12 | 111.24 | 109.42 |
| TF-IDF | 0.6057 | 0.7587 | 0.8921 | 117.18 | 113.56 | 112.43 |

Small floating-point differences across platforms are possible.

The Friedman tests should identify statistically significant differences across representations for both local \(R\) and absolute error at \(k \in \{5,10,20\}\), with small Kendall's \(W\) effect sizes.

## Notebook roles

### `01_build_public_representations.ipynb`

This is an **author-side artifact-generation notebook**.

It documents how the released representation files were assembled from the private, already-preprocessed corpus. When complete embeddings from the original experiment are available, they are reused and packaged into compressed `.npz` files. The existing TF-IDF matrix is likewise preserved.

Because the original judicial texts are intentionally not distributed, this is **not expected to be executed from the public repository**. It is included for methodological transparency.

### `02_reproduce_experiments.ipynb`

This is the **reproducibility notebook**.

It uses only the files under `data/` and reproduces:

- cosine-similarity neighborhoods;
- \(R(k)\);
- neighbor-based MAE;
- the main \(R(k)\) figure;
- Friedman tests;
- Kendall's \(W\);
- pairwise Wilcoxon tests with Holm correction;
- average ranks;
- the compact results table.

No original decision text or process identifier is required.

## Generated outputs

Running Notebook 02 produces:

```text
results/
├── figures/
│   ├── rk_all_representations.pdf
│   └── rk_all_representations.png
├── metrics/
│   └── rk_mae.csv
├── statistics/
│   ├── average_ranks.csv
│   ├── friedman_tests.csv
│   └── wilcoxon_holm.csv
└── tables/
    └── main_results.csv
```

## Artifact integrity

`SHA256SUMS.txt` contains checksums for the released public artifacts.

The representation-generation configuration and software environment recorded during artifact creation are available in:

```text
artifact_manifest.json
```

## Notes on reproducibility

- All representation matrices follow the exact row order defined by `data/decision_metadata.csv`.
- The diagonal of each cosine-similarity matrix is excluded before nearest-neighbor ranking.
- Statistical comparisons use the same decisions across all representations, yielding a paired repeated-measures design.
- The public artifact package is sufficient to reproduce the quantitative evaluation reported in the paper; generation from raw judicial documents is intentionally outside the public release.

## Citation

Citation information will be added after publication.

