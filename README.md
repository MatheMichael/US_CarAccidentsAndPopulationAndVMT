<p align="center">
  <img src="carl-hunley-jr-ytiIHYBHl1o-unsplash.jpg" alt="Urban traffic image" width="400">
</p>

<p align="center">
  <sub>Photo by <a href="https://unsplash.com/@workbycarl?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Carl Hunley Jr</a> on <a href="https://unsplash.com/photos/a-city-street-with-tall-buildings-ytiIHYBHl1o?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a></sub>
</p>

# US Car Accidents: A State-Level Regression Analysis

This project investigates how **state-level traffic accident counts** in the U.S. can be explained by interpretable, real-world predictors: **population size** and **vehicle miles traveled (VMT)** patterns. While the underlying dataset is widely used on Kaggle, this regression-based analysis — combining public demographic and infrastructure data — fills a gap in prior work.

---

## Dataset Overview

**Primary dataset:** [US Accidents (Kaggle)](https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents)  
- ~7.7 million accident records (2016–2023), covering 49 states  
- Sourced from traffic APIs, DOTs, law enforcement, sensors, and cameras

**Additional datasets:**
-  [2020 U.S. Census Population Estimates](https://www.census.gov/data/tables/time-series/demo/popest/2020s-national-total.html)
-  [2022 FHWA Vehicle Miles Traveled (VMT)](https://www.fhwa.dot.gov/policyinformation/statistics/2022/vm2.cfm)

**Acknowledgements:**  
> Moosavi, S., et al., “A Countrywide Traffic Accident Dataset.” 2019  
> Moosavi, S., et al., "Accident Risk Prediction..." SIGSPATIAL, 2019

---

## Motivation and Baseline Insight

Many traffic safety models rely on detailed exposure data such as vehicle miles traveled (VMT), road infrastructure metrics, or socioeconomic indicators. While such variables provide fine-grained insight, they are often harder to collect, interpret, or apply quickly in early-stage analysis.

This project begins with a deliberately simple and interpretable question:

> **Can population size alone explain variability in traffic accident counts across U.S. states?**

The answer, surprisingly, is **yes — to a significant degree**.  
A simple linear log-log regression using only state-level population data yields an adjusted R² of over **0.80**. The resulting model is: $$A(P)\approx e^{-10.87}\cdot P^{1.41}\cdot e^\epsilon,$$
where $\epsilon\sim\text{N}(-0.063,0.689^2).$
<p align="center">
  <img src="A(P) model.jpg" alt="Accident count using population model" width="750">
</p>

<p align="center">

This baseline result is:
- **Interpretable:** Population is a universally understood, policy-relevant variable.
- **Actionable:** It enables rough risk estimation even without complex exposure metrics.
- **Foundational:** It sets a high-performing benchmark to test the value of additional predictors.

### Why Population Is a Reasonable Standalone Predictor

Many predictors commonly used in traffic modeling — such as number of vehicles, number of licensed drivers, economic activity, and total vehicle miles traveled — tend to scale closely with **population size**. Because of these high correlations, **population can serve as a proxy** for a wide range of state-level characteristics that influence accident frequency.

By focusing first on population as a standalone predictor, this analysis tests whether a **simple, accessible metric** can explain accident variability effectively — without introducing unnecessary redundancy or collinearity. The strong initial model performance (adjusted R² > 0.80) supports this assumption and demonstrates the utility of population as a baseline explanatory variable.

From this baseline, the analysis then builds toward a stronger model by incorporating a second feature: the **ratio of rural to urban vehicle miles traveled (VMT)**, capturing geographic driving characteristics that further refine the explanation of accident patterns across states.

---

## Objective

To explore whether **accident count variability across U.S. states** can be explained using:
- Total state population
- A custom metric representing rural vs. urban driving patterns

This project emphasizes **interpretable regression**, data quality, and insight-driven feature engineering.

---

## Model Evolution

| Step | Predictors | Adjusted R² | Notes |
|------|------------|-------------|-------|
| 1️⃣ | `Population` | 0.780 | Baseline OLS model |
| 2️⃣ | `Population` (after outlier removal) | 0.811 | Improved residuals; Shapiro-Wilk normality confirmed |
| 3️⃣ | `log(Population)`, `VMT_rural / VMT_urban` | **0.85** | Final model with feature engineering |

---
The final model is:

$$
A(P, V) = e^{-6.64} \cdot P^{1.17} \cdot e^{-0.62V} \cdot e^{\epsilon},
$$

where $\epsilon \sim \text{N}(-0.06,\ 0.69^2)$, $A$ is the number of accidents, $P$ is the population size, and $V$ is the rural-to-urban VMT ratio.

It would be interesting to conduct a similar analysis in other regions; I conjecture that a comparable model—with different exponents—would also provide a good fit.

##  Techniques & Insights

###  Data Cleaning
- Removed **duplicate records** from the accidents dataset
- Standardized formats across public datasets
- Handled outliers based on residual analysis and statistical tests

###  Feature Engineering
- Created a custom metric: **`RurPerUrb = VMT_rural / VMT_urban`**
- Captures geographic driving character — states with more rural traffic may differ in accident profiles
- This new feature was **statistically significant** and improved model performance

###  Statistical Modeling
- Used **OLS regression** via `statsmodels`
- Validated assumptions:
  - Checked residual distributions
  - Conducted Shapiro-Wilk normality tests
  - Evaluated model fit using R², AIC/BIC, p-values
- Final model:
  - Adjusted R² = 0.837
  - All predictors significant at `p < 0.001`

###  Data Integration
- Merged three public datasets on state-level identifiers
- Cleaned and reshaped U.S. Census and FHWA data for compatibility

---

##  Key Findings

- **Population alone explains ~81%** of the variance in state-level accident counts
- Adding **rural-to-urban VMT ratio** increases adjusted R² to **~84%**
- States with higher `RurPerUrb` (i.e., more rural driving) tend to experience **fewer total accidents**, controlling for population

---

##  Files in This Repo

- `CarAccPopAndVMT.ipynb` — Complete analysis: cleaning, feature design, modeling, and interpretation
- `README.md` — Project summary and documentation
- Visual figures (`.jpg`, `.png`) stored in the root directory



## Tools Used

- Python, Jupyter Notebook  
- pandas, statsmodels, matplotlib, seaborn  
- Public datasets (Kaggle, U.S. Census, FHWA)

---

##  Contact & Purpose

This project was developed as part of my transition into data science and quantitative roles. It reflects:

- End-to-end analysis with real-world, imperfect data  
- Feature engineering based on domain insights  
- Interpretable modeling with rigorous validation  
- Clear, reproducible communication of results

I’m open to feedback, collaboration, or opportunities — feel free to connect via [LinkedIn](https://www.linkedin.com/in/michael-bersudsky).
