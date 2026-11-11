# Predicting Equipment Failures: Random Forest vs XGBoost

Predicting whether an industrial asset will have an incident in the next 7 days, using weekly sensor readings and an asset registry.

## Data
- 4,722 weekly sensor records (load, bearing temperature, vibration, pressure, runtime, maintenance history) for 260 assets
- Asset registry: region, vendor, asset type, criticality, commission year

## Data cleaning
- Removed 42 duplicate rows
- Standardised inconsistent category labels (e.g. "Auto", "AUTO", "automatic")
- Fixed mixed date formats (YYYY-MM-DD and DD/MM/YYYY)
- Set impossible values to missing (negative load, runtime above 168 hours a week)
- Joined sensor data to the registry and engineered features (asset age, load group)

## Modelling
- **Asset-level train/test split** (195 / 65 assets) so the same asset never appears in both sets
- Missing values imputed with **training-set medians only**, to avoid data leakage
- **Random forest** (ranger): tuned `mtry` and `min.node.size` using out-of-bag error
- **XGBoost:** grouped 5-fold cross-validation, early stopping and a tuning grid of learning rate, depth and sampling
- Evaluated with ROC AUC, log loss, sensitivity and specificity, because only 13.5% of weeks had incidents

## Results
| Model | Test log loss | Test ROC AUC |
| --- | --- | --- |
| Random forest | 0.409 | 0.654 |
| XGBoost (tuned) | 0.408 | 0.662 |

- XGBoost was slightly better, but the difference is small.
- An untuned XGBoost model clearly overfit after about 6 boosting rounds: training loss kept falling while validation loss rose. Early stopping and regularisation fixed this.
- Exploratory analysis showed incident risk was highest when **load and vibration were both high** (27.7%).

## Tools
R, tidyverse, ranger, xgboost, ggplot2

## Files
- `analysis.Rmd` – code
- `report.html` – full report (download and open in a browser)
