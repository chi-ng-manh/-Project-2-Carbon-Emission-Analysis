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
**Result:**
|Product_name|Average PCF|
|------------|-----------|
|Wind Turbine G128 5 Megawats|3718044.00|
|Wind Turbine G132 5 Megawats|3276187.00|
|Wind Turbine G114 2 Megawats|1532608.00|
|Wind Turbine G90 2 Megawats|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|TCDE|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Mercedes-Benz S-Class (S 500)|85000.00|
|Mercedes-Benz SL (SL 350)|72000.00|
        

### **2. What are the industry groups of these products?**
        SELECT 
        	industry_group
        FROM
        	product_emissions as prod_em
        LEFT JOIN
        	industry_groups as indus_gr
        ON 
        	prod_em.industry_group_id = indus_gr.id
        GROUP BY 
        	industry_group;
        
**Result:**
|industry_group|
|--------------|
|"Consumer Durables, Household and Personal Products"|
|"Food, Beverage & Tobacco"|
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|"Mining - Iron, Aluminum, Other Metals"|
|"Pharmaceuticals, Biotechnology & Life Sciences"|
|"Textiles, Apparel, Footwear and Luxury Goods"|
|Automobiles & Components|
|Capital Goods|
|Chemicals|
|Commercial & Professional Services|
|Consumer Durables & Apparel|
|Containers & Packaging|
|Electrical Equipment and Machinery|
|Energy|
|Food & Beverage Processing|
|Food & Staples Retailing|
|Gas Utilities|
|Household & Personal Products|
|Materials|
|Media|
|Retailing|
|Semiconductors & Semiconductor Equipment|
|Semiconductors & Semiconductors Equipment|
|Software & Services|
|Technology Hardware & Equipment|
|Telecommunication Services|
|Tires|
|Tobacco|
|Trading Companies & Distributors and Commercial Services & Supplies|
|Utilities|

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

**Result:**
|Industry|Average PCF|
|--------|-----------|
|Electrical Equipment and Machinery|891050.73|
|Automobiles & Components|35373.48|
|"Pharmaceuticals, Biotechnology & Life Sciences"|24162.00|
|Capital Goods|7391.77|
|Materials|3208.86|
|"Mining - Iron, Aluminum, Other Metals"|2727.00|
|Energy|2154.80|
|Chemicals|1949.03|
|Media|1534.47|
|Software & Services|1368.94|

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
**Result:**
|Company|Average PCF|
|-------|-----------|
|"Gamesa Corporación Tecnológica, S.A."|2444616.00|
|"Hino Motors, Ltd."|191687.00|
|Arcelor Mittal|83503.50|
|Weg S/A|53551.67|
|Daimler AG|43089.19|
|General Motors Company|34251.75|
|Volkswagen AG|26238.40|
|Waters Corporation|24162.00|
|"Daikin Industries, Ltd."|17600.00|
|CJ Cheiljedang|15802.83|

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
**Result:**
|Country|Average PCF|
|-------|-----------|
|Germany|2444616.00|
|Greece|191687.00|
|Colombia|17600.00|
|Lithuania|13251.00|
|Italy|10000.00|
|India|9328.00|
|South Africa|3550.50|
|China|3499.50|
|Canada|3025.00|
|Belgium|2344.00|

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

**Result:**
|year|avg_pcf|pct_change|
|----|-------|----------|
|2013|2399.3190||
|2014|2457.5827|2.42834321|
|2015|43188.9044|1657.37338971|
|2016|6891.5210|-84.04330673|
|2017|4050.8452|-41.21986714|

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
**Result:**
|industry_group|total_decrease_pct|
|--------------|------------------|
|"Food, Beverage & Tobacco"|-196.41717187|
|Capital Goods|-143.61970549|
|Food & Staples Retailing|-107.95931381|
|Technology Hardware & Equipment|-96.55792833|
|Media|-80.10368066|
|Software & Services|-72.81799830|
|Materials|-63.76749666|
|Consumer Durables & Apparel|-60.54991280|
|Automobiles & Components|-19.69193058|
|Commercial & Professional Services|-17.54537597|
|Retailing|-13.15743767|
|Telecommunication Services|-12.01923077|


