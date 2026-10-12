# AI-Driven Customer Churn Prediction System

> **A production-grade, end-to-end Machine Learning System** designed to predict customer churn, quantify financial revenue exposure (MRR/ARR at risk), execute real-time model training & convergence analysis, and prescribe data-driven retention playbooks.

![Python](https://img.shields.io/badge/Python-3.9%20%7C%203.10%20%7C%203.11-blue?logo=python)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-1.3.2-orange?logo=scikitlearn)
![XGBoost](https://img.shields.io/badge/XGBoost-2.0.3-green)
![Streamlit](https://img.shields.io/badge/Streamlit-1.29-red?logo=streamlit)
![License](https://img.shields.io/badge/License-MIT-purple)

---

## 📌 Business Overview & Capabilities

In subscription businesses, **acquiring a new customer costs 5–7× more than retaining an existing one**. Customer churn directly degrades Monthly Recurring Revenue (MRR) and Enterprise Valuation. This system combines predictive machine learning with financial telemetry and prescriptive strategy.

### 5 Interactive Dashboard Modules:
1. **Executive Overview**: High-level portfolio KPIs, churn probability distribution (binned into 10% tiers), contract distribution, and payment method risk diagnostics.
2. **Upload Dataset & Predict Churn**: Universal ML inference supporting any uploaded CSV or Excel file (`.csv`, `.xlsx`). Auto-detects feature types, scores records in milliseconds, and outputs risk categories (High / Medium / Low).
3. **Business Revenue & Financial Risk**: SQL-driven financial telemetry computing Monthly Revenue at Risk (MRR), Annualized Recurring Revenue (ARR), ARPU differences, and an interactive **Retention Campaign ROI Simulator**.
4. **Model Testing & Evaluation**: Live epoch-by-epoch neural network training simulation with real loss and accuracy convergence tracking, interactive epoch count selector (1–100), and confusion matrix display.
5. **Customer Retention Strategy**: Dynamic diagnostics pairing diagnosed customer portfolio problems (contract traps, payment friction, onboarding gaps) with root cause analyses, strategic solutions, SLAs, and capital impact projections.

---

## 🤖 Model Performance & Evaluation

Two primary models were benchmarked on the benchmark IBM Telco dataset (7,043 customer accounts):

| Model | Accuracy | Precision | Recall | F1 Score | AUC-ROC | Status |
|---|---|---|---|---|---|---|
| **Random Forest Classifier** | **76.8%** | **54.8%** | **70.4%** | **61.7%** | **0.8362** | **Production Champion ★** |
| **XGBoost Classifier** | **74.2%** | **50.8%** | **78.8%** | **61.8%** | **0.8212** | Benchmark Baseline |

- **Top Predictors**: RiskScore (engineered), ChargesPerTenure, Contract type, MonthlyCharges, and tenure length.
- **Production Selection**: Random Forest delivers higher AUC-ROC (0.8362) and better probability calibration for churn risk tiering.

---

## 🚀 Quick Start (Local Setup)

### 1. Clone the repository
```bash
git clone https://github.com/tpsreekar37-dotcom/churn-prediction-system.git
cd churn-prediction-system
```

### 2. Create and activate a virtual environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS / Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Run the Streamlit Dashboard
```bash
streamlit run dashboard/app.py
```
Open **`http://localhost:8501`** in your browser.

---

## ☁️ Deployment on Render (Free Tier)

This repository includes `render.yaml` and `Procfile` preconfigured for Render's free Web Service runtime:

1. Sign in to [Render](https://render.com/).
2. Click **New +** → **Web Service**.
3. Connect your GitHub repository (`churn-prediction-system`).
4. Configure service settings:
   - **Environment**: `Python`
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `streamlit run dashboard/app.py --server.port $PORT --server.address 0.0.0.0 --server.headless true`
   - **Instance Type**: `Free`
5. Click **Create Web Service**. Render will automatically build the environment and host the live application.

---

## 📁 Repository Structure

```
churn-prediction-system/
├── app.py                      # Root entry point forwarding to dashboard
├── Procfile                    # Web service process configuration
├── render.yaml                 # Render Blueprint automated specification
├── .python-version             # Python version definition (3.10.13)
├── requirements.txt            # Production dependencies
├── teseataset.xlsx             # Demo test dataset for immediate upload testing
│
├── dashboard/
│   ├── app.py                  # Streamlit Enterprise Application (5 pages)
│   └── style.css               # Styling definitions
│
├── src/
│   ├── predict.py              # ML batch & single inference engine
│   ├── generic_pipeline.py     # Universal dataset preprocessor
│   ├── sql_integration.py      # SQLite database analytics engine
│   ├── train_model.py          # Random Forest & XGBoost model trainers
│   ├── preprocessing.py        # Data cleaning and feature scaling
│   ├── feature_engineering.py  # Feature engineering definitions
│   └── utils.py                # Core constants & path helpers
│
├── models/
│   ├── best_model.pkl          # Trained Random Forest model (production)
│   ├── xgboost.pkl             # Trained XGBoost model
│   ├── scaler.pkl              # Fitted MinMaxScaler
│   ├── encoders.pkl            # Categorical encoders
│   └── model_results.json      # Benchmark performance metrics
│
├── data/
│   ├── churn.db                # SQLite database with benchmark runs
│   ├── raw/                    # Raw Telco benchmark CSV
│   └── processed/              # Processed datasets
│
└── tests/
    └── verify_all.py           # Verification and end-to-end test suite
```

---

## 🛡️ Security & Privacy Notice
This repository contains no hardcoded credentials, secret keys, or sensitive personally identifiable customer data. All customer records in sample datasets use synthetic, anonymized identifiers.

---

## 📄 License
Released under the [MIT License](LICENSE).
