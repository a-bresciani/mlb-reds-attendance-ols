# Cincinnati Reds: What Actually Brings Fans to the Stadium?

[![Open in nbviewer](https://img.shields.io/badge/nbviewer-render-orange)](https://nbviewer.org/github/<a-bresciani>/mlb-reds-attendance-ols/blob/main/MLB_Attendance_Final_v3_fixed.ipynb)
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<a-bresciani>/mlb-reds-attendance-ols/blob/main/MLB_Attendance_Final_v3_fixed.ipynb)
[![Python](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Author:** a-bresciani
**Project:** Multiple Linear Regression (OLS) on MLB game-day attendance

A reproducible regression study on **80 home games** of the Cincinnati Reds for one MLB season. The goal is to identify which observable factors (opponent quality, weather, schedule, promotions) actually drive paid attendance, and to test the model against the four classical OLS assumptions.

---

## 1. Headline Result

The model selected by best-subset search on Adjusted R-squared, with all coefficients significant at the 5% level, is:

```
Attendance = beta_0 + beta_1 * OpWin% + beta_2 * Weekend + beta_3 * Promotion + epsilon
```

| Metric | Value |
|---|---|
| Variables | OpWin% + Weekend + Promotion |
| Adjusted R-squared | 0.339 |
| F-statistic p-value | 1.47e-07 |
| All coefficients p < 0.05 | Yes |
| Max VIF | 1.26 (no multicollinearity) |
| Square-root transformation gain | +0.004 (negligible) |

Coefficients (95% CI):

| Variable | Coefficient | Interpretation |
|---|---|---|
| OpWin% | +33.2 | Each 1-unit increase in opponent win rate (x1000) adds about 33 fans |
| Weekend | +5,152 | Weekend games attract on average ~5,150 more fans than weekday games |
| Promotion | +3,244 | Promotion days add on average ~3,240 fans |

---

## 2. Repository Structure

```
.
├── MLB_Attendance_Final_v3_fixed.ipynb   # main notebook (run this)
├── G4_excel_dataset.csv                  # input data, 80 home games
├── requirements.txt                      # pinned Python dependencies
├── README.md                             # this file
└── .gitignore
```

The notebook expects the CSV in the same directory. The loader resolves both `G4_excel_dataset.csv` and `G4 excel dataset.csv` automatically.

---

## 3. Notebook Sections

1. Setup and data loading (robust CSV resolver, type coercion, NA drop)
2. Exploratory analysis: distribution, correlation matrix, categorical box plots
3. Step 1: Variance Inflation Factor check and single-variable OLS regressions
4. Step 2: Best subset search across all 31 non-empty subsets, ranked by Adjusted R-squared
5. Step 3: Full interpretation of the best model
6. Step 4: Residual analysis (Residuals vs Fitted, Q-Q, Scale-Location, histogram) and formal tests (Jarque-Bera, Durbin-Watson, Breusch-Pagan)
7. Step 5: Square-root transformations as a robustness check
8. 2D visualisations (scatter with OLS fit, ranked Adj R-squared comparison)
9. 3D interactive Plotly visualisations (regression planes, KDE density surface)
10. Conclusions and business recommendations
11. Appendix A: extended exploratory analyses (3D correlation topology, network graph, KDE waves)
12. Appendix B: earlier draft sections preserved for reference

---

## 4. Methodology Notes

- **Cleaning:** rows with non-numeric values are coerced to NaN and dropped. No imputation, no winsorisation.
- **Scale conventions:** `Win%` and `OpWin%` are stored as integers in the source file, where 543 means a 0.543 win rate. The model coefficients should be read on this same scale.
- **Selection criterion:** Adjusted R-squared, with the additional filter that every coefficient must be significant at the 5% level. AIC and BIC are reported alongside as a cross-check.
- **Multicollinearity:** all VIFs below 1.3, well under the conventional 5.0 threshold.
- **Residual diagnostics:** Breusch-Pagan supports homoscedasticity. Jarque-Bera flags mild non-normality driven by a handful of high-promotion games in the right tail; with n = 80 the OLS estimator remains unbiased and inference is robust by the Central Limit Theorem.

---

## 5. Reproducibility

### Requirements
- Python 3.10 or higher
- The packages pinned in `requirements.txt`
- Jupyter (Lab or Notebook) for interactive execution

### Local setup

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
.\.venv\Scripts\Activate.ps1

pip install --upgrade pip
pip install -r requirements.txt

jupyter lab MLB_Attendance_Final_v3_fixed.ipynb
```

### Run end-to-end without opening Jupyter

```bash
jupyter nbconvert --to notebook --execute MLB_Attendance_Final_v3_fixed.ipynb \
    --output MLB_Attendance_executed.ipynb \
    --ExecutePreprocessor.timeout=180
```

### Determinism
Random seed is fixed at the top of the notebook (`np.random.seed(42)`). KMeans calls in the appendix use `random_state=42, n_init=10`. Output should be bit-identical across runs on the same package versions.

---

## 6. Data Dictionary

| Column | Type | Description |
|---|---|---|
| Team | string | Always "Reds" in this dataset |
| Attendance | int | Paid attendance for the game |
| Temp | int | Game-time temperature in degrees Fahrenheit |
| Win% | int | Reds' season win rate at game time, x1000 (e.g. 543 = 0.543) |
| OpWin% | int | Opponent's season win rate at game time, x1000 |
| Weekend | int (0/1) | 1 if Saturday or Sunday, 0 otherwise |
| Promotion | int (0/1) | 1 if a promotional event was held, 0 otherwise |

n = 80 cleaned observations.

---

