# Sales_Performace_Dashboard
This Power BI dashboard provides a comprehensive analysis of sales performance and growth trends across time, products, customers, and countries.

/Sales-Growth-Dashboard/

│

├── README.md            <- Project Overview

├── Dashboard.pbix       <- Power BI Dashboard file (Not uploaded)

├── Sales_Growth_Data/   <- Folder for datasets (CSV/Excel)

└── Assets/
    └── Sales_Dashboard_Screenshot.png

## 📌 Key Highlights

# ✅ Total Sales Revenue:

$145.28M generated from 5,020,569 units sold

Average Order Value (AOV): $28.94

# 📈 Growth Metrics:

Year-over-Year (YoY): 🔼 33.3%

Quarter-over-Quarter (QoQ): 🔼 6.7%

Month-over-Month (MoM): 🔼 2.1%

# 🏆 Best Performing Product:

Côte de Blaye (Beverages)

Units Sold: 66,372

Sales: $1.74M

Sales: $159K

# 📊 Visuals Included

| Section                    | Description                                                |
| -------------------------- | ---------------------------------------------------------- |
| **Monthly Sales Growth**   | Time series line chart tracking monthly sales fluctuations |
| **Quarterly Sales Growth** | Donut chart showing quarterly distribution                 |
| **Yearly Sales Growth**    | Line chart comparing yearly performance from 2015–2018     |
| **Top Customers**          | Bar chart with top 10 customers based on sales             |
| **Sales by Category**      | Horizontal bar chart showing category-wise performance     |
| **Sales by Country**       | Regional analysis with USA, Germany, France as leaders     |

# 🌎 Sales by Country

| Country | Sales |
| ------- | ----- |
| USA     | \$21M |
| Germany | \$18M |
| France  | \$16M |
| Brazil  | \$15M |
| UK      | \$11M |
| ...     | ...   |

# 💡 Business Use Case

This dashboard helps stakeholders:

1. Monitor top and bottom-performing products

2. Track global sales trends by region

3. Identify key customer accounts

4. Make informed sales and marketing decisions based on trend analysis

# 📌 Tools & Tech Stack

1. Power BI Desktop

2. DAX for KPIs and Measures:

   📅 Power BI DAX Calendar Table
   
                'Date' =
                ADDCOLUMNS (
                    CALENDAR ( MIN ( Sales[Order Date] ), MAX ( Sales[Order Date] ) ),
                    "Year", YEAR ( [Date] ),
                    "Quarter", FORMAT ( [Date], "\QQ" ),
                    "MonthNumber", MONTH ( [Date] ),
                    "Month", FORMAT ( [Date], "MMM" ),
                    "MonthYear", FORMAT ( [Date], "MMM YY" ),
                    "YearMonth", FORMAT ( [Date], "YYYY-MM" ),
                    "Day", DAY ( [Date] ),
                    "DayName", FORMAT ( [Date], "DDD" ),
                    "WeekNum", WEEKNUM ( [Date], 2 ),
                    "WeekStart", [Date] - WEEKDAY([Date], 2) + 1,
                    "WeekEnd", [Date] + (7 - WEEKDAY([Date], 2)),
                    "YearWeekNum", YEAR([Date]) * 100 + WEEKNUM ( [Date], 2 )
                )

    Total Sales Revenue = SUM(Sales[Total Sales])

    Sales Same Period Last Year
   
        Sales Same Period Last Year = 
        CALCULATE (
            SUM(Sales[Total Sales]),
            SAMEPERIODLASTYEAR('''Date'''[Date])
        )
   
    Top 1 Product & Category
   
        Top 1 Product & Category = 
        CALCULATE (
            VAR MaxSalesProductTable =
                TOPN (
                    1,
                    SUMMARIZE (
                        Products,
                        Products[Product Name],
                        Products[Product Category],
                        "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                    ),
                    [SalesAmount],
                    DESC
                )
            VAR TopProductRow = SELECTCOLUMNS(MaxSalesProductTable, "Product", Products[Product Name], "Category", Products[Product Category])
            RETURN
                CONCATENATEX (
                    TopProductRow,
                    [Product] & " - " & [Category],
                    ""
                )
        )

   No.of Units of Top 1 Product
   
        No.of Units of Top 1 Product = 
        CALCULATE (
            SUM ( Sales[Units Sold] ),
            TOPN (
                1,
                SUMMARIZE (
                    Products,
                    Products[Product Name],
                    "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                ),
                [SalesAmount],
                DESC
            )
        )&"- Units sold"

   Total Sales Top 1 Product
   
        Total Sales Top 1 Product = 
        CALCULATE (
            SUM ( Sales[Total Sales] ),
            TOPN (
                1,
                SUMMARIZE (
                    Products,
                    Products[Product Name],
                    "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                ),
                [SalesAmount],
                DESC
            )
        )&" - Sales"


    Least Perform Product & Category
   
        Least Perform Product & Category = 
        CALCULATE (
            VAR MinSalesProductTable =
                TOPN (
                    1,
                    SUMMARIZE (
                        Products,
                        Products[Product Name],
                        Products[Product Category],
                        "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                    ),
                    [SalesAmount],
                    ASC
                )
            VAR LeastProductRow = SELECTCOLUMNS(MinSalesProductTable, "Product", Products[Product Name], "Category", Products[Product Category])
            RETURN
                CONCATENATEX (
                    LeastProductRow,
                    [Product] & " - " & [Category],
                    ""
                )
        )

    No.of Units of Least Perform Product


           No.of Units of Least Perform Product = CALCULATE (
            SUM ( Sales[Units Sold] ),
            TOPN (
                1,
                SUMMARIZE (
                    Products,
                    Products[Product Name],
                    "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                ),
                [SalesAmount],
                ASC
            )
        )&"- Units sold"


   Total Sales Least Perform Product

        Total Sales Least Perform Product = 
        CALCULATE (
            SUM ( Sales[Total Sales] ),
            TOPN (
                1,
                SUMMARIZE (
                    Products,
                    Products[Product Name],
                    "SalesAmount", CALCULATE ( SUM ( Sales[Total Sales] ) )
                ),
                [SalesAmount],
                ASC
            )
        )&" - Sales"

   
   Total Units Sold
   
       Total Units Sold = SUM(Sales[Units Sold])

   Avg. Order Value (AOV)
   
       Avg. Order Value (AOV) = [Total Sales Revenue] / [Total Units Sold]

   MoM Sales Growth %
   
        MoM Sales Growth % = 
        VAR CurrentMonth = SUM(Sales[Total Sales])
        VAR Previous_Month = CALCULATE(SUM(Sales[Total Sales]), DATEADD('''Date'''[Date], -1, MONTH))
        RETURN
        IF(NOT(ISBLANK(Previous_Month)), (CurrentMonth - Previous_Month) / Previous_Month, BLANK())
   
   MoM Growth Arrow
   
        MoM Growth Arrow = 
        VAR Growth = [MoM Sales Growth %]
        RETURN 
        IF(
            ISBLANK(Growth), 
            BLANK(),
            FORMAT(Growth, "0.0%") & 
            " " & 
            SWITCH(TRUE(),
                Growth > 0, "▲",
                Growth < 0, "▼",
                "-"
            )
        )

YoY Sales Growth %

    YoY Sales Growth % = 
    VAR CurrentYear = SUM(Sales[Total Sales])
    VAR Previous_Year = CALCULATE(SUM(Sales[Total Sales]), DATEADD('''Date'''[Date], -1, YEAR))
    RETURN
    IF(NOT(ISBLANK(Previous_Year)), (CurrentYear - Previous_Year) / Previous_Year, BLANK())

YoY Growth Arrow

    YoY Growth Arrow = 
    VAR Growth = [YoY Sales Growth %]
    RETURN 
    IF(
        ISBLANK(Growth), 
        BLANK(),
        FORMAT(Growth, "0.0%") & 
        " " & 
        SWITCH(TRUE(),
            Growth > 0, "▲",
            Growth < 0, "▼",
            "-"
        )
    )

QoQ Growth Arrow

    QoQ Growth Arrow = 
    VAR CurrentQuarter = [Total Sales Revenue]
    VAR Previous_Quarter = 
        CALCULATE(
            [Total Sales Revenue],
            DATEADD('''Date'''[Date], -1, QUARTER)
        )
    VAR Growth = 
        IF(
            NOT(ISBLANK(Previous_Quarter)),
            DIVIDE(CurrentQuarter - Previous_Quarter, Previous_Quarter),
            BLANK()
        )
    RETURN 
    IF(
        ISBLANK(Growth), 
        BLANK(),
        FORMAT(Growth, "0.0%") & " " &
        SWITCH(TRUE(),
            Growth > 0, "▲",
            Growth < 0, "▼",
            "-"
        )
    )


4. Data Modeling (Star Schema)

   ![image](https://github.com/user-attachments/assets/3f73a3aa-b8e4-449a-8cb7-aa0662cd57e6)

6. Excel/CSV (Source Data)

   Please find in repository

# 📬 Contact

Created by: Rohan Anpothle

📧 anpothle.rohan@gmail.com
