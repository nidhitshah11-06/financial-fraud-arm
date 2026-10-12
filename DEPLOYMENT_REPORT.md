# Streamlit Community Cloud Deployment Readiness Report

**Project:** Financial Fraud Pattern Discovery

**Target:** Streamlit Community Cloud

**Status:** Dashboard code and generated outputs are published to GitHub; the Community Cloud app has not been deployed.

## Executive summary

The dashboard entry point is [app/dashboard.py](./app/dashboard.py). The dashboard, requirements, notebook, and generated output files are published on the `main` branch. The large risk-score tables are stored as gzip-compressed CSVs; the dashboard falls back to those files when uncompressed CSVs are unavailable.

The local Streamlit app responded successfully at `http://localhost:8501`. No Community Cloud app was created or published, so cloud startup and interactions still need to be verified after connecting the repository.

## Application overview

- **Framework:** Streamlit
- **Entry point:** `app/dashboard.py`
- **Dependencies:** Streamlit, pandas, and scikit-learn; the dashboard also imports NumPy.
- **Generated outputs:** Association rules, frequent itemsets, fraud-focused rules, risk scores, high-risk transactions, and model comparison data.
- **Data source:** Synthetic PaySim financial transaction data and generated CSV outputs.

The dashboard resolves the `outputs` directory relative to the repository root. This works when the entry point is launched as `streamlit run app/dashboard.py` from the project root and when Community Cloud runs the configured entry point from the repository.

## Changes and checks completed

- Added `scikit-learn` to `requirements.txt` for threshold selection and evaluation metrics.
- Updated the dashboard threshold evaluation to use a stratified calibration split and report metrics separately from the scored CSV.
- Published the app's generated outputs and notebook analysis to GitHub.
- Compressed both large risk-score CSVs. Their decompressed SHA-256 hashes were verified against the original files.
- Confirmed the local Streamlit server responds with HTTP 200.

The local health check and compressed-file verification do not establish that Community Cloud can deploy the app or that every dashboard interaction works there.

## Data availability

The following dashboard data files are published:

- `outputs/fraud_rules.csv` and `outputs/frequent_itemsets.csv` for the core association-rule views.
- `outputs/fraud_focused_rules.csv` and `outputs/high_risk_transactions.csv` for the focused-rule and high-risk transaction sections.
- `outputs/fraud_risk_scores.csv.gz` (about 4.96 MB compressed) and `outputs/model_comparison.csv` for risk scoring and evaluation.

The dashboard checks for each ordinary `.csv` path first and uses the corresponding `.csv.gz` file when the uncompressed file is absent. This preserves the same CSV contents while keeping individual Git files below GitHub's 100 MB limit.

## Deployment blocker and options

The local uncompressed risk-score CSV exceeds GitHub's normal 100 MB per-file limit. The published gzip-compressed copy avoids that limit and retains the complete scored data.

No data was reduced or moved to external storage. If another deployment platform imposes tighter repository or bandwidth limits, confirm its support and limits before choosing an external data store or Git LFS.

## Steps to publish after resolving data delivery

1. Open Streamlit Community Cloud and create an app connected to this repository's `main` branch.
2. Set the main file path to `app/dashboard.py`.
3. Confirm the app is using the repository's `requirements.txt`.
4. Deploy and review startup logs. Verify the sidebar data status, overview metrics, risk-scoring tab, evaluation tab, and high-risk transaction tab.
5. If any future data is externalized, configure credentials through Community Cloud secrets rather than committing credentials to the repository.

## Readiness assessment

- **App entry point identified:** Yes
- **Dependency declaration updated:** Yes
- **Dashboard and generated outputs pushed to GitHub:** Yes
- **Local server responds:** Yes
- **Complete dashboard tested in Community Cloud:** No
- **Generated score data available in the GitHub repository:** Yes, compressed
- **Community Cloud app published:** No

**Conclusion:** The repository contains the dashboard and its generated data, including the full risk-score table in compressed form. The remaining deployment step is to configure and validate the app in Streamlit Community Cloud.
