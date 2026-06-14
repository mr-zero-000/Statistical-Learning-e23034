
---

```markdown
# Statistical Learning — Assignment 6
Empirical implementations and technical analyses for Gaussian Process Regression (GPR) and Multi-Variable Linear Regression applied to thermal dynamics and building energy demands.

---

## 📂 Repository File Breakdown

This directory contains the computational workflows and analytical summaries across the following core files:

* **`Analyse.ipynb`**: Preliminary data loading pipelines via `kagglehub`, exploratory data analysis (EDA), target cross-correlation mapping, and structural feature significance modeling.
* **`Answer Script.ipynb`**: Complete optimization and verification pipeline, producing final algorithmic diagnostics, parameter weights, and performance metrics.
* **`Summerized_Version.ipynb`**: A consolidated executive summary notebook streamlining the code down to foundational execution blocks, cross-validation metrics, and core diagnostic plots.

---

## 🛠️ Requirements & Quick Start

To set up the environment and run these notebooks locally, ensure you have your Kaggle API credentials configured (if required by `kagglehub`) and install the pinned dependencies:

```bash
pip install kagglehub numpy pandas matplotlib seaborn scikit-learn scipy

```

Open and execute any of the notebooks via your preferred workspace:

```bash
jupyter notebook "Answer Script.ipynb"

```

---

## 📝 Problem Formulations & Key Findings

---

### Task 1: Gaussian Process Regression (ENB2012 Dataset)

#### 🔹 Objective & Unified Framework

The dataset maps 8 structural features ($X_1$ to $X_8$) to independent building performance dynamics: Heating Load ($Y_1$) and Cooling Load ($Y_2$).

Rather than running isolated pathways, their tight cross-correlation ($r = 0.86$) justifies grouping them into a single, comprehensive joint target metric:


$$Y_{\text{joint}} = \frac{Y_1 + Y_2}{2}$$

We model this response using non-parametric **Gaussian Process Regression (GPR)** matched with an isotropic Matérn $\nu = 2.5$ covariance function built with Automatic Relevance Determination (ARD):


$$k(\mathbf{x}, \mathbf{x}') = \sigma_f^2 \left(1 + \frac{\sqrt{5}d}{\ell} + \frac{5d^2}{3\ell^2}\right) \exp\left(-\frac{\sqrt{5}d}{\ell}\right) + \sigma_n^2 \delta_{ij}$$


where feature-relevance distances are weighted by optimized feature length-scales:


$$d = \sqrt{\sum_{m=1}^{8} \frac{(x_m - x_m')^2}{\ell_m^2}}$$

#### 🔹 Analytical Conclusions

* **Unified Modeling Viability:** The unified parameter model maps building behavior efficiently, hitting an exceptional $R^2$ score of **$0.9829$** and a low RMSE of **$1.214 \text{ kWh/m}^2$** on unseen data.
* **Feature Sensitivity:** The ARD optimized inverse length-scales ($\frac{1}{\ell_m^2}$) show that **Overall Height ($X_5$)** and **Relative Compactness ($X_1$)** act as the primary structural drivers of thermal consumption. Physical properties like **Orientation ($X_6$)** have near-zero relevance metrics, proving they can be safely removed from early-stage structural optimizations.

---

### Task 2: Linear Regression (Green Building Dataset)

#### 🔹 Feature Selection & Parameter Justification

To build a highly generalizable model without incorporating environmental noise, parameters are isolated using an absolute Pearson correlation threshold filter ($|r| > 0.05$) against `predicted_energy_demand`.

Out of 12 candidate parameters, **4 features** satisfy the selection criteria:

1. **`hvac_energy` ($r = 0.887$)** – Direct active mechanical load tracking.
2. **`temperature` ($r = 0.354$)** – Primary exterior thermodynamic thermal driver.
3. **`insulation_thickness` ($r = -0.222$)** – Direct passive thermal resistance parameter.
4. **`solar_radiation` ($r = 0.089$)** – Secondary radiative thermal gain input.

Irrelevant tracking parameters like `co2_level`, `wind_speed`, and `light_level` drop completely below the filter boundary and are excluded.

#### 🔹 Operational Fit & Performance

The resulting Multi-Variable Ordinary Least Squares (OLS) linear equation takes the following structure:


$$\hat{y} = \beta_0 + \beta_1(\text{hvac\_energy}) + \beta_2(\text{temperature}) + \beta_3(\text{solar\_radiation}) + \beta_4(\text{insulation\_thickness})$$

* **Hold-Out Verification Score:** Achieving an $R^2$ of **$0.9951$** shows an incredible linear fit when bounded by active energy demands and structural envelope parameters.
* **Model Stability:** 5-Fold Cross-Validation verifies structural stability with a tight mean distribution score of **$0.9953 \pm 0.0003$**.
* **Standardized Weight Profile:** Evaluating the optimized coefficients shows that **`hvac_energy`** exerts the dominant positive impact on scaling energy demands, while **`insulation_thickness`** behaves as an inverse structural driver, effectively cutting net thermal loads down as thickness expands.

```

```
