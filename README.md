# Customer-Analytics-Project
Customer RFM Analysis using Power Bi

## Business Overview
Adventure Works is a fictional company  created by Microsoft to provide a realistic business scenario for database and business intelligence (BI) applications. The company operates in the manufacturing and distribution sector, specializing in the production of bicycles and related accessories. It serves a global market, with a strong presence in  America, Europe, and Australia.

## Scope of Analysis
1. **Customer Demographics**
- Understand characteristics of a customer base
2. **Customer Segmentations**
- Divide customers into distinct groups based on shared characteristics or behaviors
- RFM Analysis

**Dataset**:
1 Fact Table containts details of sales and order date and 4 dimensions table ( Customer, Date, Geography, Territory)

## Data Loading and Preparation
After Loading the 5 datasets, I've made some operations before starting devoloping my dashboards:
- Calculate age of customer adding a new calculate columns:
```
Age = DATEDIFF(DimCustomer[BirthDate],TODAY(), YEAR )
```
- Calculate age band of customer adding a new calculate columns:
```
Age band = 
SWITCH(
    TRUE(),
    [Age] >= 45 && [Age] <= 54, "45-54",
    [Age] >= 55 && [Age] <= 64, "55-64",
    [Age] >= 65 && [Age] <= 74, "65-74",
    [Age] >= 75 && [Age] <= 84, "75-84",
    [Age] >= 85 && [Age] <= 94, "85-94",
    ">94"
    )
```
- Add calculate measure to create a RFM table
```
Last Transaction Date = MAXX(FILTER('FactInternetSales','FactInternetSales'[CustomerKey]='FactInternetSales'[CustomerKey]),'FactInternetSales'[OrderDate])
Recency Value = DATEDIFF([Last Transaction Date],TODAY(),DAY)
Frequency Value = DISTINCTCOUNT('FactInternetSales'[SalesOrderNumber])
Monetary Value = CALCULATE(SUM('FactInternetSales'[SalesAmount]),
 ALLEXCEPT('FactInternetSales','FactInternetSales'[CustomerKey]))
```
then calculate the RFM Table to develop my RFM analysis:
```
RFM Table = SUMMARIZE('FactInternetSales', 'FactInternetSales'[CustomerKey], "Recency Value", [Recency Value], "Frequency Value", [Frequency Value], "Monetary Value", [Monetary Value])
```
After that I calculate the score for Recency, Frequency and Monetary, adding the new columns to the table:
```
Recency Score = 
SWITCH(
    True(),
    [Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.20),"5",
    [Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.40),"4",
    [Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.60), "3",
    [Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.80), "2", "1"
)

Frequency Score = 
SWITCH(
    True(),
    [Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.20),"1",
    [Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.40),"2",
    [Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.60), "3",
    [Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.80), "4", "5"
)

Monetary Score = 
SWITCH(
    True(),
    [Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.20),"1",
    [Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.40),"2",
    [Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.60), "3",
    [Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.80), "4", "5"
)

RFM SCORE = 'RFM Table'[Recency Score] & 'RFM Table'[Frequency Score] & 'RFM Table'[Monetary Score]
```

### Dashboards Overview
I created three distinct dashboards:


- **Customer Demographics** - an Overview of customer characteristics
- **Customer Segmentation** - RFM analysis
- **Customer Details** - Customer Details

### Key Insights from the Customer Demographics Dashboards

![image](https://github.com/user-attachments/assets/017e4f9f-2008-464e-8cde-6ac83c8b73e8)

#### General Performance:
- Total Sales: $29,4 Mln
- Number of Customers : 18,484 K
- Sales by Country: USA (9,389 Mln), Australia (9,061 Mln), Europe (8,929 Mln) and Canada (1,977 Mln)
- Sales by Gender: Male 49,54% and Female 50,46%
- Sales by Marital Status: Married 48,27% and Single 51,73%
- Sales by Age: 45-54 (6,4 Mln), 55-64 (11,4 Mln), 65-74 (7,7 Mln), 75-84 (3,1 Mln), 85-94 (0,7 Mln), >94 ( 32,012 K)

### Key Insights from the Customer Segmentation Dashboard

![image](https://github.com/user-attachments/assets/cb7eec4a-0b54-451f-8633-c864f21b6663)

#### General Insights:
Premises about Segmentation:
- About to slept: Customers who have purchased before but have shown declining engagement or inactivity.
- At risk: Customers with reduced frequency of purchase or engagement, indicating potential churn.
- Cannot lose them: High-value customers with recent inactivity or signs of disengagement.
- Champions: Most loyal and valuable customers who frequently purchase and engage with your brand.
- Hibernating Customers: Customers who were once active but have not engaged in a significant period.
- Loyal: Regular customers who consistently engage with and purchase from your brand.
- Lost Customers: Customers who have completely stopped engaging despite prior activity.
- Loyal: Regular customers who consistently engage with and purchase from your brand.
- Need Attention: Customers with decent engagement but show early signs of decreasing interest or activity.
- New Customers: First-time buyers who recently started engaging with your brand.
- Potential Loyalist: Customers with growing interest and engagement, indicating a potential to become loyal.
- Promising: New or occasional customers with good potential based on recent behavior.

**Key Insight**
1.1 Segments with the Highest Number of Customers
- New Customers (3.2K) and Promising (2.6K) are the largest groups.
- Lost Customers (1.4K) and About To Sleep (1.1K) indicate segments at risk of churn.

1.2 Segments Driving the Most Sales
- Champions ($8.3Mln) and At Risk ($7.3Mln) dominate revenue.
- Cannot Lose Them ($3.9Mln) and Loyal ($6.2Mln) are key segments to maintain.
- Lost Customers, Hibernating, and Potential Loyalists (<$0.1Mln) have marginal sales.

1.3 Recency, Frequency, and Monetary Value
- Champions have the highest values across all metrics, confirming their importance.
- At Risk and Cannot Lose Them maintain high monetary value but show signs of risk.
- New Customers and Promising have good recency but low frequency and monetary value.

**Strategic Reccomendtions**

2.1 Loyalty and Retention
-  Champions: Strengthen loyalty programs with exclusive previews or premium discounts.
- Loyal & Cannot Lose Them: Implement VIP programs and personalized offers to keep engagement high.
- At Risk: Use incentives such as discounts or special bundles to reactivate purchases.

2.2 Recovering Customers
- About To Sleep & Hibernating Customers: Targeted reactivation campaigns with promotions and reminders.
- Lost Customers: Investigate reasons for churn through surveys and recovery strategies.

2.3 Growth and Conversions
- Promising & New Customers: Educational campaigns and promotions to encourage repeat purchases.
- Potential Loyalists: Incentives and recognition programs to convert them into loyal customers.

2.4 Conclusions:
- Focus on retaining high-value segments (Champions, Loyal, Cannot Lose Them).
- Act quickly on at-risk customers (At Risk, About To Sleep, Hibernating).
- Increase conversion efforts on emerging segments (New Customers, Promising, Potential Loyalists).

### Customer Details Dashboard

![image](https://github.com/user-attachments/assets/8d883c73-e8f5-4291-959d-abb0e004861b)

