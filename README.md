# Phishing Email Detection (R Analysis)

An end‑to‑end R pipeline comparing Random Forest and Gradient Boosting classifiers on an imbalanced phishing‑email dataset, using multiple resampling strategies and probability‑threshold tuning to optimize detection performance.

---

## 📊 Project Overview

- **Goal:** Automatically flag phishing emails with a balanced trade‑off between false positives and false negatives.  
- **Workflow:**  
  1. **Data split:** 60 % train / 20 % validation / 20 % test (stratified).  
  2. **Resampling:** Original, down‑sampling, up‑sampling, ROSE synthetic sampling.  
  3. **Modeling:**  
     - Random Forest (100 trees)  
     - Gradient Boosting (GBM, 100 trees, lr = 0.1)  
  4. **Threshold tuning:** Scan 0.01–0.99 on validation to maximize F₁ or recall.  
  5. **Champion evaluation:** Retrain best models on combined 80 % development set and report final metrics on the held‑out test set.  
- **Interactive report:** [View on RPubs](https://rpubs.com/lukahere007/1299856)  

---

## 🗂️ Data

- **Source file:** `data/email_phishing_data.csv`  
- **Features:** NLP‑derived indicators (token counts, URL flags, header cues, etc.)  
- **Target:** `label` (factor with levels `ham` / `phish`)

---
## 📈 Final Model Comparison

| Sampling  | Model         | Threshold | Balanced Accuracy | Precision | Recall  | F₁     |
|:---------:|:-------------:|----------:|------------------:|----------:|--------:|-------:|
| Up        | RandomForest  |      0.57 |            0.6377 |    0.0480 |  0.3759 | 0.0851 |
| Original  | RandomForest  |      0.09 |            0.5748 |    0.2039 |  0.1579 | 0.1780 |
| Original  | GBM           |      0.06 |            0.5794 |    0.1411 |  0.1729 | 0.1554 |

---

### 🔑 Key Findings

- **Highest Recall**  
  - **Up‐sampled RandomForest @ 0.57**  
  - **Recall = 0.376** (catches ~38 % of phishing)  
  - **Cost:** Precision plummets to 4.8 % (≈95 % of alerts are false positives), F₁ = 0.085.  

- **Best F₁ & Balanced Accuracy**  
  - **Original RandomForest @ 0.09**  
  - **F₁ = 0.178**, Balanced Accuracy = 0.575, Precision = 0.204, Recall = 0.158  
  - **Original GBM @ 0.06** performs similarly (F₁ = 0.155, BA = 0.579).

- **Best‐Balanced Models** (top 2 by Balanced Accuracy):  
  1. **Up + RandomForest** (BA = 0.6377)  
  2. **Original + GBM**       (BA = 0.5794)  

---

### 🎯 Recommendation

- **For balanced performance** (precision vs. recall), deploy **Original + RandomForest @ 0.09**.  
- **If maximum recall** is paramount (tolerating many false alarms), choose **Up‑sampled RandomForest @ 0.57**, but be aware that nearly all flagged messages will be false positives.  

## 🔧 Requirements

- **R version:** 4.3 or later  
- **R packages:**  
  ```r
  install.packages(c(
    "caret", "ROSE", "randomForest", "gbm", "ranger", "pROC"
  ))
