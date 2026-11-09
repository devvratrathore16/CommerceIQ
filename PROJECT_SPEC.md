# CommerceIQ --- Project Specification

**Version:** 0.1\
**Status:** Phase 0 --- Foundation\
**Project type:** End-to-end data analytics and applied machine
learning\
**Target roles:** Data Analyst, Junior Data Scientist, Business
Intelligence Analyst\
**Planned duration:** 4 weeks

## 1. Executive Summary

CommerceIQ is an end-to-end analytical project built around the
Brazilian E-Commerce Public Dataset by Olist. It will examine revenue
performance, customer purchasing behaviour, retention, seller and
delivery operations, and customer experience.

The project will combine: - Relational data modelling and analytical SQL
in PostgreSQL. - Data cleaning, exploratory analysis and statistical
testing in Python. - Customer segmentation using RFM features and
K-Means clustering. - Late-delivery classification using supervised
machine learning. - An interactive Power BI report. - Evidence-backed
business recommendations and reproducible documentation.

The project is an analytical decision-support prototype, not a
production e-commerce platform or a live operational prediction service.

## 2. Business Context

Olist is a Brazilian marketplace connecting sellers with customers. The
public dataset contains anonymised historical order, customer, product,
seller, payment, delivery and review information.

CommerceIQ will investigate four broad management questions:

1.  **Revenue:** What drives order value and revenue across time,
    product categories, sellers and regions?
2.  **Customers:** Which customers demonstrate higher historical value,
    and how do purchasing and repeat-order patterns differ?
3.  **Operations:** Where do delivery delays occur, and which order
    characteristics are associated with late delivery?
4.  **Experience:** How are delivery outcomes and review scores related,
    and what should the business investigate further?

The analysis must distinguish observed associations from causal claims.
The dataset is observational and historical; it cannot by itself prove
that a factor caused a business outcome.

## 3. Objectives and Success Criteria

### 3.1 Analytical objectives

-   Build a documented relational database from the source CSV files.
-   Validate row counts, keys, nulls, duplicates and join cardinalities.
-   Produce reproducible SQL analyses using joins, CTEs, aggregations
    and window functions.
-   Conduct exploratory and statistical analysis with clearly stated
    assumptions.
-   Create customer-level RFM features and interpretable customer
    segments.
-   Train and evaluate a late-delivery prediction model without target
    leakage.
-   Deliver a multi-page Power BI report with clearly defined KPIs.
-   Translate findings into specific, evidence-backed recommendations.

### 3.2 Completion criteria

The project is complete when: - All included source files are documented
and their provenance is recorded. - Data-quality checks and
transformation steps can be rerun. - Analytical tables have documented
grain and metric definitions. - SQL scripts run against the project
database. - RFM and clustering outputs include feature definitions,
scaling, cluster selection rationale and segment profiles. - The
delivery model uses only features available at its explicitly defined
prediction point. - Model evaluation includes a time-aware holdout where
feasible, baseline comparison, precision, recall, F1, ROC-AUC and
confusion matrix. - The Power BI report contains validated KPIs and
communicates limitations. - The README explains setup, data acquisition,
execution order, findings and limitations.

## 4. Dataset Selection and Provenance

**Primary dataset:** Brazilian E-Commerce Public Dataset by Olist\
**Publisher:** Olist, distributed through Kaggle\
**Dataset page:**
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce\
**Expected scope:** Approximately 100,000 orders and nine CSV files,
covering historical Brazilian marketplace activity from 2016--2018.

Expected source files: - `olist_orders_dataset.csv` -
`olist_order_items_dataset.csv` - `olist_order_payments_dataset.csv` -
`olist_order_reviews_dataset.csv` - `olist_customers_dataset.csv` -
`olist_products_dataset.csv` - `olist_sellers_dataset.csv` -
`olist_geolocation_dataset.csv` -
`product_category_name_translation.csv`

**Important:** On download, record the Kaggle dataset version, download
date, source URL and the license displayed on the dataset card. Follow
its attribution and use conditions. Do not redistribute the raw files in
the Git repository. The repository should provide download instructions
instead.

### 4.1 Scope control

The core project will use the nine Olist files above. The separate Olist
Marketing Funnel dataset is excluded from the four-week MVP unless the
core work is completed early.

The geolocation file is large and contains multiple coordinate rows per
ZIP prefix. It should be aggregated to a documented ZIP-prefix lookup
before joining; joining raw geolocation rows directly can multiply
records.

## 5. Analytical Workstreams

### Workstream A --- Data Engineering and SQL

-   Inventory files, inspect schemas and profile data.
-   Load source data into PostgreSQL staging tables.
-   Create typed analytical tables and relational constraints where
    appropriate.
-   Validate primary keys, foreign keys, nulls, duplicates and row
    counts.
-   Build reusable views or materialised analytical tables.
-   Write business SQL for revenue, orders, products, sellers,
    customers, payments, delivery and reviews.

Expected SQL techniques: - Multi-table joins with cardinality checks. -
CTEs and subqueries. - Window functions (`ROW_NUMBER`, `LAG`, rolling or
cumulative aggregates). - Conditional aggregation and date bucketing. -
Data-quality and reconciliation queries.

### Workstream B --- Business Intelligence and EDA

Core analysis: - Orders and revenue trends by month. - Average order
value (AOV), item revenue and freight value. - Category and seller
performance. - Regional distribution of orders and revenue. - Payment
method and installment patterns. - Delivery duration and late-delivery
rate. - Review-score distribution and relationship with delivery
outcomes.

Metric definitions must be documented. For example: - **Order count:**
distinct eligible `order_id`, not line-item row count. - **Item
revenue:** sum of `price` from order items. - **Freight value:** sum of
`freight_value` from order items. - **AOV:** item revenue divided by
distinct eligible orders, with the eligible order population stated. -
**Late delivery:** actual customer delivery timestamp is later than
estimated delivery timestamp, for delivered orders with both timestamps
present.

Do not describe item revenue plus freight as company profit. The dataset
does not provide a complete cost structure or profit data.

### Workstream C --- Customer Intelligence: RFM + K-Means

**Goal:** Describe historical customer purchasing patterns and create
interpretable customer segments.

Customer identity: - Use `customer_unique_id` to identify a person
across orders. - Do not use `customer_id` as the person-level key; it is
assigned to an order-specific customer record.

RFM features: - **Recency:** days between a fixed snapshot date and the
customer's most recent eligible purchase. - **Frequency:** number of
distinct eligible orders in the observation window. - **Monetary:**
total item spend across eligible orders in the observation window.

Method: 1. Define and document an observation window and snapshot date
after inspecting the data's date coverage. 2. Use a consistent
eligible-order population; document exclusions. 3. Aggregate to one row
per `customer_unique_id`. 4. Inspect skew, outliers and the large number
of one-time purchasers. 5. Apply suitable transformations and scaling,
documenting choices. 6. Establish a simple RFM scoring baseline. 7. Fit
K-Means and compare plausible cluster counts using inertia/elbow and
silhouette score, alongside interpretability. 8. Profile clusters using
original-unit RFM statistics and customer counts. 9. Assign descriptive
segment names only after examining the profiles.

Caveats: - The dataset is historical and many customers may have only
one observed purchase. - A segment is a statistical grouping, not a
verified customer persona. - K-Means results depend on feature
engineering, scaling, outliers and chosen `k`. - Do not claim customer
lifetime value unless a defensible CLV definition and observation
horizon are implemented. RFM monetary value is historical spend, not
lifetime value.

### Workstream D --- Late-Delivery Prediction

**Goal:** Classify whether an eligible order will arrive after its
estimated delivery date.

Target: - `is_late = 1` when actual customer delivery timestamp is later
than estimated delivery timestamp. - `is_late = 0` when actual delivery
timestamp is on or before estimated delivery timestamp. - Initially
train only on delivered orders with both actual and estimated delivery
timestamps. Report how many records are excluded and why.

Prediction-time definition: - Define the prediction point explicitly
before feature engineering. - Recommended MVP framing: prediction at
carrier handoff / dispatch, if the required timestamp and feature
coverage support it. - Candidate features must be known by that point,
such as purchase calendar features, order item count, order value,
freight value, product attributes, seller/customer state, and elapsed
time from purchase to carrier handoff. - Do not use actual customer
delivery date, actual delivery duration, review score, review text, or
any other post-outcome information as model features. - Verify every
feature's availability at the prediction point and remove leakage-prone
fields.

Models: - Baseline: majority-class or simple rule baseline. - Logistic
Regression. - Random Forest. - Optional gradient boosting only if time
and environment permit.

Evaluation: - Use a chronological train/validation/test split where
feasible, rather than relying only on a random split. - Compare with a
simple baseline. - Report confusion matrix, precision, recall, F1 and
ROC-AUC. - Discuss class imbalance and choose a decision threshold based
on the operational use case. - Inspect false positives and false
negatives. - Explain that model performance on historical 2016--2018
data may not generalise to current operations.

### Workstream E --- Power BI and Business Recommendations

Planned report pages: 1. **Executive Overview:** revenue, orders, AOV,
late-delivery rate, review score and monthly trends. 2. **Sales &
Product Performance:** category, seller, product and regional
breakdowns. 3. **Customer Intelligence:** RFM distribution, segment
sizes, segment value and repeat-purchase patterns. 4. **Delivery &
Experience:** delivery performance, late-delivery patterns and review
outcomes. 5. **Predictive Model Insights:** model evaluation and
interpretable feature importance, if the final model outputs can be
safely and clearly communicated.

Recommendations must cite the analysis supporting them. Distinguish
measured findings from proposed actions and avoid claiming that the
project has improved a real company's KPIs.

## 6. Proposed Data Model

Initial logical entities: - `customers` --- order-specific customer
record plus persistent `customer_unique_id`. - `orders` --- one row per
order. - `order_items` --- one row per item within an order. -
`order_payments` --- one row per payment sequence. - `order_reviews` ---
review records associated with orders. - `products` --- one row per
product. - `sellers` --- one row per seller. - `geolocation_zip` ---
aggregated ZIP-prefix geographic lookup. -
`product_category_translation` --- category translation lookup.

Core relationships: - `customers.customer_id` → `orders.customer_id` -
`orders.order_id` → `order_items.order_id` - `orders.order_id` →
`order_payments.order_id` - `orders.order_id` →
`order_reviews.order_id` - `order_items.product_id` →
`products.product_id` - `order_items.seller_id` → `sellers.seller_id` -
`products.product_category_name` → category translation

The raw tables have different grains. Never join multiple one-to-many
tables and aggregate without checking whether the join multiplies rows.
For example, joining order items directly to multiple payment rows can
inflate item revenue. Build separate order-level or item-level
aggregates before combining them.

## 7. Technology Stack

-   **Language:** Python
-   **Data manipulation:** Pandas, NumPy
-   **Database:** PostgreSQL
-   **SQL access:** SQLAlchemy / psycopg
-   **Statistics:** SciPy and statsmodels where useful
-   **Machine learning:** scikit-learn
-   **Visualisation:** Matplotlib and Seaborn
-   **BI:** Power BI
-   **Analysis environment:** Jupyter notebooks
-   **Version control:** Git and GitHub

Keep the stack focused. Do not add a web application, API, orchestration
platform or cloud deployment to the MVP.

## 8. Repository Structure

``` text
commerceiq/
├── data/
│   ├── raw/                 # ignored by Git
│   ├── processed/            # ignored by Git where appropriate
│   └── README.md             # acquisition instructions
├── docs/
│   ├── PROJECT_SPEC.md
│   ├── DEVELOPMENT_ROADMAP.md
│   ├── DATA_DICTIONARY.md
│   ├── METRIC_DEFINITIONS.md
│   └── MODEL_CARD.md
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_eda_and_statistics.ipynb
│   ├── 04_customer_segmentation.ipynb
│   └── 05_late_delivery_prediction.ipynb
├── sql/
│   ├── 01_create_schema.sql
│   ├── 02_data_quality_checks.sql
│   ├── 03_business_analysis.sql
│   └── 04_analytical_views.sql
├── src/
│   └── commerceiq/
│       ├── config.py
│       ├── data/
│       ├── features/
│       ├── segmentation/
│       └── prediction/
├── dashboards/
│   └── powerbi/
├── reports/
│   └── business_insights.md
├── tests/
├── .gitignore
├── requirements.txt
└── README.md
```

## 9. Risks and Limitations

-   **Historical data:** The dataset represents 2016--2018, not current
    market conditions.
-   **Geographic context:** ZIP-prefix geolocation is approximate.
-   **Profitability:** Cost and margin data are not sufficiently
    available to calculate true profit.
-   **Customer identity:** Use `customer_unique_id` for person-level
    repeat-purchase analysis.
-   **Sparse repeat purchasing:** Many customers may have one observed
    order; interpret segmentation accordingly.
-   **Selection bias:** Delivery prediction labels are available only
    for delivered orders with complete timestamps; cancelled and
    unavailable outcomes require separate treatment.
-   **Target leakage:** All model features must be available at the
    declared prediction point.
-   **Observational analysis:** Associations do not establish causation.
-   **License:** Verify and comply with the current dataset-card license
    and attribution requirements.

## 10. Definition of Done

CommerceIQ is ready for portfolio publication when: - \[ \] Data source,
version, license and acquisition instructions are documented. - \[ \]
Raw data is excluded from Git. - \[ \] PostgreSQL schema and import
process are reproducible. - \[ \] Data-quality and join-cardinality
checks pass or documented exceptions exist. - \[ \] SQL analysis covers
core business questions. - \[ \] EDA and statistical findings are
reproducible. - \[ \] RFM features and K-Means segments are justified
and profiled. - \[ \] Delivery model has a documented prediction point
and leakage review. - \[ \] Model comparison and error analysis are
complete. - \[ \] Power BI pages and KPI definitions are validated. - \[
\] Business recommendations are traceable to findings. - \[ \] README,
limitations and execution instructions are complete.
