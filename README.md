# Performance Analysis Report in Power BI.

# Interactive Dashboard Link : https://app.powerbi.com/groups/me/reports/c52522e5-1eab-40e4-9cc1-78df19c84cf2/6408a0bd8a2cc31d1fa2?experience=power-bi

## Table of Content:
- [Business Case](#business-case)
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools Used](#tools-used)
- [Data Preparation](#data-preparation)
- [Creating Virtual Tables](#creating-virtual-tables)
- [Explorative Data Analysis](#explorative-data-analysis)
- [Measures using DAX](#measures-using-dax)
- [Data-Model](#data-model)
- [Results-And-Finding](#results-and-findings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)

### Business Case:
As a Data Analyst of Fresh Farm Co. you are required to create a Performance Analysis Report for the business given the following records- Accounts, Plant Hierarchy, Plant Fact in a Microsoft Excel File.

### Project Overview:
This Data Analysis Project aims to provide insights into the performance analysis of Fresh Farm Co. between 2022 and 2024.By analyzing various aspect of the sales & profitability data. We seek to identify trends, make data driven recommendations an gain a deep understanding of the company's performance 

### Data Source:
The following source data were stored in Microsoft Excel File
- Plant_Fact: Detailing the "fact" records of Fresh Farm Co.
- Account : Detailing the customer records of Fresh Farm Co.
- Plant_Hierarchy : Detailing product information of Fresh Farm Co.


### Tools Used:
Microsoft Power BI[Download here](https:microsoft.PowerBI)
- Power Query
- Measures(Using DAX)
- Data Models
- Visuals & Reports

### DATA PREPARATION
Data Ingestion & Cleaning :
Data Preparation Phase- In Power BI environment, Data was extracted from Excel files into Power Query and cleaned by applying the following techniques.

- Load data into Power BI desktop, dataset is a xlsx file.
- Open Power Query Editor & in "View" tab, under Data Preview Section, Check "Column Distribution", "Column Quality", "Column Profile" for each of the dataset.
- Data Profiling: Check summary statistics and quality of the data.
- Data Cleaning: Removing duplicate records from assigned primary columns, checking for outliers, renaming columns appropriately and so on.
- Data Transformation: Converting data into suitable format for analysis(date, numerical, text format and so on.)

### Creating Virtual Tables.
Cleaned Data were loaded to create a virtual tables as follows

- Dim_Product
- Dim_Account
- Fact_Sales.

 ### Explorative Data Analysis:

- Sales trend for respective year.
- Quantity trend for each year.
- Profitability trend for each year.
- Year-To-Date analysis of Sales, Quantity and Profitability.
- Prior-Year-To-Date Analysis of Sales, Quantity, Profitability

### To achieve our Explorative Data Analysis, the following "Calculated" table and "Measures"  were created.

#### Calculated Table:
 Date(calendar table)
 Calculated Table
- Date(calendar table)
```Dim_Date = 
   CALENDAR(
    DATE(2022, 1, 1), 
    DATE(2024, 12, 31)
)
        Inpast =
 VAR lastsalesdate = MAX(Fact_sales[Date_Time])
 VAR lastsalesdatePY = EDATE(lastsalesdate, -12)
 RETURN
 Dim_Date[Date] <= lastsalesdatePY
```

### Measures using DAX

- Slicer table :containing these columns Sales aggrgate, Quantity aggregate, Gross_Profit % 

### Base Measures:
    - Sales:
      - Sales = SUM(Fact_Sales[Sales_USD])

    - Quantity:
      - Quantity = SUM(Fact_Sales[Quantity])

    - COGs:                     #We needed the COGs to be able to calculate Gross Profit 
      - COGs = SUM(Fact_Sales[COGs_USD])

    -Gross_Profit:
     - Gross_Profit = [Sales]-[COGs]

### We need to create two "SWITCHES", for our PYTD and YTD for each of our Base Measures Sales, Quantity, Gross_Profit.

*** SWITCHES are use to move from one variable to another and vice-versa. They're particularly useful for creating dynamic calculations that change based on different criteria or conditions (In this context, Base "Measures-Sales, Quantity, Gross_Profit).

*** YTD(Year To Date) refers to the total value or cumulative sum of a measure (like sales, revenue, or profit) from the start of the current year up to the current date. Assuming today's date is 20/08/2024. The YTD sales would be the total sales from January 1, 2024, to August 20, 2024.

*** PYTD(Previous Year To Date) refers to the total value or cumulative sum of a measure from the start of the previous year up to the same point in time as the current date. Assuming today's date is 20/08/2024. The PYTD sales would be the total sales from January 1, 2023, to August 20, 2023.

### Base Measures for PYTD

- PYTD_Sales:
```  
 PYTD_Sales = 
 CALCULATE(
     [Sales], 
     SAMEPERIODLASTYEAR(Dim_Date[Date]),
     Dim_Date[Inpast] = TRUE
 )
```
-PYTD_Quantity:
```
PYTD_Quantity = 
 CALCULATE(
     [Quantity], 
     SAMEPERIODLASTYEAR(Dim_Date[Date]),
     Dim_Date[Inpast] = TRUE
 )
```
PYTD_Gross_Profit:
```
PYTD_Gross_Profit = 
 CALCULATE(
     [Gross_Profit], 
     SAMEPERIODLASTYEAR(Dim_Date[Date]),
     Dim_Date[Inpast] = TRUE
 )
```
#### Base Measures For YTD

YTD_Sales:
```
YTD_Sales = TOTALYTD([Sales],Fact_Sales[Date_Time])
```
```
YTD_Quantity:
- YTD_Quantity = TOTALYTD([Quantity],Fact_Sales[Date_Time])
```
```
YTD_Gross_Profit:
- YTD_Gross_Profit = TOTALYTD([Gross_Profit],Fact_Sales[Date_Time])
```

#### Switche for PYTD:
```
 S_PYTD =
  VAR selected_value =SELECTEDVALUE(Slicer_Values[Values])
  VAR result = SWITCH(selected_value,
      "Sales", [PYTD_Sales],
      "Quantity", [PYTD_Quantity],
      "Gross_Profit", [PYTD_Gross_Profit],
      BLANK()
  )
  RETURN
  result
```
#### Switch for YTD:
```
 S_YTD =
  VAR selected_value =SELECTEDVALUE(Slicer_Values[Values])
  VAR result = SWITCH(selected_value,
      "Sales", [YTD_Sales],
      "Quantity", [YTD_Quantity],
      "Gross_Profit", [YTD_Gross_Profit],
      BLANK()
  )
  RETURN
  result
```
### Comparing YTD Measures against PYTD using Switch:
```
 YTD vs PYTD =[S_YTD]-[S_PYTD]
```

### Data Model
Create a Data Model View, by connecting the Primary keys in our respective Dimension tables(Date, Account, Product) to the corresponding Foreign Keys in the Sales Fact table.
 
### Results And Findings:

- A detailed analysis comparing current year-to-date performance against the previous year reveals a concerning trend. Across a majority of our product categories and operating regions, we are experiencing a decline in both profitability and sales volume. This downturn is particularly evident in the quantity of products sold."
- 


### Recommendations:
- Identify Lagging Products: Pinpoint specific product categories or SKUs that are driving the decline.
- Analyze Customer Feedback: Gather customer insights to understand why these products are losing traction.
- Re-evaluate Product Positioning: Consider if the product's features, pricing, or marketing strategy need adjustment.
- Analyze Competitor Activity: Evaluate if competitors have introduced new products or  changed pricing strategies.
- Refine Sales Strategies: Consider training sales teams on new techniques or providing additional resources.
- Enhance Marketing Campaigns: Re-evaluate marketing strategies to ensure they are reaching the target audience effectively.
- Improve Customer Experience: Focus on enhancing customer satisfaction and loyalty.


### Limitations:
Time Constraint - This performance report was created under a tight schedule to make best of available resources.


### References:
- Gimini AI.
- Microsoft Power BI Official Documentation.





