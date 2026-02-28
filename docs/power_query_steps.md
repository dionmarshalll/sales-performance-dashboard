# Data Cleaning & Preparation (Power Query)

This document summarizes the main **Power Query** steps used to clean, transform, and integrate five raw CSV files into an analytics-ready dataset for the Sales Performance Dashboard (2022–2024).

---

## 1) Data Ingestion (Load CSV Files)
Imported the following CSV files into Power BI using **Get Data → Text/CSV**:
- `customer.csv`
- `product_stock_table.csv`
- `sales_data.csv`
- `sales_target.csv` *(available but not used for target vs actual analysis in the current scope)*
- `country_flag.csv` *(used for flag images/visual cues)*

---

## 2) Standardization & Data Type Fixes
For each table, I ensured consistent column data types to prevent aggregation errors:
- Converted date columns to **Date** format (e.g., transaction date / created date).
- Converted numeric fields to **Whole Number / Decimal Number** (e.g., revenue, cost, profit, sessions, views).
- Converted categorical fields to **Text** (e.g., channel, lead source, campaign, country, plan).
- Set currency/decimal precision consistently for financial KPIs.

---

## 3) Data Quality Checks (Validation Rules)
Performed basic data validation to improve reliability:
- Checked for **blank or null** values in primary identifiers (e.g., Customer ID, Product ID, Transaction ID).
- Removed or filtered invalid records where keys were missing (if applicable).
- Standardized inconsistent category values (e.g., trimming spaces, fixing casing for country/channel/lead source).
- Ensured there were no unexpected negative values for revenue/cost where not logically possible (if applicable).

---

## 4) Cleaning Individual Tables

### A) `sales_data` (Fact Source)
Main cleaning and preparation steps:
- Ensured transaction-level grain was consistent (one row = one sales record).
- Cleaned financial columns (Revenue/Cost/Profit) and ensured numeric types were correct.
- Standardized date/time fields and kept a clean `Date` column for calendar relationship.
- Normalized categorical columns used for slicing (channel, lead source, campaign, region/country).
- Kept only relevant columns needed for modeling and visualization.

### B) `customer` (Customer Dimension Source)
- Standardized customer identifiers (Customer ID).
- Cleaned customer attributes (plan type, ratings, feedback labels).
- Removed duplicates on Customer ID where necessary, keeping the latest/most complete record.
- Ensured rating fields and behavior fields (views/sessions/follow-ups) were numeric.

### C) `product_stock_table` (Product/Inventory Source)
- Standardized Product ID and product naming fields.
- Cleaned stock-related columns (stock quantity / availability fields) and ensured numeric types.
- Kept product attributes needed for analysis (product name/category if available).

### D) `country_flag` (Supporting Dimension)
- Ensured Country/Code fields matched naming convention used in sales/customer tables.
- Cleaned image URL/path fields for flag display (if applicable).
- Prepared for joining on Country (or Country Code).

### E) `sales_target` (Out of Scope for Current Dashboard)
- Imported and cleaned as a potential future enhancement.
- Not used in visuals because the current dashboard focuses on **historical performance** only.

---

## 5) Key Transformations (Power Query)
To prepare the dataset for a dimensional model, I applied key transformations such as:
- **Remove Columns**: retained only analysis-relevant attributes to reduce model noise.
- **Remove Errors**: handled type conversion errors and invalid entries.
- **Replace Values**: standardized inconsistent labels (e.g., “social media” vs “Social Media”).
- **Trim/Clean Text**: removed trailing spaces and hidden characters in text fields.
- **Create/Confirm Keys**: ensured consistent key columns exist for relationships:
  - Customer Key (Customer ID)
  - Product Key (Product ID)
  - Date Key (Date)
  - Country Key (Country or Country Code)
  - Campaign / Channel / Lead Source keys (when required)

---

## 6) Table Integration (Merge / Join)
Built integrated dimensions using **Merge Queries** in Power Query:
- Joined `sales_data` with supporting lookup tables when necessary (e.g., country flags by country/country code).
- Ensured that join keys were standardized (same naming/casing) before merging.
- Validated join results to avoid duplicate expansions or many-to-many issues.

---

## 7) Dimensional Modeling Preparation (Creating Dim Tables)
Restructured data into a star schema by creating dimension tables from the cleaned sources:

Common approach:
- Created reference dimension tables (e.g., Product, Customer, Country, Lead Source, Sales Channel, Campaign).
- Removed duplicates in dimension tables to ensure **one unique row per key**.
- Kept only descriptive attributes in dim tables to support slicing/filtering.

Examples (based on model):
- `dim_customer` (Customer ID, Plan, Rating, Feedback, etc.)
- `dim_product` (Product ID, Product Name, Category, etc.)
- `dim_country` (Country/Country Code, Region, Flag)
- `dim_calendar` (Date, Month, Year, Quarter)
- `dim_sales_channel`, `dim_lead_source`, `dim_marketing_campaign` (unique lists)

---

## 8) Calendar Table (Time Intelligence Support)
Created a dedicated `dim_calendar` to support consistent time-based analysis:
- Date, Year, Month, Month Name, Quarter
- Enabled proper relationships and supported DAX time intelligence (e.g., PY, YoY growth)

---

## 9) Relationship Validation (Before Building Visuals)
Before building dashboard visuals, I validated the model to ensure:
- `fact_sales_data` connects to dims via **1-to-many** relationships.
- Filter direction is consistent (dims filter fact).
- No ambiguous relationships / accidental many-to-many connections.
- Slicers work as expected across all pages (Revenue, Customer, Product).

---

## 10) Output
After cleaning and preparation, the final model enables:
- Reliable KPI aggregation (Revenue, Cost, Profit, Conversion Rate, Rating)
- Interactive analysis by channel, lead source, campaign, product, customer plan, and country
- Consistent time-based comparisons across 2022–2024
