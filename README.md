# Liquor Sales Analysis using Hadoop & MapReduce

This project focuses on analyzing liquor sales data using a combination of **Hadoop ecosystem tools** and **Python-based MapReduce** with `MRJob`. The goal is to extract business insights from a large dataset (~4.4 GB) through batch processing and structured queries.

---

## Use Case

Understanding retail liquor sales performance across stores, counties, and vendors in Iowa to:

- Identify top-performing stores and products
- Detect seasonal sales trends
- Guide inventory and marketing strategies
- Evaluate vendor contributions and optimize partnerships

---

## Dataset Overview

- **Name**: Iowa Liquor Sales
- **Source**: [data.iowa.gov](https://data.iowa.gov)
- **Size**: 4.4 GB (CSV)
- **Fields**:
  - Store & Vendor info
  - Product Category
  - Date of Sale
  - Volume, Bottle Size, Revenue

---

## Tech Stack
- Python (Pandas, MRJob)
- Google Colab
- AWS RDS (MySQL)
- Amazon EMR with HBase & Sqoop
- Hadoop MapReduce

---
## Workflow Overview

### 1️⃣ Data Cleaning and Preprocessing

- Loaded in chunks with Pandas
- Renamed columns (e.g., `state_bottle_retail` → `bottle_price`)
- Dropped null/duplicate values
- Parsed dates and engineered features: `year`, `month`, `day`, `day_of_week`
- Outlier handling using IQR
- Exported:
  - `Liquor_Sales_df_new1.csv`
  - `liquor_sales.txt` (tab-separated for Hadoop compatibility)

---

### 2️⃣ Data Ingestion (AWS Cloud Setup)

- Cleaned data was uploaded to **AWS RDS (MySQL)**.
- Using **Sqoop**, ingested structured data into **HBase**.
- Verified table creation using **Hive shell**.
- Sample queries to check data types and count records.
- Primary Key in HBase: `Invoice/Item Number`


#### ✅ AWS RDS (MySQL)
- Hosted on: `liquordbinstance.cxzwuat5vlim.us-east-1.rds.amazonaws.com`
- Imported using `LOAD DATA LOCAL INFILE` via MySQL Workbench
- Cleaned schema with renamed columns and correct types

#### ✅ EMR + HBase + Sqoop
- EMR cluster with Hadoop, Hive, HBase, Sqoop, ZooKeeper
- Sqoop used to move data from RDS → HBase using:
```bash
sqoop import \
  --connect jdbc:mysql://<rds-endpoint>:3306/liquor_sales \
  --username admin \
  --password <your_password> \
  --table liquor_sales \
  --hbase-table liquor_sales_hbase \
  --column-family salesinfo \
  --hbase-row-key invoice_item_number \
  --split-by store_number \
  --driver com.mysql.jdbc.Driver
```

---

### Batch Analysis Using MapReduce

Performed in **Google Colab** using the `MRJob` Python library on the cleaned dataset (`liquor_sales.txt`, tab-separated).

#### 🔍 MapReduce Tasks and Insights

| Task | Objective | Key Findings |
|------|-----------|--------------|
| **Task 4** | Compute total revenue by store | Store 4209 led with $1,733.64, followed by 4214 and 4233 |
| **Task 5** | Identify top-selling liquor categories | Top: Vodka 80 Proof, Flavored Vodka, Grape Brandy, Tequila |
| **Task 6** | Perform county-level sales analysis | Scott, Story, and Webster counties had highest revenue |
| **Task 7** | Analyze store performance (by bottle sales) | Store 4209 sold 168 bottles, Store 4214 sold 109 bottles |
| **Task 8** | Detect sales trends over time | Peaks in May 2012, July 2012, and May 2015 |
| **Task 9** | Evaluate vendor performance | Bacardi led with $42,983; Brown-Forman and Sazerac followed |

Each task was implemented as a separate MapReduce job using `MRJob`. Results were interpreted using simple aggregations and sorting logic in reducers.

---

## Recommendations

- 📦 **Inventory Strategy**: Increase stock in high-performing stores like Store 4209 and 4214
- 📈 **Marketing Focus**: Promote high-demand categories like Vodka and Tequila
- 📅 **Seasonal Planning**: Prepare inventory spikes for May–July sales peaks
- 🧹 **Data Quality**: Clean inconsistencies in vendor/county names for scalable insights
- 🧑‍💼 **Vendor Optimization**: Prioritize relationships with Bacardi, Brown-Forman

---


