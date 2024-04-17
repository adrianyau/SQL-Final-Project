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

|city          |total_transaction_revenue|
|--------------|-------------------------|
|Sunnyvale     |849.24                   |
|Sydney        |358                      |
|Austin        |35.78                    |
|Mountain View |8.98                     |


These are the countries with the highest level of transaction revenues on the site: 

|country       |total_transaction_revenue|
|--------------|-------------------------|
|United States |3208.950032              |
|Australia     |358                      |



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

```sql
/* The units sold is presumed to have the amount of products sold in one order linked to the visit id, which in turn, is joined with the product information and city information to calculate the average for each city. */

WITH visitor_and_units_sold AS(
	SELECT DISTINCT visit_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.city, CAST(AVG(units_sold) AS INTEGER) AS avg_num_of_products
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.visit_id = vus.visit_id
JOIN products p
	ON als.product_sku = p.sku
WHERE als.city != '(not set)' AND city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY als.city

/* The units sold is presumed to have the amount of products sold in one order linked to the visit id, which in turn, is joined with the product information and country information to calculate the average for each city. */

WITH visitor_and_units_sold AS(
	SELECT DISTINCT visit_id, units_sold
	FROM analytics
	WHERE units_sold IS NOT NULL
)
SELECT als.country, CAST(AVG(units_sold) AS INTEGER) AS avg_num_of_products
FROM all_sessions als
JOIN visitor_and_units_sold vus
	ON als.visit_id = vus.visit_id
JOIN products p
	ON als.product_sku = p.sku
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY als.country
```



Answer:

These are the following cities with the average number of products as follows:

|city                                          |avg_num_of_products|
|----------------------------------------------|-------------------|
|Ann Arbor                                     |1                  |
|Atlanta                                       |1                  |
|Austin                                        |1                  |
|Bangkok                                       |1                  |
|Berlin                                        |1                  |
|Bogota                                        |1                  |
|Chicago                                       |6                  |
|Dallas                                        |1                  |
|Detroit                                       |1                  |
|Dublin                                        |1                  |
|Hong Kong                                     |1                  |
|Houston                                       |3                  |
|Hyderabad                                     |2                  |
|Kirkland                                      |1                  |
|London                                        |1                  |
|Mountain View                                 |1                  |
|Munich                                        |1                  |
|New York                                      |2                  |
|Palo Alto                                     |1                  |
|Paris                                         |1                  |
|Pittsburgh                                    |4                  |
|San Francisco                                 |2                  |
|San Jose                                      |1                  |
|Santiago                                      |1                  |
|Seattle                                       |1                  |
|Singapore                                     |1                  |
|Sunnyvale                                     |2                  |
|Tel Aviv-Yafo                                 |1                  |
|Toronto                                       |3                  |
|Washington                                    |1                  |
|Zurich                                        |1                  |




These are the following countires with the average number of products as follows:

|country                                       |avg_num_of_products|
|----------------------------------------------|-------------------|
|Australia                                     |1                  |
|Austria                                       |1                  |
|Belgium                                       |1                  |
|Bulgaria                                      |2                  |
|Canada                                        |3                  |
|Chile                                         |1                  |
|Colombia                                      |1                  |
|Denmark                                       |1                  |
|Egypt                                         |1                  |
|France                                        |1                  |
|Germany                                       |1                  |
|Hong Kong                                     |1                  |
|India                                         |1                  |
|Indonesia                                     |1                  |
|Ireland                                       |1                  |
|Israel                                        |1                  |
|Japan                                         |2                  |
|Maldives                                      |1                  |
|Mexico                                        |2                  |
|Netherlands                                   |1                  |
|Romania                                       |1                  |
|Singapore                                     |1                  |
|South Korea                                   |1                  |
|Sweden                                        |1                  |
|Switzerland                                   |1                  |
|Taiwan                                        |1                  |
|Thailand                                      |1                  |
|United Kingdom                                |1                  |
|United States                                 |2                  |
|Vietnam                                       |1                  |



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

```sql
/* The product categories were ranked and partitioned by each city to see product categories by highest number of orders.  Once estabished the SQL code, it was nested with another CTE function to filter out the top product category for each city. */

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

/* The product categories were ranked and partitioned by each city to see product categories by highest number of orders.  Once estabished the SQL code, it was nested with another CTE function to filter out the top product category for each country. */

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

The top product category for each city is as follows:

|city          |v2_product_category|
|--------------|-------------------|
|Ann Arbor     |Home/Apparel/Men's/Men's-T-Shirts/|
|Atlanta       |Home/Apparel/Men's/Men's-T-Shirts/|
|Atlanta       |Home/Bags/         |
|Austin        |Home/Apparel/Men's/Men's-Performance Wear/|
|Austin        |Home/Office/Notebooks & Journals/|
|Bangkok       |Home/Drinkware/    |
|Berlin        |Home/Office/       |
|Bogota        |Home/Apparel/Men's/Men's-T-Shirts/|
|Chicago       |Home/Shop by Brand/|
|Dallas        |Home/Shop by Brand/YouTube/|
|Detroit       |Drinkware          |
|Dublin        |Home/Bags/         |
|Dublin        |Home/Shop by Brand/YouTube/|
|Hong Kong     |Home/Apparel/Men's/Men's-T-Shirts/|
|Hyderabad     |Home/Accessories/Stickers/|
|Kirkland      |Home/Office/       |
|London        |Home/Office/Notebooks & Journals/|
|Mountain View |Home/Apparel/Men's/Men's-Outerwear/|
|Munich        |Home/Apparel/Headgear/|
|New York      |Home/Bags/Backpacks/|
|Palo Alto     |Nest-USA           |
|Paris         |Home/Shop by Brand/Android/|
|Pittsburgh    |Home/Office/Notebooks & Journals/|
|San Bruno     |Home/Bags/         |
|San Francisco |Home/Apparel/Women's/Women's-T-Shirts/|
|San Jose      |Home/Apparel/Men's/Men's-T-Shirts/|
|Santiago      |Home/Bags/         |
|Seattle       |Home/Nest/Nest-USA/|
|Sunnyvale     |Housewares         |
|Tel Aviv-Yafo |Home/Shop by Brand/YouTube/|
|Toronto       |Home/Apparel/Headgear/|
|Washington    |Home/Apparel/Kid's/Kid's-Infant/|
|Zurich        |Home/Apparel/Men's/Men's-T-Shirts/|


The top category for each country is as follows:

|country       |v2_product_category|
|--------------|-------------------|
|Australia     |Home/Accessories/Stickers/|
|Austria       |Home/Accessories/Housewares/|
|Belgium       |Home/Drinkware/Water Bottles and Tumblers/|
|Bulgaria      |Home/Apparel/Men's/Men's-T-Shirts/|
|Canada        |Home/Apparel/Headgear/|
|Chile         |Home/Bags/         |
|Colombia      |Home/Apparel/Men's/Men's-T-Shirts/|
|Denmark       |Home/Drinkware/    |
|Egypt         |Home/Shop by Brand/Android/|
|France        |Home/Shop by Brand/Android/|
|France        |Headgear           |
|Germany       |Home/Apparel/Headgear/|
|Germany       |Home/Office/       |
|Hong Kong     |Home/Nest/Nest-USA/|
|India         |Home/Accessories/Stickers/|
|Indonesia     |Home/Bags/Backpacks/|
|Ireland       |Home/Shop by Brand/YouTube/|
|Ireland       |Home/Bags/         |
|Israel        |Home/Shop by Brand/YouTube/|
|Japan         |Home/Accessories/  |
|Maldives      |Home/Apparel/Men's/Men's-Outerwear/|
|Mexico        |Home/Nest/Nest-USA/|
|Netherlands   |Home/Apparel/Men's/Men's-T-Shirts/|
|South Korea   |Home/Electronics/Electronics Accessories/|
|Sweden        |Home/Electronics/  |
|Switzerland   |Home/Apparel/Men's/Men's-T-Shirts/|
|Taiwan        |Home/Apparel/Men's/Men's-Performance Wear/|
|Thailand      |Home/Drinkware/    |
|United Kingdom|Home/Office/Notebooks & Journals/|
|United States |Housewares         |
|Vietnam       |Home/Shop by Brand/YouTube/|



I find that it was difficult to find a pattern in the product categories because there was still missing information from cities, countries, or both.  I would think that the product category can change between cities and countries should the missing information be received.



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

```sql
/* The top selling product from each city was determined by joining the product name and city based on the number of orders.  The product with the most number of order was ranked and filtered out by cities. */

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
SELECT ts.city, ts.v2_product_name
FROM top_selling_products_by_city ts
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
SELECT ts.country, ts.v2_product_name
FROM top_selling_products_by_country ts
WHERE rank = 1
```



Answer:

These are the top-selling products for each city:

|city          |v2_product_category|
|--------------|-------------------|
|Ann Arbor     |Home/Apparel/Men's/Men's-T-Shirts/|
|Atlanta       |Home/Apparel/Men's/Men's-T-Shirts/|
|Atlanta       |Home/Bags/         |
|Austin        |Home/Apparel/Men's/Men's-Performance Wear/|
|Austin        |Home/Office/Notebooks & Journals/|
|Bangkok       |Home/Drinkware/    |
|Berlin        |Home/Office/       |
|Bogota        |Home/Apparel/Men's/Men's-T-Shirts/|
|Chicago       |Home/Shop by Brand/|
|Dallas        |Home/Shop by Brand/YouTube/|
|Detroit       |Drinkware          |
|Dublin        |Home/Bags/         |
|Dublin        |Home/Shop by Brand/YouTube/|
|Hong Kong     |Home/Apparel/Men's/Men's-T-Shirts/|
|Hyderabad     |Home/Accessories/Stickers/|
|Kirkland      |Home/Office/       |
|London        |Home/Office/Notebooks & Journals/|
|Mountain View |Home/Apparel/Men's/Men's-Outerwear/|
|Munich        |Home/Apparel/Headgear/|
|New York      |Home/Bags/Backpacks/|
|Palo Alto     |Nest-USA           |
|Paris         |Home/Shop by Brand/Android/|
|Pittsburgh    |Home/Office/Notebooks & Journals/|
|San Bruno     |Home/Bags/         |
|San Francisco |Home/Apparel/Women's/Women's-T-Shirts/|
|San Jose      |Home/Apparel/Men's/Men's-T-Shirts/|
|Santiago      |Home/Bags/         |
|Seattle       |Home/Nest/Nest-USA/|
|Sunnyvale     |Housewares         |
|Tel Aviv-Yafo |Home/Shop by Brand/YouTube/|
|Toronto       |Home/Apparel/Headgear/|
|Washington    |Home/Apparel/Kid's/Kid's-Infant/|
|Zurich        |Home/Apparel/Men's/Men's-T-Shirts/|


These are the top-selling products for each country:

|country       |v2_product_category|
|--------------|-------------------|
|Australia     |Home/Accessories/Stickers/|
|Austria       |Home/Accessories/Housewares/|
|Belgium       |Home/Drinkware/Water Bottles and Tumblers/|
|Bulgaria      |Home/Apparel/Men's/Men's-T-Shirts/|
|Canada        |Home/Apparel/Headgear/|
|Chile         |Home/Bags/         |
|Colombia      |Home/Apparel/Men's/Men's-T-Shirts/|
|Denmark       |Home/Drinkware/    |
|Egypt         |Home/Shop by Brand/Android/|
|France        |Home/Shop by Brand/Android/|
|France        |Headgear           |
|Germany       |Home/Apparel/Headgear/|
|Germany       |Home/Office/       |
|Hong Kong     |Home/Nest/Nest-USA/|
|India         |Home/Accessories/Stickers/|
|Indonesia     |Home/Bags/Backpacks/|
|Ireland       |Home/Shop by Brand/YouTube/|
|Ireland       |Home/Bags/         |
|Israel        |Home/Shop by Brand/YouTube/|
|Japan         |Home/Accessories/  |
|Maldives      |Home/Apparel/Men's/Men's-Outerwear/|
|Mexico        |Home/Nest/Nest-USA/|
|Netherlands   |Home/Apparel/Men's/Men's-T-Shirts/|
|South Korea   |Home/Electronics/Electronics Accessories/|
|Sweden        |Home/Electronics/  |
|Switzerland   |Home/Apparel/Men's/Men's-T-Shirts/|
|Taiwan        |Home/Apparel/Men's/Men's-Performance Wear/|
|Thailand      |Home/Drinkware/    |
|United Kingdom|Home/Office/Notebooks & Journals/|
|United States |Housewares         |
|Vietnam       |Home/Shop by Brand/YouTube/|



Similar to Question 3 in the 'starting_with_questions.md' file, I would think that the missing information could alter the top-selling products between cities and countries.



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```sql
/* Revenue was linked with the visit ID, for which it was joined to calculate the revenue by city. */

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

/* Revenue was linked with the visit ID, for which it was joined to calculate the revenue by country. */

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

|city          |sum_revenue|
|--------------|-----------|
|New York      |1103       |
|Sunnyvale     |608        |
|Mountain View |481        |
|Seattle       |357        |
|Chicago       |306        |
|San Francisco |276        |
|San Jose      |153        |
|Palo Alto     |151        |
|Tel Aviv-Yafo |32         |
|Zurich        |16         |


Here is the summary of the revenue impact generated from each country:

|country       |sum_revenue|
|--------------|-----------|
|United States |4629       |
|Israel        |32         |
|Switzerland   |16         |

While it was easier to draw the 'revenue' data from the 'analytics' table, there is information on the 'unit_sold' and 'unit_price' to calculate the revenue.  To see the variance:

```sql
/* Comparision between 'revenue' column versus calculated revenue by 'units_sold' * 'unit_price' by city. */
WITH revenue AS (
	SELECT DISTINCT visit_id, revenue / 1000000 AS revenue, CAST((units_sold * unit_price) / 1000000 AS INTEGER) AS calc_revenue
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.city, SUM(r.revenue) AS sum_revenue, SUM(r.calc_revenue) AS sum_calc_revenue, (SUM(r.calc_revenue) - SUM(r.revenue)) / SUM(r.revenue) * 100 AS revenue_diff_pct
FROM all_sessions als
JOIN revenue r
	ON als.visit_id = r.visit_id
WHERE als.city != '(not set)' AND als.city != 'not available in demo dataset'
GROUP BY als.city
ORDER BY sum_revenue DESC

/* Comparision between 'revenue' column versus calculated revenue by 'units_sold' * 'unit_price' by country. */

WITH revenue AS (
	SELECT DISTINCT visit_id, revenue / 1000000 AS revenue, CAST((units_sold * unit_price) / 1000000 AS INTEGER) AS calc_revenue
	FROM analytics
	WHERE units_sold IS NOT NULL AND revenue IS NOT NULL
)
SELECT als.country, SUM(r.revenue) AS sum_revenue, SUM(r.calc_revenue) AS sum_calc_revenue, (SUM(r.calc_revenue) - SUM(r.revenue)) / SUM(r.revenue) * 100 AS revenue_diff_pct
FROM all_sessions als
JOIN revenue r
	ON als.visit_id = r.visit_id
WHERE als.country != '(not set)'
GROUP BY als.country
ORDER BY sum_revenue DESC
```

Here is the revenue compared to the calculated revenue by (units sold * unit price) for each city:

|city         |sum_revenue|sum_calc_revenue|revenue_diff_pct        |
|-------------|-----------|----------------|------------------------|
|New York     |1103       |1101            |-0.18132366273798730700 |
|Sunnyvale    |608        |602             |-0.98684210526315789500 |
|Mountain View|481        |467             |-2.91060291060291060300 |
|Seattle      |357        |357             |0.00000000000000000000  |
|Chicago      |306        |298             |-2.61437908496732026100 |
|San Francisco|276        |261             |-5.43478260869565217400 |
|San Jose     |153        |149             |-2.61437908496732026100 |
|Palo Alto    |151        |149             |-1.32450331125827814600 |
|Tel Aviv-Yafo|32         |24              |-25.00000000000000000000|
|Zurich       |16         |14              |-12.50000000000000000000|


Here is the revenue compared to the calculated revenue by (units sold * unit price) for each country:

|country      |sum_revenue|sum_calc_revenue|revenue_diff_pct        |
|-------------|-----------|----------------|------------------------|
|United States|4629       |4558            |-1.53380859796932382800 |
|Israel       |32         |24              |-25.00000000000000000000|
|Switzerland  |16         |14              |-12.50000000000000000000|
