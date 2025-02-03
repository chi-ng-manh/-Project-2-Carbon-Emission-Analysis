# -Project-2-Carbon-Emission-Analysis
# ❓ Questions to research
### **1. Which products contribute the most to carbon emissions?**

        SELECT 
        	Product_name,
        	ROUND(avg(carbon_footprint_pcf),2) as 'Average PCF'
        FROM
        	product_emissions
        GROUP BY 
        	Product_name
        ORDER BY 
        	ROUND(avg(carbon_footprint_pcf),2) desc
        Limit 10;
        

### **2. What are the industry groups of these products?**
        SELECT 
        	industry_group
        FROM
        	product_emissions as prod_em
        LEFT JOIN
        	industry_groups as indus_gr
        ON 
        	pro.industry_group_id = indus_gr.id
        GROUP BY 
        	industry_group;
        

### **3. What are the industries with the highest contribution to carbon emissions?**

        SELECT 
        	industry_group as Industry, 
        	ROUND(avg(pro.carbon_footprint_pcf),2) as 'Average PCF'
        FROM
        	product_emissions as pro
        JOIN
        	industry_groups as indus
        ON 
        	pro.industry_group_id = indus.id
        GROUP BY 
        	industry_group
        ORDER BY ROUND(AVG(pro.carbon_footprint_pcf),2) desc
        limit 10;

### **4. What are the companies with the highest contribution to carbon emissions?**
        SELECT 
        	company_name as Company, 
        	round(avg(pro.carbon_footprint_pcf),2) as 'Average PCF'
        FROM
        	product_emissions as pro
        JOIN
        	companies
        ON 
        	pro.company_id = companies.id
        GROUP BY 
        	company_name
        ORDER BY 
        	round(avg(pro.carbon_footprint_pcf),2) desc
        limit 10;

### **5. What are the countries with the highest contribution to carbon emissions?**
        SELECT 
        	country_name as Country, 
        	round(avg(pro.carbon_footprint_pcf),2) as 'Average PCF'
        FROM
        	product_emissions as pro
        JOIN
        	countries
        ON 
        	pro.company_id = countries.id
        GROUP BY 
        	country_name
        ORDER BY 
        	round(avg(pro.carbon_footprint_pcf),2) desc
        limit 10;

### **6. What is the trend of carbon footprints (PCFs) over the years?**
        WITH Yearly_PCF AS (
            SELECT 
                year,
                AVG(carbon_footprint_pcf) AS avg_pcf
            FROM product_emissions
            GROUP BY year
            )
        SELECT 
            y1.year,
            y1.avg_pcf,
            (y1.avg_pcf - y2.avg_pcf) / y2.avg_pcf * 100 AS pct_change
        FROM Yearly_PCF y1
        LEFT JOIN Yearly_PCF y2 ON y1.year = y2.year + 1
        ORDER BY y1.year;
        
### **7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?**
        WITH Industry_Yearly_PCF AS (
            SELECT 
                pe.year,
                ig.industry_group,
                AVG(pe.carbon_footprint_pcf) AS avg_pcf
            FROM product_emissions pe
            JOIN industry_groups ig ON pe.industry_group_id = ig.id
            GROUP BY pe.year, ig.industry_group
        ),
        Industry_Trend AS (
            SELECT 
                y1.industry_group,
                y1.year,
                y1.avg_pcf,
                (y1.avg_pcf - y2.avg_pcf) / y2.avg_pcf * 100 AS pct_change
            FROM Industry_Yearly_PCF y1
            LEFT JOIN Industry_Yearly_PCF y2 
                ON y1.industry_group = y2.industry_group AND y1.year = y2.year + 1
        )
        SELECT 
            industry_group,
            SUM(pct_change) AS total_decrease_pct
        FROM Industry_Trend
        WHERE pct_change < 0 
        GROUP BY industry_group
        ORDER BY total_decrease_pct ASC;
