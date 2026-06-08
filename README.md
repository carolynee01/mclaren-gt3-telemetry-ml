# 🏁 ML-Based Telemetry Analysis & Traction Limits Prediction | McLaren 720S GT3

An end-to-end Data Engineering and Machine Learning project focused on predicting vehicle traction limits (ABS/TC interventions) based on high-frequency telemetry data (200 Hz) extracted from the **Assetto Corsa Competizione** simulator at the **Brands Hatch** circuit.

[🚀 HTML Report](https://carolynee01.github.io/mclaren-gt3-telemetry-ml/telemetry_analysis_report.html)

---

## 🚀 Project Overview
The main objective of this engineering project is to transition from traditional, reactive telemetry analysis to a **proactive, predictive AI framework**. By leveraging advanced machine learning algorithms, the system acts as a real-time early warning module, detecting critical tyre grip degradation seconds before the vehicle's stock electronic control units (ECU) deploy ABS or Traction Control.

### Key Features:
* **High-Frequency Engineering:** Processing and cleaning raw streaming-like telemetry data.
* **Feature Engineering:** Implementing Information Theory metrics (Mutual Information) to isolate the most significant dynamic predictors.
* **Imbalanced Data Strategy:** Handling severely skewed data (~10% intervention vs ~90% stable driving) using advanced downsampling via `themis`.
* **Comparative Modeling:** Training and tuning 5 separate architectures (**Random Forest, XGBoost, MLP Neural Networks, CART Decision Trees, Logistic Regression**) using the modern `tidymodels` ecosystem in R.
* **Model Explainability (XAI):** Breaking down the "black box" of ensemble models using Global Variable Importance (VIP).
* **Production Deployment Ready:** Serializing the final pipeline into an `.rds` object capable of real-time stream inference loop computing (<5ms).

---

## 📊 Key Results & Performance

After rigorous **5-fold Cross-Validation** and final testing on an untouched, highly imbalanced production test set, the models achieved outstanding metrics:

| Model | ROC AUC (Mean) | Accuracy (Mean) |
| :--- | :---: | :---: |
| **🥇 Random Forest (Selected)** | **0.998** | **98.2%** |
| 🥈 Gradient Boosting (XGBoost) | 0.987 | 94.7% |
| 🥉 Neural Network (MLP) | 0.971 | 91.8% |

### Production Test Set Metrics (Random Forest):
* **ROC AUC:** `0.998`
* **Sensitivity (Recall):** `99.8%` — *Critical safety metric: out of thousands of slip frames, the model missed only 22 events.*
* **Specificity:** `97.1%` — *Extremely low false alarm rate during clean driving phases.*
* **Precision:** `79.9%` — *The remaining 20% accounts for the pre-intervention phase, where the model accurately forecasts vehicle instability before hardware activation.*

---

## 🛠️ Tech Stack & Ecosystem
* **Language:** R 
* **Framework:** `tidymodels` (recipes, parsnip, workflows, tune, yardstick)
* **Data Manipulation:** `tidyverse` (`dplyr`, `tidyr`, `ggplot2`)
* **Sampling Techniques:** `themis` (Downsampling)
* **XAI & Diagnostics:** `vip`, `stringr`
* **Environment:** RMarkdown / HTML Interactive Document (with code folding feature)

---

## 📈 Insights from Vehicle Dynamics & XAI
According to the **Variable Importance Plot (VIP)**, the machine learning model successfully reconstructed the underlying laws of physics:
1. **`SUS_TRAVEL_LR` (Rear Left Suspension Travel):** Identified as the top predictor. The model discovered that deep rear suspension decompression (unloading) during trail-braking phases aggressively triggers wheel lockups due to massive longitudinal weight transfer.
2. **`WHEEL_SPEED_LF/RF` & `THROTTLE`:** Secondary key indicators tracking the delta velocity of steering wheels and torque load application at corner exits.

---

## 💻 Project Structure
```text
├── telemetry_analysis_report.Rmd  # Core RMarkdown source code
├── telemetry_analysis_report.html # Interactive HTML production report
├── mclaren_rf_telemetry_model.rds # Serialized, deployment-ready ML pipeline
└── README.md                       # Repository documentation
