# carbon_emissions

## A. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

![image](https://github.com/user-attachments/assets/ca27b8c5-5117-428c-be32-ff2959162e01)

### A.1. Data structure
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![image](https://github.com/user-attachments/assets/27a738a8-07e0-45d6-becb-a68945a28738)


### A.2. Original dataset
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2014|85|28|11|2014|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2015|85|28|6|2015|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|

## B. Data exploration
### B.1. Retrieve duplicates
```sql
SELECT *, COUNT(*) AS duplicated_count
FROM product_emissions pe 
GROUP BY id, company_id, country_id, industry_group_id,
		year, product_name, weight_kg, carbon_footprint_pcf,
		upstream_percent_total_pcf, operations_percent_total_pcf, downstream_percent_total_pcf
HAVING COUNT(*) > 1;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|duplicated_count|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|----------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|2|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|2|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|2|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|2|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|2|

### B.2. Remove druplicates

## C. Data analysis
### C.1. Which products contribute the most to carbon emissions?
```sql
SELECT product_name, ROUND(AVG(carbon_footprint_pcf),2) AS average_pcf
FROM product_emissions
GROUP BY product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```

|product_name|average_pcf|
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

The leaders of the most carbon emission contribution products belong to Wind Turbine.

### C.2. What are the industry groups of these products?
```sql
SELECT pe.product_name, ig.industry_group
FROM product_emissions pe
JOIN industry_groups ig
ON pe.industry_group_id = ig.id
GROUP BY product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```

|product_name|industry_group|
|------------|--------------|
|Wind Turbine G128 5 Megawats|Electrical Equipment and Machinery|
|Wind Turbine G132 5 Megawats|Electrical Equipment and Machinery|
|Wind Turbine G114 2 Megawats|Electrical Equipment and Machinery|
|Wind Turbine G90 2 Megawats|Electrical Equipment and Machinery|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|Automobiles & Components|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|Materials|
|TCDE|Materials|
|Mercedes-Benz GLE (GLE 500 4MATIC)|Automobiles & Components|
|Mercedes-Benz S-Class (S 500)|Automobiles & Components|
|Mercedes-Benz SL (SL 350)|Automobiles & Components|

Top 10 product comes from heavy industry including: Electrical Equipment and Machinery, Automobiles & Components, and Materials.

### C.3. What are the industries with the highest contribution to carbon emissions?
```sql
SELECT ig.industry_group, ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_pcf
FROM industry_groups ig
JOIN product_emissions pe
ON pe.industry_group_id = ig.id
GROUP BY ig.industry_group
ORDER BY SUM(carbon_footprint_pcf) DESC
LIMIT 10;
```

|industry_group|total_pcf|
|--------------|---------|
|Electrical Equipment and Machinery|9801558.00|
|Automobiles & Components|2582264.00|
|Materials|577595.00|
|Technology Hardware & Equipment|363776.00|
|Capital Goods|258712.00|
|"Food, Beverage & Tobacco"|111131.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|72486.00|
|Chemicals|62369.00|
|Software & Services|46544.00|
|Media|23017.00|

### C.4. What are the companies with the highest contribution to carbon emissions?
```sql
SELECT c.company_name, ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_pcf
FROM companies c 
JOIN product_emissions pe
ON c.id = pe.company_id
GROUP BY c.company_name
ORDER BY SUM(carbon_footprint_pcf) DESC
LIMIT 10;
```

|company_name|total_pcf|
|------------|---------|
|"Gamesa Corporación Tecnológica, S.A."|9778464.00|
|Daimler AG|1594300.00|
|Volkswagen AG|655960.00|
|"Mitsubishi Gas Chemical Company, Inc."|212016.00|
|"Hino Motors, Ltd."|191687.00|
|Arcelor Mittal|167007.00|
|Weg S/A|160655.00|
|General Motors Company|137007.00|
|"Lexmark International, Inc."|132012.00|
|"Daikin Industries, Ltd."|105600.00|

### C.5. What are the countries with the highest contribution to carbon emissions?
```sql
SELECT c.country_name, ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_pcf
FROM countries c 
JOIN product_emissions pe
ON c.id = pe.company_id
GROUP BY c.country_name
ORDER BY SUM(carbon_footprint_pcf) DESC
LIMIT 10;
```

|country_name|total_pcf|
|------------|---------|
|Germany|9778464.00|
|Lithuania|212016.00|
|Greece|191687.00|
|Japan|132012.00|
|Colombia|105600.00|
|South Africa|35505.00|
|France|21364.00|
|Italy|20000.00|
|Ireland|11160.00|
|India|9328.00|

### C.6. What is the trend of carbon footprints (PCFs) over the years?
```sql
SELECT year, ROUND(SUM(carbon_footprint_pcf),2) AS total_pcf
FROM product_emissions
GROUP BY year;
```

![image](https://github.com/user-attachments/assets/3c21b405-a71d-4c5e-86ad-20fe0dd9a3b9)

### C.7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
