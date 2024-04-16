Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```sql
/* The total transaction revenues were filtered, summed up, and grouped according to the city. */
SELECT city, SUM(total_transaction_revenue::REAL / 1000000) AS total_transaction_revenue
FROM all_sessions
WHERE city != '(not set)' AND city!= 'not available in demo dataset' AND total_transaction_revenue IS NOT NULL AND transaction_id IS NOT NULL
GROUP BY city
ORDER BY total_transaction_revenue DESC

/* The total transaction revenues were filtered, summed up, and grouped according to the country. */
SELECT country, SUM(total_transaction_revenue::REAL / 1000000) AS total_transaction_revenue
FROM all_sessions
WHERE country != '(not set)' AND total_transaction_revenue IS NOT NULL AND transaction_id IS NOT NULL
GROUP BY country
ORDER BY total_transaction_revenue DESC
```



Answer:

These are the cities with the highest level of transaction revenues on the site: 

Sunnyvale at 849.24, 
Sydney at 358.00, 
Austin at 35.78, and 
Mountain View at 8.98.

These are the countries with the highest level of transaction revenues on the site: 

United States at 3,208.95 and 
Australia at 358.00.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

```sql
/* The units sold is presumed to have the amount of products sold in one order linked to the visit id, which in turn, is joined with the city information to calculate the average for each city. */

WITH visitor_and_units_sold AS(
	SELECT DISTINCT visit_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.city, CAST(AVG(units_sold) AS INTEGER)
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.visit_id = vus.visit_id
WHERE als.city != '(not set)' AND city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY als.city

/* The units sold is presumed to have the amount of products sold in one order linked to the visit id, which in turn, is joined with the country information to calculate the average for each country. */

WITH visitor_and_units_sold AS(
	SELECT DISTINCT visit_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.country, CAST(AVG(units_sold) AS INTEGER)
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.visit_id = vus.visit_id
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY als.country
```


Answer:

These are the following cities with the average number of products as follows:



These are the following countires with the average number of products as follows:






**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

```sql
/* The product categories were ranked and partitioned by each city to see its product categories by most number of orders.  The SQL code was nested with another CTE function to filter out the top product category in each city. */

WITH top_product_categories_by_city AS (

WITH city_sum_of_orders AS (
	SELECT DISTINCT visit_id, SUM(units_sold) AS sum_of_orders
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY visit_id
)
SELECT 
	als.city, 
	als.v2_product_category,
	RANK () OVER (PARTITION BY als.city ORDER BY cso.sum_of_orders DESC) AS rank
FROM all_sessions als
JOIN city_sum_of_orders cso
	ON als.visit_id = cso.visit_id
WHERE als.city != '(not set)' AND als.city != 'not available in demo dataset' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_category, cso.sum_of_orders
)

SELECT ts.city, ts.v2_product_category
FROM top_product_categories_by_city ts
WHERE rank = 1

/* The product categories were ranked and partitioned by each country to see its product categories by most number of orders.  The SQL code was nested with another CTE function to filter out the top product category in each country. */
WITH top_product_categories_by_country AS (

WITH country_sum_of_orders AS (
	SELECT DISTINCT visit_id, SUM(units_sold) AS sum_of_orders
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY visit_id
)
SELECT 
	als.country, 
	als.v2_product_category,
	cso.sum_of_orders,
	RANK () OVER (PARTITION BY als.country ORDER BY cso.sum_of_orders DESC) AS rank
FROM all_sessions als
JOIN country_sum_of_orders cso
	ON als.visit_id = cso.visit_id
WHERE als.country != '(not set)' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_category, cso.sum_of_orders
)

SELECT ts.country, ts.v2_product_category
FROM top_product_categories_by_country ts
WHERE rank = 1
```

Answer:



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

/* The top selling product from each city was determined by joining the product name and city based on the number of orders.  The product with the most number of order was ranked and filtered out by cities. */
```sql
WITH top_selling_products_by_city AS (

WITH number_of_orders AS (
	SELECT DISTINCT visit_id, COUNT(visit_id) AS top_sellers
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY visit_id
)
SELECT
	als.city, 
	als.v2_product_name, 
	noo.top_sellers,
	RANK () OVER(PARTITION BY als.city ORDER BY noo.top_sellers DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.visit_id = noo.visit_id
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_name, noo.top_sellers
ORDER BY als.city
)
SELECT *
FROM top_selling_products_by_city
WHERE rank = 1


/* The top selling product from each country was determined by joining the product name and country based on the number of orders.  The product with the most number of order was ranked and filtered out by countries. */
WITH top_selling_products_by_country AS (

WITH number_of_orders AS (
	SELECT DISTINCT visit_id, COUNT(visit_id) AS top_sellers
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY visit_id
)
SELECT
	als.country, 
	als.v2_product_name, 
	noo.top_sellers,
	RANK () OVER(PARTITION BY als.country ORDER BY noo.top_sellers DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.visit_id = noo.visit_id
WHERE als.country != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_name, noo.top_sellers
ORDER BY als.country
)
SELECT *
FROM top_selling_products_by_country
WHERE rank = 1
```



Answer:

I find that it is difficult to find a pattern in the products sold because there is missing information from cities, countries, and both, but there were orders applied.  Had this missing information be available in the dataset, I think it would affect the results for the top product in their respective cities and countries.



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```sql
WITH revenue AS (
	SELECT DISTINCT visit_id, revenue / 1000000 AS revenue
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.city, SUM(r.revenue) AS sum_revenue
FROM all_sessions als
JOIN revenue r
	ON als.visit_id = r.visit_id
WHERE als.city != '(not set)' AND als.city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY sum_revenue DESC


WITH revenue AS (
	SELECT DISTINCT visit_id, revenue / 1000000 AS revenue
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.country, SUM(r.revenue) AS sum_revenue
FROM all_sessions als
JOIN revenue r
	ON als.visit_id = r.visit_id
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY sum_revenue DESC
```



Answer:

Here is the summary of the revenue impact generated from each city:

New York at 1,103,
Sunnyvale at 608, 
Mountain View at 481, 
Seattle at 357, 
Chicago at 306, 
San Francisco at 276, 
San Jose at 153, 
Palo Alto at 151, 
Tel Aviv-Yafo at 32, and 
Zurich at 16.

Here is the summary of the revenue impact generated from each country:

United States at 4,629
Israel at 32, and
Switzerland at 16.


