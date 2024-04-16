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

Ahmedabad with 1, Ann Arbor with 1, Atlanta with 5, Austin with	5, Bangkok with	1, Berlin with 1, Bogota with 1, Cambridge with 2, Charlotte with 10, Chicago with 9, Courbevoie with 1, Cupertino with 1, Dallas with 1, Denver with 2, Detroit with 1, Dublin with 1, Fremont with 1, Hamburg with 2, Helsinki with 1, Hong Kong with	2, Houston with	3, Hyderabad with 2, Irvine with 1, Jakarta with 1, Jersey City with 8, Kirkland with 2, Kitchener with 1, London with 1, Los Angeles with 5, Madrid with 1, Milpitas with 4, Minato with 1, Montevideo with 1, Mountain View with 13, Munich with 1, Nashville with 6, New York with 11, Osaka with 1, Palo Alto with 1, Paris with 3, Phoenix with 1, Pittsburgh with 4, Salem with 11, San Bruno with 71, San Diego with 1, San Francisco with 1, San Jose with 12, Santa Clara with 1, Santa Monica with 2, Santiago with 1, Seattle with 15, Seoul with 1, Singapore with 1, South San Francisco with 1, Stockholm with 1, Sunnyvale with 14, Sydney with 1, Tel Aviv-Yafo with 1, Toronto with 2, Washington with 1, Yokohama with 1, Zhongli District with 1, and Zurich with 1.

These are the following countires with the average number of products as follows:

Australia with 1, Austria with 1, Belarus with 1, Belgium with 1, Bulgaria with 2, Canada with 3, Chile with 1, Colombia with 1, Czechia with 25, Denmark with 1, Dominican Republic with 1, Egypt with 1, Finland with 1, France with 1, Germany with 1, Guatemala with 1, Hong Kong with 2, India with 1, Indonesia with 1, Ireland with 1, Israel with 1, Japan with 2, Maldives with 1, Mexico with 2, Netherlands with 1, Norway with 1, Panama with 1, Portugal with 1, Romania with 1, Russia with 1, Singapore with 1, South Korea with	1, Spain with 1, Sri Lanka with 1, Sweden with 1, Switzerland with 1, Taiwan with 1, Thailand with 1, United Kingdom with 1, United States with 27, Uruguay with 1, and Vietnam with 1.



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

DRAFT:

```sql
WITH top_product_categories_by_city AS (

WITH city_sum_of_orders AS (
	SELECT DISTINCT full_visitor_id, SUM(units_sold) AS sum_of_orders
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT 
	als.city, 
	als.v2_product_category,
	cso.sum_of_orders,
	RANK () OVER (PARTITION BY als.city ORDER BY cso.sum_of_orders DESC) AS rank
FROM all_sessions als
JOIN city_sum_of_orders cso
	ON als.full_visitor_id = cso.full_visitor_id
WHERE als.city != '(not set)' AND als.city != 'not available in demo dataset' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_category, cso.sum_of_orders
)

SELECT *
FROM top_product_categories_by_city
WHERE rank = 1


WITH top_product_categories_by_country AS (

WITH country_sum_of_orders AS (
	SELECT DISTINCT full_visitor_id, SUM(units_sold) AS sum_of_orders
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT 
	als.country, 
	als.v2_product_category,
	cso.sum_of_orders,
	RANK () OVER (PARTITION BY als.country ORDER BY cso.sum_of_orders DESC) AS rank
FROM all_sessions als
JOIN country_sum_of_orders cso
	ON als.full_visitor_id = cso.full_visitor_id
WHERE als.country != '(not set)' AND als.v2_product_category != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_category, cso.sum_of_orders
)

SELECT *
FROM top_product_categories_by_country
WHERE rank = 1
```

Answer:

I do not think that there is a pattern on product categories from visitors in each city and country.  For example, the largest product category sold in the city of Mountain View, United States is stickers for 5,776 units.  However, the largest product category sold in the country of the United States is dog frisbee for 11,328 units.  We do not know which cities sold dog frisbess in the United States.  Hypotheically, if it were discouvered that the majority of these products were purchased in the city of Mountain View, United States, then the largest product category sold should be dog frisbees.



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

DRAFT:

```sql
WITH top_selling_products_by_city AS (

WITH number_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS top_sellers
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT
	als.city, 
	als.v2_product_name, 
	noo.top_sellers,
	RANK () OVER(PARTITION BY als.city ORDER BY noo.top_sellers DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.city, als.v2_product_name, noo.top_sellers
ORDER BY als.city
)
SELECT *
FROM top_selling_products_by_city
WHERE rank = 1


WITH top_selling_products_by_country AS (

WITH number_of_orders AS (
	SELECT DISTINCT full_visitor_id, COUNT(full_visitor_id) AS top_sellers
	FROM analytics
	WHERE units_sold IS NOT NULL
	GROUP BY full_visitor_id
)
SELECT
	als.country, 
	als.v2_product_name, 
	noo.top_sellers,
	RANK () OVER(PARTITION BY als.country ORDER BY noo.top_sellers DESC) AS rank
FROM all_sessions als
JOIN number_of_orders noo
	ON als.full_visitor_id = noo.full_visitor_id
WHERE als.country != '(not set)' AND als.v2_product_category != '${escCatTitle}'
GROUP BY als.country, als.v2_product_name, noo.top_sellers
ORDER BY als.country
)
SELECT *
FROM top_selling_products_by_country
WHERE rank = 1
```



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

DRAFT:

```sql
WITH revenue AS (
	SELECT DISTINCT full_visitor_id, revenue, units_sold, unit_price
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.city, SUM(r.revenue) AS sum_revenue
FROM all_sessions als
JOIN revenue r
	ON als.full_visitor_id = r.full_visitor_id
WHERE als.city != '(not set)' AND als.city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY sum_revenue DESC


WITH revenue AS (
	SELECT DISTINCT full_visitor_id, revenue, units_sold, unit_price
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.country, SUM(r.revenue) AS sum_revenue,
FROM all_sessions als
JOIN revenue r
	ON als.full_visitor_id = r.full_visitor_id
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY sum_revenue DESC
```



Answer:

Here is the summary of the revenue impact generated from each city:

Mountain View 10470949996
San Bruno 4230990000
Sunnyvale 2999357772
New York 2996275708
Chicago	1383630000
Kirkland 1103520000
San Francisco 954103332
Jersey City 933850000
Charlotte 932885550
Los Angeles 926009999
Seattle	732825713
Palo Alto 709000000
Austin 643689998
Milpitas 494300000
Toronto	447480000
Ann Arbor 355879999
Salem 246430000
San Jose 236350000
Cambridge 147430000
Fremont	124000000
Atlanta	83000000
South San Francisco 82420000
Denver 41980000
Yokohama 30880000
Zurich	16990000

Here is the summary of the revenue impact generated from each country:

United States at 115,229,279,900, Canada at 48,767,0000, Germany at 69,980,000, Japan at 30,880,000, and Switzerland at 16,990,000.


