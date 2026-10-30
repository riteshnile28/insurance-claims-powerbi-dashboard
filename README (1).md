# Insurance Claims & Policy Analytics Dashboard (Power BI)

3-page interactive Power BI dashboard that analyses an insurance company's policy portfolio, premium collection, payment performance and claim processing, built on a relational data model with DAX measures.

> **Note:** The dataset is synthetic (generated for learning and portfolio purposes). It is modelled on a typical Indian insurance business operating in Maharashtra and nearby states, with amounts in INR.

## Objectives

- Track the overall claim approval rate and how claims are distributed by status, type, policy type and city
- Understand the policy portfolio: policies by type and status, premium and coverage amounts
- Monitor premium collection and payment performance by method, status and over time
- Measure claim processing efficiency and the amount sitting in unresolved claims

## Dataset

| File | Rows | Description |
|---|---|---|
| `Claims.csv` | 900 | Claim records (Jan 2023 - Oct 2026): claim type, claim amount, approved amount, status, settlement date, processing days |
| `Customers.csv` | 500 | Customer demographics: gender, age, city, state, segment (Individual / Family / Small Business), join date, annual income |
| `Policies.csv` | 750 | Policy type (Motor / Health / Life / Home / Travel), start and end date, premium, coverage amount, status, agent |
| `Payments.csv` | 1,984 | Premium payments and claim payouts with payment date, amount, status and method |

Data quality notes:

- Every `CustomerID`, `PolicyID` and `ClaimID` reference matches its parent table, and there are no duplicate rows.
- The only blanks are logical ones: `SettlementDate` is empty for claims that are still Pending or Under Review (or Rejected), and `ProcessingDays` is empty for claims that have not been settled. `ClaimID` in `Payments` is empty for premium payments.
- The latest records are from 2026, so 2026 figures cover a partial year.

## Data Model

Star-style relational model with `Customers` and `Policies` as the core tables:

- `Customers` -> `Policies` (CustomerID)
- `Customers` -> `Claims` (CustomerID)
- `Policies` -> `Claims` (PolicyID)
- `Policies` -> `Payments` (PolicyID)
- `Claims` -> `Payments` (ClaimID, claim payouts only)

This lets a single slicer such as City, Policy Type or Claim Status filter claims, policies and payments together.

## Key DAX Measures

```DAX
Total Claims = COUNTROWS(Claims)
Approved Claims = CALCULATE([Total Claims], Claims[ClaimStatus] = "Approved")
Rejected Claims = CALCULATE([Total Claims], Claims[ClaimStatus] = "Rejected")
Claim Approval Rate % = DIVIDE([Approved Claims], [Total Claims])
Total Approved Amount = SUM(Claims[ApprovedAmount])
Total Policies = COUNTROWS(Policies)
Total Customers = DISTINCTCOUNT(Customers[CustomerID])
Total Coverage Amount = SUM(Policies[CoverageAmount])
Total Premium Amount = SUM(Policies[PremiumAmount])
Total Payment Amount = SUM(Payments[PaymentAmount])
```

## Dashboard Pages

### Page 1: Insurance Claims Analytics (Executive Overview)

<img width="622" height="340" alt="page 1 final" src="https://github.com/user-attachments/assets/ffc36d16-2102-4cec-9ede-0fecc92441e1" />


A one-glance summary of the business and its claims performance.

- **KPI cards:** 900 total claims, 750 total policies, 500 total customers, **55.22%** claim approval rate and INR **6.02M** total approved amount.
- **Claims Trend by Year (line chart):** claims were 222 in 2023, dipped to 211 in 2024, peaked at 260 in 2025 and stand at 207 in 2026 (partial year, data runs to October).
- **Claims by Status (donut):** Approved 497 (55.22%), Rejected 165 (18.33%), Pending 140 (15.56%) and Under Review 98 (10.89%).
- **Claims by Policy Type (bar chart):** Motor leads with 328 claims, followed by Health (227), Life (192), Home (97) and Travel (56).
- **Slicers:** Policy Type and Claim Status filter every visual on the page.

### Page 2: Claim Performance & Analysis

<img width="608" height="343" alt="page 2 final" src="https://github.com/user-attachments/assets/82c03896-3caa-4ee6-baea-c6adf5f05eed" />


Looks at claim status, settlement trends and processing efficiency.

- **Cards:** 900 total claims, 497 approved, 165 rejected and a 55.22% approval rate.
- **Claims by Claim Type:** Accident (144) and Other (138) are the most frequent; Death (106) and Theft (116) are the least frequent.
- **Claim Amount by Status:** approved claims carry the highest value (about INR 7.79M), followed by Rejected (INR 2.75M), Pending (INR 2.27M) and Under Review (INR 1.42M).
- **Monthly Claim Trend:** monthly claim counts swing between roughly 11 and 30 with no strong long-term trend; the highest month is in 2025.
- **Claims by City:** Pune (113) and Jalgaon (112) lead, followed by Mumbai (103) and Thane (101); Navi Mumbai (59) and Solapur (65) are lowest.
- **Slicers:** City and Claim Type filter all visuals on the page.

### Page 3: Policy & Payment Analytics

<img width="610" height="342" alt="page 3 final " src="https://github.com/user-attachments/assets/58ad258a-c5f4-4720-940f-78a7abacee61" />


Shows the policy portfolio, premium collection and payment behaviour.

- **Cards:** 750 policies, INR 827M total coverage, INR 19.45M total payment amount and INR 13.64M total premium amount.
- **Policies by Type (donut):** Motor 270 (36%), Health 205 (27.33%), Life 156 (20.8%), Home 68 (9.07%) and Travel 51 (6.8%).
- **Premium Amount by Policy Type:** Life brings in the most premium (INR 4.95M), then Health (INR 3.95M) and Motor (INR 3.79M); Home and Travel are small.
- **Payments by Method:** UPI is the most used method (479 payments), followed by Credit Card (316) and Net Banking (302); Cheque (160) is the least used.
- **Payment by Status (donut):** 92.04% Paid, 4.39% Failed (87 payments) and the rest Pending (71 payments).
- **Monthly Payment Trend:** payments rose sharply from 2022 to 2023 (about INR 4.64M) and stayed near INR 4.5M a year in 2024 and 2025. 2022 and 2026 are partial years.
- **Policies by Status:** 528 Active, 195 Expired and 27 Cancelled.

## Key Insights

- **Overall:** 900 claims were filed against 750 policies by 500 customers (319 customers have raised at least one claim). The approval rate is **55.22%**, with INR **6.02M** paid out on approved claims.
- **Approval vs rejection:** 165 claims (**18.33%**) were rejected, while another 238 claims (**26.44%**, worth about INR 3.69M) are still Pending or Under Review, so a quarter of all claims are unresolved.
- **Payout ratio:** approved claims were paid INR 6.02M against INR 7.79M claimed, about **77%** of the claimed amount.
- **Processing time:** settled claims took on average about **23.7 days** (range 2 to 45 days), with similar averages across claim types.
- **Policy types:** Motor has the most policies (36%) and the most claims (328), but **Life** generates the most premium (INR 4.95M, about 36% of the total). Approval rates are similar across policy types (about 54% to 59%), with Travel the highest.
- **Geography:** Pune and Jalgaon account for the most claims; Navi Mumbai has the fewest.
- **Customers:** Individuals raise about 61% of claims, Family about 31% and Small Business about 8%.
- **Payments:** 92% of payments succeed, but about 8% are Failed or Pending, which delays premium collection. UPI is the most popular method.
- **Portfolio health:** 70% of policies are Active, 26% Expired and under 4% Cancelled.

## Tools & Skills

Power BI Desktop, Power Query, Data Modeling, DAX, data visualization, dashboard design.

## Files in this Repository

```
├── data/
│   ├── Claims.csv
│   ├── Customers.csv
│   ├── Payments.csv
│   └── Policies.csv
├── 1_executive_overview.png
├── 2_claim_performance.png
├── 3_policy_payment.png
├── Insurance_Claims_Dashboard.pbix
└── README.md
```

## Conclusion

- The business approved **55.22%** of its 900 claims and paid out INR 6.02M, while 18.33% were rejected.
- More than a quarter of claims (238, about INR 3.69M) are still Pending or Under Review, so speeding up the review process is the clearest efficiency opportunity.
- Motor drives claim volume, but Life and Health drive premium income, so claims handling and pricing should be tracked by policy type.
- About 8% of payments are Failed or Pending; following up on these and encouraging UPI and other digital methods can improve collection.
- Priorities: clear the pending-claim backlog, review the high-volume claim cities (Pune, Jalgaon, Mumbai, Thane) and reduce failed payments.

## Author

**Ritesh Nile**
[GitHub](https://github.com/your-username) | [LinkedIn](https://www.linkedin.com/in/ritesh-nile28/)

