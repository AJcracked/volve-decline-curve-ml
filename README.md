# ML vs. Traditional Decline Curve Analysis for Well Production Forecasting

Comparing a physics-based petroleum engineering method (Arps decline curves) against machine learning models for forecasting oil production decline, using real field data from the Volve Field (Equinor, North Sea).

## Background

Traditional decline curve analysis (Arps, 1945) has been the industry-standard method for forecasting oil well production for decades. This project asks a straightforward question: **does modern machine learning actually outperform this 80-year-old method, or does the traditional approach still hold up?**

As a petroleum engineer transitioning into data science, this project is deliberately built to combine both worlds — using domain knowledge to interpret results a generic data science approach would miss.

## Dataset

* **Source:** [Volve Field production data](https://www.equinor.com/energy/volve-data-sharing) (Equinor, publicly released North Sea field dataset)
* **Well analyzed:** F-14H (a production well with a full, continuous history from 2008–2016)
* **Granularity:** Daily production data, aggregated to monthly averages for decline curve fitting

## Methodology

1. **Data cleaning:** Filtered to producer-well rows, removed shut-in days (zero-production days from well maintenance/operations), aggregated to monthly averages
2. **Traditional approach:** Fitted three Arps decline models — Exponential, Harmonic, and Hyperbolic — and selected the best fit based on R²/RMSE rather than assumption
3. **ML approach:** Built a chronological train/test split (80/20) — critical for time-series data, since a random split would leak future information into training. Tested Random Forest with two feature sets: raw time, and lagged production values (previous 1-3 months)
4. **Comparison:** Evaluated all models on the same held-out future period (2015-2016) using R² and RMSE

## Key Findings

|Model|R²|RMSE|
|-|-|-|
|**Arps Exponential**|**-2.976**|**117.4**|
|Random Forest (time only)|-6.046|156.3|
|Random Forest (lag features)|-6.423|160.4|

**Arps Exponential decline outperformed both ML approaches.**

* The hyperbolic fit converged on a decline exponent b ≈ 0.0001 (effectively exponential), and harmonic decline visibly underperformed — the well declines faster than a harmonic model assumes.
* Random Forest (regardless of feature set) produced a near-flat forecast because **tree-based models cannot extrapolate beyond the range of values seen during training** — they predict by matching new inputs to learned "buckets," and have no mechanism for continuing a trend into unseen territory. Arps, as a continuous mathematical equation, has no such ceiling.
* Even Arps overshot the true decline in 2015-2016 — the well fell faster than its 2008-2014 trend predicted, plausibly due to accelerating water breakthrough as the field's water injection support pushed water into the producing well over time.

**Takeaway:** for this well, with this data volume, tree-based ML models are structurally unsuited to this forecasting task — not due to poor tuning, but due to a fundamental extrapolation limitation. This doesn't mean ML is inferior to Arps universally; it means model architecture matters enormously, and equation-based or sequence-aware models (ARIMA, LSTM) would be a more appropriate ML comparison for this class of problem.

## Limitations \& Future Work

* Random Forest was tested as a representative "off-the-shelf" ML model; results reflect a limitation shared by tree-based models generally, not Random Forest specifically
* Multivariate features (downhole pressure, choke size, water cut) were explored informally and showed water cut as a strong secondary driver of decline, but weren't fully re-validated in this notebook — a natural next step
* Only one well (F-14H) was analyzed; extending to F-12H and multi-well training is a planned v2
* LSTM/sequence models, purpose-built for extrapolation, are a promising next test

## Tools Used

Python, pandas, NumPy, matplotlib, seaborn, scikit-learn, SciPy (`curve\\\_fit` for Arps fitting)

## Files

* `volve\\\_decline\\\_curve\\\_analysis.ipynb` — full analysis notebook



