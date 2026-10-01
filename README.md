# Customer Shopping Behavior Analysis

An end-to-end data analytics project that turns raw retail transaction data into business insights, using **Python**, **SQL (PostgreSQL)** and **Power BI**.

## Business Problem

A retail company has noticed shifting purchase patterns across demographics, product categories and sales channels. Management wants to know which factors (discounts, reviews, seasons, payment preferences, etc.) drive purchases and repeat business.

> **Core question:** *How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?*

The full problem statement is in `Business Problem  Document.pdf`.

## Project Workflow

```
Raw CSV  ->  Python (clean + feature engineering)  ->  PostgreSQL  ->  SQL analysis  ->  Power BI dashboard  ->  Report / Presentation
```

1. **Data Preparation (Python):** clean and transform the raw dataset.
2. **Data Analysis (SQL):** load into PostgreSQL and answer business questions.
3. **Visualization (Power BI):** build an interactive dashboard.
4. **Presentation:** summarize findings and recommendations for stakeholders.

## Repository Structure

```
Project_data_analysis/
├── Business Problem  Document.pdf              # Problem statement and deliverables
├── customer_shopping_behavior.csv              # Raw dataset (3,900 rows x 18 columns)
├── customer_behaviour_analysis.ipynb           # Python: cleaning, feature engineering, DB load
├── customer_analysis.sql                       # 10 SQL business queries (PostgreSQL)
├── customer_behavior_dashboard.pbix            # Power BI dashboard
└── Customer-Shopping-Behavior-Analysis.pptx    # Stakeholder presentation
```

## Dataset

`customer_shopping_behavior.csv` contains **3,900 purchases** with 18 columns:

| Group | Columns |
|-------|---------|
| Customer | Customer ID, Age, Gender, Location, Subscription Status |
| Product | Item Purchased, Category, Size, Color, Season |
| Transaction | Purchase Amount (USD), Discount Applied, Promo Code Used, Payment Method, Shipping Type |
| Behavior | Review Rating, Previous Purchases, Frequency of Purchases |

Only `Review Rating` has missing values (37 rows).

## 1. Data Preparation (Python)

Done in `customer_behaviour_analysis.ipynb` with `pandas`:

- **Explored** the data with `head()`, `describe()` and null checks.
- **Imputed** the 37 missing review ratings with the **median rating of each product category**.
- **Standardized column names** to snake_case (e.g. `Purchase Amount (USD)` -> `purchase_amount`).
- **Created `age_group`**: age quartiles labelled Young Adult, Adult, Middle-aged, Senior.
- **Created `purchase_frequency_days`**: converted text frequencies (Weekly, Monthly, Quarterly, ...) into numbers of days.
- **Dropped `promo_code_used`** because it is identical to `discount_applied` in every row.
- **Loaded the result into PostgreSQL** (database `customer_behaviour`, table `customer`) via SQLAlchemy.

## 2. SQL Analysis

`customer_analysis.sql` answers 10 business questions:

| # | Question |
|---|----------|
| 1 | Total revenue from male vs. female customers |
| 2 | Customers who used a discount but still spent above the average |
| 3 | Top 5 products by average review rating |
| 4 | Average spend: Standard vs. Express shipping |
| 5 | Do subscribers spend more? (customers, average spend, revenue) |
| 6 | Top 5 products by share of purchases with a discount |
| 7 | Customer segments (New / Returning / Loyal) by previous purchases |
| 8 | Top 3 most purchased products within each category |
| 9 | Are repeat buyers (more than 5 previous purchases) likely to subscribe? |
| 10 | Revenue contribution of each age group |

Techniques used: aggregations, subqueries, CTEs, `CASE` expressions and window functions (`ROW_NUMBER() OVER (PARTITION BY ...)`).

## 3. Power BI Dashboard

`customer_behavior_dashboard.pbix` is a single-page interactive dashboard with:

- KPI cards (number of customers, average purchase amount, average review rating)
- Bar/column charts and a donut chart (revenue and customers by category, age group, etc.)
- Slicers for **Category**, **Gender**, **Shipping Type**, **Subscription Status** and **Age Group**

## 4. Key Findings (from the dataset)

- **Average order is about $60**, with purchase amounts ranging from $20 to $100.
- **Clothing is the largest category** by orders and revenue, followed by Accessories, Footwear and Outerwear.
- **43% of purchases used a discount.**
- **Male customers generate more total revenue** than female customers, largely because they make up a larger share of purchases; average spend per purchase is nearly the same (~$60).
- **Shipping type and subscription status have little effect on average spend** (about $58-$61 across all groups), so these are not strong drivers of order size.
- **Most customers are already repeat buyers**: using the SQL segmentation rules, the vast majority fall into the "Loyal" segment (more than 10 previous purchases).

> **Note:** run `customer_analysis.sql` against your database to confirm exact figures. Some numbers in the presentation (e.g. subscriber spend uplift, segment percentages, gender revenue lead) do not match the CSV and should be reviewed before sharing.

## Recommendations

- **Promote subscriptions** by offering benefits that change behavior, since subscribers do not currently spend more per purchase.
- **Build loyalty rewards** to retain the large base of repeat customers.
- **Use discounts selectively**, targeting high-value customers and products where discounts drive volume.
- **Feature top-rated products** in marketing campaigns.
- **Focus on the strongest categories and age groups** when planning inventory and campaigns.

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python (pandas, SQLAlchemy, psycopg2) | Data cleaning, feature engineering, database load |
| Jupyter Notebook | Analysis environment |
| PostgreSQL | Data storage and querying |
| Power BI | Interactive dashboard |
| PowerPoint | Stakeholder presentation |

## How to Run

1. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2-binary jupyter
   ```
2. **Create a PostgreSQL database** named `customer_behaviour`.
3. **Run the notebook** `customer_behaviour_analysis.ipynb`. Update the connection settings (username, password, host, port) in the database cell first. It cleans the data and writes the `customer` table.
4. **Run the queries** in `customer_analysis.sql` using pgAdmin or `psql`.
5. **Open** `customer_behavior_dashboard.pbix` in Power BI Desktop.

> Do not commit real database credentials. Use environment variables or a config file excluded from version control.

## Possible Improvements

- Add visualizations (EDA charts) to the notebook.
- Add statistical tests to check whether differences (e.g. by gender or subscription) are significant.
- Build customer segments with RFM or clustering.
- Move the DB credentials into a `.env` file and add a `requirements.txt`.
