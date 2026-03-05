# Marketing Analytics Portfolio Project

This repo contains an end-to-end **marketing analytics** mini–case study for the fictional online retailer **ShopEasy**.  
It includes a SQL Server database backup, SQL cleaning/transform queries, a Python sentiment enrichment script for customer reviews, and a Power BI dashboard + supporting DAX calendar table. Two PowerPoint decks document the business case and results.

---

## What’s in here (artifacts)

### Data / Database
- **`PortfolioProject_MarketingAnalytics.bak`**  
  SQL Server backup for the `PortfolioProject_MarketingAnalytics` database (restore locally to reproduce the analysis).

- **`fact_customer_reviews_enrich.csv`**  
  Exported/enriched customer review fact table including:
  `SentimentScore`, `SentimentCategory`, and `SentimentBucket` (1363 rows × 9 columns).

### SQL (data modeling + cleaning)
- **`dim_customers.sql`**  
  Enriches customers by joining `dbo.customers` with `dbo.geography` (country/city).

- **`dim_products.sql`**  
  Adds a **price bucket** (`Low` / `Medium` / `High`) via `CASE` logic.

- **`fact_customer_journey.sql`**  
  Identifies duplicates with a CTE and then shows a **deduped** query using `ROW_NUMBER()`.  
  Also fills missing `Duration` values using a windowed average by `VisitDate`.

- **`fact_customer_reviews.sql`**  
  Normalizes review text by trimming double spaces.

- **`fact_engagement_data.sql`**  
  Normalizes `ContentType`, splits `ViewsClicksCombined` into `Views` and `Clicks`, formats dates, and filters out newsletters.

### Python (text analytics)
- **`customer_reviews_enrichment.py`**  
  Pulls review data from SQL Server, runs **VADER sentiment analysis**, and writes an output CSV:
  - computes `SentimentScore` (compound)
  - combines **text sentiment** + **star rating** into `SentimentCategory`
  - buckets scores into `SentimentBucket`

> Note: the script currently connects to `ALI-LT2024\SQLEXPRESS` using Windows Integrated Auth.

### Power BI
- **`Dashboard.pbix`**  
  The Power BI report/dashboard for KPIs like engagement, conversion trends, and customer feedback.

- **`Calendar DAX Script.txt`**  
  DAX calendar table definition covering **2023-01-01 → 2025-12-31**, with Year/Month/Quarter/weekday columns.

### Slides / Storytelling
- **`Marketing Analytics Business Case (Clean).pptx`** (5 slides)  
  Business problem framing for ShopEasy:
  - reduced engagement
  - decreased conversions
  - high marketing expense vs ROI
  - need for customer feedback analysis

- **`Presentation.pptx`** (7 slides)  
  Results-oriented deck (high-level):
  - decreased conversion rates
  - reduced engagement
  - customer rating + sentiment insights
  - goals and actions

---

## Problem statement (from the deck)
ShopEasy launched multiple online campaigns but saw:
- **Reduced customer engagement**
- **Decreased conversion rates**
- **Marketing spend increasing without expected ROI**
- A need to leverage **customer feedback** (reviews/sentiment) to guide improvements

---

## How to reproduce locally

### 1) Restore the SQL Server database (`.bak`)
1. Open **SQL Server Management Studio (SSMS)**  
2. Restore database from: `PortfolioProject_MarketingAnalytics.bak`  
3. Ensure your restored DB name matches the Python script:
   `PortfolioProject_MarketingAnalytics`

*(If your SQL Server instance/name differs, update the connection string in the Python script.)*

### 2) Run the SQL scripts (optional but recommended)
Execute the SQL files to generate cleaned/enriched views or tables (depending on how you use them):
- `dim_customers.sql`
- `dim_products.sql`
- `fact_customer_journey.sql`
- `fact_customer_reviews.sql`
- `fact_engagement_data.sql`

### 3) Enrich reviews with sentiment (Python)
**Requirements**
- Python 3.9+ recommended
- Packages: `pandas`, `pyodbc`, `nltk`

Install:
```bash
pip install pandas pyodbc nltk
```

Run:
```bash
python customer_reviews_enrichment.py
```

Output:
- `fact_customer_reviews_with_sentiment.csv` (created by the script)

> If you want the script to output exactly the included file name, edit the last line accordingly.

### 4) Open the Power BI dashboard
1. Open `Dashboard.pbix` in Power BI Desktop
2. Create/confirm a Date table using the DAX in `Calendar DAX Script.txt`
3. Refresh data sources (point to your restored SQL Server DB)

---

## Notes / Implementation details

### Sentiment logic
The sentiment pipeline uses **VADER compound score** plus the star `Rating`:
- score > 0.05 → positive leaning (then rating resolves mixed cases)
- score < -0.05 → negative leaning
- otherwise neutral leaning (rating resolves positive/negative vs neutral)

### Customer journey deduping
`fact_customer_journey.sql` demonstrates:
- how to detect duplicates (CTE + `ROW_NUMBER()`)
- how to keep the first record per (CustomerID, ProductID, VisitDate, Stage, Action)
- how to impute missing `Duration` by average duration for that day



---

## Summary
This is a fully built marketing analytics pipeline combining SQL-based cleaning/modeling, Python sentiment enrichment of customer reviews (VADER), and a Power BI dashboard with a dedicated calendar table. The project answers a business case for an e-commerce company facing lower engagement and conversion despite increased campaign spend, and it turns review text + ratings into actionable sentiment categories to guide optimization.

---


