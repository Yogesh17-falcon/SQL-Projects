# Northwind Database

### 1. Using Orders table, write the query to count distinct customers who purchase anything from Northwind
Expected output: Single number denoting the distinct transacting customers

```sql
SELECT
    COUNT(DISTINCT customer_id) AS distinct_customers_count
FROM 
    orders;
```

### 2. Get the details of the orders made by VINET, TOMSP, HANAR, VICTE, SUPRD, CHOPS from the orders table.
Expected columns in the output – Order_id, order_date, customer_id, Ship_country and Employee_id

```sql
SELECT
    order_id, 
    order_date, 
    customer_id, 
    ship_country, 
    employee_id
FROM 
    orders
WHERE
    customer_id
    IN
    ('VINET', 'TOMSP', 'HANAR', 'VICTE', 'SUPRD', 'CHOPS');
```

### 3. According to the customers table, list down the customer_ids which start from "L" and end at "S"
Expected columns in the output – Customer_id

```sql
SELECT
    customer_id
FROM 
    customers
WHERE
    customer_id LIKE 'L%'
    AND
    customer_id LIKE '%S';
```

### 4.	According to the customers table, list down the customer_ids of france which starts from “L”
Expected columns in the output – Customer_id

```sql
SELECT
    customer_id
FROM 
    customers
WHERE
    country = 'France'
    AND
    customer_id LIKE 'L%';
```

### 5. The company is planning to give a 10% discount on products above 10 dollars price point(including). Get the list of the product_id which are going to be listed at discounted price
Expected columns in the output – Product_id

```sql
SELECT
    product_id
FROM 
    products
WHERE
    unit_price >= 10;
```

### 6.	According to the products table, which category_ids have more than 500 units_in_stock?
Expected columns in the output – category_id, total units_in_stock

```sql
SELECT
    category_id, 
    SUM(units_in_stock) AS total_units_in_stock
FROM 
    products
GROUP BY
    category_id
HAVING
    SUM(units_in_stock) > 500;
```

### 7. According to the products table, list the supplier_ids responsible for supplying exactly 5 products from the list.
Expected columns in the output –  supplier id, total products supplied

```sql
SELECT
    supplier_id, 
    COUNT(product_id) AS total_products_supplied
FROM 
    products
GROUP BY
    supplier_id
HAVING
    COUNT(product_id) = 5;
```

### 8. Using the orders table, create a table where the count of orders placed would be mentioned against every customer_id.
Expected columns in the output – Customer_id, count of orders

```sql
SELECT
    customer_id, 
    COUNT(order_id) AS count_of_orders
FROM
    orders
GROUP BY
    customer_id;
```

### 9.	Using the orders table, create a table where the count of orders placed would be mentioned against every customer_id but only for customers having at least 10 orders
Expected columns in the output – Customer_id, count of orders

```sql
SELECT
    customer_id, 
    COUNT(order_id) AS count_of_orders
FROM
    orders
GROUP BY
    customer_id
HAVING
    COUNT(order_id) >= 10;
```

### 10.	The Order_Details table is unique at the order_id and product_id levels. It shows the various products ordered for every order_id. Northwind is using bigger boxes for orders having 6 or more product_ids. Can you extract the list of order ids along with the count of products ordered?
Expected output: Order_id, count of products

```sql
SELECT
    order_id, 
    COUNT(product_id) AS count_of_products
FROM
    order_details
GROUP BY
    order_id
HAVING
    COUNT(product_id) >= 6;
```
