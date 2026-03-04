# 📊 KPI & DAX Documentation

## Data-Driven Sales Optimization – Textile Wholesale
### 1️⃣ KPI Framework Overview

This project follows a layered KPI architecture to ensure scalability, clarity, and reusability:

* Base Measures → Core financial calculations

* Retailer-Level Measures → Customer behavior tracking

* Derived KPIs → Business performance indicators

* Strategic Metrics → Risk, Growth & Profitability insights

* Time Intelligence Measures → Month-on-Month analysis

This structured approach ensures a clean semantic model and efficient measure dependency.

### 2️⃣ Base Financial Measures

These measures form the foundation for all higher-level KPIs.

#### 🔹 Total Sales

Purpose: Calculates total revenue generated.

Total Sales = SUM(Sales[Selling Price])

#### 🔹 Total Cost Amount

Purpose: Calculates total procurement cost.

Total cost amt = SUM(Sales[Cost Price])

#### 🔹 Profit Amount

Purpose: Calculates absolute profit earned.

Profit amt = [Total Sales] - [Total cost amt]

#### 🔹 Overall Profit Margin (%)

Purpose: Measures profitability relative to cost.

Overall Profit Margin (%) =
DIVIDE([Profit amt], [Total cost amt], 0)

#### 🔹 Total Orders

Purpose: Counts distinct sales transactions.

Total orders = DISTINCTCOUNT(Sales[Order_ID])

#### 🔹 Total Quantity Sold

Purpose: Measures volume sold.

Total Qtn Sold = SUM(Sales[Quantity_Sold])

#### 🔹 Total Retailers

Purpose: Counts unique active retailers.

Total Retailers = DISTINCTCOUNT(Sales[Retailer_ID])

### 3️⃣ Retailer-Level Measures

These measures analyze customer lifecycle and behavior.

#### 🔹 Active Months

Purpose: Measures retailer lifecycle duration.

Active months =
DATEDIFF(
    CALCULATE(MIN(Sales[Order_Date])),
    CALCULATE(MAX(Sales[Order_Date])),
    MONTH
) + 1

#### 🔹 Orders per Retailer

Purpose: Calculates transaction frequency per retailer.

Orders per retailer =
CALCULATE(
    [Total orders],
    ALLEXCEPT(Sales, Sales[Retailer_ID])
)

#### 🔹 Repeat Retailers

Purpose: Identifies retailers with more than one order.

Repeat Retailers =
CALCULATE(
    DISTINCTCOUNT(Sales[Retailer_ID]),
    FILTER(
        VALUES(Sales[Retailer_ID]),
        [Orders per retailer] > 1
    )
)

#### 🔹 Retention Month

Purpose: Calculates total active months used in CLV computation.

Retention Month =
CALCULATE(
    DISTINCTCOUNT('Date Table'[Year-Month]),
    Sales
)

### 4️⃣ Sales Performance & Ranking KPIs
#### 🔹 Average Order Value (AOV)

Purpose: Measures average revenue per transaction.

Average order value =
DIVIDE([Total Sales], [Total orders])

#### 🔹 Orders per Month

Purpose: Measures purchasing frequency.

Order per month =
DIVIDE([Total orders], [Active months])

#### 🔹 Retailer Profit Rank

Purpose: Ranks retailers based on profitability.

Retailer Profit Rank =
RANKX(
    ALLSELECTED(Retailer[Retailer_Name]),
    [Overall Profit Margin (%)],
    ,
    DESC
)

### 5️⃣ Revenue Concentration & Risk Metrics
#### 🔹 Top 1 Retailer Sales
Top 1 retailer =
CALCULATE(
    [Total Sales],
    TOPN(
        1,
        VALUES(Sales[Retailer_ID]),
        [Total Sales],
        DESC
    )
)

#### 🔹 Top 5 Retailer Sales
Top 5 retailer =
CALCULATE(
    [Total Sales],
    TOPN(
        5,
        VALUES(Sales[Retailer_ID]),
        [Total Sales],
        DESC
    )
)

#### 🔹 Sales Concentration Index (SCI)

Purpose: Measures revenue dependency risk.

Sales Concentration Index (SCI) =
DIVIDE([Top 5 retailer], [Total Sales])

#### 🔹 Top Retailer Contribution
Top retailer contribution =
DIVIDE([Top 1 retailer], [Total Sales])

### 6️⃣ Strategic Business KPIs
#### 🔹 Customer Lifetime Value (CLV)

Purpose: Estimates long-term revenue contribution per retailer.

CLV =
[Average order value] *
[Order per month] *
[Retention Month]

#### 🔹 Contribution Margin (%)

Purpose: Measures profitability relative to revenue.

Contribution margin % =
DIVIDE(
    [Total Sales] - [Total cost amt],
    [Total Sales]
) * 100

#### 🔹 Repeat Purchase Rate

Purpose: Measures customer loyalty.

Repeat Purchase Rate =
DIVIDE([Repeat Retailers], [Total Retailers])

### 7️⃣ Time Intelligence Measures
#### 🔹 Current Month Sales (MTD)
Current month Sale =
CALCULATE(
    [Total Sales],
    DATESMTD('Date Table'[Date])
)

#### 🔹 Previous Month Sales
Previous Month Sales =
CALCULATE(
    [Total Sales],
    DATEADD('Date Table'[Date], -1, MONTH)
)

#### 🔹 Sales Growth Momentum (MoM)

Purpose: Tracks short-term revenue trend.

Sales Growth Momentum =
DIVIDE(
    [Current month Sale] - [Previous Month Sales],
    [Previous Month Sales]
)

#### 🔹 First Order Date
First Order Date = MIN(Sales[Order_Date])

#### 🔹 Last Order Date
Last Order Date = MAX(Sales[Order_Date])

### 🎯 Architectural Strength of This Model

✔ Clean measure layering

✔ Strategic KPI alignment with BRD

✔ Use of CALCULATE, FILTER, TOPN, RANKX

✔ Proper Time Intelligence implementation

✔ Risk-based analytics using SCI

✔ Customer lifecycle modeling (CLV)


This documentation demonstrates the ability to design a structured KPI framework rather than isolated calculations.
