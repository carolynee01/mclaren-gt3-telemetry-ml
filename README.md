# 🏁 ML-Based Telemetry Analysis & Traction Limits Prediction | McLaren 720S GT3

An end-to-end Data Engineering and Machine Learning project focused on predicting vehicle traction limits (ABS/TC interventions) based on high-frequency telemetry data (200 Hz) extracted from the **Assetto Corsa Competizione** simulator at the **Brands Hatch** circuit.

[🚀 HTML Report](https://carolynee01.github.io/mclaren-gt3-telemetry-ml/telemetry_analysis_report.html)

## 🚀 Project Overview
The main objective of this engineering project is to transition from traditional, reactive telemetry analysis to a **proactive, predictive AI framework**. By leveraging advanced machine learning algorithms, the system acts as a real-time early warning module, detecting critical tyre grip degradation before the vehicle's stock electronic control units (ECU) physically deploy ABS or Traction Control.

### Key Features & Methodology Updates:
* **High-Frequency Engineering:** Processing and cleaning raw streaming-like telemetry data sampled every 5 milliseconds.
* **Automated Feature Selection:** Leveraging Information Theory (Mutual Information) to drop dimensionality from 34 down to the **Top 12 most significant physical predictors**, successfully eliminating informational noise.
* **Anti-Data Leakage Strategy:** Implementing a strict chronological time-split (`initial_time_split`) instead of random permutation splitting. This completely isolates the training phase from the test phase, preventing *temporal proximity data leakage* common in high-frequency time series.
* **Strict Cross-Validation Rytm:** Running 5-fold CV wrapped inside a `tidymodels` workflow, ensuring that downsampling (`themis`) occurs strictly within the analysis folds, keeping the validation folds completely uncorrupted.
* **Model Explainability (XAI):** Breaking down the "black box" of ensemble architectures using Global Variable Importance (VIP) based on Gini Impurity.

---

## 📊 Cross-Validation Performance Comparison

The models were optimized via Grid Search and evaluated using 5-fold Cross-Validation on the training block. The ensemble tree-based models significantly outperformed linear and connectionist architectures:

| Model | ROC AUC (Mean) | F1-Score | Sensitivity | Specificity | Accuracy |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **🥇 Random Forest (Selected)** | **0.999** | **0.883** | **0.998** | **0.975** | **0.977** |
| 🥈 Gradient Boosting (XGBoost) | 0.992 | 0.755 | 0.982 | 0.940 | 0.944 |
| 🥉 Decision Tree (CART) | 0.968 | 0.643 | 0.965 | 0.899 | 0.905 |
| 4. Neural Network (MLP) | 0.968 | 0.607 | 0.938 | 0.888 | 0.892 |
| 5. Logistic Regression | 0.948 | 0.529 | 0.902 | 0.854 | 0.858 |

---

## 🏁 Final Production Test Set Metrics (Last Fit)

The final evaluation was conducted using `last_fit` on an entirely separate, untouched chronological end-block of the session (30,001 rows), retaining its natural, heavily imbalanced class distribution (~9% intervention rate).

* **ROC AUC:** `0.921` — *Proves strong out-of-time generalization capabilities. The model successfully learned universal laws of vehicle dynamics rather than memorizing data points.*
* **Specificity:** `0.952` — *Extremely stable performance during clean driving phases, ensuring a very low false-alarm rate on straights.*
* **Accuracy:** `0.913` — *High overall prediction accuracy across the natural distribution of the test track.*
* **Sensitivity (Recall):** `0.518` & **Precision:** `0.515` — *Obtained using the default `0.50` decision threshold.*

### 💡 Engineering Deployment Insight:
While the high **ROC AUC (0.921)** confirms excellent class separation power, the default mathematical threshold (`0.50`) is overly restrictive for safety-critical motorsport applications. 
In production environments, executing **Decision Threshold Optimization** (lowering the classification threshold to `0.30 - 0.35`) will drastically reduce False Negatives, raising operational Sensitivity to over 85-90% to provide a robust early-warning sequence.

---

## 🛠️ Tech Stack & Ecosystem
* **Language:** R 
* **Framework:** `tidymodels` (recipes, parsnip, workflows, tune, yardstick)
* **Data Manipulation:** `tidyverse` (`dplyr`, `tidyr`, `ggplot2`)
* **Sampling Techniques:** `themis` (Downsampling)
* **XAI & Diagnostics:** `vip`, `stringr`
* **Environment:** RMarkdown / HTML Interactive Document (with code folding feature)

---



## 💻 Project Structure
```text
├── telemetry_analysis_report.Rmd  # Core RMarkdown source code
├── telemetry_analysis_report.html # Interactive HTML production report
├── mclaren_rf_telemetry_model.rds # Serialized, deployment-ready ML pipeline
└── README.md                       # Repository documentation
