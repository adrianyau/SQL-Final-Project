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

City		Average Number of Products
"Ahmedabad"	1
"Ann Arbor"	1
"Atlanta"	5
"Austin"	5
"Bangkok"	1
"Berlin"	1
"Bogota"	1
"Cambridge"	2
"Charlotte"	10
"Chicago"	9
"Courbevoie"	1
"Cupertino"	1
"Dallas"	1
"Denver"	2
"Detroit"	1
"Dublin"	1
"Fremont"	1
"Hamburg"	2
"Helsinki"	1
"Hong Kong"	2
"Houston"	3
"Hyderabad"	2
"Irvine"	1
"Jakarta"	1
"Jersey City"	8
"Kirkland"	2
"Kitchener"	1
"London"	1
"Los Angeles"	5
"Madrid"	1
"Milpitas"	4
"Minato"	1
"Montevideo"	1
"Mountain View"	13
"Munich"	1
"Nashville"	6
"New York"	11
"Osaka"	1
"Palo Alto"	1
"Paris"	3
"Phoenix"	1
"Pittsburgh"	4
"Salem"	11
"San Bruno"	71
"San Diego"	1
"San Francisco"	1
"San Jose"	12
"Santa Clara"	1
"Santa Monica"	2
"Santiago"	1
"Seattle"	15
"Seoul"	1
"Singapore"	1
"South San Francisco"	1
"Stockholm"	1
"Sunnyvale"	14
"Sydney"	1
"Tel Aviv-Yafo"	1
"Toronto"	2
"Washington"	1
"Yokohama"	1
"Zhongli District"	1
"Zurich"	1

Country		Average Number of Products
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





