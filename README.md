# Root Cause Analysis Dashboard

Power BI and SQL-based dashboard to analyze and visualize the main factors behind changes in KPIs such as Sales, Quantity, Profit Margin, and Average Discount through Decomposition Diagram

<img width="1368" height="657" alt="image" src="https://github.com/user-attachments/assets/68c5638f-9c31-4568-80a9-37f34772ccac" />


## DB_Schema
<img width="914" height="796" alt="image" src="https://github.com/user-attachments/assets/5c98fbca-429d-49b8-ac8f-6bee9e6af1fd" />


## Folders

- SQL: Table SQL, sample business queries
- PowerBI: PBIX dashboard file and screenshot
- Data: Example data and table dictionary
- DB_Schema: Database schema diagram

## Core Tables

- dim_Geo
- dim_Product
- fct_Orders
- Returns
- dim_Customer
- dim_Date

## Dashboard Highlights

- Select and analyze KPIs (Sales, Quantity, etc.)
- Root cause (fishbone) drilldown
- Filters for scenario, state, category, year, etc.
- See highest/lowest breakdowns by state or city

## Usage

Open `ANANAY_RCA.pbix` for full interactive dashboard.

Review `DB_Schema/db_schema.png` for the backend structure.

Use scripts/queries in `SQL/` to explore the database.

Load the sample data from `Data/` if you want to test or demo.
```

## SQL/data_model.sql

```sql
CREATE TABLE dim_Geo (
    GeoID INT PRIMARY KEY,
    State VARCHAR(50),
    City VARCHAR(50),
    Region VARCHAR(50)
);

CREATE TABLE dim_Product (
    ProductID INT PRIMARY KEY,
    Category VARCHAR(50),
    SubCategory VARCHAR(50),
    ProductName VARCHAR(100)
);

CREATE TABLE fct_Orders (
    OrderID INT PRIMARY KEY,
    ProductID INT,
    GeoID INT,
    CustomerID INT,
    OrderDate DATE,
    Sales FLOAT,
    Quantity INT,
    Discount FLOAT,
    Profit FLOAT,
    FOREIGN KEY (ProductID) REFERENCES dim_Product(ProductID),
    FOREIGN KEY (GeoID) REFERENCES dim_Geo(GeoID),
    FOREIGN KEY (CustomerID) REFERENCES dim_Customer(CustomerID)
);

CREATE TABLE Returns (
    ReturnID INT PRIMARY KEY,
    OrderID INT,
    Returned CHAR(3),
    FOREIGN KEY (OrderID) REFERENCES fct_Orders(OrderID)
);

CREATE TABLE dim_Customer (
    CustomerID INT PRIMARY KEY,
    CustomerName VARCHAR(100)
);

CREATE TABLE dim_Date (
    DateID INT PRIMARY KEY,
    Date DATE,
    Year INT,
    Month INT,
    DayOfWeek VARCHAR(10),
    EndOfMonth INT,
    EndOfQuarter INT
);
```

## SQL/sample_queries.sql

```sql
-- Total Sales by Category/Subcategory
SELECT p.Category, p.SubCategory, SUM(o.Sales) AS TotalSales
FROM fct_Orders o
JOIN dim_Product p ON o.ProductID = p.ProductID
GROUP BY p.Category, p.SubCategory;

-- Yearly Quantity by State
SELECT d.Year, g.State, SUM(o.Quantity) AS TotalQty
FROM fct_Orders o
JOIN dim_Date d ON o.OrderDate = d.Date
JOIN dim_Geo g ON o.GeoID = g.GeoID
GROUP BY d.Year, g.State;

-- Returns Rate by State
SELECT g.State, 
       COUNT(r.ReturnID) * 100.0 / COUNT(DISTINCT o.OrderID) AS ReturnRate
FROM fct_Orders o
JOIN dim_Geo g ON o.GeoID = g.GeoID
LEFT JOIN Returns r ON o.OrderID = r.OrderID
GROUP BY g.State;
```

## Data/data_dictionary.txt

```
dim_Geo: State, City, and Region info
dim_Product: Product details including Category and Subcategory
fct_Orders: Sales transactions and KPIs
Returns: Returned order tracking
dim_Customer: Customer details
dim_Date: Calendar info (Year, Month, Weekday, etc.)
```


## MADE BY ANANAY AGGARWAL
