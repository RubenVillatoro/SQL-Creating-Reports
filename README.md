# SQL-Creating-Reports :page_with_curl:
Using MS SQL to create reports from Northwind DB.
# Concepts Shown:
Classifying Data with CASE WHEN and GROUP BY
 & Multi-level Aggregation

# :pushpin: Solutions

1.  **Comparing Values in the UnitInStock Column**

```sql
SELECT 
	ProductID,
	ProductName,
	UnitsInStock,
	CASE 
		WHEN UnitsInStock > 100 THEN 'High'
		WHEN UnitsInstock > 50 THEN 'Moderate'
		WHEN UnitsInStock > 0 THEN 'Low'
		WHEN UnitsInstock = 0 THEN 'None'
		END AS Availability
		FROM Products;
```

***

2.  **Breakdown Employees into Seniority Level**

```sql
SELECT
FirstName,
LastName,
HireDate,
CASE 
	WHEN HireDate > '2020-01-01' THEN N'junior'
    WHEN HireDate > '2017-01-01' THEN N'middle'
    WHEN HireDate < '2017-01-01' THEN N'senior'
    END AS Experience
FROM Employees;
```

***

3.  **Find Shipping Cost per Country**

```sql
SELECT 
  OrderID,
  CustomerID,
  ShipCountry,
  CASE
    WHEN ShipCountry = N'USA' OR ShipCountry = N'Canada' THEN 0.0
	ELSE 10.0
  END AS ShippingCost
FROM Orders
WHERE OrderID BETWEEN 10720 AND 10730;
```

***

4.  **Split Customers into two categories**

```sql
SELECT
COUNT(CASE
      WHEN ContactTitle = N'Owner' THEN CustomerID END) AS RepresentedByOwner,
COUNT(CASE
      WHEN ContactTitle != N'Owner' THEN CustomerID END) AS NotRepresentedByOwner
FROM Customers;
```

***

5.  **How many Orders have been processed by employees in the WA Region**

```sql
SELECT
COUNT(CASE
      WHEN Region = N'WA' THEN OrderID END) AS OrdersWAEmployees,
COUNT(CASE
      WHEN Region != N'WA' THEN OrderID END) AS OrdersNotWAEmployees
FROM Employees E
JOIN Orders O 
ON E.EmployeeID=O.EmployeeID;
```

***

6.  **Show the number of orders with low, average and high freight in each country**

```sql
SELECT 
  ShipCountry,
  COUNT(CASE
    WHEN Freight < 40.0 THEN OrderID END) AS LowFreight,
  COUNT(CASE
    WHEN Freight >= 40.0 AND Freight < 80.0 THEN OrderID END) AS AvgFreight,
  COUNT(CASE
    WHEN Freight >= 80.0 THEN OrderID END) AS HighFreight
FROM Orders
GROUP BY ShipCountry;
```

***

7.  **What's the average number of products in each category?**

```sql
WITH ProductsCategoryCount AS (
  SELECT
    CategoryID,
    COUNT(ProductID) AS CountProducts
  FROM Products
  GROUP BY CategoryID
)
SELECT AVG(CountProducts) AS AvgProductCount
FROM ProductsCategoryCount;
```

***

8.  **For each employee, determine the average number of items they processed per order, for all orders placed in 2020.**

```sql
WITH ItemsProcessed AS (
SELECT
O.EmployeeID,
O.OrderID,
SUM(Quantity) AS ItemCount
FROM OrderDetails OD
JOIN Orders O
ON OD.OrderID=O.OrderID
WHERE O.OrderDate >= '2020-01-01' AND O.OrderDate < '2021-01-01'
GROUP BY O.EmployeeID, O.OrderID
  )
  SELECT 
  FirstName,
  LastName,
  AVG(ItemCount) AS AvgItemCount
  FROM ItemsProcessed IP
  JOIN Employees E
  ON IP.EmployeeID=E.EmployeeID
  GROUP BY  E.EmployeeID, E.FirstName, E.LastName;
```

***

9.  **Create a report that show high and low value customers with count of each**

```sql
WITH CustomerClass AS(
  SELECT
  CustomerID,
  CASE
  	WHEN SUM(Quantity * UnitPrice) >20000
  		THEN N'high-value'
  		ELSE N'low-value'
  		END AS Category
  FROM Orders O
  JOIN OrderDetails OD
  ON O.OrderID = OD.OrderID
  GROUP BY CustomerID
  )
SELECT
	Category,
    COUNT(CustomerID) AS CustomerCount
FROM CustomerClass
GROUP BY Category;
```

***
