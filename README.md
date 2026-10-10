# NBA2k25-Height-Econometrics

# Econometric Analysis of Height vs. Overall Rating in NBA 2K25

## 📌 Executive Summary
Does physical height inherently dictate a player's rating in video game logistics, or are overall ratings purely driven by skill attributes? This econometric research project investigates the relationship between **height (`height_cm`)** and **overall player rating (`overall`)** across all active NBA players in *NBA 2K25* ($N = 457$).

Through iterative OLS (Ordinary Least Squares) regression modeling, this study tests whether initial positive correlations between height and overall rating are statistically robust or driven by **Omitted Variable Bias (OVB)**. The final model demonstrates that once specific skill ratings (shooting, defense, playmaking, and athleticism) are controlled for, height has **no statistically significant effect** on overall rating—confirming that the game’s underlying rating algorithm heavily prioritizes skill attributes over raw physical stature.

---

## 🛠️ Tech Stack & Key Libraries
* **Language:** R
* **Core Packages:** `tidyverse` (`dplyr`, `ggplot2`, `tidyr`, `readr`), `stargazer`, `knitr`, `kableExtra`
* **Econometric & Diagnostic Testing:** `AER`, `car`, `lmtest`, `zoo`, `sandwich`

---

## 📊 Dataset Overview
The analysis utilizes data scraped for all active NBA players in *NBA 2K25* (`current_nba_players.csv`). 

### Key Variables Analyzed:
* **Dependent Variable ($Y$):** `overall` (Player Overall Rating, range: 67–98)
* **Primary Explanatory Variable ($X_1$):** `height_cm` (Height in centimeters, range: 173–226 cm)
* **Control Variables:** `weight_kg`
* **Skill Attributes:** `three_point_shot`, `driving_dunk`, `standing_dunk`, `speed`, `ball_handle`, `pass_accuracy`, `interior_defense`, `perimeter_defense`, `steal`, `block`, `offensive_rebound`, `defensive_rebound`

---

## 📈 Econometric Methodology & Model Specifications

Three separate regression models were specified to evaluate the stability of the height coefficient:

1. **Model 1 (Simple Linear Regression):**
   $$\text{overall} = \beta_0 + \beta_1(\text{height}) + u$$
2. **Model 2 (Multiple Regression with Physical Controls):**
   $$\text{overall} = \beta_0 + \beta_1(\text{height}) + \beta_2(\text{weight}) + u$$
3. **Model 3 (Full Specification with Skill Attributes):**
   $$\text{overall} = \beta_0 + \beta_1(\text{height}) + \beta_2(\text{weight}) + \sum \beta_k(\text{Skill Attributes}_k) + u$$

---

## 🔬 Regression Results & Key Findings

| Variable | Model 1 (Simple) | Model 2 (Controls) | Model 3 (Full Specification) |
| :--- | :---: | :---: | :---: |
| **`height_cm`** | 0.055 (p = 0.111) | -0.020 (p = 0.676) | **-0.024 (p = 0.643)** |
| **`weight_kg`** | — | 0.091** (p = 0.015) | **0.064** (p = 0.041) |
| **`pass_accuracy`** | — | — | **0.198*** (p < 0.001) |
| **`defensive_rebound`**| — | — | **0.136*** (p < 0.001) |
| **`three_point_shot`** | — | — | **0.105*** (p < 0.001) |
| **`interior_defense`** | — | — | **0.097*** (p < 0.001) |
| **Observations** | 457 | 456 | 443 |
| **$R^2$** | **0.006** | **0.020** | **0.535** |
| **Adjusted $R^2$** | 0.003 | 0.016 | **0.520** |

*Significance levels: *p<0.1; **p<0.05; ***p<0.01*

### Key Insights:
* **Height is Not a Statistically Significant Predictor:** In Model 3, the coefficient for `height_cm` is **-0.024** with a p-value of **0.643**. Controlling for skills reveals that height alone does not boost overall rating.
* **Model Fit Explanatory Power:** Moving from Model 1 ($R^2 = 0.006$) to Model 3 ($R^2 = 0.535$) increased explanatory power by over **53%**, proving that skill ratings account for the majority of variation in overall player ratings.
* **Top Drivers of Overall Rating:** `pass_accuracy` ($\beta = 0.198$), `defensive_rebound` ($\beta = 0.136$), `three_point_shot` ($\beta = 0.105$), and `interior_defense` ($\beta = 0.097$) emerged as the strongest statistically significant positive contributors.

---

## 🧪 Classical Linear Model (CLM) Diagnostics

To validate statistical inference, the six Classical Linear Model (CLM) assumptions were thoroughly tested on Model 3:

* **MLR.1 (Linear in Parameters):** Satisfied. All variables enter the OLS equation linearly without non-linear transformations.
* **MLR.2 (Random Sampling):** Evaluated. Data includes the entire population of active NBA players represented in *NBA 2K25*, validating internal consistency for the 2024–2025 season.
* **MLR.3 (No Perfect Multicollinearity):** Satisfied. While high multicollinearity exists naturally between height, weight, and position-based attributes (e.g., dunking and interior defense), standard errors remain stable without exact linear dependency.
* **MLR.4 (Exogeneity / Omitted Variable Bias):** Models 1 and 2 suffered heavily from omitted variable bias. Model 3 effectively mitigates OVB by controlling for key offensive and defensive skill metrics.
* **MLR.5 (Homoskedasticity):** Tested via the **Breusch-Pagan Test** (`bptest(m3)`):
  * **$\text{BP} = 17.56$, $\text{df} = 14$, $\text{p-value} = 0.2276$**
  * *Result:* Failed to reject the null hypothesis of homoskedasticity ($p > 0.05$). Residual variance is constant, confirming OLS standard errors are appropriate and reliable.
* **MLR.6 (Normality of Residuals):** Plotting residual distributions against a normal curve showed a standard bell-shaped curve centered near zero. By the **Central Limit Theorem ($N = 443$)**, OLS test statistics ($t$-tests and $F$-tests) remain valid.

---

## 📁 Repository Structure
```text
.
├── data/
│   └── current_nba_players.csv         # NBA 2K25 Player Attribute Dataset
├── scripts/
│   └── ECON_425_Research_Project.Rmd   # Complete R Markdown script
├── outputs/
│   ├── models1.txt                     # Stargazer regression outputs
│   └── residual_histogram.png          # MLR.6 diagnostic residual plot
├── ECON_425_Research_Project.pdf       # Original Academic Research Paper
└── README.md                           # Project documentation
