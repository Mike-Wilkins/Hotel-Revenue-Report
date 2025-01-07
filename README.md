# Hotel Revenue Report

## Table of Contents
* [Business Requirements](#business-requirements)
* [Data Source](#data-source)
* [Objectives](#objectives)
* [Data Exploration Notes](#data-exploration-notes)
* [Data Cleaning](#data-cleaning)
* [Test the Dataset](#test-the-dataset)
* [Power BI Visualisation](#power-bi-visualisation)
* [Analysis](#analysis)


## Business Requirements

As part of our company annual review, we would like to present to our stakeholders a comprehensive dashboard
detailing the business performance between 2018 and 2020. The dashboard should address the following:

- **Is our hotel revenue growing by year?**<br/>
   We have two hotel types, so it would be good to segment revenue by hotel type.<br/>
   As our business is represented globally, it would also be good to segment revenue by country.
- **Should we increase our parking lot size?**<br/>
   We want to understand whether there is a trend in guest with personal cars.
- **What trends can we see in the data?**<br/>
   Focus on average daily rate and guests to explore seasonality.

## Data Source

The hotel group have provided an Excel file containing five speadsheets:
- 2018 Bookings
- 2019 Bookings
- 2020 Bookings
- Meal Costs (FB, HB, BB, etc)
- Market Segment (Online, Direct, Group, Corporate bookings, etc)

## Objectives
- Explore the Excel data provided by the client
- Load the data into SQL Server (SSMS)
- Clean the data with SQL
- Test the data with SQL
- Visualise the data in Power BI
- Generate the findings based on the insights

## Data Exploration Notes
- The separate booking records for 2018, 2019 and 2020 have identical column headers. These will need to be merged in order to perform trend insights.
- After a single booking records dataset is created, we will need to join the Meal Costs and Market Segment tables to create a unified dataset.
- The booking records do not contain a column for Revenue. This will need to be created in SQL. The revenue can be calculated using the Stays(WeekNights), Stays(WeekendNights), DailyRate and Discount columns.
- We have more data than we need, so some of these columns would need to be removed.

## Data Cleaning
The aim is to refine our dataset to ensure it is structured and ready for analysis.

The cleaned data should meet the following criteria and constraints:
- All tables should be merged to create a single unified dataset with only relevant columns being retained.
- A new Revenue column should be added to the dataset.
- All data types should be appropriate for the contents of each column.

**1. Create a single booking records dataset:**

``` SQL
SELECT * INTO dbo.Hotels FROM dbo.['2018$']
UNION
SELECT * FROM dbo.['2019$']
UNION
SELECT * FROM dbo.['2020$']
```
**2. Join Meal Cost and Market Segment tables onto dbo.Hotels:**

``` SQL
LEFT JOIN dbo.market_segment$ ON hotels.market_segment = dbo.market_segment$.market_segment
LEFT JOIN dbo.meal_cost$ ON hotels.meal = dbo.meal_cost$.meal
```
**3. Create Revenue column of type DECIMAL within the SELECT statement:**
   
``` SQL
CONVERT(DECIMAL(10,2),((stays_in_week_nights + stays_in_weekend_nights)*adr)*(1-Discount),2) AS Revenue
```
**4. Convert Stays(WeekNights) and Stays(WeekendNights) to type INT:**

``` SQL
CAST(stays_in_week_nights AS INT) as "Stays in Week Nights",
CAST(stays_in_weekend_nights AS INT) as "Stays in Weekend Nights"
```
**5. Create a view to store the transformed data:**

``` SQL
CREATE VIEW HotelRevenueReport AS

SELECT 
	CONVERT(VARCHAR(10), CAST(reservation_status_date AS DATE), 23) as "Reseveration Date",
	arrival_date_day_of_month as "Arrival Date (Day)",
	arrival_date_month as "Arrival Date (Month)",
	arrival_date_year as "Arrival Date (Year)",
	DATEFROMPARTS(arrival_date_year,(MONTH('1' + [arrival_date_month] +'00')),arrival_date_day_of_month) as "Booking Date",
	CAST(stays_in_week_nights AS INT) as "Stays in Week Nights",
	CAST(stays_in_weekend_nights AS INT) as "Stays in Weekend Nights",
	CONVERT(DECIMAL(10,2),adr) as "Daily Rate",
	Discount,
	CONVERT(DECIMAL(10,2),((stays_in_week_nights + stays_in_weekend_nights)*adr)*(1-Discount),2) AS Revenue,
	required_car_parking_spaces as "Required Parking Spaces",
	hotel as Hotel,
	country as Country
	
FROM dbo.Hotels

LEFT JOIN dbo.market_segment$ ON hotels.market_segment = dbo.market_segment$.market_segment
LEFT JOIN dbo.meal_cost$ ON hotels.meal = dbo.meal_cost$.meal
```

## Test the Dataset

**1. Check each column for NULLS, indicating complete data for all records.**

``` SQL
SELECT COUNT(*) AS 'Null Count'
FROM 
	[Projects].[dbo].[HotelRevenueReport]
WHERE 
	'Reservation Date' IS NULL
	OR
	'Arrival Date (Day)' IS NULL
	OR
	'Arrival Date (Month)' IS NULL

-- Repeat for each column
```
**2. All columns within the new dataset should have approproate data types.**

![image](https://github.com/Mike-Wilkins/Hotel-Revenue-Report/blob/main/DataTypeSummary.png)

## Power Bi Visualisation

https://github.com/user-attachments/assets/ed8dfba2-b43f-4f9f-8a5e-578e65b3366e

## Analysis
- The data showed a positive trend in global revenue between the years 2018 and 2020 with City Hotels returning approx $1M more than Resort Hotels over that same period.
- Peak revenue for Resort Hotels appeared during July and August accross all time periods.
- The results did not produce any definitive evidence that supports the need to expand customer parking areas in the future.
