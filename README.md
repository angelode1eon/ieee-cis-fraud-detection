# IEEE-CIS Fraud Detection: Exploratory Data Analysis & Risk Fingerprinting

An exploratory risk analytics and predictive feature analysis on card transaction data using Python, Pandas, and Seaborn. This project evaluates baseline fraud imbalances, stress-tests intuitive fraud assumptions (e.g., mismatch flags), and engineers a multi-variable fraud fingerprint delivering significant statistical lift over baseline rates.

---

## Project Overview
Credit card fraud detection is characterized by severe class imbalances and asymmetric misclassification penalties. This study uses a subset of the **IEEE-CIS Fraud Detection benchmark** to analyze structural risk patterns, audit missingness mechanisms across engineered identity attributes, and formulate precision heuristics that identify dense fraud clusters without incurring unmanageable false-positive decline rates.

* **Dataset Size:** 50,132 transactions across 56 feature columns
* **Baseline Observed Fraud Rate:** 2.71% (1,357 fraudulent vs. 48,775 legitimate transactions)
* **Core Toolkit:** Python, Pandas, NumPy, Matplotlib, Seaborn

---

## Key Findings & Feature Insights

### 1. The Candidate Fraud Fingerprint (5.75× Lift)
Combining an elevated digital transaction channel with an unaged card footprint and high short-term transaction velocity isolates an acute risk concentration:
$$\text{ProductCD} == \text{'C'} \quad\land\quad \text{D1} == 0 \quad\land\quad \text{C1} \ge 2$$

* **Baseline Fraud Rate:** 2.71%
* **Fingerprint Observed Fraud Rate:** **15.56%** (1,677 matching transactions)
* **Observed Lift:** **5.75×** over baseline
* **Business Limitation:** Despite the substantial lift, **84.44% of flagged transactions remain legitimate**. Applying an automated hard block would trigger roughly 5.4 false declines per true fraudster intercepted. This rule serves as a trigger for step-up verification (e.g., 3DS/SMS OTP) rather than outright transaction cancellation.

### 2. Refuting Intuitive Assumptions: Address Verification Flags (`M5`)
* **Conventional Assumption:** Mismatched customer and bank verification details (`M5 == 'F'`) should indicate higher fraud probability.
* **Observed Reality:** Mismatched records exhibit a lower fraud rate of **2.04%** (0.75× lift), whereas matching records (`M5 == 'T'`) show an elevated fraud rate of **4.35%** (1.61× lift).
* **Root Cause:** Selection bias—`M5` is missing for 64.3% of transactions and is selectively captured during specific merchant checkout flows.

### 3. Entity Symmetry & Velocity Analysis (`P_emaildomain` vs. `R_emaildomain`)
* Restricting to transactions where both purchaser and recipient email domains are populated:
  * **Identical Domains (`P == R`):** 7.12% fraud rate (**2.63× lift**) across 8,667 transactions.
  * **Different Domains (`P != R`):** 1.34% fraud rate (0.50× lift).
* **Behavioral Rationale:** Scripted checkout attacks and card-testing bots frequently generate throwaway credentials across standard public providers (e.g., `@gmail.com`) for both sender and recipient inputs.

### 4. Feature Space Diagnostics
* **Velocity Features (`C1`):** Severely right-skewed (skewness: 8.65). Median count for legitimate transactions is 1.0 vs. 2.0 for fraud; the 95th percentile reaches 60.0 for legitimate transactions vs. 627.2 for fraud.
* **Timedelta Features (`D1`):** Mean elapsed days for legitimate transactions is 82.5 days vs. 41.2 days for fraudulent transactions, underscoring the vulnerability of newer accounts.
* **V-Features (`V1–V10`):** Uniform missingness at 69.38%. High collinearity exists between paired counters (e.g., $r = 0.92$ between `V4` and `V5`), while `V10` shows the greatest mean separation between legitimate (0.484) and fraudulent (0.193) transactions.

---

## Summary Comparison

| Segment / Condition | Transaction Volume | Fraud Rate | Lift over Baseline | Operational Recommendation |
| :--- | :--- | :--- | :--- | :--- |
| **Full Dataset Baseline** | 50,132 | 2.71% | 1.00× | Baseline monitoring |
| **Commercial Product (`ProductCD == 'C'`)** | 4,939 | 10.06% | 3.72× | Prioritize velocity screening |
| **Fingerprint Match (`ProductCD=='C' & D1==0 & C1>=2`)** | 1,677 | **15.56%** | **5.75×** | Route to dynamic step-up 2FA |
| **Symmetric Emails (`P_email == R_email`)** | 8,667 | 7.12% | 2.63× | Device fingerprint & IP check |
| **Match Verification (`M5 == 'T'`)** | 8,090 | 4.35% | 1.61× | Counter-intuitive; avoid hard block |

---

## Repository Structure

```text
├── data/
│   ├── .gitkeep
│   └── ieee-cis-intro.csv           # Transaction records (or download link)
├── notebooks/
│   └── fraud_detection_eda.ipynb    # Complete EDA and fingerprint analysis
├── requirements.txt                 # Project dependencies
├── .gitignore
└── README.md
