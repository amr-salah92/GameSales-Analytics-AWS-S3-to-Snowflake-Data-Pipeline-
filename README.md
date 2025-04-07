## Table of Contents  
- [Project Name](#project-name)  
- [Project Background](#project-background)  
- [Project Goals](#project-goals)  
- [Data Structure & Initial Checks](#data-structure--initial-checks)  
- [Executive Summary](#executive-summary)  
- [Insights Deep Dive](#insights-deep-dive)  
- [Recommendations](#recommendations)  
- [Technical Details](#technical-details)  
- [Assumptions and Caveats](#assumptions-and-caveats)  

---

## Project Name  
**NextGen Games Inc. Global Sales Optimization**  
**Analysis:** Automated ingestion of 12M+ sales records from AWS S3 into Snowflake to drive regional inventory strategy and marketing ROI analysis.  

---

## Project Background  
**Company Overview**  
NextGen Games Inc. (est. 2010) is a video game publisher with $220M annual revenue (2023), operating in 15 countries. Key business drivers:  
- **Inventory Turnover**: Target 8x/year (current: 6.2x).  
- **Regional Profit Margins**: NA (62% of profits), EU (24%), APAC (14%).  

**Data Context**  
Prior to November 2023, sales data was siloed:  
- **Source 1**: Legacy CSV files in AWS S3 (8M records, 2015–2022).  
- **Source 2**: SQL Server (3M records, 2023).  
- **Source 3**: Manual Excel uploads (1M records, Q3 2023).  

---

## Project Goals  
1. **Automate Daily ETL**: Reduce data latency from 48 hours to 4 hours.  
2. **Standardize Product Catalog**: Fix 18% inconsistent product codes (e.g., "PRD-101" vs "101").  
3. **Enable Regional Profit Analysis**: Identify underperforming markets (e.g., APAC mobile games).  

**Key Analysis Categories**:  
1. Platform performance by region  
2. Genre popularity trends  
3. Seasonal sales volatility  
4. Publisher ROI  

---

## Data Structure & Initial Checks  
**Core Table**: `PRODUCT` (1.2M processed records)  

| Column | Description | Data Issues Resolved | Business Impact |  
|--------|--------------|----------------------|------------------|  
| **Rank_** | Sales rank per region (e.g., #1 in NA). | - 2% duplicates (deleted via `ROW_NUMBER()`).<br>- Negative ranks set to `NULL`. | Critical for regional leaderboard dashboards. |  
| **Name** | Game title (e.g., "CyberQuest VR"). | - 230 missing values filled via publisher API.<br>- 15% casing inconsistencies standardized. | Impacts cross-region title performance tracking. |  
| **Platform** | Console type (e.g., PlayStation). | - 8% legacy platforms (e.g., "Wii U") mapped to "Legacy".<br>- 6 standardized categories. | Determines hardware-specific inventory orders. |  
| **Year_** | Release year (2015–2023). | - 0.5% outliers (e.g., "2030") replaced with `NULL`. | Used for YoY growth calculations. |  
| **Month_** | Release month (e.g., "November"). | - 4% nulls defaulted to "November".<br>- Non-standard entries (e.g., "Q4") split into derived fields. | Avoid direct aggregation; use `Year-Month` combo. |  
| **Genre** | Category (e.g., Action). | - 12% multi-genre entries split into arrays.<br>- 3 custom genres added. | Guides $15M annual development budget. |  
| **Publisher** | Distributor (e.g., Nintendo). | - 20% typos fixed (e.g., "Eletronic Arts" → "EA").<br>- Indie publishers grouped as "Independent". | Key for partner contract renewals. |  
| **Country** | Sales country (e.g., Japan). | - 1.2% region mismatches corrected (e.g., "Mexico" → NA). | Used for tax compliance reporting. |  
| **NA_SALES** | North America revenue. | - 5% negatives flagged as refunds.<br>- Excludes pre-orders. | Primary metric for NA warehouse stock levels. |  
| **GLOBAL_SALES** | Worldwide revenue. | - 10% variance vs. finance reports (under audit). | High-level trend analysis only. |  
| **NA_PROFIT** | NA net profit. | - 15% missing margins imputed at 30%. | Drives NA marketing budget ($8M quarterly). |  

---

## Executive Summary  
**Top Insights for the COO**:  
1. **PlayStation sales dropped 12% YoY** despite 20% higher ad spend – reallocate budget to VR (+38% growth).  
2. **NA holiday sales (Nov–Dec)** exceed annual average by 28% – stock 15% more inventory in Q4.  
3. **Action games dominate NA** (3:1 ROI vs RPGs) but underperform in Japan – prioritize localization for Simulation titles.  

---

## Insights Deep Dive  
### Platform Performance  
- **VR games grew 38% YoY** but only contribute 4% of total revenue.  
- **Mobile platform profits fell 15% YoY** despite 40% higher ad spend.  

---

## Recommendations  
1. **Inventory Management**: Reallocate 5,000 Switch units from NA to EU warehouses.  
2. **Marketing Strategy**: Reduce Mobile ad spend by 20% and reinvest in VR campaigns.  
3. **Product Development**: Localize 5 Simulation games for Japan.  

---

## Technical Details  
- **AWS S3**: Low-cost storage for raw CSV/JSON files.  
- **Snowflake**: Auto-ingestion via Snowpipe.  
- **SQL Processing**: `FILE FORMAT CSV_FORMAT` handled inconsistent encodings.  

---

## Assumptions and Caveats  
1. **Missing `Month_` Values**: 4% assumed to be November.  
2. **Negative Sales**: $1.2M treated as refunds.  
3. **Legacy Platforms**: "Wii U" mapped to "Legacy".  
4. **Margin Imputation**: 30% default margin applied where data was missing.  
