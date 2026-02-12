# 🧠 Vauban 50 – End-to-End Session-Level Purchase & Product Prediction (PySpark)

## 🚀 Project Overview

This project develops a full end-to-end machine learning system for **Vauban 50**, a premium accessories brand, to:

1. Predict whether a website session will result in a purchase (binary classification)
2. Predict which specific product will be purchased (conditional multi-class classification)
3. Derive actionable marketing and growth insights from behavioral data

The modeling framework is designed for **real-time deployment**, strict temporal validation, and realistic production simulation.

---

## 🏆 Final Performance – Real Holdout Evaluation

The model was evaluated on a **fully unseen holdout dataset**.

Our team ranked **Top 3 overall** among all teams in final holdout accuracy.

### Binary Conversion Model (Holdout)

- Accuracy ≈ **94.8%**
- Strong sensitivity & specificity balance
- High F1-score
- Stable generalization under temporal drift

The holdout data was never joined with order tables during scoring, ensuring a realistic production setting.

---

## 🧩 Modeling Strategy

The business problem is decomposed into a **two-stage prediction framework**:

### Stage 1 — Binary Conversion Model  
Predict: *Will this session convert?*

### Stage 2 — Conditional Multi-Class Product Model  
Predict: *Which product will be purchased (if conversion occurs)?*

This mirrors real business logic: product choice is only relevant once purchase intent exists.

---

## ⏳ Temporal Validation Strategy (Leakage Prevention)

All models operate at the `website_session_id` level.

Strict chronological split:

- **Training:** Mar 2022 – Nov 2023  
- **Validation:** Dec 2023 – Jun 2024  
- **Test:** Jul 2024 – Jan 2025  
- **Holdout (Final Scoring):** Feb 2025 – Mar 2025  

The holdout dataset was never used in training or tuning.

No future information was used in feature construction.

---

## ⚙️ Feature Engineering

Only session-level context and in-session behavioral signals were used.

No historical aggregates were engineered.

### Feature Groups

**Session Context**
- `is_repeat_session`
- traffic source indicators
- device type flags

**Engagement Intensity**
- `pv_count`
- `session_duration_sec`
- `avg_time_between_pv`
- `long_pauses`

**Funnel Progression**
- `cart_pageviews`
- `billing_pageviews`
- `shipping_pageviews`

**Product Exposure**
- `viewed_corepack`
- `viewed_techfortress`
- `viewed_ecoshell`
- `viewed_airlite`

Missing numerical values were filled with a sentinel value (`-1`), safely handled by tree-based models.

---

## 🌲 Binary Conversion Model

Models evaluated:

- Logistic Regression (L1 & L2)
- Random Forest
- Gradient Boosted Trees

### Final Model: Random Forest

Selected based on:

- Validation AUC ≈ **0.978**
- High recall
- Strong lift
- Stability under aggressive feature removal
- Robustness to class imbalance

Final configuration:
- `numTrees = 200`
- `maxDepth = 8`
- `subsamplingRate = 0.7`
- Class weighting enabled  

### Robustness Testing

Three stress tests were conducted:

1. Full feature set
2. Removal of top importance features
3. Removal of billing/cart/shipping features

Validation AUC remained stable across all tests, confirming the model does not rely on narrow or leakage-prone signals.

---

## 🧠 Multi-Class Product Model

The product prediction model is trained conditionally on conversion.

Target:
- `primary_product_id` (single winner product)

Key elements:

- 14 selected features
- Balanced Random Forest
- Sampling to mitigate imbalance for newly launched products
- Product-specific pageviews dominate importance

---

## 🔄 Session-to-User Aggregation

Although predictions are generated at session level, final outputs are aggregated at user level:

- **Binary:** maximum predicted conversion probability per user  
- **Multi-class:** average product probability per user  
- Product predictions are gated by binary conversion prediction  

This approach preserves highest-intent signals and aligns with ranking-based targeting use cases.

---

## 📊 Business Insights

### 1️⃣ Marketing Channel Efficiency
Paid search channels generate the majority of total net profit.

**Recommendation:**
- Maintain GSearch as primary volume driver
- Scale BSearch for margin optimization

### 2️⃣ Landing Page Optimization
Lander_5 achieved the highest conversion rate.

**Recommendation:**
- Deploy Lander_5 as primary entry page

### 3️⃣ Smart Work-Hours Trigger (Behavioral Targeting)

If:
- Paid search
- Desktop device
- 9am–5pm

Then:
- Trigger personalized professional landing page
- Increase conversion among high-intent daytime users

---

## 🧪 Realistic Scoring Simulation

To simulate real production:

- Orders and labels were removed
- The model scored future sessions
- Predictions were matched ex-post with realized outcomes

Performance remained strong under temporal drift, confirming deployability.

---

## 🛠 Tech Stack

- PySpark  
- Spark ML  
- Pandas  
- Matplotlib  
- Google Colab  

---

## 📂 Repository Structure
notebooks/ → Full modeling pipeline (PySpark)
slides/ → Executive + technical presentation
outputs/ → Sample holdout predictions

---

## 🎯 What This Project Demonstrates

- Production-aware ML design  
- Strict temporal validation  
- Leakage prevention  
- Robustness testing  
- Class imbalance handling  
- Two-stage modeling framework  
- Strong real holdout performance (Top 3 overall)  
- Business-aligned data science  

