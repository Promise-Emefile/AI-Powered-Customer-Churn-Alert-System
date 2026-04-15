# 🚨 AI-Powered Customer Churn Alert System

An intelligent, multi-agent system that automatically identifies at-risk customers using machine learning and sends actionable churn alerts via email. Built with CrewAI, scikit-learn, and Gmail automation.

---

## 🎯 Overview

This system uses **autonomous AI agents** working together in a crew to:

1. **Fetch** customer data from MySQL database
2. **Engineer** features with categorical encoding
3. **Train** a Random Forest churn prediction model
4. **Score** all customers weekly/monthly
5. **Alert** the business team with HIGH/MEDIUM risk customers

**Key Metrics:**
- 🔴 **HIGH RISK**: Churn probability ≥ 75%
- 🟡 **MEDIUM RISK**: Churn probability ≥ 50%
- ✅ **Email alerts** with customer details for immediate action

---

## 🏗️ Architecture

### Multi-Agent CrewAI System

```
┌─────────────────────────────────────────────────────────────┐
│                    CHURN PREDICTION CREW                     │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Data Engineer Agent]  →  [Data Scientist Agent]  →  [ML Engineer Agent]
│     Fetch Raw Data         Engineer Features         Train Model
│       ✓ MySQL              ✓ Encoding               ✓ Random Forest
│       ✓ CSV Export         ✓ Validation             ✓ SMOTE Balance
│                                                      ✓ Model Save
│
│                    [PREDICTION CREW]
│
│  [Data Engineer]  →  [Data Scientist]  →  [Churn Analyst Agent]
│     Fetch Data        Engineer Features    ✓ Score Customers
│                                            ✓ Identify At-Risk
│                                            ✓ Send Email Alert
│
└─────────────────────────────────────────────────────────────┘
```

### Three Operation Modes

| Mode | Purpose | Schedule |
|------|---------|----------|
| `train` | Train initial model | One-time |
| `predict` | Score all customers & alert | Manual |
| `schedule` | Auto-run monthly | 1st of month, 8:00 AM |

---

## 📋 Features

✅ **Automated Data Pipeline**
- MySQL database integration
- Automatic feature engineering
- Categorical encoding (Label Encoding)
- Data quality validation

✅ **Machine Learning**
- Random Forest Classifier (200 trees, depth=10)
- SMOTE for class imbalance handling
- ROC-AUC evaluation metrics
- Model persistence (pickle)

✅ **Intelligent Agents**
- CrewAI autonomous agents
- Sequential task execution
- Natural language task descriptions
- Tool-based architecture

✅ **Email Alerts**
- Gmail SMTP integration
- Formatted churn reports
- HIGH/MEDIUM risk breakdown
- Customer details included

✅ **Scheduling**
- APScheduler for monthly runs
- Cron-based automation
- Easy to customize

---

## 🚀 Quick Start

### Prerequisites

```bash
Python 3.9+
MySQL 5.7+
Gmail account with App Password
```

### Installation

```bash
# Clone repository
git clone https://github.com/yourusername/churn-alert-system.git
cd churn-alert-system

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Configuration

Create `.env` file in project root:

```env
# Database
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=churn_db

# Gmail
ALERT_EMAIL_SENDER=your_email@gmail.com
ALERT_EMAIL_RECIPIENT=team@company.com
ALERT_EMAIL_PASSWORD=your_16_char_app_password

# OpenAI
OPENAI_API_KEY=sk-xxx...
OPENAI_MODEL_NAME=gpt-3.5-turbo
```

**Get Gmail App Password:**
1. Go to https://myaccount.google.com/apppasswords
2. Select "Mail" and "Windows Computer"
3. Copy the 16-character password to `.env`

### Database Setup

```sql
CREATE TABLE customer_data (
    CustomerID VARCHAR(50),
    gender VARCHAR(10),
    SeniorCitizen INT,
    Partner VARCHAR(10),
    Dependents VARCHAR(10),
    tenure INT,
    PhoneService VARCHAR(10),
    MultipleLines VARCHAR(50),
    InternetService VARCHAR(50),
    OnlineSecurity VARCHAR(10),
    OnlineBackup VARCHAR(10),
    DeviceProtection VARCHAR(10),
    TechSupport VARCHAR(10),
    StreamingTV VARCHAR(10),
    StreamingMovies VARCHAR(10),
    Contract VARCHAR(50),
    PaperlessBilling VARCHAR(10),
    PaymentMethod VARCHAR(50),
    MonthlyCharges DECIMAL(10,2),
    TotalCharges DECIMAL(10,2),
    Churn VARCHAR(10)
);
```

---

## 📖 Usage

### 1. Train the Model (First Time)

```python
from AI_Powered_customer_churn import main

main("train")
```

**Output:**
```
✅ Model Training Complete
===========================
ROC-AUC Score : 0.8523
Model saved to: models/churn_model.pkl

Classification Report:
              precision    recall  f1-score
           0       0.89      0.87      0.88
           1       0.72      0.76      0.74
```

### 2. Run One-Time Prediction

```python
main("predict")
```

**Output:**
```
✅ Identified 125 at-risk customers
   🔴 HIGH   : 50
   🟡 MEDIUM : 75

📧 Connecting to Gmail SMTP server...
✅ Email sent successfully to team@company.com
```

### 3. Start Monthly Scheduler

```python
main("schedule")
```

Runs automatically on the **1st of every month at 8:00 AM**. Press `Ctrl+C` to stop.

---

## 📧 Email Alert Example

```
Subject: ⚠️ Monthly Churn Alert — Action Required

Monthly Churn Alert — April 2026

ALERT SUMMARY:
🔴 HIGH RISK CUSTOMERS: 50
🟡 MEDIUM RISK CUSTOMERS: 75

Total At-Risk: 125

Details:
   CustomerID  churn_probability  risk_tier
   CUST001     0.8234             HIGH
   CUST002     0.7891             HIGH
   CUST003     0.6234             MEDIUM
   ...

Action Required: Contact these customers immediately for retention.
```

---

## 📁 Project Structure

```
churn-alert-system/
├── AI_Powered_customer_churn.ipynb  # Main notebook
├── my_utils.py                       # Utility functions
├── .env                              # Environment variables
├── requirements.txt                  # Python dependencies
├── models/
│   └── churn_model.pkl              # Trained model
├── temp/
│   ├── raw_data.csv                 # Raw customer data
│   ├── engineered_data.csv          # Processed features
│   └── at_risk.csv                  # High/Medium risk customers
└── README.md                         # This file
```

---

## 🔧 Configuration Options

### Model Hyperparameters

Edit in `train_model()`:

```python
model = RandomForestClassifier(
    n_estimators=200,        # Number of trees
    max_depth=10,            # Max tree depth
    min_samples_split=5,     # Min samples to split
    class_weight="balanced"  # Handle class imbalance
)
```

### Risk Tier Thresholds

Edit in `assign_tier()`:

```python
def assign_tier(prob):
    if prob >= 0.75:      # HIGH threshold
        return "HIGH"
    elif prob >= 0.50:    # MEDIUM threshold
        return "MEDIUM"
    else:
        return "LOW"
```

### Schedule Timing

Edit in `main("schedule")`:

```python
scheduler.add_job(
    run_monthly_prediction,
    trigger="cron",
    day=1,        # Change to run on different dates
    hour=8,       # Change hour (0-23)
    minute=0      # Change minute (0-59)
)
```

---

## 📊 Model Performance

Typical metrics on telecom churn dataset:

| Metric | Score |
|--------|-------|
| ROC-AUC | 0.852 |
| Precision (Churn) | 0.72 |
| Recall (Churn) | 0.76 |
| F1-Score | 0.74 |

---

## 🛠️ Troubleshooting

### Email Not Sending

```
❌ EMAIL AUTHENTICATION FAILED
```

**Solution:** Verify Gmail App Password is correct (not your regular password)

### Database Connection Error

```
ERROR: Can't connect to MySQL server
```

**Solution:** Check `.env` credentials and ensure MySQL is running:
```bash
mysql -u root -p
```

### Model File Not Found

```
ERROR: No such file or directory: 'models/churn_model.pkl'
```

**Solution:** Run `main("train")` first to create the model

### Missing Dependencies

```
ModuleNotFoundError: No module named 'crewai'

---

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request


---

## 👨‍💼 Author

**Promise Emefile** - Data Analyst & AI Engineering

- Linked: www.linkedin/in/promise-emefile
- Email: promiseemefile@gmail.com

---

## 🙏 Acknowledgments

- **CrewAI** - Multi-agent AI framework
- **scikit-learn** - Machine learning library
- **pandas** - Data manipulation
- **APScheduler** - Task scheduling
- **imbalanced-learn** - SMOTE for class imbalance

---

## 📞 Support

Found a bug? Have a suggestion?

- 📧 Email: promiseemefile@gmail.com
- 🐛 Create an Issue on GitHub
- 💬 Start a Discussion

---

## 🎯 Roadmap

- [ ] Add XGBoost model option
- [ ] Dashboard for visualizations
- [ ] Slack integration for alerts
- [ ] API endpoint for real-time scoring
- [ ] Model performance tracking
- [ ] A/B testing framework
- [ ] Batch prediction endpoint

---

**Last Updated:** April 2026  
**Status:** ✅ Production Ready
