## Project Overview

Rising food prices significantly affect the nutritional status and overall health of populations, especially for low-income families. When the cost of nutritious foods like fruits, vegetables, and lean proteins increases, access to a balanced diet becomes difficult. This economic barrier often forces individuals to choose cheaper, less nutritious options, leading to malnutrition, undernourishment, and diet-related diseases. Understanding these dynamics is essential for creating effective policies and interventions that promote food security and public health. This case study explores the impact of food prices on health and proposes solutions to improve food security and nutrition.

<img width="616" alt="Health" src="https://github.com/user-attachments/assets/c3d68215-5986-441c-9d9b-3cfe30e826e7">

## Expected Outcomes
+ Improved understanding of healthy diet affordability.
+ Insights into the relationship between undernourishment and food prices.
+ Identification of key factors contributing to high food prices.
+ Clear assessment of health impacts resulting from unaffordable diets.
+ Evidence-based recommendations for policymakers and stakeholders to reduce food prices, increase diet affordability, and enhance public health.

## Analytics tools
For this inquiry, the following tools were used:
1. Excel - For data import, cleaning, and standardization.
2. MySQL - For data analysis
3. PowerBI - For visualization

## Tables and column names
1. Dailycost of a healthy diet 
  + Entity	
  + Code	
  + Year	
  + Cost of a healthy diet
2. Monthly change in food prices over the years -
  + Entity
  + Code
  + Year
  + Indicator of Food Price Anomalies (IFPA), by Consumer Food Price Index
3. Share of population that cannot afford a healthy diet -
  + Entity
  + Code
  + Year
  + Share of the population who cannot afford a healthy diet
4. Share of population that is undernourished -
  + Entity
  + Code
  + Year
  + Prevalence of undernourishment (%)
5. Share employed in agriculture, industry, and services -
  + Entity
  + Code
  + Year
  + Employment in agriculture (% of total employment) (modeled ILO estimate)
  + Employment in industry (% of total employment) (modeled ILO estimate)
  + Employment in services (% of total employment) (modeled ILO estimate)

## Questions answered
For this analysis, the following questions were answered:
1. For selected african countries, what is the daily cost of living from 2017 to 2021?
2. How do food price anomalies compare to the cost of a healthy diet?
3. What is the correlation between undernourisment prevalence and food price anomalies?
4. How are workers in the agricultural, industrial, and services sectors affected by the fluctuations in food prices?
5. How does undernourishment prevalence compare to diet affordability in some african territories?

## Key Insights
+ By 2021, Nigeria and Ghana have the highest healthy diet costs among the listed countries, followed by Benin, while Chad, Cameroon, and Niger maintain more affordable diet costs.
+ In Nigeria, the percentage of employment in agriculture decreased slightly over the years, from 41.35% in 2010 to 38.55% in 2021. Food price anomalies (IFPA) also showed fluctuations, with higher positive anomalies in recent years (1.02 in 2017, 0.71 in 2020).
+ The average affordability cost for Nigeria rises consistently over the years, starting from 3.56 in 2017 to 4.32 in 2021. This trend indicates a steady increase in the cost of a healthy diet.
+ For Nigeria and its neighbors, the cost of a healthy diet tends to increase over time regardless of fluctuations in food price anomalies. This means that other underlying factors, such as inflation or structural issues in the food system, may have a stronger impact on the rising costs of healthy diets than short-term price anomalies.

## Recommendations
1. Apply targeted investments in infrastructure, irrigation, and technology to boost productivity and income stability for agricultural workers.
2. Since undernourishment prevalence increased even in years when price anomalies were not at their peak, the government should improve food aid and their policies to mitigate the impact of price anomalies on undernourishment.
3. Establish price stabilization policies, such as strategic food reserves or subsidies, to buffer the impact of food price anomalies.

## Code
```Sql
CREATE DATABASE diet_analysis;

SELECT DISTINCT entity
FROM `daily cost of a healthy diet per person`;


----------------Diet affordability Nigeria--------------------------------------------------------

SELECT entity,
       year,
       ROUND(AVG(`healthy diet cost`), 2) AS avg_affordability
FROM `daily cost of a healthy diet per person`
WHERE entity = 'Nigeria'
GROUP BY year;

---------------Prevalence of undernourishment and correlation with food prices nigeria--------------------------

SELECT undernourished.year,
       undernourished.entity,
       AVG(`Indicator of Food Price Anomalies (IFPA)`) AS avg_price_anomalies,
       AVG(`Prevalence of undernourishment (%)`) AS avg_undernourishment,
       ROUND(AVG(`healthy diet cost`), 2) AS avg_healthy_diet_cost
FROM `share of pop that is undernourished nigeria` AS undernourished
JOIN `monthly change in food price over the years` AS indicator
ON undernourished.entity = indicator.entity
AND undernourished.year = indicator.year
JOIN `daily cost of a healthy diet per person` AS daily_cost
ON indicator.entity = daily_cost.entity
AND indicator.year = daily_cost.year
WHERE undernourished.entity = 'Nigeria'
GROUP BY undernourished.year, undernourished.entity;

--------------------Employment factors influencing high food prices Nigeria-----------------------------------------------------

SELECT ifpa.Year,
		ifpa.entity,
        ROUND(AVG(`% of total employment in agriculture`), 2) AS agriculture_employment_percent, 
       ROUND(AVG(`% of total employment in industry`), 2) AS industry_employment_percent, 
       ROUND(AVG(`% of total employment in services`), 2) AS services_employment_percent, 
       AVG(`Indicator of Food Price Anomalies (IFPA)`) AS avg_ifpa
FROM `monthly change in food price over the years` AS ifpa
JOIN `share employment agriculture industry services` AS services
ON ifpa.year = services.year
AND ifpa.entity = services.entity
WHERE ifpa.entity = 'Nigeria'
GROUP BY 1, 2;


-----------------------Health implications of unaffordable diets Nigeria------------------------

SELECT
		undernourished.Year, 
        undernourished.entity,
		AVG(`Share of the population who cannot afford a healthy diet`) AS diet_unaffordability, 
       AVG(`Prevalence of undernourishment (%)`) AS avg_undernourishment
FROM `share of pop that is undernourished nigeria` AS undernourished
JOIN `percentage of pop who cannot afford a healthy diet` AS affordability
ON undernourished.year = affordability.year
AND undernourished.entity = affordability.entity
WHERE undernourished.entity = 'Nigeria'
GROUP BY 1, 2;

----------------Comparison with neighboring countries-----------------------------------------------
----------------Analysis of diet affordability-------------------------------------------------------
SELECT entity,
year,
ROUND(AVG(`healthy diet cost`), 2) AS avg_affordability
FROM `daily cost of a healthy diet per person`
WHERE entity IN ('Nigeria', 'Benin', 'Chad', 'Cameroon', 'Ghana', 'Niger')
GROUP BY year, entity;

---------------Prevalence of undernourishment and correlation with food prices--------------------------

       SELECT undernourished.year,
       undernourished.entity,
       AVG(`Indicator of Food Price Anomalies (IFPA)`) AS avg_price_anomalies,
       AVG(`Prevalence of undernourishment (%)`) AS avg_undernourishment,
       ROUND(AVG(`healthy diet cost`), 2) AS avg_healthy_diet_cost
FROM `share of pop that is undernourished nigeria` AS undernourished
JOIN `monthly change in food price over the years` AS indicator
ON undernourished.entity = indicator.entity
AND undernourished.year = indicator.year
JOIN `daily cost of a healthy diet per person` AS daily_cost
ON indicator.entity = daily_cost.entity
AND indicator.year = daily_cost.year
GROUP BY undernourished.year, undernourished.entity;

--------------------Factors influencing high food prices-----------------------------------------------------

SELECT ifpa.Year,
		ifpa.entity,
        ROUND(AVG(`% of total employment in agriculture`), 2) AS agriculture_employment_percent, 
       ROUND(AVG(`% of total employment in industry`), 2) AS industry_employment_percent, 
       ROUND(AVG(`% of total employment in services`), 2) AS services_employment_percent, 
       AVG(`Indicator of Food Price Anomalies (IFPA)`) AS avg_ifpa
FROM `monthly change in food price over the years` AS ifpa
JOIN `share employment agriculture industry services` AS services
ON ifpa.year = services.year
AND ifpa.entity = services.entity
WHERE ifpa.entity IN ('Nigeria', 'Benin', 'Togo', 'Chad', 'Cameroon', 'Ghana')
GROUP BY 1, 2;

---------------------------Health implications of unaffordable diets----------------------------------

SELECT
		undernourished.Year, 
        undernourished.entity,
		AVG(`Share of the population who cannot afford a healthy diet`) AS diet_unaffordability, 
       AVG(`Prevalence of undernourishment (%)`) AS avg_undernourishment
FROM `share of pop that is undernourished nigeria` AS undernourished
JOIN `percentage of pop who cannot afford a healthy diet` AS affordability
ON undernourished.year = affordability.year
AND undernourished.entity = affordability.entity
WHERE undernourished.entity IN ('Nigeria', 'Benin', 'Togo', 'Chad', 'Cameroon', 'Ghana')
GROUP BY 1, 2;


