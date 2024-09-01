# RFM_Project
**RFM (Recency, Frequency, Monetary)** analysis is a marketing technique used to identify and segment customers based on their purchasing behavior. The acronym stands for:

- **Recency:** How recently a customer has made a purchase.
- **Frequency:** How often a customer makes a purchase within a certain time period.
- **Monetary:** How much money a customer has spent over a specified time period.
## Key Concepts of RFM:
- ***Recency:*** Measures the time elapsed since the customer's last purchase. Customers who purchased more recently are more likely to purchase again.
- ***Frequency:*** Measures the number of times the customer has made a purchase during a specified period. Frequent buyers are often more loyal.
- ***Monetary:*** Measures the total amount of money spent by the customer during a specified period. Customers who spend more are often more valuable.

## Steps to Perform RFM Analysis:
**Data Collection:** Gather transaction data that includes the date of each purchase, customer ID, and the monetary value of each transaction.

**Scoring:** Assign a score to each customer for each of the RFM dimensions. Typically, the scores are ranked (e.g., on a scale of 1 to 5) with 5 being the best score.

**Recency:** The most recent buyers receive higher scores.
**Frequency:** More frequent buyers receive higher scores.
**Monetary:** Customers who spend more receive higher scores.
**Customer Segmentation:** Combine the three scores to create a three-digit RFM score (e.g., 555, 344). Customers can then be segmented into different groups based on these scores.

**Analysis:** Use the RFM scores to identify customer segments that are the most valuable, the most at risk, etc. This can help target marketing efforts more effectively.

**Action:** Develop targeted marketing strategies for different customer segments. For example, high RFM score customers might be targeted with loyalty programs, while low RFM score customers might receive reactivation campaigns.

## Example of RFM Segmentation:
- ***Champions:*** High Recency, High Frequency, High Monetary (e.g., 555) – Your best customers.
- ***Loyal Customers:*** High Frequency, High Monetary, but possibly not as recent.
- ***Potential Loyalists:*** High Recency and Frequency, but lower Monetary.
- ***At Risk:*** High Frequency and Monetary in the past, but low Recency – might be losing interest.
- ***Need Attention:*** Medium scores in all categories – not your best, but important to nurture.
- ***Lost:*** Low Recency, Frequency, and Monetary – customers who haven’t engaged in a long time.

RFM analysis is a powerful tool for understanding customer behavior and optimizing marketing strategies based on data-driven insights.

## Coding Sample
***Dataset***
```python
CustomerID,TransactionDate,Amount
1,2024-08-15,150
1,2024-07-15,200
2,2024-08-05,50
2,2024-07-01,100
3,2024-06-20,300
3,2024-05-15,250
4,2024-08-01,100
4,2024-04-01,75
```
***Pre-install package***
```python
# Install Pandas
!pip install pandas
!pip install datetime
```

***Code Implementation***
```python
import pandas as pd
from datetime import datetime

# Load the data
data = pd.read_csv('transactions.csv')

# Convert TransactionDate to datetime
data['TransactionDate'] = pd.to_datetime(data['TransactionDate'])

# Define the current date for recency calculation
current_date = datetime.now()

# Calculate Recency, Frequency, and Monetary values
rfm_df = data.groupby('CustomerID').agg({
    'TransactionDate': lambda x: (current_date - x.max()).days,
    'Amount': ['count', 'sum']
}).reset_index()

# Rename columns
rfm_df.columns = ['CustomerID', 'Recency', 'Frequency', 'Monetary']

# Assign RFM scores
def rfm_score(df):
    df['RecencyScore'] = pd.qcut(df['Recency'], 5, labels=False) + 1
    df['FrequencyScore'] = pd.qcut(df['Frequency'], 5, labels=False) + 1
    df['MonetaryScore'] = pd.qcut(df['Monetary'], 5, labels=False) + 1
    return df

rfm_df = rfm_score(rfm_df)

# Combine the RFM scores into a single RFM score
rfm_df['RFMScore'] = (rfm_df['RecencyScore'].astype(str) +
                      rfm_df['FrequencyScore'].astype(str) +
                      rfm_df['MonetaryScore'].astype(str))

print(rfm_df)

# Example output
#    CustomerID  Recency  Frequency  Monetary  RecencyScore  FrequencyScore  MonetaryScore RFMScore
# 0           1       30          2        350             5                4              4     544
# 1           2       61          2        150             4                4              3     443
# 2           3       76          2        550             3                4              5     345
# 3           4      120          2        175             1                4              2     412

