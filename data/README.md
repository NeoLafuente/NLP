# Data

This directory contains the EXIST 2025 Task 1 dataset used in Assignment 1. **The data files are not included in this repository.** Access must be requested directly from the task organisers (see below).

---

## Access

Data is available for research purposes upon completing the official request form:

> **Request form:** https://forms.office.com/e/ikGpvRQ1qv

Once access is granted, place the files in the directories described below, preserving the exact filenames.

---

## Directory Layout

```
data/
├── training/
│   └── EXIST2025_training.json   # 6,920 labelled tweets
├── dev/
│   └── EXIST2025_dev.json        # 1,038 labelled tweets (used as held-out test set)
└── test/
    └── EXIST2025_test_clean.json # 2,076 unlabelled tweets (for EvALL submission)
```

---

## Dataset Overview

| Split | Size | Labels available |
|---|---|---|
| Training | 6,920 | Yes |
| Dev (held-out test) | 1,038 | Yes |
| Test (blind) | 2,076 | No |

The corpus is **bilingual** (English and Spanish), with a near-equal language split across all partitions (~53% Spanish / ~47% English). The class distribution is approximately balanced: 51% sexist / 49% not sexist in the training set.

---

## File Format

Each file is a JSON object keyed by tweet ID. Each entry contains:

| Field | Type | Description |
|---|---|---|
| `id_EXIST` | string | Unique tweet identifier |
| `tweet` | string | Raw tweet text |
| `lang` | string | Language code (`en` or `es`) |
| `labels_task1_1` | list[string] | Individual annotations from 6 annotators (`YES`/`NO`). **Absent in test split.** |
| `annotators_*` | various | Annotator demographic metadata (gender, age, ethnicity, education, country) |

Example entry (training/dev):
```json
{
  "tweet": "A woman needs love, to fill the fridge...",
  "lang": "en",
  "labels_task1_1": ["YES", "YES", "NO", "YES", "YES", "YES"]
}
```

---

## Label Schema

This project uses **majority voting** to derive a single hard label from the six per-instance annotations:

- An instance is labelled **`YES` (sexist)** if 4 or more annotators voted YES.
- An instance is labelled **`NO` (not sexist)** otherwise.
- **Tie-breaking rule:** in the event of a 3–3 split, the label defaults to `YES`.

The resulting binary labels are mapped to integers for model training: `YES → 1`, `NO → 0`.

> Note: The dataset was collected under the **Learning with Disagreement (LeWiDi)** paradigm — all individual annotations are preserved rather than a single adjudicated label. The majority vote used here is one possible aggregation strategy; soft-label approaches are also supported by the task evaluation platform (EvALL).

---

## Task Definition

**Subtask 1.1 — Sexism Identification** is a binary classification task. A tweet is labelled sexist (`YES`) not only when it actively promotes sexist views, but also when it describes or reports a sexist situation, or critically comments on sexist behaviour, provided the content itself involves or depicts sexist discourse.

| Label | Meaning |
|---|---|
| `YES` (sexist) | Contains sexist expressions, attitudes, or behaviours directed at individuals on the basis of gender — including overt misogyny, gender stereotypes, reported harassment, or ironic/humorous sexism |
| `NO` (not sexist) | Does not contain sexist expressions; neutral with respect to gender-based discrimination, or touches on gender topics without reproducing or endorsing sexist attitudes |

---

## Citation

```
Plaza, L., Carrillo-de-Albornoz, J., Arcos, I., Rosso, P., Spina, D., Amigó, E.,
Gonzalo, J., & Morante, R. (2025). Overview of EXIST 2025: Learning with Disagreement
for Sexism Identification and Characterization in Tweets, Memes, and TikTok Videos.
In: Experimental IR Meets Multilinguality, Multimodality, and Interaction. CLEF 2025.
Lecture Notes in Computer Science, vol. 16089. Springer, Cham.
https://link.springer.com/book/10.1007/978-3-032-04354-2
```
