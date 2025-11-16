## Repo context (quick)

- This is a Kaggle-style House Prices competition workspace. Key files:
  - `train.csv`, `test.csv`, `sample_submission.csv` — raw competition data.
  - `data_description.txt` — column definitions (useful for feature interpretation).
  - `datamining-houseprice.ipynb` — main exploratory notebook and modeling pipeline.

## What an AI coding agent should know first

- The notebook uses the Kaggle input path `/kaggle/input/house-prices-advanced-regression-techniques/` to read CSVs. When running locally, prefer reading `./train.csv` and `./test.csv` from repo root or update paths.
- Common libraries imported in the notebook: `pandas`, `numpy`, `matplotlib`, `seaborn`, `missingno`, `scikit-learn` (model selection, preprocessing, metrics), and `lightgbm` for modeling. Use these as the minimal environment when running or refactoring the notebook into scripts.
- The target column is `SalePrice`. The notebook applies log transforms (expm1/ log1p) in places — preserve that behavior when converting or reusing model code.

## Practical instructions for making edits or adding code

1. Start from `datamining-houseprice.ipynb` — it contains the end-to-end flow (EDA → preprocessing → model). Use it as the canonical reference for expected inputs/outputs.
2. When converting notebook cells to modules/scripts:
   - Keep data-loading logic robust: attempt `/kaggle/input/...` then fallback to local `./train.csv`/`./test.csv` paths.
   - Preserve seed/validation splits used in the notebook (look for `train_test_split` calls) to reproduce scores.
   - Preserve LightGBM training hyperparameters and the target transform (log/expm1) — these affect predictions and submission format.
3. Do not modify raw CSV files in-place. Write derived artifacts (features, models, predictions) under a new `artifacts/` or `outputs/` folder.

## Project-specific patterns to follow

- Notebook-first workflow: most logic lives in the notebook; expect experiments to be iterative and exploratory rather than production-ready modules.
- Quick visual/EDA helpers are used (`missingno`, seaborn). Keep plotting and diagnostics out of production scripts; move plotting to an `analysis/` or `notebooks/` folder.
- When adding tests or scripts, provide a small data smoke test that loads a few rows from `train.csv` (don't include full dataset in tests) to validate I/O and transforms.

## Debugging & running locally

- To run the notebook locally, ensure a Python environment contains: pandas, numpy, matplotlib, seaborn, missingno, scikit-learn, and lightgbm. If you create a `requirements.txt`, pin these packages.
- Quick tips:
  - Notebook expects `/kaggle/input/...` — change to `./train.csv` when running outside Kaggle, or set an environment variable `KAGGLE_INPUT` and resolve path in loader.
  - The notebook uses `expm1`/`log1p` transforms. When computing metrics, ensure you inverse-transform predictions before writing `sample_submission.csv`.

## Files to inspect for any change-impact

- `datamining-houseprice.ipynb` — primary flow and examples to mirror.
- `data_description.txt` — feature meanings; check here before changing feature engineering decisions.
- `train.csv`, `test.csv`, `sample_submission.csv` — data and expected submission format.

## Example snippets (from repo) to preserve behavior

- Data load in notebook (preserve fallback behavior):

  try:
      df_train = pd.read_csv('/kaggle/input/house-prices-advanced-regression-techniques/train.csv')
      df_test = pd.read_csv('/kaggle/input/house-prices-advanced-regression-techniques/test.csv')
  except FileNotFoundError:
      df_train = pd.read_csv('./train.csv')
      df_test = pd.read_csv('./test.csv')

- Modeling: LightGBM is used for the final model. Keep the same metric transforms when reproducing results (log target, expm1 back-transform).

## When to ask the repo owner

- If you need to introduce a project-level structure (e.g., `src/`, `requirements.txt`, CI), ask before changing original files — this repo is primarily a single-file notebook project and changes may shift intent.

---
If any section is unclear or you want the file expanded to include a suggested `requirements.txt` or a starter `scripts/` layout, tell me which option you prefer and I will update this file.
