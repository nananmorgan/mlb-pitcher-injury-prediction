# MLB Pitcher Injury Risk Prediction

## Overview
Pitcher injuries are among the most disruptive and costly events in baseball. The average annual salary for top pitchers in the AL is $42M, making injury prevention a high-stakes business problem. This project uses publicly available Statcast pitching data and MLB injury records to identify workload and biomechanical features that correlate with a pitcher being placed on the Injured List (IL), with the goal of providing actionable early warning signals for team coaching and medical staff.

## Key Results
- Best model (Random Forest with starter/reliever classification) achieved **AUC-ROC of 0.6582** on the test set, with a cross-validated AUC of 0.6578; consistent scores indicating the model is stable and not overfitting
- Logistic Regression baseline: AUC 0.6132 | Random Forest v1: AUC 0.6541 | Random Forest v2 (with starter/reliever): AUC 0.6582
- **Most actionable finding**: Injured pitchers showed a mean fastball velocity decline of **-0.11 mph** in the 30 days before IL placement, while healthy pitchers showed an increase of +0.02 mph (p < 0.001)
- Injured pitchers appeared in fewer games (5.9 vs 6.9, p < 0.001) and threw more pitches per appearance (44.7 vs 42.4, p < 0.05) during the pre-injury window
- Top predictors by feature importance: total pitches, games pitched, batters faced, and velocity metrics

## Data Sources
- **Statcast data** (2021–2025 seasons) fetched via the `pybaseball` library: ~3.1 million pitch records aggregated into 87,000 pitcher-game appearances
- **Injury List (IL) transactions** (2022–2025) pulled from the MLB Stats API: 8,149 pitcher-related IL transactions
- No pre-built dataset file included: data is collected programmatically via the data collection notebook

## Methods
- **Data preparation**: Filtered to regular season only; aggregated pitch-level data into per-game summaries (total pitches, average velocity, pitch mix percentages, strike percentage)
- **Feature engineering**: Built 30-day pre-injury lookback windows for each IL placement event, creating a single feature vector of 16 features per pitcher-injury event. Control group created from healthy pitchers and healthy windows from pitchers who later went on IL
- **Starter/reliever classification**: Derived from actual game-level data (pitchers who started 50%+ of their appearances classified as starters)
- **Modeling**: Logistic Regression (interpretable baseline), Random Forest v1 (15 features), Random Forest v2 (16 features with starter/reliever). All models used 80/20 stratified train-test split with 5-fold stratified cross-validation
- **Evaluation**: AUC-ROC as the primary metric, plus precision, recall, and confusion matrices

## Features (16 total)
Total pitches in 30-day window, games pitched, average pitches per game, batters faced, average fastball velocity, average velocity across all pitch types, maximum fastball velocity, velocity trend (first half vs second half of window), average spin rate, average horizontal break, average vertical break, pitch type diversity, fastball usage percentage, strike percentage, average days between appearances, starter/reliever indicator

## Repository Contents
```
mlb-pitcher-injury-prediction/
├── pitcher_injury_data_collection.ipynb  # Data collection and feature engineering
├── pitcher_injury_prediction.ipynb       # EDA, modeling, and evaluation
├── presentation.pdf                      # Project presentation slides
├── project_writeup.docx                  # Full project write-up and findings
├── project_Q&A.docx                      # Project Q&A document
└── README.md
```

## How to Run
1. Install dependencies: `pip install pandas numpy scikit-learn pybaseball matplotlib seaborn mlb-statsapi`
2. Run `pitcher_injury_data_collection.ipynb` first to collect Statcast and IL data and engineer features
3. Run `pitcher_injury_prediction.ipynb` to perform EDA, train models, and evaluate results

## Tools
Python, scikit-learn, pybaseball, MLB Stats API, pandas, numpy, matplotlib, seaborn

## Limitations
The moderate AUC of ~0.66 reflects the inherent difficulty of injury prediction from workload data alone. Factors such as training intensity, medical history, sleep, and nutrition are not captured in publicly available Statcast data. Additionally, 61% of IL placements were excluded because pitchers lacked sufficient Statcast data in the 30-day pre-injury window. Model results should be interpreted as one input in a broader, multi-factor risk assessment alongside medical evaluation and coaching staff input.
