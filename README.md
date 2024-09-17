# Exploring London's Travel Network Using SQL

London, historically known as "Londinium" during Roman times, is home to [over 8.5 million residents](https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationestimates/bulletins/populationandhouseholdestimatesenglandandwales/census2021unroundeddata#population-and-household-estimates-england-and-wales-data), speaking more than [300 languages](https://web.archive.org/web/20080924084621/http://www.cilt.org.uk/faqs/langspoken.htm). Although the City of London covers just over one square mile, earning its nickname "The Square Mile," Greater London expands across 32 boroughs and spans 606 square miles.

<br><br>
<div align="center">
  
![London_Skyline_(125508655)](https://github.com/user-attachments/assets/263fa9e1-7362-4fd6-8bad-d1ca7443cb27)

<div align="left">
  <br><br>

Given that London's roads were originally designed for horse-drawn carriages, the rapid growth of the city and its population necessitated the development of an efficient public transport network. Since 2000, this has been managed by **Transport for London** (TfL), under the guidance of the London Mayor's office. TfL oversees a variety of services, including the London Underground, Overground, buses, trams, the Docklands Light Railway (DLR), river services (such as the Clipper and [Emirates Airline cable car](https://en.wikipedia.org/wiki/London_cable_car)), and even taxis.

TfL makes its data available to the public through the [London Datastore](https://data.london.gov.uk/dataset). In this project, we will explore public transport journey data using SQL to answer key questions about journey volumes by transport type.

---

## Project Overview

This project is part of the DataCamp learning program and focuses on analyzing transport data from London using SQL. Through this project, I developed SQL skills by working with real-world datasets. The project explores public transport journeys, answering three key questions:

1. **What are the most popular transport types, measured by the total number of journeys?**
2. **Which five months and years were the most popular for the Emirates Airline cable car?**
3. **Which five years recorded the lowest number of Underground & DLR journeys?**

---

## Dataset Details

The data is housed in a **Snowflake** database called `TFL`, with a single table named `JOURNEYS`. This table includes the following columns:

| Column | Definition | Data Type |
|--------|------------|-----------|
| `MONTH` | Month in number format, e.g., `1` equals January | `INTEGER` |
| `YEAR` | Year of the record | `INTEGER` |
| `DAYS` | Number of days in the given month | `INTEGER` |
| `REPORT_DATE` | The date the data was reported | `DATE` |
| `JOURNEY_TYPE` | Type of transport used (e.g., bus, underground) | `VARCHAR` |
| `JOURNEYS_MILLIONS` | Number of journeys in millions | `FLOAT` |

---

## SQL Queries

Below are the SQL queries used to answer the project's questions:

### 1. Most Popular Transport Types

This query calculates the total number of journeys for each transport type and ranks them in descending order:

```sql
SELECT 
  TFL.JOURNEYS.JOURNEY_TYPE,
  ROUND(SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS), 2) AS TOTAL_JOURNEYS_MILLIONS
FROM TFL.JOURNEYS
GROUP BY 1
ORDER BY 2 DESC;
```

## 2. Emirates Airline Popularity

This query identifies the five months with the highest number of Emirates Airline journeys, excluding null values, and rounds the number of journeys to two decimal places:

```sql
SELECT 
  TFL.JOURNEYS.MONTH,
  TFL.JOURNEYS.YEAR,
  ROUND(SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS), 2) AS ROUNDED_JOURNEYS_MILLIONS
FROM TFL.JOURNEYS
WHERE TFL.JOURNEYS.JOURNEY_TYPE = 'Emirates Airline'
GROUP BY TFL.JOURNEYS.MONTH, TFL.JOURNEYS.YEAR
HAVING ROUNDED_JOURNEYS_MILLIONS IS NOT NULL
ORDER BY 3 DESC
LIMIT 5;
```

## 3. Least Popular Years for Underground & DLR

This query lists the five years with the lowest number of Underground & DLR journeys:

```sql
SELECT 
  TFL.JOURNEYS.YEAR,
  TFL.JOURNEYS.JOURNEY_TYPE,
  ROUND(SUM(TFL.JOURNEYS.JOURNEYS_MILLIONS), 2) AS TOTAL_JOURNEYS_MILLIONS
FROM TFL.JOURNEYS
WHERE TFL.JOURNEYS.JOURNEY_TYPE = 'Underground & DLR'
GROUP BY TFL.JOURNEYS.YEAR, TFL.JOURNEYS.JOURNEY_TYPE
ORDER BY 3
LIMIT 5;
```

## Conclusions

After analyzing public transport journey data from Transport for London (TfL) using SQL, we discovered the following insights:

1. **Most Popular Transport Types**:
   - The **Bus** is the most popular mode of transport by a large margin, with over **24,905 million journeys**.
   - The **Underground & DLR** comes in second with **15,020 million journeys**, followed by the **Overground** with **1,666 million journeys**.
   - Other transport modes like **TfL Rail**, **Tram**, and **Emirates Airline** have significantly lower usage, with Emirates Airline recording the lowest number of journeys at **14.58 million**.
   
2. **Emirates Airline Popularity**:
   - The Emirates Airline is most popular during the months of **May** and **June 2012**, with the highest journey volume of **0.53 million** in May 2012.
   - The next highest journey volumes occurred in **April 2012**, **May 2013**, and **May 2015**.
   - These results suggest a peak in Emirates Airline usage during **2012**, likely due to the increased number of visitors for the London Olympics, as well as a slight resurgence in 2013 and 2015.

3. **Least Popular Years for Underground & DLR**:
   - The lowest number of Underground & DLR journeys was recorded in **2020**, with **310.18 million journeys**, likely due to the COVID-19 pandemic and subsequent lockdowns.
   - Journey volumes increased in **2021** to **748.45 million** and continued to rise in **2022** to **1,064.86 million** as restrictions eased.
   - The pre-pandemic years **2010** and **2011** saw higher journey volumes, with **1,096.15 million** and **1,156.65 million** journeys, respectively.

