# Manosphere Register Mutation and Detection Failure

This repository contains the notebooks for my capstone project:

**The Language Has Changed, the Ideology Has Not: Register Mutation and Machine Blindness in Manosphere Reddit Discourse, 2012-2025**

The project examines whether manosphere language on Reddit changes over time and whether those changes make older detection methods less reliable.

## Research question

How has manosphere discourse changed across Reddit eras from 2012 to 2025, and what does this mean for automated detection of misogynistic or manosphere-aligned content?

## Scope

These notebooks work with Reddit post titles, similarity scores and LLM-coded trope labels.

The project does not claim to measure individual users' beliefs, offline behaviour, or the whole manosphere ecosystem. SBERT is used to compare meaning, not to prove endorsement. The LLM classifier is used carefully to help validate labels and select cases for closer reading.

## Repository structure

```text
capstone_manosphere_github_ready/
  README.md
  requirements.txt
  .gitignore

  notebooks/
    01_data_collection_preprocessing.ipynb
    02_quantitative_nlp_pipeline.ipynb
    03_trope_classification_false_negatives.ipynb

  data/
    README.md
    raw/
    processed/
    outputs/

```

## Notebook guide

### 1. Data collection and preprocessing

`notebooks/01_data_collection_preprocessing.ipynb`

This notebook builds the project corpus. It loads and parses the source data, applies the inclusion and exclusion choices, and exports the CSV files used by Notebook 2.

Expected outputs include:

* `Master_Manosphere_Dataset.csv`
* `exrpdf.csv`
* `comparison_v3.csv`

### 2. Quantitative NLP pipeline

`notebooks/02_quantitative_nlp_pipeline.ipynb`

This notebook tests the broad quantitative pattern across eras using Jaccard distance, TF-IDF, VADER, SBERT, logistic regression, temporal generalisation and bootstrap confidence intervals.

Notebook 2 creates `clean_df.csv`, which Notebook 3 uses.

### 3. Trope classification and false-negative analysis

`notebooks/03_trope_classification_false_negatives.ipynb`

This notebook validates the LLM-assisted trope classification workflow and identifies false-negative cases for qualitative analysis.

It includes prompt development, hand-coded validation, held-out testing, F1, Cohen's kappa, reliability decisions and case selection.

Notebook 3 can call the Groq API for Llama 3.3 70B classification, but it can also use saved output files where final classifications have already been generated.

## Data files

Place the required CSV files in the `data/` folder before running the notebooks.

Notebook 2 expects:

* `Master_Manosphere_Dataset.csv`
* `comparison_v3.csv`
* `sigma_anchors.csv`
* `exrpdf.csv`

Notebook 3 expects:

* `clean_df.csv`
* `gold_standard_FINAL.csv`
* `gs_test_with_predictions.csv`
* `corpus_with_tropes.csv`

See `data/README.md` for more details on where files should go.

## API setup for Notebook 3

Notebook 3 uses the Groq API. The API key is not stored in the notebook.

Set `GROQ_API_KEY` in your environment before running API-dependent cells. The notebook reads it using `os.environ.get("GROQ_API_KEY")`.

## Run order

1. Run Notebook 1.
2. Check that the exported filenames match the Notebook 2 inputs.
3. Run Notebook 2 and check that `clean_df.csv` has been created.
4. Set `GROQ_API_KEY` if running API cells in Notebook 3.
5. Run Notebook 3, or use the saved classification outputs intentionally.
6. Save all notebooks after successful execution.

## Main results to check

Notebook 2 should reproduce the main quantitative results used in the paper, including:

* Jaccard Legacy to Sigma: 0.6275
* Control turnover: 0.8129
* VADER: H = 6.9177, p = 0.0315
* Temporal generalisation test F1: 0.772
* F1 drop: 22 percentage points, 95 per cent CI [18.6, 25.7]

Notebook 3 should reproduce the trope validation results:

* classifier gold standard: 114 posts
* development set: 39 posts
* held-out test set: 75 posts
* separate qualitative case set: 12 posts
* Prescription: F1 = 0.829, Cohen's kappa = 0.765
* Rejection: F1 = 0.692, Cohen's kappa = 0.531

Crisis and Hate are not used as the main quantitative findings because the validation scores were too weak.

## Notes on interpretation

A few limits are important:

* Jaccard turnover alone does not prove manosphere-specific language change.
* VADER is useful for tone, but it cannot detect coded ideology.
* SBERT measures semantic proximity, not endorsement.
* Temporal generalisation is a transfer test, not a production moderation model.
* The LLM classifier is used cautiously, with some categories excluded.
