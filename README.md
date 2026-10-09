# Telco Customer Churn: Drivers and Retention Scenarios

Among 7,043 customers in IBM's sample data, **26.54% churned**. Contract type shows the clearest difference: **42.71%** of month-to-month customers churned, versus **2.83%** of two-year customers. In the logistic regression, month-to-month contracts were associated with **6.16 times the churn odds** of two-year contracts after accounting for the other model features. The test-set ROC-AUC was **0.830**.

The highest-churn customer segment combined shorter tenure and higher monthly charges; **49.18%** of that group churned. Three possible actions are a service check-in for that segment, a contract-options message for a separate month-to-month group, and a payment-method guide for a separate electronic-check group. Under explicitly assumed effects and contact costs, their combined **one-month amount after outreach** ranges from **$1,173.68 to $15,084.02**, with a **$7,987.03 base case**. These are planning scenarios, not measured savings or causal effects.

## Results at a glance

| Analysis | Result |
| --- | --- |
| Data cleaning | 7,043 rows retained; no missing values after handling 11 blank `TotalCharges` values for zero-tenure customers |
| Contract and churn | Chi-square p = 5.86 × 10⁻²⁵⁸; Cramér's V = 0.410 |
| Monthly charges and churn | Mann–Whitney p = 3.31 × 10⁻⁵⁴; rank-biserial = 0.242; churners' median charge was $15.225 higher |
| Logistic regression | Month-to-month versus two-year odds ratio = 6.16 (95% CI 4.27–8.89); test ROC-AUC = 0.830 |
| Churner detection | Standard recall/precision = 0.495/0.607; balanced recall/precision = 0.799/0.503 at a 0.5 threshold |
| Segmentation | Four KMeans groups selected from k = 3–6; observed churn ranges from 4.76% to 49.18% |
| Impact scenario | Low/base/high one-month amounts after outreach = $1,173.68 / $7,987.03 / $15,084.02 |

The four EDA figures, statistical tests, model tables, segment profiles, and cost assumptions are in [the notebook](churn_analysis.ipynb).

## Recommendations

1. **Start with a service check-in for a future active group resembling segment 2.** This group had the highest observed churn. The assumed base case keeps $8,949.99 in one-month charges and costs $2,204.00 to contact, leaving $6,745.99 after outreach.
2. **Pilot a no-discount contract-options message** for month-to-month customers resembling segment 0. Month-to-month customers had the highest contract churn rate. The assumed base amount after $630.00 in contact costs is $684.14; the low case is negative.
3. **Pilot a payment-method guide** for electronic-check customers resembling segment 1. Electronic-check payment was associated with higher churn. The assumed base amount after $294.00 in contact costs is $556.90; the low case is negative.

The target groups are disjoint in the historical data. The notebook gives each group's size, mean charge, assumed contact cost, and low/base/high churn reduction.

## Run the analysis

Use Python 3.12. From the repository folder, install the pinned packages:

```sh
python -m pip install -r requirements.txt
```

Download [IBM's public Telco Customer Churn CSV](https://github.com/IBM/telco-customer-churn-on-icp4d/blob/master/data/Telco-Customer-Churn.csv) into this folder and save it as `WA_Fn-UseC_-Telco-Customer-Churn.csv.xls`. The unusual extension is the local filename expected by the notebook; the contents are CSV text. On macOS or Linux:

```sh
curl -L --fail https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv -o WA_Fn-UseC_-Telco-Customer-Churn.csv.xls
jupyter nbconvert --to notebook --execute --inplace churn_analysis.ipynb
```

Open `churn_analysis.ipynb` to view the charts and outputs. The CSV is ignored by Git.

## Limitations

- This is observational data from one historical snapshot. Associations and model odds ratios do not establish causes.
- The data do not include pricing history, service problems, or experiments that could explain why customers left.
- The historical rows include customers who have already churned. The recommendations imagine future, comparable active groups; these rows are not a live outreach list.
- Outreach costs and churn reductions are assumptions. The impact range covers one month of charges and subtracts contact costs only; it is not profit or a measured treatment effect.
- Model metrics come from one stratified train/test split. KMeans groups depend on the chosen features and fixed seed.
