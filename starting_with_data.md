Question 1: 

How many 'Performance Wear' items are listed for sale between men and women?

SQL Queries:

```sql
WITH men_performance_apparel AS (
	SELECT v2_product_category, v2_product_name
	FROM all_sessions
	WHERE v2_product_category LIKE '%Men%' AND v2_product_category LIKE '%Performance Wear%'
	GROUP BY v2_product_category, v2_product_name
),
women_performance_apparel AS (
	SELECT v2_product_category, v2_product_name
	FROM all_sessions
	WHERE v2_product_category LIKE '%Women%' AND v2_product_category LIKE '%Performance Wear%'
	GROUP BY v2_product_category, v2_product_name
)
SELECT v2_product_category AS product_category_by_gender, COUNT(v2_product_category) AS num_of_performance_wear_items
FROM men_performance_apparel
GROUP BY v2_product_category
UNION
SELECT v2_product_category, COUNT(v2_product_category)
FROM women_performance_apparel
GROUP BY v2_product_category
```

Answer: 

|product_category_by_gender|num_of_performance_wear_items|
|--------------------------|-----------------------------|
|Home/Apparel/Women's/Women's-Performance Wear/|13                           |
|Home/Apparel/Men's/Men's-Performance Wear/|14                           |



Question 2: 

Of all products categorized as 'Accessories', rank the 'Top-10' most expensive items in USD.

SQL Queries:

```sql
WITH accessory_rank AS (
SELECT 
	v2_product_name,
	product_price / 1000000 AS product_price,
	currency_code,
	RANK () OVER(ORDER BY product_price DESC)
FROM all_sessions
WHERE v2_product_category LIKE '%Accessories%' AND currency_code IS NOT NULL
GROUP BY v2_product_name, product_price, currency_code
LIMIT 10
)

SELECT v2_product_name, CONCAT(product_price::TEXT,' ',currency_code), rank
FROM accessory_rank
```

Answer:

These are the Top-10 Accessory Items according to its product price in USD:

|v2_product_name|concat|rank|
|---------------|------|----|
|Google Phone Sanitizer|97 USD|1   |
|Google Phone Sanitizer|78 USD|2   |
|UpCycled Handlebar Bag|59 USD|3   |
|Google Four Color EDC Flashlight|59 USD|3   |
|Google Flashlight|59 USD|3   |
|UpCycled Bike Saddle Bag|49 USD|6   |
|Google Flashlight|47 USD|7   |
|26 oz Double Wall Insulated Bottle|24 USD|8   |
|20 oz Stainless Steel Insulated Tumbler|24 USD|8   |
|Android 24 oz Contigo Bottle|23 USD|10  |



Question 3: 

For context, the sentiment score is a number that tells how words elicit emotional responses and opinions whether 'positive', 'negative', or 'neutral'.  For the sake of this exercise, let us consider a sentiment score of 0.5 or above as a sign of 'positive' feedback on a scale between -1.0 and 1.0.  Find water bottle products that have a sentiment score above 0.5.

SQL Queries:

```sql
SELECT TRIM(FROM name) AS name, sku, sentiment_score
FROM products
WHERE name LIKE '%Water Bottle%' AND sentiment_score IS NOT NULL AND sentiment_magnitude IS NOT NULL
GROUP BY name, sku, sentiment_score
HAVING sentiment_score > 0.5
ORDER BY sentiment_score DESC
```

Answer:

There are 2 Water Bottles that have a sentiment score above 0.5:

|name         |sku |sentiment_score|
|-------------|----|---------------|
|22 oz Water Bottle|GGOEGDHC018299|0.9            |
|22 oz Water Bottle|GGOEGAAX0074|0.7            |

