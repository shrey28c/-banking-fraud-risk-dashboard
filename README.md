# -banking-fraud-risk-dashboard
# Banking Customer Credit & Fraud Risk Dashboard

## 📌 Problem Statement
Banks and lending institutions need to monitor customer credit health and flag potentially risky accounts before they default or commit fraud. Manually reviewing thousands of customer profiles across income, debt, repayment behavior, and credit utilization isn't scalable. This project builds a Power BI dashboard that consolidates 100,000 customer-month records into key risk indicators, allowing a risk analyst to instantly see how many customers are high-risk, which occupations and age groups carry the most risk, and how risk trends over time — replacing manual spreadsheet review with a single interactive view.

## 📊 Dataset
- **Source**: Credit Score Classification dataset (Kaggle)
- **Size**: 100,000 rows × 28 raw columns
- **Scope**: 12,500 unique customers, tracked monthly across 8 months
- **Data quality**: No missing values across any column
- **Key fields**: Annual Income, Monthly Salary, Number of Loans, Outstanding Debt, Credit Utilization Ratio, Payment Delays, Credit Mix, Credit Score (Good/Standard/Poor)

> Note: Customer names and SSNs in this dataset are synthetic placeholders from the public Kaggle dataset, not real personal data.

## 🛠️ Tools Used
- **Power BI Desktop** — data modeling, DAX measures, dashboard design
- **Power Query (M)** — data transformation and feature engineering
- **DAX** — calculated columns and measures

## 🧹 Data Preparation (Power Query)
- Imported and type-cast all 28 raw columns
- Engineered 3 segmentation columns:
  - `Income_Group`: bucketed Annual Income into 5 tiers (Low to High)
  - `Age_Group`: bucketed Age into 4 ranges (18-25, 26-35, 36-50, 50+)
  - `Utilization_Class`: bucketed Credit Utilization into Low/Medium/High
- Engineered a calculated risk column, `Fraud_Flag`: flags an account as high-risk if at least 2 of the following are true — utilization above 80%, 3+ delayed payments, 4+ active loans, or a "Poor" credit score. This is a rule-based heuristic, not a trained ML model or confirmed fraud label.

## 📐 DAX Measures
| Measure | Logic | Purpose |
|---|---|---|
| Total_Applications | `COUNTROWS('dataset')` | Total records in dataset |
| Total_Customers | `DISTINCTCOUNT(Customer_ID)` | Unique customers |
| Total_HighRisk | `CALCULATE(COUNTROWS, Fraud_Flag = 1)` | Count of flagged high-risk records |
| Fraud_Rate | `DIVIDE(Total_HighRisk, Total_Applications)` | % of records flagged high-risk |
| High_Risk_Count | `CALCULATE(COUNTROWS, Fraud_Flag > 0.7)` | High-risk count (threshold variant) |
| HighRisk_by_occupation | `CALCULATE(COUNTROWS, Fraud_Flag = 1)` | High-risk count sliced by occupation |
| Average_Annual_Income | `AVERAGE(Annual_Income)` | Average customer income |
| Avg_Outstanding_Debt | `AVERAGE(Outstanding_Debt)` | Average debt held |
| Avg_EMI | `AVERAGE(Total_EMI_per_month)` | Average monthly EMI obligation |
| Avg_Utilization | `AVERAGE(Credit_Utilization_Ratio)` | Average credit card utilization |
| Debt_to_Income_Ratio | `DIVIDE(COUNT(Outstanding_Debt), COUNT(Annual_Income))` | Debt records relative to income records |
| EMI_Burden | `DIVIDE(SUM(Total_EMI_per_month), SUM(Monthly_Inhand_Salary))` | EMI as a share of take-home salary |

## 📈 Dashboard Features
- **KPI Cards**: Total Applications, Total Customers, Fraud Rate, Total High-Risk, Average Annual Income, Average Outstanding Debt
- **Risk Count by Occupation** (bar chart)
- **Fraud by Age Group** (column chart)
- **EMI vs Credit Score** (scatter chart)
- **Total High-Risk by Month** (trend line)
- **Delay from Due Date** distribution (scatter chart)
- **Delay from Due Date vs Credit Score** (donut chart)
- **Interactive slicers**: Age Group, Credit Score, Income Group

## 🔍 Key Insights
- Average annual income across customers is ~$50,500, with average outstanding debt of ~$1,426 and average monthly EMI of ~$108.
- Average credit utilization sits at ~32%, but a meaningful share of customers exceed the 80% threshold used in the risk flag.
- Of all 100,000 records, roughly 54% trip the high-risk flag under the composite rule, signaling that delayed payments and loan count are major drivers worth deeper investigation.
- Credit Score breakdown: 53% Standard, 29% Poor, 18% Good.

## 📂 Repository Structure
\`\`\`
banking-fraud-risk-dashboard/
├── README.md
├── data/
│   └── credit_risk_dataset.csv
├── dashboard/
│   └── banking_fraud_risk_dashboard.pbix
└── screenshots/
    └── dashboard_overview.png
\`\`\`

## 🚀 How to Use
1. Clone or download this repository
2. Open `dashboard/banking_fraud_risk_dashboard.pbix` in Power BI Desktop
3. If prompted, point the data source to `data/credit_risk_dataset.csv`
4. Explore the dashboard using the slicers to filter by Age Group, Credit Score, or Income Group

 ![Dashboard Screenshot](screenshots/dashboard_overview.png)
