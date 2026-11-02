# Deep-Dive Analysis: Customer Value Segmentation

**Approach chosen:** *Segmentation Analysis – use business rules to segment customers* (one of the three examples in the Task 3 brief).

**Why this one?** Cohort analysis needs repeat purchases (only 5.5% of customers have them) and funnel analysis needs a process-stage field (none exists). Segmentation works on every row, and it answers a practical question: **who and what drives the revenue?**

## 1. Business question
> Which order-value segments, products, cities and age groups generate the revenue, and where is the biggest opportunity to grow it?

## 2. Data preparation (cleaning log)
| Issue found | Rows | Action |
|---|---|---|
| Missing `City` | 13 (1.3%) | Labelled `Unknown` (not guessed) |
| Missing `Age` | 20 (2.0%) | Kept; `Age_Group = Unknown`, `Age_Missing_Flag = 1` |
| Repeated `Order_ID` (ORD100050 ×9) | 9 | Kept as separate transactions (all other fields differ); added `Transaction_ID` and `Dup_Order_ID_Flag` |
| `Customer_ID` with different names | 52 IDs | Kept; flagged `ID_Name_Conflict_Flag` |
| Partial month (Jan 2026, 5 orders) | 5 | Flagged `Is_Partial_Month`, excluded from MoM |
| Exact duplicate rows | 0 | – |
| `Quantity × Unit_Price ≠ Total_Sales` | 0 | – |

Output: `Dataset/Cleaned_Sales_Dataset.csv` (1,000 rows, 25 columns).

## 3. Segmentation rules (business rules on order value)
Thresholds are the 33rd, 66th and 90th percentiles of `Total_Sales`, so each tier is defined by a clear, repeatable rule.

| Segment | Rule (order value) | Orders | % Orders | Revenue | % Revenue | AOV |
|---|---|---|---|---|---|---|
| **Premium** | ≥ ₹311,131 (top 10%) | 100 | 10.0% | ₹38.47M | **27.6%** | ₹384,709 |
| **High** | ₹163,053 – ₹311,130 | 240 | 24.0% | ₹54.26M | **38.9%** | ₹226,103 |
| **Medium** | ₹66,959 – ₹163,052 | 330 | 33.0% | ₹36.48M | 26.2% | ₹110,552 |
| **Low** | < ₹66,959 | 330 | 33.0% | ₹10.18M | **7.3%** | ₹30,853 |

Secondary segmentations: Age Group (18–25, 26–35, 36–45, 46–55, 56–65, Unknown), City, Category/Product, Gender.

## 4. Findings

### 4.1 Revenue is concentrated in a third of orders
High + Premium = **34% of orders but 66.5% of revenue**. The bottom 33% of orders (Low) gives just 7.3%. The top 10% of orders alone bring 27.6%, and the top 20% bring 46.8%.

### 4.2 Electronics dominate the top tier
Electronics (Laptop + Mobile) generate 36.4% of total revenue, but **41.4% of Premium-segment revenue**. Laptops have the highest Premium share of any product: **13.5% of Laptop orders are Premium vs 10.0% overall**. Shoes (8.3%) and Chairs (8.8%) have the lowest.

### 4.3 Category baskets are similar; volume is what separates them
AOV only ranges from ₹127K (Fashion) to ₹145K (Grocery). Electronics leads because it has **two products and 354 orders (35.4%)**, not because the basket is bigger. Fashion has the lowest AOV and the lowest revenue share (14.2%).

### 4.4 Seasonality: a clear Q3 dip
| Quarter | Revenue | Orders |
|---|---|---|
| Q1 | ₹35.48M | 256 |
| Q2 | ₹36.12M | 248 |
| **Q3** | **₹30.37M** | 237 |
| Q4 | ₹37.43M | 259 |

Q3 is **~16% below the average of the other three quarters**. August (₹9.45M) and September (₹9.18M) are the two lowest months; October rebounds **+36.2%**. December has the most orders (98) but one of the lowest AOVs (₹125.5K), which suggests more small purchases (possibly discount-driven).

### 4.5 City: volume and basket value tell different stories
- **Patna** leads revenue (₹19.29M, 13.8%); **Bengaluru** has the highest AOV (₹153.9K).
- **Gaya** has the lowest AOV (₹122.9K), and **Delhi** the second lowest (₹128.8K), despite a normal order count (117 and 125), so basket value, not traffic, is the problem there.
- **Pune** is the reverse: fewest orders (99) but a high AOV (₹146.6K), so it needs more reach, not bigger baskets.

### 4.6 Age and gender: small differences
Revenue is spread evenly across age groups (₹22–30M each). The 26–35 group has the most orders (219) but the lowest AOV (₹135.3K); the 18–25 group has the smallest Premium share (8.2%). Male AOV is ₹141.8K vs Female ₹136.9K. These gaps are small and **not statistically tested** here (hypothesis testing is Task 4).

## 5. Recommendations
1. **Protect and grow the High/Premium tiers.** Keep stock and promotion focus on Laptops and Mobiles; use "upgrade" offers to move High-tier buyers into Premium.
2. **Plan a Q3 campaign.** Aug–Sep is the weak spot; targeted offers or bundles could lift it toward the ~₹12M monthly run-rate seen in other months.
3. **Raise basket value in Gaya and Delhi** with bundles or minimum-order incentives; **drive reach in Pune**.
4. **Build loyalty.** Only 5.5% of customers buy twice; a repeat-purchase programme is the largest untapped lever.
5. **Fix data capture:** unique Order_IDs, one Customer_ID per customer, mandatory City and Age.

## 6. Limitations
- One year of data, almost entirely single-purchase customers, so cohort/churn analysis is not meaningful.
- Distributions are quite even (typical of generated or sample data), so many differences are modest. Findings are directional, not proven.
- Segmentation is by order value (transaction level) because customers rarely repeat; with more history, switch to true customer-level RFM.
