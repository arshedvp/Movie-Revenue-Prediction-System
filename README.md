# Movie Revenue Prediction System

A Streamlit app and training pipeline that predicts box office revenue from movie features and provides marketing strategy insights. Trained on the TMDB 5000 movies dataset.

## Features
- Predicts estimated box office `revenue` from inputs like budget, runtime, release timing, genres, popularity, votes.
- Computes `ROI` tiers (Excellent/Good/Moderate/Loss) for quick profitability guidance.
- Clusters movies into 4 types using `KMeans` to tailor marketing strategy suggestions.
- Contextual `marketing tips` based on release month (summer, holiday, awards season).
- Shows advanced insights on budget efficiency, runtime impact, and release timing.
- Clean Streamlit UI with sidebar guide and raw input preview.

## Project Structure
```
Movie_prediction/
├─ app.py                      # Streamlit app for predictions and insights
├─ preprocess_and_train.py     # Data prep, feature engineering, model training, saving artifacts
├─ requirements.txt            # Python dependencies
├─ tmdb_5000_movies.csv        # Main dataset
├─ tmdb_5000_credits.csv       # (Unused presently; kept for future work)
└─ models/                     # Saved artifacts after training
   ├─ revenue_model.pkl
   ├─ scaler.pkl
   ├─ clustering_model.pkl
   └─ feature_list.pkl
```

## How It Works
1. `preprocess_and_train.py`
   - Loads `tmdb_5000_movies.csv`.
   - Filters rows with positive `budget` and `revenue`.
   - Feature engineering:
     - `release_month`, `release_year` from `release_date`.
     - `num_genres` from parsed `genres` JSON.
     - `production_companies_count` from parsed `production_companies` JSON.
     - `keywords_count` from parsed `keywords` JSON.
     - Uses original columns: `budget`, `runtime`, `popularity`, `vote_average`, `vote_count`.
   - Scales features with `StandardScaler`.
   - Trains `XGBRegressor` to predict `revenue`.
   - Trains `KMeans` (n_clusters=4) on scaled features for movie-type clustering.
   - Saves artifacts in `models/` and persists `feature_list` to keep input column order aligned with the app.

2. `app.py`
   - Loads the trained artifacts from `models/`.
   - Collects movie attributes via Streamlit inputs.
   - Builds an input row honoring `feature_list` ordering, fills any missing columns with 0.
   - Scales input and predicts `revenue`; computes `ROI = (revenue - budget)/budget`.
   - Predicts cluster to show movie type and strategy suggestion.
   - Displays marketing tips based on release season, plus advanced insights.

## Prerequisites
- Python 3.10+ recommended
- Windows PowerShell (commands below use PowerShell syntax)

## Setup
1. Create and activate a virtual environment:
```powershell
# From the project root (Movie_prediction)
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```
2. Install dependencies:
```powershell
pip install -r requirements.txt
```

## Train the Models
Run the training script to create the `models/` artifacts.
```powershell
python preprocess_and_train.py
```
Expected output includes dataset loading info, train/test R², and a confirmation that models were saved to `models/`.

## Run the App
Start the Streamlit UI:
```powershell
streamlit run app.py
```
Then open the local URL printed by Streamlit (e.g., `http://localhost:8501`).

### Using the App
- Fill in budget, runtime, release date, genres count, production companies count, keywords count, expected popularity, votes.
- Click `Predict Revenue`.
- Review:
  - Predicted revenue (formatted with thousands separators).
  - ROI classification and percentage.
  - Movie type (cluster) with strategy guidance.
  - Season-aware marketing tip.
  - Optional advanced insights and raw input data.

## Data Notes
- Source: TMDB 5000 dataset.
- Current pipeline uses `tmdb_5000_movies.csv`. The `tmdb_5000_credits.csv` is included but not used yet.
- Several columns contain JSON-like strings (e.g., `genres`, `production_companies`, `keywords`), which are parsed to counts.
- Rows with non-positive `budget` or `revenue` are removed to focus on informative samples.

## Model Details
- Regressor: `XGBRegressor (n_estimators=100, random_state=42)`.
- Scaling: `StandardScaler` fit on training features.
- Clustering: `KMeans (n_clusters=4, random_state=42)` trained on scaled features to produce movie-type clusters.
- Features used:
  - `budget`, `runtime`, `release_month`, `release_year`,
  - `num_genres`, `production_companies_count`, `keywords_count`,
  - `popularity`, `vote_average`, `vote_count`.

## Troubleshooting
- "Models not found": Run `python preprocess_and_train.py` to generate the `models/` files.
- Import or build errors:
  - Ensure the venv is active: `.\.venv\Scripts\Activate.ps1`.
  - Reinstall requirements: `pip install -r requirements.txt`.
- Streamlit not recognized: `pip install streamlit` or re-run requirements install.
- Dataset not found: Ensure `tmdb_5000_movies.csv` exists in project root and has the expected columns.
- Windows execution policy preventing venv activation: run PowerShell as Administrator and set `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`.

## Roadmap / Ideas
- Use `tmdb_5000_credits.csv` to add cast/crew features.
- Add genre-specific models and calibration.
- Explainable AI (SHAP) for feature attributions per prediction.
- Export predictions as reports.

## License
This repository is for educational/demonstration purposes. Add a license if you plan to distribute.

## Acknowledgments
- TMDB 5000 Movies dataset.
- Streamlit, scikit-learn, XGBoost.
