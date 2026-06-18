# The Language Has Changed, But the Signal Remains

## Register Mutation and Machine Blindness in Manosphere Reddit Discourse, 2012–2025

This repository contains the analysis notebooks and supporting files for my M6001 Capstone Academic Output at the London Interdisciplinary School.

The project examines whether manosphere language on Reddit changes over time, whether ideological alignment persists beneath surface register change, and whether those changes make older detection methods less reliable.

## Research question

How has manosphere discourse changed across Reddit eras from 2012 to 2025, and what does this mean for automated detection of misogynistic or manosphere-aligned content?

## Scope and interpretation

These notebooks work with Reddit post titles, semantic similarity scores, temporal generalisation tests, and LLM-assisted trope labels.

The project does not claim to measure individual users' beliefs, offline behaviour, or the whole manosphere ecosystem. Sentence-BERT (SBERT) is used to compare semantic proximity to anchor statements, not to prove endorsement. The LLM classifier is used cautiously to support trope labelling and case selection; categories that did not meet the validation threshold are excluded from the main quantitative findings.

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
    raw/
    processed/
    outputs/
    README.md
```

## Notebook guide

### 1. Data collection and preprocessing

`notebooks/01_data_collection_preprocessing.ipynb`

This notebook builds the project corpus. It loads and parses the source data, applies the inclusion and exclusion choices, labels eras, and exports the CSV files used by Notebook 2.

Expected outputs include:

```text
Master_Manosphere_Dataset.csv
exrpdf.csv
comparison_v3.csv
```

### 2. Quantitative NLP pipeline

`notebooks/02_quantitative_nlp_pipeline.ipynb`

This notebook tests the broad quantitative pattern across eras using:

* Jaccard distance
* term frequency–inverse document frequency (TF-IDF)
* Valence Aware Dictionary and sEntiment Reasoner (VADER)
* Sentence-BERT (SBERT)
* logistic regression
* temporal generalisation testing
* bootstrap confidence intervals

Notebook 2 creates `clean_df.csv`, which Notebook 3 uses.

### 3. Trope classification and false-negative analysis

`notebooks/03_trope_classification_false_negatives.ipynb`

This notebook validates the LLM-assisted trope classification workflow and identifies false-negative cases for qualitative analysis.

It includes prompt development, hand-coded validation, held-out testing, F1 scores, Cohen's kappa, reliability decisions, and case selection.

Notebook 3 can call the Groq API for Llama 3.3 70B classification, but it can also use saved output files where final classifications have already been generated.

## Data files

Place the required CSV files in the `data/` folder before running the notebooks.

Notebook 2 expects:

```text
Master_Manosphere_Dataset.csv
comparison_v3.csv
sigma_anchors.csv
exrpdf.csv
```

Notebook 3 expects:

```text
clean_df.csv
gold_standard_FINAL.csv
gs_test_with_predictions.csv
corpus_with_tropes.csv
```

## API setup for Notebook 3

Notebook 3 uses the Groq API. The API key is not stored in the notebook.

Set `GROQ_API_KEY` in your environment before running API-dependent cells. The notebook reads it using:

```python
os.environ.get("GROQ_API_KEY")
```

Saved classification outputs are included so that the reported analysis can be checked without rerunning all API calls.

## Run order

1. Run Notebook 1.
2. Check that the exported filenames match the Notebook 2 inputs.
3. Run Notebook 2 and check that `clean_df.csv` has been created.
4. Set `GROQ_API_KEY` if running API cells in Notebook 3.
5. Run Notebook 3, or use the saved classification outputs intentionally.
6. Save all notebooks after successful execution.

## Reproducibility note

The notebooks are designed to reproduce the analysis reported in the paper from the saved CSV outputs. Exact reruns of LLM classification may vary if the Groq-hosted model changes, so the repository includes saved classification outputs used in the submitted analysis.

The reported results use:

```text
random_state = 42 where sampling or splitting is required
Llama 3.3 70B Versatile via Groq API
temperature = 0
Sentence-BERT model: all-MiniLM-L6-v2
```

Package versions are listed in `requirements.txt`.

## Main results to check

Notebook 2 should reproduce the main quantitative results used in the paper, including:

```text
Jaccard Legacy to Sigma: 0.6275
Control Legacy to Sigma turnover: 0.8129
VADER: H = 6.9177, p = 0.0315
Temporal generalisation test F1: 0.772
F1 drop: 22 percentage points
95% confidence interval: [18.6, 25.7]
```

Notebook 3 should reproduce the trope validation results:

```text
Classifier gold standard: 114 posts
Development set: 39 posts
Held-out test set: 75 posts
Separate qualitative mechanism-analysis sample: 12 posts
Total coded cases across validation and qualitative phases: 126 posts
```

Main classifier results:

```text
Prescription: F1 = 0.829, Cohen's kappa = 0.765
Rejection: F1 = 0.692, Cohen's kappa = 0.531
```

Crisis and Hate are not used as main quantitative findings because the validation scores were too weak.

## Notes on interpretation

A few limits are important:

* Jaccard turnover alone does not prove manosphere-specific language change.
* TF-IDF identifies distinctive terms, not ideological meaning by itself.
* VADER is useful for affective tone, but it cannot detect coded ideology.
* SBERT measures semantic proximity, not endorsement.
* Temporal generalisation is a transfer test, not a production moderation model.
* Cohen's kappa measures classifier-coder agreement in this project, not intercoder reliability.
* The LLM classifier is used cautiously, with categories excluded where validation was insufficient.
* The qualitative cases are mechanism-analysis cases, not a representative sample of all manosphere discourse.

## Ethics and data handling

The project uses publicly available Reddit post titles. User identifiers are removed before analysis. Example posts used in qualitative or practitioner-facing materials are lightly paraphrased where necessary to reduce searchability and prevent direct attribution.

## AI use

LLMs were used as part of the research workflow for trope classification and prompt testing, as described in the academic output. Generative AI was also used for limited editing support, such as improving wording, checking clarity, and formatting documentation. All analysis decisions, interpretation, validation, and final academic claims remain the author's responsibility.
