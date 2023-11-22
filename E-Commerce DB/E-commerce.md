# E-Commerce Database

## 1. Retrieve names and contact details of customers in a specific geographic location.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    C.customerID,
    C.FirstName,
    C.LastName,
    C.EmailAddress
FROM 
    sales S JOIN customers C  
    ON S.customer_ID = C.customerID
    JOIN territories T
    ON S.territory_ID = T.territory_id
WHERE
    T.country = 'United States';
```

## 2. Show product names and prices sorted in descending order of price.

```sql
SELECT 
    ProductName,
    ProductPrice 
FROM 
    Products
ORDER BY 2 DESC;
```

## 3. Select all sales product records where the quantity sold is greater than 500.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    Product_ID,
    SUM(OrderQuantity) AS Total_Quantity
FROM 
    sales
GROUP BY 1 
HAVING SUM(OrderQuantity) > 500
ORDER BY 2 DESC;
```

## 4. Find customers who have made purchases in the last month.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT
    DISTINCT C.FirstName
FROM sales S JOIN customers C  
    ON S.customer_ID = C.customerID
WHERE 
    OrderDate >= (SELECT MAX(OrderDate) - INTERVAL '1 month' FROM sales);
```

## 5. Display the top 5 products with the highest sales quantity.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    ProductName,
    SUM(OrderQuantity) AS Total_Quantity
FROM 
    sales S JOIN products P  
    ON S.product_ID = P.product_ID
GROUP BY 1 
ORDER BY 2 DESC
LIMIT 5;
```

## 6. List the products with sales quantities between 5 and 10.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
),
product_rank as 
(
    SELECT 
        ProductName,
        SUM(OrderQuantity) AS Total_Quantity,
        ROW_NUMBER() over (ORDER BY SUM(OrderQuantity) DESC) as ranks
    FROM 
        sales S JOIN products P  
        ON S.product_ID = P.product_ID
    GROUP BY 1
)

SELECT
    ProductName,
    Total_Quantity
FROM
    product_rank
WHERE
    ranks between 5 and 10;
```

## 7. Retrieve the 10 most recent sales records.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT * FROM SALES
ORDER BY OrderNumber DESC
LIMIT 10;
```

## 8. Calculate the total number of customers.

```sql
SELECT COUNT(CustomerID) FROM Customers;
```

## 9. Find the average price of products in each category.

```sql
SELECT 
    CategoryName,
    ROUND(AVG(ProductPrice),2) as TotalPrice
FROM 
    products P JOIN product_subcategories SC
    ON P.subCategory_id = SC.subcategoryID
    JOIN product_categories C
    ON SC.categoryID = C.productCategoryID
GROUP BY 1
ORDER BY 2 DESC;
```

## 10. Determine the total revenue generated from sales.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Total_Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID;
```

## 11. Identify the territory with the highest total sales.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    Territory_id,
    Country,
    Region,	
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Total_Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
    JOIN Territories T
    ON S.territory_ID = T.territory_id
GROUP BY 1
ORDER BY 4 DESC;
```

## 12. Compute the average quantity of items sold per sale.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    ROUND(AVG(OrderQuantity),2)
FROM 
    SALES;
```

## 13. Retrieve the names of customers who made purchases, along with the product names they bought.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    CONCAT(C.FirstName,' ',C.LastName) as CustomerName,
    P.productName
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
    JOIN Customers C  
    ON S.customer_ID = C.customerID;
```

## 14. List all sales records with customer names, product names, and territory details.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    CONCAT(C.FirstName,' ',C.LastName) as CustomerName,
    P.ProductName,
    T.Region,
    T.country
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
    JOIN Customers C  
    ON S.customer_ID = C.customerID
    JOIN Territories T
    ON S.territory_ID = T.territory_id;
```

## 15. Find the products with no associated sales records.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    DISTINCT P."ProductName"
FROM 
    sales S right join products P 
    ON S.product_ID = P.product_ID
WHERE
    S.product_ID is null;
```

## 16. Identify customers who have not made any purchases.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    DISTINCT CONCAT(C.FirstName,' ',C.LastName) as CustomerName
FROM 
    SALES S right JOIN Customers C  
    ON S.customer_ID = C.customerID
WHERE
    S.customer_ID is null;
```

## 17. Find products with prices higher than the average price.

```sql
SELECT
    DISTINCT ProductName
FROM 
    Products 
WHERE 
    ProductPrice>(SELECT AVG(ProductPrice) FROM Products)
ORDER BY 
    ProductPrice DESC;
```

## 18. Identify products with sales quantities greater than the average quantity.

```sql
WITH sales AS 
(
    SELECT * 
    FROM 
        Sales_2015
    UNION
    SELECT * 
    FROM 
        Sales_2016
    UNION
    SELECT * 
    FROM 
        Sales_2017
),
cte2 AS
(
    SELECT
        P.ProductName,
        SUM(S.OrderQuantity) AS sales_quantity 
    FROM 
        Products P
                 JOIN
        SALES S 
    ON 
        P.Product_ID=S.Product_ID
    GROUP BY 1 
)

SELECT 
    DISTINCT ProductName
FROM 
    cte2
WHERE
    sales_quantity > (SELECT AVG(sales_quantity) FROM cte2);
```

## 19. Display average price, average sales, and total revenue generated by each category.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    CategoryName,
    ROUND(AVG(ProductPrice),2)as Avg_Price,
    ROUND(AVG(OrderQuantity),2)as Avg_Order_Quantity,
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID 
    JOIN Product_Subcategories SC
    ON P.subCategory_id = SC.subcategoryID
    JOIN product_categories C
    ON SC.categoryID = C.productCategoryID
GROUP BY 1;
```

## 20. Find the number of sales made in each month.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    TO_CHAR(OrderDate,'yyyy-mm') AS Month,
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID 
GROUP BY 1
ORDER BY 1 DESC;
```

## 21. Find the customers with the highest total purchase amount.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    CONCAT(C.FirstName,' ',C.LastName) as CustomerName,
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
    JOIN Customers C  
    ON S.customer_ID = C.customerID
GROUP BY 1
ORDER BY 2 DESC;
```

## 22. Calculate the total sales revenue for each month and territory.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    TO_CHAR(OrderDate,'yyyy-mm') AS Month,
    Country,
    Region,
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Total_Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
    JOIN Territories T
    ON S.territory_ID = T.territory_id
GROUP BY 1,2,3
ORDER BY 4 DESC;
```

## 23. Find the month with the highest sales and the product sold during that month.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
),
max_month as
(
    SELECT 
        TO_CHAR(OrderDate,'yyyy-mm') as  m_month,
        ROUND(SUM(ProductPrice * OrderQuantity),2) as Total_Revenue
    FROM 
        sales S JOIN products P 
        ON S.product_ID = P.product_ID
    GROUP BY 1
)

, max_sale_month as (
    SELECT
        *
    FROM
        SALES
    WHERE
        TO_CHAR(OrderDate,'yyyy-mm') = 
        (SELECT m_month FROM max_month WHERE 
        Total_Revenue = (SELECT MAX(Total_Revenue) FROM max_month)))
    
SELECT 
    DISTINCT ProductName
FROM 
    max_sale_month S JOIN Products P 
    ON S.product_ID = P.product_ID;
```

## 24. Find the sales records for the last quarter.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT
    *
FROM 
             sales S JOIN customers C  
    ON S.customer_ID = C.customerID
WHERE 
            OrderDate >= (SELECT MAX(OrderDate) - INTERVAL '3 months' FROM sales);
```

## 25. Identify the busiest day of the week for sales.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)

SELECT 
    TO_CHAR(OrderDate,'Day') AS weekday,
    ROUND(SUM(ProductPrice * OrderQuantity),2) as Total_Revenue
FROM 
    sales S JOIN products P 
    ON S.product_ID = P.product_ID
GROUP BY 1
ORDER BY 2 DESC;
```

## 26. Find out the first sale date and last sale date of all products.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
)
    
SELECT 
    DISTINCT Product_ID, 
    FIRST_VALUE(OrderDate) OVER (PARTITION BY Product_ID ORDER BY OrderDate) as first_sale_date,
    FIRST_VALUE(OrderDate) OVER (PARTITION BY Product_ID ORDER BY OrderDate DESC) as last_sale_date 
FROM 
              sales;
```

## 27. Figure out order quantity trends of products.

```sql
WITH sales AS 
(
    SELECT * 
    FROM 
        Sales_2015
    UNION
    SELECT * 
    FROM 
        Sales_2016
    UNION
    SELECT * 
    FROM 
        Sales_2017
),
cte2 AS
(
    SELECT
        Product_ID,
        OrderDate,
        LAG(SUM(OrderQuantity)) OVER (PARTITION BY Product_ID ORDER BY OrderDate) as prev_sale_qty, 
        SUM(OrderQuantity) as current_quantity, 
        LEAD(SUM(OrderQuantity)) OVER (PARTITION BY Product_ID ORDER BY OrderDate) as next_sale_qty 
    FROM 
             sales
    GROUP BY 
             Product_ID, OrderDate
)

SELECT 
    Product_ID,
    OrderDate,
    prev_sale_qty,
    current_quantity,
    next_sale_qty
FROM 
    cte2;
```

## 28. Monthly revenue growth.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
),
monthly_revenue as 
(
    SELECT 
        TO_CHAR(OrderDate,'yyyy-mm') as month,
        LAG(SUM(ProductPrice * OrderQuantity)) OVER (ORDER BY TO_CHAR(OrderDate,'yyyy-mm')) as Prev_Month_Revenue,
        ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue,
        LEAD(SUM(ProductPrice * OrderQuantity)) OVER (ORDER BY TO_CHAR(OrderDate,'yyyy-mm')) as Next_Month_Revenue
    FROM 
        sales S JOIN products P 
        ON S.product_ID = P.product_ID 
    GROUP BY 1
    ORDER BY 1 ASC
)

SELECT
    month,
    ROUND(Prev_Month_Revenue,2) as Prev_Month_Revenue,
    Revenue, 
    ROUND(((Revenue / Prev_Month_Revenue) - 1) * 100 , 2) as revenue_growth 
FROM
    monthly_revenue;
```

## 29. List out the top 5% highest contributing customers.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION
    SELECT * FROM sales_2017
),
per_rank_cust AS
(
    SELECT 
        CONCAT(C.FirstName,' ',C.LastName) as CustomerName,
        ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue,
        NTILE(100) OVER (ORDER BY SUM(ProductPrice * OrderQuantity) DESC) as per_rank
    FROM 
        sales S JOIN products P 
        ON S.product_ID = P.product_ID
        JOIN Customers C  
        ON S.customer_ID = C.customerID
    GROUP BY 1
)

SELECT
    CustomerName,
    Revenue
FROM
    per_rank_cust
WHERE
    per_rank < 5;
```

## 30. Summation of current monthly revenue and previous monthly revenue.

```sql
WITH sales AS
(
    SELECT * FROM sales_2015
    UNION
    SELECT * FROM sales_2016
    UNION


    SELECT * FROM sales_2017
),
monthly_rev as
(
    SELECT 
        TO_CHAR(OrderDate,'yyyy-mm') as month,
        ROUND(SUM(ProductPrice * OrderQuantity),2) as Revenue
    FROM 
        sales S JOIN products P 
        ON S.product_ID = P.product_ID 
    GROUP BY 1
    ORDER BY 1 ASC
)

SELECT
    *,
    SUM(Revenue) OVER (ORDER BY month ROWS BETWEEN 1 PRECEDING AND CURRENT ROW)
FROM
    monthly_rev;
```

