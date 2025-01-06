# Hotel Revenue Report

## Table of Contents
* [Business Requirements](#business-requirements)
* [Data Source](#data-source)
* [Objectives](#objectives)
* [Data Exploration Notes](#data-exploration-notes)


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
