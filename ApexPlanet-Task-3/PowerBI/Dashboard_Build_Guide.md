# Power BI Build Guide – `Sales_Analysis_Dashboard.pbix`

> **Important:** a `.pbix` file can only be created inside Power BI Desktop (Windows), so it could not be generated here.
> Everything it needs is ready in this folder. Following this guide takes about 45–60 minutes.
> `Screenshots/01_Dashboard_Preview.png` shows the target layout.

## Step 1 – Load data (5 min)
1. Power BI Desktop → **Get Data → Text/CSV** → `Dataset/Cleaned_Sales_Dataset.csv` → **Transform Data**.
2. Rename the query to **Sales**. Set types: `Order_Date` = Date; `Total_Sales`, `Unit_Price` = Fixed decimal; `Quantity`, `Age`, `Month_Index` = Whole number; flags = Whole number; everything else = Text. **Close & Apply**.
3. Sort-by-column (Column tools): `Year_Month` by `Month_Index`; `Value_Segment` by `Value_Segment_Order`.
4. **View → Themes → Browse for themes** → `Task3_Theme.json`.

## Step 2 – Add measures (5 min)
Home → **Enter data** → create an empty table named **Measures**. Add every measure from `DAX_Measures.dax` (New measure → paste). Format: revenue/AOV as ₹ with 0 decimals; percentages as 0.0%.

## Step 3 – Page 1: "Executive Overview"
| Visual | Fields |
|---|---|
| 5 KPI **Cards** | Total Revenue, Total Orders, Average Order Value, MoM Growth % (Latest Full Month), Repeat Customer Rate |
| **Line chart** – Monthly revenue | X: `Year_Month`; Y: Total Revenue; filter visual: `Is_Partial_Month` = 0 |
| **Clustered bar** – Revenue by Category | Y: Category; X: Total Revenue (data labels: Revenue Share %) |
| **Bar** – Revenue by City | Y: City; X: Total Revenue |
| **Slicers** | Category, City, Gender, Age_Group, Quarter (Tile / dropdown style) |
| **Table (optional)** | Product, Total Orders, Total Revenue, Average Order Value |

## Step 4 – Page 2: "Customer Segmentation Deep Dive"
| Visual | Fields |
|---|---|
| **Clustered column** – Orders vs Revenue share | X: `Value_Segment`; Y: Order Share %, Revenue Share % |
| **100% stacked column** – Segment mix by category | X: `Value_Segment`; Legend: `Category`; Y: Total Revenue |
| **Matrix (heat-map)** | Rows: `City`; Columns: `Category`; Values: Total Revenue; conditional formatting → background colour scale |
| **Bar** – AOV by City | Y: City; X: Average Order Value; add constant line = overall AOV; colour below-average red |
| **Column + line** – Orders vs AOV by month | X: `Year_Month`; Columns: Total Orders; Line: Average Order Value |
| **Scatter** – City volume vs basket | Details: City; X: Total Orders; Y: Average Order Value; Size: Total Revenue |
| **Decomposition tree** | Analyze: Total Revenue; Explain by: Value_Segment → Category → City → Age_Group |
| **Cards** | High + Premium Revenue Share %, Premium Order Rate |

## Step 5 – Interactivity (10 min)
- **View → Sync slicers**: sync Category / City / Age_Group across both pages.
- **Drill-through**: on a hidden page "City Detail", add `City` to drill-through fields.
- **Bookmarks + buttons**: "Overview" ↔ "Deep Dive" navigation buttons.
- **Tooltips**: enable on the line chart to show AOV and Orders.
- **Edit interactions**: turn cross-filtering on between all visuals.

## Step 6 – Save and publish
1. Save as `PowerBI/Sales_Analysis_Dashboard.pbix`.
2. Take screenshots (Win + Shift + S) of each page → save in `Screenshots/` (e.g., `Page1_Overview.png`).
3. **Live link options**
   - *Power BI Service → Publish → File → Publish to web*: needs a Power BI work/school account. Personal Gmail/Outlook accounts are usually not accepted.
   - **Free alternative that meets "link to live dashboard":** Looker Studio (Google account) → connect the CSV via Google Sheets → rebuild the same charts. Mention the tool in your README.
4. Paste the link in `README.md` (placeholder provided).

## Dashboard QA checklist (values must match)
| Check | Expected |
|---|---|
| Total Revenue | ₹139,399,440 |
| Total Orders | 1,000 |
| AOV | ₹139,399 |
| MoM Growth (latest full month) | −2.6% |
| Repeat Customer Rate | 5.5% |
| Premium / High / Medium / Low orders | 100 / 240 / 330 / 330 |
| Revenue share (Premium/High/Medium/Low) | 27.6% / 38.9% / 26.2% / 7.3% |

## 5–7 minute LinkedIn video outline
1. **0:00–0:30** Intro: who you are, the dataset, the objective.
2. **0:30–1:30** The 5 KPIs and why they were chosen (open `KPI_Definitions.md`).
3. **1:30–2:30** Data cleaning decisions (duplicate Order_ID, missing City/Age, partial month).
4. **2:30–5:00** Live dashboard demo: slicers, drill-through, decomposition tree. Highlight "34% of orders = 66.5% of revenue", the Q3 dip, and the Gaya/Delhi AOV gap.
5. **5:00–6:30** Recommendations and limitations.
6. **6:30–7:00** What you learned + link to GitHub repo.
