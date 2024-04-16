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

These are the cities with the highest level of transaction revenues on the site: Sunnyvale at 849.24, Sydney at 358.00, Austin at 35.78, and Mountain View at 8.98.

These are the countries with the highest level of transaction revenues on the site: United States at 3,208.95 and Australia at 358.00.




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

DRAFT:

```sql
/* The units sold is presumed to have the amount of products sold in one order linked to the full visitor id, which in turn, is joined with the city information to calculate the average for each city. */

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

/* The units sold is presumed to have the amount of products sold in one order linked to the full visitor id, which in turn, is joined with the country information to calculate the average for each country. */

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

These are the following cities with the average number of products as follows:

Ahmedabad with 1, Ann Arbor with 1, Atlanta with 5, Austin with	5, Bangkok with	1, Berlin with 1, Bogota with 1, Cambridge with 2, Charlotte with 10, Chicago with 9, Courbevoie with 1, Cupertino with 1, Dallas with 1, Denver with 2, Detroit with 1, Dublin with 1, Fremont with 1, Hamburg with 2, Helsinki with 1, Hong Kong with	2, Houston with	3, Hyderabad with 2, Irvine with 1, Jakarta with 1, Jersey City with 8, Kirkland with 2, Kitchener with 1, London with 1, Los Angeles with 5, Madrid with 1, Milpitas with 4, Minato with 1, Montevideo with 1, Mountain View with 13, Munich with 1, Nashville with 6, New York with 11, Osaka with 1, Palo Alto with 1, Paris with 3, Phoenix with 1, Pittsburgh with 4, Salem with 11, San Bruno with 71, San Diego with 1, San Francisco with 1, San Jose with 12, Santa Clara with 1, Santa Monica with 2, Santiago with 1, Seattle with 15, Seoul with 1, Singapore with 1, South San Francisco with 1, Stockholm with 1, Sunnyvale with 14, Sydney with 1, Tel Aviv-Yafo with 1, Toronto with 2, Washington with 1, Yokohama with 1, Zhongli District with 1, Zurich with 1.

These are the following countires with the average number of products as follows:

"Australia"	1
"Austria"	1
"Belarus"	1
"Belgium"	1
"Bulgaria"	2
"Canada"	3
"Chile"	1
"Colombia"	1
"Czechia"	25
"Denmark"	1
"Dominican Republic"	1
"Egypt"	1
"Finland"	1
"France"	1
"Germany"	1
"Guatemala"	1
"Hong Kong"	2
"India"	1
"Indonesia"	1
"Ireland"	1
"Israel"	1
"Japan"	2
"Maldives"	1
"Mexico"	2
"Netherlands"	1
"Norway"	1
"Panama"	1
"Portugal"	1
"Romania"	1
"Russia"	1
"Singapore"	1
"South Korea"	1
"Spain"	1
"Sri Lanka"	1
"Sweden"	1
"Switzerland"	1
"Taiwan"	1
"Thailand"	1
"United Kingdom"	1
"United States"	27
"Uruguay"	1
"Vietnam"	1



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

DRAFT:

```sql
WITH top_product_categories_by_city AS (

WITH num_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS num_of_visitors
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT 
	als.city, 
	als.v2_product_category,
	SUM(noo.num_of_visitors),
	RANK () OVER (PARTITION BY als.city ORDER BY noo.num_of_visitors DESC) AS rank
FROM all_sessions als
JOIN num_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_category, noo.num_of_visitors
)
SELECT *
FROM top_product_categories_by_city
WHERE rank = 1


WITH top_categories_by_countries AS (

WITH num_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS num_of_visitors
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT 
	als.country, 
	als.v2_product_category,
	SUM(noo.num_of_visitors),
	RANK () OVER (PARTITION BY als.country ORDER BY noo.num_of_visitors DESC) AS rank
FROM all_sessions als
JOIN num_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE city != '(not set)' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_category, noo.num_of_visitors
)
SELECT *
FROM top_categories_by_countries
WHERE rank = 1
```

Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

DRAFT:

```sql
WITH top_selling_products_by_city AS (

WITH number_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS num_of_visitors
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT
	als.city, 
	als.v2_product_name, 
	noo.num_of_visitors,
	RANK () OVER(PARTITION BY als.city ORDER BY noo.num_of_visitors DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_name, noo.num_of_visitors
ORDER BY als.city
)
SELECT *
FROM top_selling_products_by_city
WHERE rank = 1


WITH top_selling_products_by_country AS (

WITH number_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS num_of_visitors
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT
	als.country, 
	als.v2_product_name, 
	noo.num_of_visitors,
	RANK () OVER(PARTITION BY als.country ORDER BY noo.num_of_visitors DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE als.country != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_name, noo.num_of_visitors
ORDER BY als.country
)
SELECT *
FROM top_selling_products_by_country
WHERE rank = 1



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





