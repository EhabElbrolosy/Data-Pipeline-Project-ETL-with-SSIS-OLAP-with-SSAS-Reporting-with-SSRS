## Project Overview
This project showcases the full lifecycle of building a Data Warehouse from raw data to insights

1. **Data Extraction (E):**  
   - Collected data from multiple sources.  
   - Consolidated all records into a Staging Area for initial processing.  

2. **Data Transformation (T):**  
   - Cleaned datasets by handling NULL values and duplicates.  
   - Applied data type conversions to ensure schema consistency 
   - Standardized values and created calculated fields  
   - Established surrogate keys for dimension tables.

3. **Data Loading (L):**  
   - Designed and implemented a simple Snowflake Schema (Fact tables + Dimension tables).  
   - Loaded transformed data into the Data Warehouse using SSIS package.  

## Next Steps:

4. **Analysis & OLAP (SSAS):**  
   - Built OLAP Cubes to enable slicing, dicing, drill-down, and roll-up operations.  
   - Defined measures, hierarchies, and KPIs for deeper insights.  

5. **Reporting & Visualization (SSRS):**  
   - Designed interactive reports and dashboards.  
   - Delivered insights for decision-making and performance tracking.  

