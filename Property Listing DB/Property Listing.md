# Property Listing Database 

### 1. Top 5 Most Expensive Listings Overall

```sql
SELECT 
    id, host_name, price 
FROM 
    listings 
ORDER BY 
    price DESC
LIMIT 5;
```

### 2. Number of Listings in Each Neighbourhood

```sql
SELECT 
    neighbourhood,
    COUNT(id) AS no_of_listings
FROM 
    listings 
GROUP BY 
    neighbourhood
ORDER BY 
    no_of_listings DESC;
```

### 3. Average Price of Listings in Each Neighbourhood

```sql
SELECT 
    neighbourhood,
    ROUND(AVG(price), 2) AS avg_price_of_listings
FROM 
    listings 
GROUP BY 
    neighbourhood
ORDER BY 
    avg_price_of_listings DESC;
```

### 4. Overall Occupancy Rate for Listings

```sql
SELECT 
    100 - ROUND((AVG(availability_365) / 365) * 100, 2) AS occupancy_rate
FROM 
    listings;
```

### 5. Average Nights Booked per Month

```sql
SELECT 
    id,
    (365 - availability_365) / 12 AS nights_booked 
FROM 
    listings
ORDER BY 
    nights_booked DESC;
```

### 6. Highest-Rated Listings Based on Guest Reviews

```sql
WITH cte AS (
    SELECT 
        id, name,
        SUBSTRING(name FROM '★([0-9.]+)') AS rating
    FROM 
        listings
)

SELECT
    *
FROM
    cte
WHERE
    rating = '5.0';
```

### 7. Average Review Score for Listings in Different Neighbourhoods

```sql
WITH cte AS (
    SELECT 
        neighbourhood,
        SUBSTRING(name FROM '★([0-9.]+)')::numeric AS rating
    FROM 
        listings
)

SELECT
    neighbourhood,
    ROUND(AVG(rating), 2) AS avg_rating
FROM
    cte
WHERE
    rating IS NOT NULL
GROUP BY
    neighbourhood
ORDER BY 
    avg_rating DESC;
```

### 8. Top 5 Hosts with the Most Listings

```sql
SELECT 
    host_name,
    COUNT(id) AS no_of_listings
FROM 
    listings
GROUP BY 
    host_name
ORDER BY 
    no_of_listings DESC
LIMIT 5;
```

### 9. Average Number of Listings per Host

```sql
WITH cte AS (
    SELECT 
        host_name,
        COUNT(id) AS cnt
    FROM 
        listings
    GROUP BY 
        host_name
)

SELECT
    ROUND(AVG(cnt), 2) AS Avg_no_of_listings
FROM
    cte;
```

### 10. Most Common Room Type Provided by Hosts

```sql
SELECT 
    room_type,
    COUNT(*) AS no_of_rooms
FROM 
    listings
GROUP BY 
    room_type
ORDER BY 
    no_of_rooms DESC
LIMIT 1;
```

### 11. Listings with Certain Room Type - Ratings and Prices

```sql
WITH cte AS (
    SELECT 
        room_type,
        SUBSTRING(name FROM '★([0-9.]+)')::numeric AS rating,
        price
    FROM 
        listings
)

SELECT
    room_type,
    ROUND(AVG(rating), 2) AS avg_rating,
    ROUND(AVG(price), 2) AS avg_price
FROM
    cte
WHERE
    rating IS NOT NULL
GROUP BY
    room_type
ORDER BY 
    avg_rating DESC;
```

### 12. Total Revenue Generated from Listings in a Specific Neighbourhood

```sql
SELECT 
    neighbourhood,
    SUM((365 - availability_365) * price) AS revenue
FROM 
    listings
GROUP BY 
    neighbourhood 
ORDER BY 
    revenue DESC;
```

### 13. Top 5 Listings Contributing the Most to Overall Revenue

```sql
SELECT 
    ID,
    name,
    host_name,
    SUM((365 - availability_365) * price) AS revenue
FROM 
    listings
GROUP BY 
    1, 2, 3 
ORDER BY 
    revenue DESC
LIMIT 5;
```

### 14. Average Price Difference Between Superhosts and Regular Hosts

```sql
WITH cte AS (
    SELECT 
        host_name,
        COUNT(id) AS no_of_listing,
        AVG(price) AS avg_price
    FROM 
        listings
    GROUP BY 
        host_name
)

SELECT 
    CASE 
        WHEN no_of_listing > 25 THEN 'SUPER_HOST'
        ELSE 'REGULAR_HOST'
    END AS host_classification,
    AVG(avg_price) AS avg_price
FROM
    cte
GROUP BY 
    host_classification;
```

### 15. Identify Outliers in Pricing Distribution

```sql
SELECT 
    *
FROM 
    listings
WHERE 
    price > (SELECT AVG(price) + 2 * STDDEV(price) FROM listings)
    OR 
    price < (SELECT AVG(price) - 2 * STDDEV(price) FROM listings);
```

### 16. Correlations Between Review Scores and Listing Characteristics

```sql
WITH cte AS (
    SELECT 
        room_type,
        SUBSTRING(name FROM '★([0-9.]+)')::numeric AS rating,
        price,
        minimum_nights,
        number_of_reviews,
        reviews_per_month :: numeric,
        calculated_host_listings_count,
        availability_365,
        number_of_reviews_ltm
    FROM 
        listings
)

SELECT
    room_type,
    ROUND(AVG(price), 2) AS price,
    ROUND(AVG(rating), 2) AS rating,
    ROUND(AVG(minimum_nights), 2) AS minimum_nights,
    ROUND(AVG(number_of_reviews), 2) AS number_of_reviews,
    ROUND(AVG(reviews_per_month), 2) AS reviews_per_month,
    ROUND(AVG(calculated_host_listings_count), 2) AS calculated_host_listings_count,
    ROUND(AVG(availability_365), 2) AS availability_365,
    ROUND(AVG(number_of_reviews_ltm), 2) AS number_of_reviews_ltm
FROM
    cte
WHERE
    rating IS NOT NULL
GROUP BY
    room_type
ORDER BY 
    price DESC;
```

### 17. Distribution of Property Types

```sql
SELECT
    SPLIT_PART(name, 'in', 1) AS unit,
    ROUND(AVG(price), 2) AS avg_price,
   

 COUNT(id) AS count
FROM
    listings
GROUP BY 
    unit
ORDER BY 
    avg_price DESC;
```

### 18. Property Types - Average Price and Popularity

```sql
SELECT
    SPLIT_PART(name, 'in', 1) AS unit,
    ROUND(AVG(price), 2) AS avg_price,
    AVG(SUBSTRING(name FROM '★([0-9.]+)')::numeric) AS rating
FROM
    listings
GROUP BY 
    unit
ORDER BY 
    rating DESC;
```

### 19. Identify Listings with the Highest and Lowest Response Rates

```sql
SELECT 
    id, 
    reviews_per_month
FROM 
    listings
WHERE 
    reviews_per_month = (SELECT MIN(reviews_per_month) FROM listings)
    OR
    reviews_per_month = (SELECT MAX(reviews_per_month) FROM listings);
```
