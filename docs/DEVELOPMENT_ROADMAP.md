# CommerceIQ --- Development Roadmap

**Version:** 0.1\
**Status:** Proposed execution plan\
**Target duration:** 4 weeks\
**Working principle:** Complete and verify each phase before moving to
the next. The schedule is a target, not a reason to skip validation.

## Phase 0 --- Project Foundation

**Objective:** Establish project scope, technical decisions, repository
conventions and acceptance criteria.

Tasks: - \[ \] Confirm project name and target roles. - \[ \] Finalise
project specification. - \[ \] Finalise four-week roadmap. - \[ \]
Confirm Olist dataset source and inspect license terms. - \[ \] Create
GitHub repository. - \[ \] Create initial folder structure. - \[ \] Add
`.gitignore` for raw data, processed data, environments, secrets and
notebook checkpoints. - \[ \] Add README skeleton and data acquisition
instructions. - \[ \] Record Python and PostgreSQL versions. - \[ \]
Create initial project commit.

Deliverables: - `docs/PROJECT_SPEC.md` - `docs/DEVELOPMENT_ROADMAP.md` -
`README.md` - `.gitignore` - Initial repository structure

Exit criteria: - Project scope and exclusions are explicit. - No raw
dataset or credentials are committed. - Repository can be cloned and its
setup instructions are understandable.

## Phase 1 --- Dataset Acquisition and Understanding

**Objective:** Understand the source data before designing
transformations.

Tasks: - \[ \] Download the nine Olist CSV files from the official
Kaggle dataset page. - \[ \] Record dataset version, download date,
source and license. - \[ \] Store files under `data/raw/`. - \[ \]
Verify expected files and file sizes. - \[ \] Inspect dimensions,
columns, data types and sample rows. - \[ \] Profile nulls, duplicates,
key uniqueness and date ranges. - \[ \] Identify each table's grain. -
\[ \] Validate key relationships and one-to-many joins. - \[ \] Create a
data dictionary and initial ERD. - \[ \] Document data-quality findings
and exclusions.

Deliverables: - `notebooks/01_data_understanding.ipynb` -
`docs/DATA_DICTIONARY.md` - `docs/data_quality_profile.md` - Initial ERD

Exit criteria: - Every source table has a documented grain and key
strategy. - Important quality issues and join risks are known. - No
assumptions about row counts or null handling remain undocumented.

## Phase 2 --- PostgreSQL and Analytical SQL

**Objective:** Build a reliable relational foundation and demonstrate
practical SQL proficiency.

Tasks: - \[ \] Create the project database and schema. - \[ \] Create
staging tables with safe initial types. - \[ \] Import CSV files
reproducibly. - \[ \] Create typed/clean analytical tables or views. -
\[ \] Add appropriate primary keys, foreign keys and indexes after
validation. - \[ \] Write row-count, null, uniqueness and orphan
checks. - \[ \] Build SQL queries for revenue, order trends, categories,
sellers, regions, payments, delivery and reviews. - \[ \] Use CTEs,
window functions and conditional aggregation. - \[ \] Reconcile key SQL
metrics with independent Python calculations. - \[ \] Document metric
definitions and query assumptions.

Deliverables: - `sql/01_create_schema.sql` -
`sql/02_data_quality_checks.sql` - `sql/03_business_analysis.sql` -
`sql/04_analytical_views.sql` - `docs/METRIC_DEFINITIONS.md`

Exit criteria: - Data loads consistently. - Data-quality checks are
explainable. - Analytical queries run and key totals reconcile. - Join
multiplication is controlled and documented.

## Phase 3 --- Exploratory Data Analysis and Statistics

**Objective:** Answer the core business questions with descriptive and
inferential analysis.

Tasks: - \[ \] Analyse order and revenue trends. - \[ \] Analyse AOV,
item revenue and freight. - \[ \] Compare categories, sellers and
regions. - \[ \] Analyse payment methods and installments. - \[ \]
Analyse delivery duration and late-delivery rate. - \[ \] Analyse
review-score distributions. - \[ \] Formulate a small number of testable
hypotheses. - \[ \] Select appropriate tests after checking
assumptions. - \[ \] Report effect sizes and uncertainty where
appropriate. - \[ \] Document limitations and avoid causal claims. - \[
\] Produce a concise findings summary.

Deliverables: - `notebooks/03_eda_and_statistics.ipynb` - Charts and
summary tables - Draft business findings

Exit criteria: - Every chart has a defined population, metric and
unit. - Statistical tests include hypotheses, assumptions and
interpretation. - Findings are supported by reproducible calculations.

## Phase 4 --- Customer Intelligence: RFM and K-Means

**Objective:** Create interpretable customer segments from historical
purchasing behaviour.

Tasks: - \[ \] Define eligible orders and a fixed observation window. -
\[ \] Select a snapshot date based on data coverage and completeness. -
\[ \] Aggregate customer activity using `customer_unique_id`. - \[ \]
Calculate Recency, Frequency and Monetary features. - \[ \] Inspect
one-time buyers, skew and outliers. - \[ \] Establish RFM score-based
baseline segments. - \[ \] Transform and scale features as justified. -
\[ \] Compare K-Means cluster counts using elbow/inertia and
silhouette. - \[ \] Profile clusters in original business units. - \[ \]
Name segments only after interpreting their profiles. - \[ \] Test
segment stability/sensitivity where time permits. - \[ \] Prepare
segment-level dashboard outputs.

Deliverables: - `notebooks/04_customer_segmentation.ipynb` -
Customer-level RFM table - Cluster assignments and segment profiles -
Methodology and limitations

Exit criteria: - Snapshot date and eligible population are documented. -
Segmentation is reproducible. - Cluster selection is justified beyond
visual appearance. - Segment labels are supported by measured profiles.

## Phase 5 --- Predictive Intelligence: Late-Delivery Classification

**Objective:** Predict late delivery using only information available at
a clearly defined operational point.

Tasks: - \[ \] Define the prediction point (recommended: carrier
handoff/dispatch if data coverage supports it). - \[ \] Define the
target using actual and estimated delivery timestamps. - \[ \] Restrict
the initial labelled population to delivered orders with valid
timestamps. - \[ \] Document excluded and unlabelled orders. - \[ \]
Create features available by the prediction point. - \[ \] Review every
feature for target leakage. - \[ \] Create chronological
train/validation/test splits where feasible. - \[ \] Establish a simple
baseline. - \[ \] Train Logistic Regression. - \[ \] Train Random
Forest. - \[ \] Optionally test gradient boosting if time permits. - \[
\] Evaluate precision, recall, F1, ROC-AUC and confusion matrix. - \[ \]
Inspect false positives and false negatives. - \[ \] Explain threshold
selection and operational trade-offs. - \[ \] Document model limitations
and reproducibility.

Deliverables: - `notebooks/05_late_delivery_prediction.ipynb` - Reusable
feature/model pipeline under `src/` - Model comparison table and
evaluation plots - `docs/MODEL_CARD.md`

Exit criteria: - Prediction point and feature availability are
explicit. - No post-outcome fields are used as features. - Baseline and
candidate models are compared on held-out data. - Error analysis and
limitations are documented.

## Phase 6 --- Power BI Dashboard and Business Recommendations

**Objective:** Present validated findings to a non-technical business
audience.

Tasks: - \[ \] Prepare clean, appropriately-grained dashboard tables. -
\[ \] Define relationships and measures. - \[ \] Build Executive
Overview page. - \[ \] Build Sales & Product Performance page. - \[ \]
Build Customer Intelligence page. - \[ \] Build Delivery & Experience
page. - \[ \] Add predictive model insights where useful. - \[ \] Add
filters, drilldowns and explanatory labels. - \[ \] Reconcile dashboard
KPIs against SQL outputs. - \[ \] Write recommendations with supporting
evidence. - \[ \] State historical-data and model limitations.

Deliverables: - Power BI `.pbix` file - Dashboard screenshots for
README - `reports/business_insights.md`

Exit criteria: - KPI definitions match SQL and Python calculations. -
Dashboard filters work as intended. - Each recommendation is traceable
to a finding. - No unsupported claims of causal impact or actual
business improvement are made.

## Phase 7 --- Testing, Documentation and Portfolio Release

**Objective:** Make the project understandable, reproducible and
interview-ready.

Tasks: - \[ \] Re-run the workflow from a clean environment. - \[ \]
Test data loading and SQL scripts. - \[ \] Test feature-generation and
modelling code. - \[ \] Check notebook execution order and remove stale
outputs where appropriate. - \[ \] Finalise README with problem,
dataset, architecture, methodology, findings and screenshots. - \[ \]
Document setup and execution instructions. - \[ \] Add limitations and
ethical/data-use notes. - \[ \] Check Git history and remove accidental
data/secrets. - \[ \] Prepare resume bullets based on actual measured
results. - \[ \] Prepare interview explanations for SQL, statistics,
clustering, classification and dashboard design. - \[ \] Tag the
portfolio release.

Deliverables: - Complete GitHub repository - Reproducibility
instructions - Dashboard and findings report - Final resume bullets and
interview notes

Exit criteria: - A reviewer can understand the project without a
personal walkthrough. - Key results can be reproduced from documented
steps. - Resume claims are supported by actual project outputs.

## Four-Week Schedule

  -----------------------------------------------------------------------
  Week                    Phases                  Primary outcome
  ----------------------- ----------------------- -----------------------
  Week 1                  0, 1, 2                 Repository, dataset
                                                  profile, PostgreSQL and
                                                  SQL analysis

  Week 2                  3, 4                    EDA, statistical
                                                  findings, RFM and
                                                  K-Means

  Week 3                  5                       Leakage-safe
                                                  late-delivery model and
                                                  evaluation

  Week 4                  6, 7                    Power BI,
                                                  recommendations,
                                                  documentation and
                                                  release
  -----------------------------------------------------------------------

## Scope Priorities

**Must complete:** - Data profiling and SQL analysis. - EDA with
defensible metrics. - RFM and K-Means segmentation. - Late-delivery
prediction with leakage review. - Power BI report. - Reproducible README
and limitations.

**Optional if time remains:** - Gradient boosting model comparison. -
Segment stability analysis. - Advanced geographic analysis using
aggregated ZIP-prefix coordinates. - Additional statistical sensitivity
checks.

**Explicitly out of scope for the four-week MVP:** - Production web
application or API. - Real-time streaming. - Cloud deployment. - Full
MLOps infrastructure. - Separate marketing-funnel integration. - Claims
of causal uplift or actual operational savings.

## Working Protocol

At the end of each phase: 1. Run the defined validation checks. 2.
Review changed files and generated outputs. 3. Record issues and
decisions. 4. Update this roadmap with actual status. 5. Commit only a
coherent, verified milestone.

Do not mark a phase complete because code has been written; mark it
complete when its exit criteria pass.
