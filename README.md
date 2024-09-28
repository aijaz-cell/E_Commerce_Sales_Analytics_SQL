# E_Commerce_Sales_Analytics_SQL
This project explores an e-commerce company's sales data, providing in-depth analysis using SQL. By querying this data, you'll address key business questions like customer segmentation, sales trends, product performance, and regional performance. The project uses SQL functions to provide answers and actionable insights




Data Schema and Questions to Answer:
5. Schema Definition (ecommerce_sales_schema.sql):

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    CustomerName VARCHAR(100),
    Age INT,
    Region VARCHAR(50)
);

CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(100),
    Category VARCHAR(50),
    Price DECIMAL(10, 2)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    OrderDate DATE,
    CustomerID INT,
    ProductID INT,
    Quantity INT,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);

CREATE TABLE Regions (
    RegionID INT PRIMARY KEY,
    RegionName VARCHAR(100)
);

CREATE TABLE Shipments (
    ShipmentID INT PRIMARY KEY,
    OrderID INT,
    ShipmentDate DATE,
    DeliveryDate DATE,
    ShipmentStatus VARCHAR(50),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);



6. Business Questions and SQL Queries:
A. Sales Performance by Product Category:
Question: Which product categories generate the most revenue? What are the top-selling products?
SQL Query (sales_by_product_category.sql):
 
SELECT p.Category, SUM(o.TotalAmount) AS TotalSales
FROM Orders o
JOIN Products p ON o.ProductID = p.ProductID
GROUP BY p.Category
ORDER BY TotalSales DESC;


B. Customer Segmentation by Spending:
Question: How can customers be segmented into tiers based on their total spending (e.g., low, medium, high spenders)?
SQL Query (customer_segmentation.sql):
 
SELECT c.CustomerName, SUM(o.TotalAmount) AS TotalSpent,
    CASE
        WHEN SUM(o.TotalAmount) > 10000 THEN 'High Spender'
        WHEN SUM(o.TotalAmount) BETWEEN 5000 AND 10000 THEN 'Medium Spender'
        ELSE 'Low Spender'


    END AS SpendingSegment
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID
GROUP BY c.CustomerName
ORDER BY TotalSpent DESC;


C. Monthly and Yearly Sales Trend:
Question: What are the monthly and yearly trends in sales? Which months
or years show significant growth or decline?
 
SELECT 
    EXTRACT(YEAR FROM OrderDate) AS Year, 
    EXTRACT(MONTH FROM OrderDate) AS Month,
    SUM(TotalAmount) AS MonthlySales
FROM Orders
GROUP BY Year, Month
ORDER BY Year, Month;
D. Top 10 Best-Selling Products:


Question: What are the top 10 products in terms of units sold and total revenue?
SQL Query (top_10_products.sql):
 
SELECT p.ProductName, SUM(o.Quantity) AS TotalUnitsSold, SUM(o.TotalAmount) AS TotalRevenue
FROM Orders o
JOIN Products p ON o.ProductID = p.ProductID
GROUP BY p.ProductName
ORDER BY TotalUnitsSold DESC
LIMIT 10;


E. Year-over-Year Sales Growth:
Question: What is the year-over-year growth in total sales? Are sales growing, shrinking, or stable?
 (sales_growth_analysis.sql):
 
SELECT EXTRACT(YEAR FROM OrderDate) AS Year,
       SUM(TotalAmount) AS YearlySales,
       LAG(SUM(TotalAmount), 1) OVER (ORDER BY EXTRACT(YEAR FROM OrderDate)) AS PreviousYearSales,
       (SUM(TotalAmount) - LAG(SUM(TotalAmount), 1) OVER (ORDER BY EXTRACT(YEAR FROM OrderDate))) / 
       LAG(SUM(TotalAmount), 1) OVER (ORDER BY EXTRACT(YEAR FROM OrderDate)) * 100 AS YoYGrowth
FROM Orders
GROUP BY Year
ORDER BY Year;


F. Sales by Region:
Question: Which regions generate the most sales, and which regions need improvement?
  (regional_sales_analysis.sql):
 
SELECT c.Region, SUM(o.TotalAmount) AS TotalSales
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID
GROUP BY c.Region
ORDER BY TotalSales DESC;


G. Average Delivery Time by Region:
Question: What is the average delivery time by region? Are there delays in any regions?
 
SELECT c.Region, AVG(DATEDIFF(DeliveryDate, ShipmentDate)) AS AvgDeliveryTime
FROM Shipments s
JOIN Orders o ON s.OrderID = o.OrderID
JOIN Customers c ON o.CustomerID = c.CustomerID
GROUP BY c.Region
ORDER BY AvgDeliveryTime DESC;


H. Customer Retention and Repeat Purchase Rate:
Question: How many customers are returning to make repeat purchases?

SELECT CustomerID, COUNT(OrderID) AS TotalOrders
FROM Orders
GROUP BY CustomerID
HAVING COUNT(OrderID) > 1;

7. Analysis Documentation:
A. README.md:
Include:

Project Objective: A clear summary of the goals.
Setup Instructions: Guide users on how to set up the SQL database and run queries.
Insights: Highlight key insights from your analysis.
SQL Techniques Used: Explain the usage of advanced SQL concepts like LAG(), GROUP BY, WINDOW FUNCTIONS, etc.
Data Sources: Mention where the dataset came from (synthetic, real-world, etc.).
B. sales_analysis.md:
Create detailed documentation of each query and its findings:

Explain the business questions answered by the queries.
Present findings such as:
Sales growth trends showing the best months and years.
Top product categories by revenue and sales.
Regional sales performance for strategic insights.
Customer segments based on spending behavior.


Additional Questions to Explore:
What is the average order value for different customer segments?
How does sales performance vary across different age groups of customers?
Are there any seasonal trends (holidays, Black Friday) impacting





