# Customer Churn Prediction & Retention Strategy

A churn prediction and customer segmentation project on a telecom dataset. The goal was to figure out which customers are most likely to leave, and which ones are actually worth spending retention budget on.

Targeting the three highest ROI customer segments with a $75/customer retention offer is projected to save $2.05M net, on about $387K in spend. Roughly a 5.3x return.

## Dataset

IBM Telco Customer Churn dataset (https://www.kaggle.com/datasets/blastchar/telco-customer-churn). 7,043 customers, 21 features, including account info, billing, subscribed services, and whether the customer churned.

## Data Cleaning

Removed duplicate rows. Fixed the TotalCharges column, which was stored as text because of some blank entries for customers with zero tenure, converted those to null and cast the column to numeric. Dropped the small number of remaining rows with missing values. One-hot encoded categorical columns like contract type and payment method so they could be used in a model.

## Key Findings

Overall churn rate is 26.5%. Month-to-month contract customers churn at 42.7% vs 2.8% for two-year contracts. Electronic check payers churn at 45.3% vs 15-19% for other payment methods. Churned customers had an average tenure of 18 months vs 37.7 months for retained customers.

Contract type, payment method, and tenure turned out to be the strongest signals in the data, and they shaped the retention strategy at the end.

## Model

Trained a Decision Tree and a Random Forest to predict churn probability. Used AUC instead of plain accuracy since churn is imbalanced, a model could look accurate just by predicting no churn for everyone.

Decision Tree: 78% recall, 49% precision, 73% accuracy
Random Forest: 47% recall, 63% precision, 79% accuracy

Went with the Decision Tree even though the Random Forest scored higher overall. It caught way more actual churners, and missing a real churner costs a lot more than a wasted retention offer, so recall mattered more here.

## Customer Segments

K-Means grouped customers into 4 segments based on tenure, spend, and churn risk:

Loyal Budget: 1,773 customers, 9.8 mo avg tenure, $37.50/mo, 40% churn risk
Loyal High-Value: 1,842 customers, 59.9 mo avg tenure, $93.50/mo, 30% churn risk
New & High-Risk: 1,870 customers, 14.5 mo avg tenure, $84.70/mo, 80% churn risk
Mid-Tenure, Low-Risk: 1,547 customers, 47.4 mo avg tenure, $37.80/mo, 10% churn risk

## ROI by Segment

For each segment: expected value at risk = (monthly charge x 12) x churn risk x number of customers, minus the cost of a $75/customer retention offer.

Loyal Budget: $132,975 cost, $319,140 expected savings, $186,165 net
Loyal High-Value: $138,150 cost, $620,017 expected savings, $481,867 net
New & High-Risk: $140,250 cost, $1,520,534 expected savings, $1,380,284 net
Mid-Tenure, Low-Risk: $116,025 cost, $70,173 expected savings, -$45,852 net

Mid-Tenure, Low-Risk actually loses money if targeted, since their churn risk is only 10% and most weren't leaving anyway. That's the main insight here: rank segments by ROI, not just by risk.

## Recommendation

New & High-Risk is the top priority, best ROI by far. Push a 1-year contract upgrade and autopay enrollment, the two strongest churn signals in the data.

Loyal High-Value is second priority. Fewer at risk, but worth a lot more each, so loyalty perks over discounts.

Loyal Budget gets a light touch, lower cost outreach instead of a full incentive.

Mid-Tenure, Low-Risk gets skipped, spending here has a negative return.

## Limitations

The $75 retention cost and customer lifetime value estimate are assumptions, not real company data. The dataset is a single snapshot and doesn't show how risk changes over time for the same customer. This assumes a retention offer works at the rate implied by the risk score, which would need to be tested in practice.

## Tech Stack

Python, pandas, scikit-learn, matplotlib. Decision Tree, Random Forest, K-Means. Built in Google Colab.
