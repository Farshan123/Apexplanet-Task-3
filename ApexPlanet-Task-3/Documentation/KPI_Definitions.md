# KPI Definitions

**Project:** ApexPlanet Task 3 – Deep-Dive Analysis & Interactive Dashboarding
**Dataset:** 1,000 sales transactions, 1 Jan 2025 – 1 Jan 2026, 8 Indian cities, 5 categories, 6 products
**Currency:** Indian Rupees (₹)

Five core KPIs were chosen so that together they answer: *How much are we selling, how big is each sale, is it growing, and do customers come back?*

| # | KPI | Formula | Current value |
|---|-----|---------|---------------|
| 1 | Total Revenue | Σ Total_Sales | **₹139,399,440** (₹13.94 crore) |
| 2 | Total Orders | Count of transactions | **1,000** |
| 3 | Average Order Value (AOV) | Total Revenue ÷ Total Orders | **₹139,399** |
| 4 | MoM Revenue Growth % | (Revenue this month − Revenue last month) ÷ Revenue last month | **−2.6%** (Dec vs Nov 2025) |
| 5 | Repeat Customer Rate | Customers with 2+ orders ÷ Unique customers | **5.5%** (52 of 947) |

Supporting measures: Units Sold (5,435), Unique Customers (947), Revenue Share %.

---

## 1. Total Revenue
- **Formula:** `SUM(Total_Sales)`
- **Business rationale:** The headline top-line number. Every other KPI is read relative to it.
- **Read it with:** category, city and month slices to see where revenue comes from.
- **Check:** `Quantity × Unit_Price = Total_Sales` held on all 1,000 rows, so the revenue field is internally consistent.

## 2. Total Orders
- **Formula:** `COUNTROWS(Sales)` (each row = one transaction)
- **Business rationale:** Measures demand volume, separate from basket size. Revenue can rise because of more orders or bigger orders; this KPI separates the two.
- **Note:** `Order_ID` is **not unique** (ORD100050 appears 9 times with different customers, products and dates). Orders are therefore counted by row, not by distinct Order_ID.

## 3. Average Order Value (AOV)
- **Formula:** `Total Revenue ÷ Total Orders`
- **Business rationale:** Shows basket value. Raising AOV (bundles, upsell) is usually cheaper than acquiring new customers.
- **Spread in this data:** 127K (Fashion) to 145K (Grocery) by category; 123K (Gaya) to 154K (Bengaluru) by city.

## 4. MoM Revenue Growth %
- **Formula:** `(Revenue_month − Revenue_previous_month) ÷ Revenue_previous_month`
- **Business rationale:** Shows momentum and seasonality early, so issues (like the Aug–Sep slump) are caught quickly.
- **Rule applied:** January 2026 holds only 5 orders (1 Jan only). It is flagged `Is_Partial_Month = 1` and **excluded from growth calculations**; otherwise it would show a misleading −93%.

## 5. Repeat Customer Rate
- **Formula:** `Customers with ≥ 2 orders ÷ Distinct Customer_IDs`
- **Business rationale:** Proxy for loyalty and retention. This stands in for the churn-rate example in the brief, because a true churn rate needs customers observed across multiple periods and 94.5% of customers here placed only one order.
- **Caveat:** 52 Customer_IDs map to more than one Customer_Name, so some "repeat" customers may be ID collisions rather than real repeat buyers. Treat this KPI as an upper-bound indicator.

---

## DAX Implementation
All measures are in `PowerBI/DAX_Measures.dax`.

## Why not Churn Rate / CLV?
The brief lists Churn Rate as an example, not a requirement. With one year of data and 5.5% repeat buyers, churn would be ≈ 94% by construction and not informative, and lifetime value would equal single-order value. Repeat Customer Rate is the honest equivalent.
