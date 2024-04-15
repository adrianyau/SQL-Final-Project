Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```sql
/* Group cities by sum of transaction revenues where purchase was completed ordered by descending */
SELECT city, SUM(total_transaction_revenue::REAL / 1000000) AS total_transaction_revenue
FROM all_sessions
WHERE city != '(not set)' AND city!= 'not available in demo dataset' AND total_transaction_revenue IS NOT NULL AND transaction_id IS NOT NULL
GROUP BY city
ORDER BY total_transaction_revenue DESC

/* Group countries by sum of transaction revenues where purchase was completed ordered by descending */
SELECT country, SUM(total_transaction_revenue::REAL / 1000000) AS total_transaction_revenue
FROM all_sessions
WHERE country != '(not set)' AND total_transaction_revenue IS NOT NULL AND transaction_id IS NOT NULL
GROUP BY country
ORDER BY total_transaction_revenue DESC
```



Answer:

These are the cities with the highest level of transaction revenues on the site: Sunnyvale at 849.24, Sydney at 358.00, Austin at 35.78, and Mountain View at 8.98.

These are the countries with the highest level of transaction revenues on the site: United States at 3,208.95 and Australia at 358.00.




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

DRAFT:

```sql
WITH visitor_and_units_sold AS(
	SELECT DISTINCT full_visitor_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.city, CAST(AVG(units_sold) AS INTEGER)
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.full_visitor_id = vus.full_visitor_id
WHERE als.city != '(not set)' AND city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY als.city


WITH visitor_and_units_sold AS(
	SELECT DISTINCT full_visitor_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.country, CAST(AVG(units_sold) AS INTEGER)
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.full_visitor_id = vus.full_visitor_id
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY als.country
```


Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

DRAFT:

```sql
SELECT als.city, SUM(a.revenue) as sum_revenue, SUM(a.units_sold * a.unit_price) as calc_revenue
FROM all_sessions als
JOIN analytics a
	ON als.full_visitor_id = a.full_visitor_id
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND units_sold IS NOT NULL AND revenue IS NOT NULL
GROUP BY als.city
ORDER BY sum_revenue DESC, calc_revenue DESC


SELECT als.country, SUM(a.revenue) as sum_revenue, SUM(a.units_sold * a.unit_price) as calc_revenue
FROM all_sessions als
JOIN analytics a
	ON als.full_visitor_id = a.full_visitor_id
WHERE country != '(not set)' AND units_sold IS NOT NULL AND revenue IS NOT NULL
GROUP BY als.country
ORDER BY sum_revenue DESC, calc_revenue DESC
```



Answer:

No, because there are two different revenues provided from the database.  One set of data has the revenues, and the other set of data can be used multiplying the number of units sold and unit price, but it does not match the revenue originally on the table.





