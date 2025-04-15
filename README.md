# Carbon Emissions

## A. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

![image](https://github.com/user-attachments/assets/ca27b8c5-5117-428c-be32-ff2959162e01)

### A.1. Data model
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![image](https://github.com/user-attachments/assets/27a738a8-07e0-45d6-becb-a68945a28738)


### A.2. Data structure
Table 'product_emissions'
```sql
SELECT * FROM product_emissions pe LIMIT 10;
```
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

### B.2. Count duplicates
```sql
SELECT COUNT(product_name) AS total_product,
		COUNT(DISTINCT product_name) AS duplicated_product
FROM product_emissions;
```
|total_product|duplicated_product|
|-------------|------------------|
|1037|661|

*Due to the limitations in database access rights, we could not remove these duplicates. Thus, the research results below will be at a relative level because of this issue.*

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

*Large wind turbines, especially the G128 and G132 models, have the highest carbon footprints due to their heavy material demands. In comparison, vehicles and other products contribute far less to emissions but they are still top contribution.*

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

*Top 10 product totally comes from heavy industry including: Electrical Equipment and Machinery, Automobiles & Components, and Materials.*

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

*Electrical Equipment and Machinery leads in carbon emissions by a large margin, followed by Automobiles & Components and Materials. These top three industries account for the majority of total emissions.*

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

*Gamesa Corporación Tecnológica is the top emitter, with a carbon footprint far exceeding other companies. It’s followed by Daimler AG and Volkswagen AG, making the energy and automotive sectors key contributors.*

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

*Germany dominates in carbon emissions, mainly due to high-output manufacturers like Gamesa. Other countries contribute significantly less, with emissions linked to local production scales and industrial activity levels.*

### C.6. What is the trend of carbon footprints (PCFs) over the years?
Calculate total and average PCFs over the year:
```sql
SELECT year,
	ROUND(SUM(carbon_footprint_pcf),2) AS total_pcf,
	ROUND(AVG(carbon_footprint_pcf),2) AS avg_pcf
FROM product_emissions
GROUP BY year;
```

![image](https://github.com/user-attachments/assets/216aeef7-25f5-4e03-9ef8-41665b5a98ae)
![image](https://github.com/user-attachments/assets/210841ce-a437-4047-9279-b6993f58adc8)

*Carbon footprints (PCFs) peaked sharply in 2015, driven by high-emission projects or industrial activities, then dropped significantly in 2016 and 2017. This trend may reflect real-world efforts toward sustainability, stricter environmental regulations, or a shift to cleaner technologies post-2015 — aligning with global climate initiatives.*

### C.7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
As we concluded above, 2015 witnessed the significant carbon emission. However, when we look deeper into the dataset, there are many empty values and the data in 2015 is the most complete:
```sql
SELECT
    ig.industry_group AS 'industry_group',
    ROUND(SUM(CASE WHEN pe.year = 2013 THEN pe.carbon_footprint_pcf ELSE null END), 2) AS pcf_2013,
    ROUND(SUM(CASE WHEN pe.year = 2014 THEN pe.carbon_footprint_pcf ELSE null END), 2) AS pcf_2014,
    ROUND(SUM(CASE WHEN pe.year = 2015 THEN pe.carbon_footprint_pcf ELSE null END), 2) AS pcf_2015,
    ROUND(SUM(CASE WHEN pe.year = 2016 THEN pe.carbon_footprint_pcf ELSE null END), 2) AS pcf_2016,
    ROUND(SUM(CASE WHEN pe.year = 2017 THEN pe.carbon_footprint_pcf ELSE null END), 2) AS pcf_2017
FROM product_emissions pe
LEFT JOIN industry_groups ig
ON ig.id = pe.industry_group_id
GROUP BY ig.industry_group
ORDER BY
    pcf_2017, pcf_2016, pcf_2015, pcf_2014, pcf_2013;
```
|industry_group|pcf_2013|pcf_2014|pcf_2015|pcf_2016|pcf_2017|
|--------------|--------|--------|--------|--------|--------|
|Household & Personal Products|0.00|||||
|"Pharmaceuticals, Biotechnology & Life Sciences"|32271.00|40215.00||||
|Tobacco|||1.00|||
|Semiconductors & Semiconductors Equipment|||3.00|||
|Retailing||19.00|11.00|||
|Gas Utilities|||122.00|||
|Food & Beverage Processing|||141.00|||
|Telecommunication Services|52.00|183.00|183.00|||
|Trading Companies & Distributors and Commercial Services & Supplies|||239.00|||
|"Textiles, Apparel, Footwear and Luxury Goods"|||387.00|||
|"Consumer Durables, Household and Personal Products"|||931.00|||
|Tires|||2022.00|||
|Containers & Packaging|||2988.00|||
|"Mining - Iron, Aluminum, Other Metals"|||8181.00|||
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|||8909.00|||
|Chemicals|||62369.00|||
|Electrical Equipment and Machinery|||9801558.00|||
|Food & Staples Retailing||773.00|706.00|2.00||
|Semiconductors & Semiconductor Equipment||50.00||4.00||
|Utilities|122.00|||122.00||
|Consumer Durables & Apparel|2867.00|3280.00||1162.00||
|Media|9645.00|9645.00|1919.00|1808.00||
|Energy|750.00|||10024.00||
|Automobiles & Components|130189.00|230015.00|817227.00|1404833.00||
|Software & Services|6.00|146.00|22856.00|22846.00|690.00|
|Commercial & Professional Services|1157.00|477.00||2890.00|741.00|
|"Food, Beverage & Tobacco"|4995.00|2685.00|0.00|100289.00|3162.00|
|Technology Hardware & Equipment|61100.00|167361.00|106157.00|1566.00|27592.00|
|Capital Goods|60190.00|93699.00|3505.00|6369.00|94949.00|
|Materials|200513.00|75678.00||88267.00|213137.00|

Many industry groups have missing values, especially in the early years (2013–2014) and later years (2016–2017), suggesting:
- Incomplete reporting or data collection
- Certain industries only began measuring or disclosing PCFs during specific years

This lack of data weakens year-over-year comparisons and may skew observed trends, especially the sharp spike in 2015. The 2015 peak, for example, may be partly due to a temporary increase in reporting coverage, not just actual emissions.

Therefore, while some patterns are visible, the dataset’s usefulness is limited by missing values and duplicates. For clearer insights, more consistent and complete data across all industries and years is essential. Some insights we can get from this uncompleted dataset:
- After 2015, many sectors show a decline or stabilization, suggesting the impact of sustainability efforts and increased environmental awareness. However, Capital Goods and Materials saw a resurgence in 2017, possibly due to renewed construction or production demands.
- Software & Services and Pharmaceuticals, consistently report low emissions, reflecting their lower carbon intensity compared to heavy manufacturing sectors.
