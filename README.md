# Employee Incentive & Compensation Analytics

A compensation equity and incentive servicing analytics project — built on enterprise HR data to model pay equity, simulate incentive eligibility, and quantify the financial case for incentive restructuring.

---

## Problem Statement

Enterprise incentive programs fail when compensation structures don't reflect performance, tenure, or market rate — leading to underpaid high performers, structural pay gaps, and avoidable attrition. This project investigates those failure points using a real-world HR dataset, modeling compensation equity, incentive eligibility, and the financial trade-off between fixing pay gaps versus absorbing replacement costs.

---

## Dataset

**IBM HR Analytics Employee Attrition & Performance**
1,470 employee records · 35 variables · sourced from Kaggle

Fields used include department, job role, monthly income, performance rating, stock option level, years at company, job satisfaction, and attrition status.

---

## Key Findings

- **39.3% of the workforce is underpaid** relative to a peer-benchmarked expected salary, calculated using a linear regression compensation equity model (R² = 0.61).
- **Stock options are the strongest retention lever observed** — employees with no stock options show 24.4% attrition, compared to 5.6% for employees at the highest stock option tier.
- **The aggregate incentive gap is $11.75M**, while the projected replacement cost for high-risk employees if no action is taken is $8.05M — meaning targeted intervention on high-risk segments costs less than letting them leave, even though full workforce-wide equity correction requires phased investment.

---

## Methodology

### Layer 1 — Compensation Equity Model (Python)
Built a linear regression model predicting expected salary from performance rating, tenure, education, and role. Derived a compa-ratio (`Actual Salary / Expected Salary`) per employee, segmented into Underpaid (<0.85), Fair (0.85–1.15), and Overpaid (>1.15). Layered a rule-based incentive eligibility flag and simulated payout calculation on top, followed by a weighted attrition risk score and an ROI framework comparing incentive gap cost against replacement cost.

### Layer 2 — SQL Business Queries (DuckDB)
Wrote 6 analytical SQL queries framed as business questions: attrition by compensation band, compa-ratio distribution by job role, overtime-to-incentive cross-tab, a flight risk register of underpaid high performers, department-level incentive gap ROI ranking, and an eligibility reconciliation audit.

### Layer 3 — Power BI Dashboard
Built a 4-page dashboard on a star schema data model (1 fact table, 4 dimension tables) with DAX measures using AVERAGEX, SUMX, and FILTER.

- **Executive Summary** — workforce-level KPIs, attrition breakdown, compensation segment distribution by department
- **Compensation Equity Analysis** — expected vs. actual salary scatter plot, underpaid % by job role, education × performance rating comp-ratio matrix
- **Incentive Program Tracker** — attrition rate by stock option level, incentive eligibility rate by department, job satisfaction × compensation band heatmap
- **Participant Eligibility Register** — a filterable, risk-sorted register of individual employees with compensation gap, risk tier, and eligibility status flagged

---

## Dashboard Preview

**Executive Summary**
![Executive Summary](powerbi/screenshots/page1_executive_summary.png)

**Compensation Equity Analysis**
![Compensation Equity Analysis](powerbi/screenshots/page2_compensation_equity.png)

**Incentive Program Tracker**
![Incentive Program Tracker](powerbi/screenshots/page3_incentive_tracker.png)

**Participant Eligibility Register**
![Participant Eligibility Register](powerbi/screenshots/page4_eligibility_register.png)

---

## Business Recommendations

1. **Prioritize incentive restructuring in departments with the highest gap-to-replacement-cost ratio** — fixing pay equity is cheaper than absorbing attrition in these segments.
2. **Expand stock option eligibility** for employees currently at Level 0, given the sharp attrition drop observed at higher tiers.
3. **Run a reconciliation audit cycle** to catch employees who meet incentive eligibility criteria but are excluded from payouts — a core gap-detection function for incentive program administration.
4. **Re-evaluate compensation for high performers with low compa-ratios**, since this segment carries the highest combined risk of being both underpaid and likely to leave.

---

## Technical Approach

**Languages & libraries:** Python, Pandas, Scikit-learn, SQL
**Database:** DuckDB
**Visualization:** Power BI, DAX, Power Query
**Methodology notes:**
- DuckDB was used for its PostgreSQL-compatible syntax and ability to query flat files directly without infrastructure overhead.
- DuckDB's `ROUND()` returns `decimal.Decimal`, requiring explicit `float()` casting before Python arithmetic.
- The dataset contains no employees rated below "Excellent" (3) on a 1–4 performance scale, a known compression pattern in self-reported corporate performance data — noted here as a data limitation rather than treated as an error.

### How to run

```bash
# 1. Install dependencies
pip install pandas numpy scikit-learn matplotlib seaborn duckdb --break-system-packages

# 2. Place the dataset
# Download from Kaggle: "IBM HR Analytics Employee Attrition & Performance"
# Save to: data/raw/WA_Fn-UseC_-HR-Employee-Attrition.csv

# 3. Run Layer 1 — generates data/processed/employee_incentive_final.csv
jupyter notebook notebooks/01_compensation_equity_model.ipynb

# 4. Run Layer 2 — SQL analysis
jupyter notebook notebooks/02_sql_incentive_queries.ipynb

# 5. Open the Power BI dashboard
# powerbi/incentive_dashboard.pbix
```

---

## Repository Structure

```
employee-incentive-compensation-analytics/
├── README.md
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
│   ├── 01_compensation_equity_model.ipynb
│   └── 02_sql_incentive_queries.ipynb
├── sql/
│   └── incentive_queries.sql
└── powerbi/
    ├── incentive_dashboard.pbix
    └── screenshots/
```
