# EXIST 2025 — Sexism Detection Baseline (Assignment 1)

Reproducible baseline for **EXIST 2025, Task 1 – Subtask 1.1: Sexism Identification in Tweets**. The notebook benchmarks classical NLP pipelines (TF-IDF and FastText embeddings paired with Logistic Regression and LinearSVC) on a bilingual (English/Spanish) tweet corpus, establishing a reference point for future transformer-based approaches.

---

## Project Structure

```
.
├── data/
│   ├── training/          # 6,920 labelled tweets (EXIST2025_training.json)
│   ├── dev/               # 1,038 labelled tweets used as held-out test set (EXIST2025_dev.json)
│   ├── test/              # 2,076 unlabelled tweets for EvALL submission (EXIST2025_test_clean.json)
│   └── README.md          # Data documentation (access, format, label schema)
├── notebooks/
│   └── Assignment_1.ipynb # Main experimental notebook
├── report/
│   ├── Assignment_1.pdf   # Full written report
│   └── figures/           # Exported figures (PDF)
├── results/
│   └── submissions/       # JSON prediction files + metric plots
├── src/
│   └── dummy.txt
├── pyproject.toml
├── uv.lock
└── README.md
```

---

## Task

Binary text classification: given a tweet in English or Spanish, predict whether it contains **sexist content** (label `YES`) or not (label `NO`). The task is part of [EXIST 2025](https://nlp.uned.es/exist2025/) at CLEF 2025, evaluated with F1-Macro.

---

## Methodology

The pipeline follows three sequential stages:

**1. Preprocessing ablation** — Six strategies (raw, lowercase, no punctuation, stopword removal, stemming, lemmatisation) are evaluated via 5-fold cross-validation. *Lowercase* was selected as the best trade-off between performance (F1 = 0.6506) and computational cost.

**2. N-gram ablation** — Six n-gram ranges are compared using TF-IDF + Logistic Regression. *Unigrams only* `(1,1)` outperform all multi-word configurations, as the small corpus (~6 900 samples) cannot support the feature-space explosion introduced by higher-order n-grams.

**3. Hyperparameter optimisation** — Exhaustive grid search (5-fold CV) over two representation paradigms:

| Paradigm | Vectoriser | Classifiers | Configurations |
|---|---|---|---|
| Sparse | TF-IDF | Logistic Regression, LinearSVC | 216 each |
| Dense | FastText (trained from scratch) | Logistic Regression, LinearSVC | 24 each |

---

## Results

| Model | Representation | Best CV F1-Macro | Val F1-Macro |
|---|---|---|---|
| **Logistic Regression** | **TF-IDF** | **0.6536** | **0.75** |
| LinearSVC | TF-IDF | 0.6524 | — |
| LinearSVC | FastText | 0.5322 | 0.62 |
| Logistic Regression | FastText | 0.5234 | — |

The sparse TF-IDF + Logistic Regression model is the primary submission model. Dense FastText embeddings trained from scratch on ~6 900 tweets significantly underperform, primarily due to the small corpus size and information loss from mean-pooling.

Best sparse model hyperparameters:
```
binary=False, max_features=40000, min_df=1, sublinear_tf=True, C=1, class_weight=None
```

---

## Setup

This project uses [uv](https://github.com/astral-sh/uv) for dependency management.

```bash
# Install dependencies
uv sync

# Launch notebook
uv run jupyter notebook notebooks/Assignment_1.ipynb
```

---

## Submission Files

Predictions on the official test split are written to `results/submissions/`:

| File | Description |
|---|---|
| `sparse_preds.json` | Predictions from best TF-IDF + LR model |
| `dense_preds.json` | Predictions from best FastText + SVM model |

Both files follow the EvALL submission format (JSON list with `test_case`, `id`, and `value` fields).

---

## Citation

If you use this code or reference these results, please cite the shared task:

```
Plaza, L. et al. (2025). Overview of EXIST 2025: Learning with Disagreement for Sexism
Identification and Characterization in Tweets, Memes, and TikTok Videos.
CLEF 2025, Lecture Notes in Computer Science, vol. 16089. Springer, Cham.
```
