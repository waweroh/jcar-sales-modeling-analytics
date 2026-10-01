## 📑 Table of Contents
- [Project Overview](#-project-overview)
- [Business Context & Objectives](#-business-context--objectives)
- [Data Quality & Cleaning Pipeline](#-data-quality--cleaning-pipeline)
- [Data Modeling Architecture](#-data-modeling-architecture)
- [Dashboard Breakdown](#-dashboard-breakdown)
- [Key Insights & Strategic Recommendations](#-key-insights--strategic-recommendations)
- [Tech Stack](#-tech-stack)

---

## 📊 Project Overview
**JCars Sales & Logistics Performance Analytics** is an end-to-end Business Intelligence project that transforms a highly chaotic, unstructured raw dataset from a Kenyan automotive retailer into a robust, interactive, and actionable Power BI dashboard.

This project demonstrates the complete data lifecycle: from advanced data cleaning and ETL pipeline development using Power Query (M) and Python, to architecting a highly optimized Star Schema data model, and finally, delivering strategic business insights through DAX-driven visualizations.

## 🎯 Business Context & Objectives
JCars operates across multiple regions in Kenya, dealing in a diverse inventory of vehicles ranging from everyday sedans to heavy-duty commercial trucks. The business faced challenges with data integrity, margin erosion, and operational bottlenecks.

**Core Analytical Objectives:**
* Track overall sales performance, revenue, and gross profit margins.
* Identify top-performing vehicle makes, models, and types.
* Analyze regional, branch, and sales representative performance.
* Evaluate the ROI of different lead sources and marketing channels.
* Monitor payment collection health and operational delivery bottlenecks.
* Identify high-risk products (high returns/cancellations) and optimize logistics costs.

## 🧹 Data Quality & Cleaning Pipeline
The raw `Jcars_data.csv` dataset was intentionally messy, reflecting real-world operational data entry errors. A rigorous "Cleanse, Flag, and Preserve" philosophy was applied.

**Key Cleaning Challenges Solved:**
* **Advanced Date Parsing:** Built a custom M-code parser to handle mixed formats (Excel serial numbers, DD/MM/YYYY, MM/DD/YYYY, text formats) and corrected transposition typos (e.g., `2026-13-04` ➔ `2026-04-13`). Impossible dates (e.g., "April 31") were safely converted to `null`.
* **Currency Standardization:** Extracted and normalized mixed currency notations (KES, USD, EUR, ZAR, "9.14M") into a single base currency (KES) using static exchange rates.
* **Text Standardization:** Standardized car makes, models, and colors (e.g., "Toyta", "TOYOTA", "Bla", "Gre") to prevent dimension table fragmentation.
* **Logical Anomaly Flagging:** Instead of deleting rows with missing Order IDs or inverted dates (Delivery Date < Order Date), boolean flag columns were created to preserve financial data while isolating metadata errors for management review.

## 🏗️ Data Modeling Architecture
The data model was engineered using a **Star Schema** architecture to maximize the performance of the VertiPaq engine and simplify DAX calculations.

* **Fact Table:** `FactSales` (Contains numeric metrics, foreign keys, and a unique surrogate `SalesKey`).
* **Dimension Tables:**
  * `DimDate` (Continuous calendar table for Time Intelligence)
  * `DimProduct` (Vehicle Make, Model, Year, Fuel, Transmission, Color)
  * `DimGeography` (Region ➔ County ➔ City hierarchy)
  * `DimBranch` (Operational sales yards)
  * `DimCustomer`, `DimSalesRep`, `DimPayment`, `DimLeadSource`

**Relationship Design:**
* All relationships are **One-to-Many (1:*)** with **Single-Direction** filter flow.
* **Dual-Date Handling:** `DimDate` is connected to `FactSales` via an **Active** relationship for `OrderDateKey` and an **Inactive** relationship for `DeliveryDateKey`. The `USERELATIONSHIP` DAX function is used to dynamically analyze delivery performance without breaking the model.

## 📈 Dashboard Breakdown

### 1. Sales Performance Review
* **KPIs:** Total Revenue, Gross Profit, Margin %, and Units Sold.
* **Visuals:** Revenue & Profit by Car Make/Model, Branch Performance, County-level geographic drill-down, and Lead Source contribution (Donut chart).

### 2. Logistics Performance Review
* **Visuals:** Scatter plots analyzing Logistics Cost vs. Revenue to identify inefficient branches. Bar charts evaluating profitability by Vehicle Type (SUV, Sedan, Truck).

### 3. Payment & Operations Analysis
* **Visuals:** Stacked bar charts showing Payment Method vs. Payment Status (Paid, Pending, Partially Paid, Cancelled). Donut charts analyzing revenue distribution by Customer Type (Corporate, NGO, Government, Individual).

## 💡 Key Insights & Strategic Recommendations

### 🔍 Key Findings
* **Toyota Dominance & Margin Mix:** Toyota drives ~45% of total revenue, but European brands (Volkswagen, BMW) yield significantly higher gross profit margins per unit.
* **Marketing ROI:** Instagram and Facebook collectively generate 33.8% of all company revenue, vastly outperforming traditional walk-ins and expensive corporate tenders.
* **Cash Flow Bottlenecks:** A massive volume of revenue is trapped in "Pending" or "Partially Paid" statuses across all payment methods, starving the company of working capital.
* **Operational Inefficiencies:** Branches in the Coast and Rift Valley regions suffer from logistics costs 30-40% above the national average, severely eating into net profitability.

### 🚀 Strategic Recommendations
* **Implement Discount Guardrails:** Configure the ERP to require Director-level approval for discounts >10%. Tie sales commissions to *Gross Profit* rather than *Revenue* to stop margin-killing deals.
* **Pivot Marketing Spend:** Reallocate 20% of the corporate acquisition budget toward a formalized "Customer Referral Program" and double down on Instagram/Facebook ad spend for high-margin SUVs.
* **Audit Regional Logistics:** Conduct an immediate RFP for logistics partners in the Coast and Rift Valley regions to bring logistics costs below 5% of revenue.
* **Quality Control on High-Return Models:** Place a temporary hold on sourcing specific high-return models (e.g., Mazda CX-5) until the procurement team audits the vehicle inspection process.

## 🛠️ Tech Stack
* **Data Extraction & Transformation:** Power Query (M Language), Python (Pandas, dateutil)
* **Data Modeling & Visualization:** Microsoft Power BI Desktop
* **Analytical Calculations:** DAX (Data Analysis Expressions)
* **Source Data:** CSV (Simulated real-world messy data)