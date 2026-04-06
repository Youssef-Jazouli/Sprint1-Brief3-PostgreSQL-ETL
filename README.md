# 🗄️ PostgreSQL ETL Process

A Jupyter notebook that extracts, transforms, and loads cleaned Superstore sales data into a normalized **PostgreSQL** database, then creates analytical views ready for data visualization.

## 📋 Overview

This ETL pipeline reads the cleaned CSV output (`store_new.csv`) from the data cleaning step, normalizes it into a relational schema across 4 tables, loads it into PostgreSQL, and exposes 2 SQL views for downstream analytics and dashboarding.

## 📁 Files

| File | Description |
|------|-------------|
| `PostgreSQL_ETL_Process.ipynb` | Main ETL notebook |
| `store_new.csv` | Cleaned input data (output of `Store_Data_Cleaning.ipynb`) |

## 🔧 Requirements

```bash
pip install pandas sqlalchemy psycopg2-binary
```

A running **PostgreSQL** instance is required with a database named `superstore_db`.

## 🚀 Usage

1. Ensure PostgreSQL is running and `superstore_db` exists
2. Place `store_new.csv` in the same directory as the notebook
3. Run all cells in order:

```bash
jupyter notebook PostgreSQL_ETL_Process.ipynb
```

> **Default connection:** `postgresql://postgres:1919@localhost:5432/superstore_db`  
> Update the `create_engine()` call to match your credentials.

## 🔄 ETL Steps

### 1. Connection
Establishes a SQLAlchemy engine connected to `superstore_db` and confirms the connection.

### 2. Schema Creation (Normalization)
Drops existing tables (if any) and creates a clean normalized schema:

```
customers ──┐
products  ──┼──▶ orders (fact table)
geography ──┘
```

| Table | Primary Key | Description |
|-------|-------------|-------------|
| `customers` | `Customer ID` | Customer name and segment |
| `products` | `Product ID` | Category, sub-category, product name |
| `geography` | `geo_id` (generated) | Country, city, state, postal code, region |
| `orders` | `Row ID` | Sales facts with FK references to the 3 dimension tables |

### 3. Data Extraction & Loading
- Reads `store_new.csv` into a Pandas DataFrame
- Deduplicates and extracts each dimension table
- Generates a surrogate key (`geo_id`) for the geography table
- Merges geography back to resolve `geo_id` for the orders table
- Loads all 4 tables into PostgreSQL using `to_sql()` with `append` mode

### 4. Analytical Views
Creates 2 SQL views for reporting:

| View | Description |
|------|-------------|
| `vue_sales_analysis` | Total sales grouped by `Category` and `Region` |
| `vue_profit_analysis` | Average profit per customer, sorted descending |

### 5. Verification
Queries both views (5 rows each) and displays results to confirm the joins and data load are correct.

## 🗂️ Database Schema

```
customers
  └── Customer ID (PK), Customer Name, Segment

products
  └── Product ID (PK), Category, Sub-Category, Product Name

geography
  └── geo_id (PK), Country, City, State, Postal Code, Region

orders
  └── Row ID (PK)
      ├── Customer ID → customers
      ├── Product ID  → products
      ├── geo_id      → geography
      └── Order ID, Order Date, Ship Date, Ship Mode, Sales, profit
```

## 🔗 Project Pipeline

```
store.csv
    │
    ▼
Store_Data_Cleaning.ipynb
    │
    ▼
store_new.csv
    │
    ▼
PostgreSQL_ETL_Process.ipynb  ──▶  superstore_db
                                        │
                                        ▼
                               store_sales_dashboard.py
```
