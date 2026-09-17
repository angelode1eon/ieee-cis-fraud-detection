# IEEE-CIS Fraud Detection: Exploratory Data Analysis & Risk Fingerprinting

An exploratory risk analytics and predictive feature analysis on card transaction data using Python, Pandas, and Seaborn. This project evaluates baseline fraud imbalances, stress-tests intuitive fraud assumptions (e.g., mismatch flags), and engineers a multi-variable fraud fingerprint delivering significant statistical lift over baseline rates.

---

## Project Overview
Credit card fraud detection is characterized by severe class imbalances and asymmetric misclassification penalties[cite: 1]. This study uses a subset of the **IEEE-CIS Fraud Detection benchmark** to analyze structural risk patterns, audit missingness mechanisms across engineered identity attributes, and formulate precision heuristics that identify dense fraud clusters without incurring unmanageable false-positive decline rates[cite: 1].

* **Dataset Size:** 50,132 transactions across 56 feature columns[cite: 1]
* **Baseline Observed Fraud Rate:** 2.71% (1,357 fraudulent vs. 48,775 legitimate transactions)[cite: 1]
* **Core Toolkit:** Python, Pandas, NumPy, Matplotlib, Seaborn[cite: 1]

---

## Key Findings & Feature Insights

### 1. The Candidate Fraud Fingerprint (5.75× Lift)
Combining an elevated digital transaction channel with an unaged card footprint and high short-term transaction velocity isolates an acute risk concentration[cite: 1]:
$$\text{ProductCD} == \text{'C'} \quad\land\quad \text{D1} == 0 \quad\land\quad \text{C1} \ge 2$$

* **Baseline Fraud Rate:** 2.71%[cite: 1]
* **Fingerprint Observed Fraud Rate:** **15.56%** (1,677 matching transactions)[cite: 1]
* **Observed Lift:** **5.75×** over baseline[cite: 1]
* **Business Limitation:** Despite the substantial lift, **84.44% of flagged transactions remain legitimate**[cite: 1]. Applying an automated hard block would trigger roughly 5.4 false declines per true fraudster intercepted[cite: 1]. This rule serves as a trigger for step-up verification (e.g., 3DS/SMS OTP) rather than outright transaction cancellation[cite: 1].

### 2. Refuting Intuitive Assumptions: Address Verification Flags (`M5`)
* **Conventional Assumption:** Mismatched customer and bank verification details (`M5 == 'F'`) should indicate higher fraud probability[cite: 1].
* **Observed Reality:** Mismatched records exhibit a lower fraud rate of **2.04%** (0.75× lift), whereas matching records (`M5 == 'T'`) show an elevated fraud rate of **4.35%** (1.61× lift)[cite: 1].
* **Root Cause:** Selection bias—`M5` is missing for 64.3% of transactions and is selectively captured during specific merchant checkout flows[cite: 1].

### 3. Entity Symmetry & Velocity Analysis (`P_emaildomain` vs. `R_emaildomain`)
* Restricting to transactions where both purchaser and recipient email domains are populated:
  * **Identical Domains (`P == R`):** 7.12% fraud rate (**2.63× lift**) across 8,667 transactions[cite: 1].
  * **Different Domains (`P != R`):** 1.34% fraud rate (0.50× lift)[cite: 1].
* **Behavioral Rationale:** Scripted checkout attacks and card-testing bots frequently generate throwaway credentials across standard public providers (e.g., `@gmail.com`) for both sender and recipient inputs[cite: 1].

### 4. Feature Space Diagnostics
* **Velocity Features (`C1`):** Severely right-skewed (skewness: 8.65)[cite: 1]. Median count for legitimate transactions is 1.0 vs. 2.0 for fraud; the 95th percentile reaches 60.0 for legitimate transactions vs. 627.2 for fraud[cite: 1].
* **Timedelta Features (`D1`):** Mean elapsed days for legitimate transactions is 82.5 days vs. 41.2 days for fraudulent transactions, underscoring the vulnerability of newer accounts[cite: 1].
* **V-Features (`V1–V10`):** Uniform missingness at 69.38%[cite: 1]. High collinearity exists between paired counters (e.g., $r = 0.92$ between `V4` and `V5`), while `V10` shows the greatest mean separation between legitimate (0.484) and fraudulent (0.193) transactions[cite: 1].

---

## Summary Comparison

| Segment / Condition | Transaction Volume | Fraud Rate | Lift over Baseline | Operational Recommendation |
| :--- | :--- | :--- | :--- | :--- |
| **Full Dataset Baseline**[cite: 1] | 50,132[cite: 1] | 2.71%[cite: 1] | 1.00×[cite: 1] | Baseline monitoring[cite: 1] |
| **Commercial Product (`ProductCD == 'C'`)**[cite: 1] | 4,939[cite: 1] | 10.06%[cite: 1] | 3.72×[cite: 1] | Prioritize velocity screening[cite: 1] |
| **Fingerprint Match (`ProductCD=='C' & D1==0 & C1>=2`)**[cite: 1] | 1,677[cite: 1] | **15.56%**[cite: 1] | **5.75×**[cite: 1] | Route to dynamic step-up 2FA[cite: 1] |
| **Symmetric Emails (`P_email == R_email`)**[cite: 1] | 8,667[cite: 1] | 7.12%[cite: 1] | 2.63×[cite: 1] | Device fingerprint & IP check[cite: 1] |
| **Match Verification (`M5 == 'T'`)**[cite: 1] | 8,090[cite: 1] | 4.35%[cite: 1] | 1.61×[cite: 1] | Counter-intuitive; avoid hard block[cite: 1] |

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
