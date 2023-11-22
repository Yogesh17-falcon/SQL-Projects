# Airline Database

### 1. Represent the "book_date" column in "yyyy-mmm-dd" format using Bookings table

```sql
SELECT
    book_ref,
    TO_CHAR(book_date, 'yyyy-mon-dd') AS book_date,
    total_amount
FROM 
    bookings;
```

### 2. Get the following columns in the exact same sequence.

```sql
SELECT
    BP.ticket_no,
    BP.boarding_no,
    BP.seat_no,
    TI.passenger_id,
    TI.passenger_name
FROM 
    boarding_passes BP
    INNER JOIN
    tickets TI
ON 
    BP.ticket_no = TI.ticket_no;
```

### 3. Write a query to find the seat number which is least allocated among all the seats?

```sql
WITH cte AS
(
    SELECT
        seat_no,
        RANK() OVER (ORDER BY COUNT(seat_no) ASC) as least_no 
    FROM 
        boarding_passes 
    GROUP BY 
        1
)

SELECT
    seat_no
FROM
    cte
WHERE 
    least_no = 1;
```

### 4. In the database, identify the month-wise highest paying passenger name and passenger id.

```sql
WITH cte AS
(
    SELECT
        TO_CHAR(BO.book_date, 'mon-yy') AS Month_name,
        TI.passenger_id,
        TI.passenger_name,
        MAX(BO.total_amount) AS total_amount,
        RANK() OVER (PARTITION BY TO_CHAR(BO.book_date, 'mon-yy') ORDER BY MAX(BO.total_amount) DESC) as high_amt
    FROM
        bookings BO
        INNER JOIN
        tickets TI
    ON
        BO.book_ref = TI.book_ref
    GROUP BY
        1,2,3
)

SELECT
    Month_name,
    passenger_id,
    passenger_name,
    total_amount
FROM
    cte
WHERE
    high_amt = 1;
```

### 5. In the database, identify the month-wise least paying passenger name and passenger id?

```sql
WITH cte AS
(
    SELECT
        TO_CHAR(BO.book_date, 'mon-yy') AS Month_name,
        TI.passenger_id,
        TI.passenger_name,
        MIN(BO.total_amount) AS total_amount,
        RANK() OVER (PARTITION BY TO_CHAR(BO.book_date, 'mon-yy') ORDER BY MIN(BO.total_amount) ASC) as low_amt
    FROM
        bookings BO
        INNER JOIN
        tickets TI
    ON
        BO.book_ref = TI.book_ref
    GROUP BY
        1,2,3
)

SELECT
    Month_name,
    passenger_id,
    passenger_name,
    total_amount
FROM
    cte
WHERE
    low_amt = 1;
```

### 6. Identify the travel details of non-stop journeys or return journeys (having more than 1 flight).

```sql
WITH cte AS 
(
    SELECT
        T.passenger_id,
        T.passenger_name,
        TF.ticket_no,
        COUNT(*) as flight_count
    FROM 
        tickets T
        INNER JOIN
        ticket_flights TF 
    ON
        T.ticket_no = TF.ticket_no
        INNER JOIN
        flights F
    ON
        F.flight_id = TF.flight_id
    GROUP BY 
        1,2,3
)

SELECT
    passenger_id,
    passenger_name,
    ticket_no,
    flight_count
FROM 
    cte
WHERE 
    flight_count > 1;
```

### 7. How many tickets are there without boarding passes?

```sql
SELECT
    COUNT(*)
FROM
    tickets TI
    LEFT JOIN
    boarding_passes BO
ON
    TI.ticket_no = BO.ticket_no
WHERE
    BO.ticket_no IS NULL;
```

### 8. Identify details of the longest flight (using flights table)?

```sql
SELECT
    flight_no,
    departure_airport,
    arrival_airport,
    aircraft_code,
    MAX(scheduled_arrival - scheduled_departure) AS durations
FROM
    flights
WHERE
    scheduled_arrival - scheduled_departure =  (SELECT MAX(scheduled_arrival - scheduled_departure) FROM flights)
GROUP BY
    1,2,3,4
ORDER BY
    5 DESC;
```

### 9. Identify details of all the morning flights (morning means between 6 AM to 11 AM, using flights table)?

```sql
SELECT
    flight_id,
    flight_no,
    scheduled_departure,
    scheduled_arrival,
    'morning flight' AS Timings
FROM
    flights
WHERE
    EXTRACT(HOUR FROM scheduled_departure) >= 6 AND EXTRACT(HOUR FROM scheduled_departure) < 11;
```

### 10. Identify the earliest morning flight available from every airport.

```sql
WITH cte AS 
(
    SELECT 
        flight_id,
        flight_no,
        scheduled_departure,
        scheduled_arrival,
        departure_airport,
        'earliest morning flight' AS timings,
        RANK() OVER (PARTITION BY departure_airport ORDER BY scheduled_departure ASC) as ranks
    FROM 
        flights
    WHERE
        EXTRACT(HOUR FROM scheduled_departure) >= 2 
        AND
        EXTRACT(HOUR FROM scheduled_departure) < 6
)
SELECT 
    flight_id,
    flight_no,
    scheduled_departure,
    scheduled_arrival,
    departure_airport,
    timings
FROM
    cte
WHERE   
    ranks = 1
ORDER BY
    timings ASC;
```

Certainly! Here are the additional SQL queries formatted for a README.md file:

### 11. Find list of airport codes in Europe/Moscow timezone

```sql
SELECT
    airport_code
FROM 
    airports
WHERE
    timezone = 'Europe/Moscow';
```

### 12. Write a query to get the count of seats in various fare conditions for every aircraft code?

```sql
SELECT
    aircraft_code,
    COUNT(seats) AS seat_count,
    fare_conditions
FROM 
    seats
GROUP BY
    1,3;
```

### 13. How many aircraft codes have at least one Business class seat?

```sql
WITH cte AS
(
    SELECT
        COUNT(DISTINCT aircraft_code) AS aircraft_code_count,
        COUNT(seats) AS business_seat_count
    FROM 
        seats
    WHERE
        fare_conditions = 'Business'
)

SELECT
    aircraft_code_count
FROM
    cte
WHERE
    business_seat_count > 0;
```

### 14. Find out the name of the airport having the maximum number of departure flights

```sql
WITH cte AS
(
    SELECT
        departure_airport AS Airport_name,
        COUNT(flight_id) AS departure_flight_count
    FROM 
        flights F
        INNER JOIN
        airports A
    ON
        A.airport_code = F.departure_airport
    GROUP BY
        1
    ORDER BY
        2 DESC
    LIMIT 1
)

SELECT
    Airport_name
FROM
    cte;
```

### 15. Find out the name of the airport having the least number of scheduled departure flights

```sql
WITH cte AS
(
    SELECT
        departure_airport AS Airport_name,
        COUNT(flight_id) AS departure_flight_count
    FROM 
        flights F
        INNER JOIN
        airports A
    ON
        A.airport_code = F.departure_airport
    GROUP BY
        1
    ORDER BY
        2 ASC
    LIMIT 1
)

SELECT
    Airport_name
FROM
    cte;
```

### 16. How many flights from 'DME' airport don't have actual departure?

```sql
SELECT
    COUNT(*) AS flight_count 
FROM
    flights
WHERE
    actual_departure IS NULL
    AND
    departure_airport = 'DME';
```

### 17. Identify flight ids having a range between 3000 to 6000

```sql
SELECT
    DISTINCT F.flight_no,
    AC.aircraft_code,
    AC.range
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    range BETWEEN 3000 AND 6000;
```

### 18. Write a query to get the count of flights flying between URS and KUF?

```sql
SELECT
    COUNT(flight_id) AS flight_count
FROM
    flights
WHERE
    (departure_airport = 'URS' AND arrival_airport = 'KUF')
    OR
    (departure_airport = 'KUF' AND arrival_airport = 'URS');
```

### 19. Write a query to get the count of flights flying from either from NOZ or KRR?

```sql
SELECT
    COUNT(flight_id) AS flight_count
FROM
    flights
WHERE
    departure_airport = 'NOZ' 
    OR
    departure_airport = 'KRR';
```

### 20. Write a query to get the count of flights flying from KZN, DME, NBC, NJC, GDX, SGC, VKO, ROV

```sql
SELECT
    departure_airport,
    COUNT(flight_id) AS flight_count
FROM
    flights
WHERE
    departure_airport IN ('KZN','DME','NBC','NJC','GDX','SGC','VKO','ROV')
GROUP BY
    1;
```
Certainly! Here are the additional SQL queries formatted for a README.md file:

### 21. Write a query to extract flight details having a range between 3000 and 6000 and flying from DME

```sql
SELECT
    DISTINCT F.flight_no,
    AC.aircraft_code,
    AC.range,
    F.departure_airport
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    AC.range BETWEEN 3000 AND 6000
    AND
    F.departure_airport = 'DME';
```

### 22. Find the list of flight ids which are using aircrafts from “Airbus” company and got cancelled or delayed

```sql
SELECT
    F.flight_id,
    AC.model AS aircraft_model
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    F.status IN ('Delayed','Cancelled')
    AND
    AC.model LIKE '%Airbus%';
```

### 23. Find the list of flight ids which are using aircrafts from “Boeing” company and got cancelled or delayed

```sql
SELECT
    F.flight_id,
    AC.model AS aircraft_model
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    F.status IN ('Delayed','Cancelled')
    AND
    AC.model LIKE '%Boeing%';
```

### 24. Which airport(name) has the most cancelled flights (arriving)?

```sql
WITH cte AS
(
    SELECT  
        A.airport_name,
        RANK() OVER (ORDER BY COUNT(A.airport_name) DESC) AS rnk
    FROM
        airports A
        INNER JOIN
        flights F
    ON
        A.airport_code = F.arrival_airport
    WHERE
        F.status = 'Cancelled'
    GROUP BY
        1
)

SELECT
    airport_name
FROM
    cte
WHERE   
    rnk = 1;
```

### 25. Identify flight ids which are using “Airbus aircrafts”

```sql
SELECT
    F.flight_id,
    AC.model AS aircraft_model
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    AC.model LIKE '%Airbus%';
```

### 26. Identify date-wise last flight id flying from every airport?

```sql
WITH cte AS
(
    SELECT 
        flight_id,
        flight_no,
        scheduled_departure,
        departure_airport,
        RANK() OVER (PARTITION BY departure_airport ORDER BY scheduled_departure DESC) AS rnk
    FROM 
        flights  
)

SELECT 
    flight_id,
    flight_no,
    scheduled_departure,
    departure_airport
FROM
    cte
WHERE
    rnk = 1;
```

### 27. Identify a list of customers who will get the refund due to the cancellation of the flights and how much amount they will get?

```sql
SELECT
    T.passenger_name,
    SUM(TF.amount) AS total_refund
FROM
    tickets T
    JOIN
    ticket_flights TF
ON
    T.ticket_no = TF.ticket_no
    JOIN
    flights F
ON
    TF.flight_id = F.flight_id
WHERE
    F.status = 'Cancelled'
GROUP BY
    1;
```

### 28. Identify date-wise the first cancelled flight id flying for every airport?

```sql
WITH cte AS
(
    SELECT  
        flight_id,
        flight_no,
        scheduled_departure,
        departure_airport,
        RANK() OVER (PARTITION BY departure_airport ORDER BY scheduled_departure ASC) AS rnk
    FROM
        flights
    WHERE
        status = 'Cancelled'
)

SELECT 
    flight_id,
    flight_no,
    scheduled_departure,
    departure_airport
FROM
    cte
WHERE
    rnk = 1;
```

### 29. Identify a list of Airbus flight ids which got cancelled.

```sql
SELECT
    F.flight_id
FROM
    flights F
    INNER JOIN
    aircrafts AC
ON
    F.aircraft_code = AC.aircraft_code
WHERE
    AC.model LIKE '%Airbus%'
    AND
    F.status = 'Cancelled';
```

### 30. Identify a list of flight ids having the highest range.

```sql
WITH cte AS
(
    SELECT
        F.flight_id,
        F.flight_no AS flight_no,
        AC.range AS range
    FROM
        flights F
        INNER JOIN
        aircrafts AC
    ON
        F.aircraft_code = AC.aircraft_code
    WHERE   
        AC.range = (SELECT MAX(range) FROM aircrafts)
)

SELECT  
    flight_no,
    range
FROM
    cte;
```

