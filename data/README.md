# Data

This directory contains the de-identified representation artifacts used to reproduce the quantitative experiments reported in the paper.

## Files

```text
data/
├── README.md
├── decision_metadata.csv
└── representations/
    ├── bert_large_pt.npz
    ├── bge_m3.npz
    ├── e5_base.npz
    ├── legalbr.npz
    ├── minilm.npz
    ├── qwen3.npz
    └── tfidf_matrix.npz
```

### `decision_metadata.csv`

Contains the public metadata required by the evaluation pipeline:

- `row_index`: row position shared by all representation matrices;
- `decision_id`: opaque identifier assigned to each decision;
- `sentence_length_days`: imposed sentence length, in days;
- `crime_type`: crime category used in the experiment.

The current release contains 1,942 simple-theft decisions.

### `representations/`

Contains the text representations used in the experiments.

Dense representations are stored as compressed NumPy archives (`.npz`) with an `embeddings` array:

- `legalbr.npz`
- `e5_base.npz`
- `minilm.npz`
- `bert_large_pt.npz`
- `qwen3.npz`
- `bge_m3.npz`

TF-IDF is stored as a SciPy sparse matrix:

- `tfidf_matrix.npz`

All matrices follow exactly the row order defined by `decision_metadata.csv`.

## Privacy

The original judicial texts, process numbers, names, court information, and the mapping between public IDs and source decisions are **not included** in this repository.

The released files should be understood as de-identified/pseudonymized representation artifacts. They are provided only to reproduce the experiments without redistributing the original judicial documents.

## Usage

The artifacts in this directory are consumed directly by:

```text
notebooks/02_reproduce_experiments.ipynb
```

No preprocessing or access to the original decision texts is required to reproduce the reported quantitative results.

See the repository-level `README.md` for installation instructions, model information, evaluation details, and expected outputs.
