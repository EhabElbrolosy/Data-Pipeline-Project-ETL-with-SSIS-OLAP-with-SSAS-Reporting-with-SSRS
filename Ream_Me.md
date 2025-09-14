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
  
     <img width="1020" height="1038" alt="Desktop Screenshot 2025 09 07 - 04 27 34 98" src="https://github.com/user-attachments/assets/fd2c314c-8929-4738-b1f5-e907d3a6ba5d" />


3. **Data Loading (L):**  
   - Designed and implemented a simple Snowflake Schema (Fact tables + Dimension tables).  
   - Loaded transformed data into the Data Warehouse using SSIS package.
  
     <img width="1920" height="1080" alt="Desktop Screenshot 2025 09 13 - 17 18 02 29" src="https://github.com/user-attachments/assets/84699007-b9f5-420a-af1b-29f4f9039418" />
 

## Next Steps:

4. **Analysis & OLAP (SSAS):**  
   - Built OLAP Cubes to enable slicing, dicing, drill-down, and roll-up operations.  
   - Defined measures, hierarchies, and KPIs for deeper insights.  

5. **Reporting & Visualization (SSRS):**  
   - Designed interactive reports and dashboards.  
   - Delivered insights for decision-making and performance tracking.  

