Question 1: 

How many 'Performance Wear' items are listed for sale between genders?

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
SELECT v2_product_category AS product_category_by_gender, COUNT(v2_product_category) AS num_of_performance_items
FROM men_performance_apparel
GROUP BY v2_product_category
UNION
SELECT v2_product_category, COUNT(v2_product_category)
FROM women_performance_apparel
GROUP BY v2_product_category
```

Answer: 

There are 104 items under Men's Performance Wear and there are 48 items under Women's Performance Wear.

Question 2: 

Of all 'Accessories', rank the 'Top-10' most expensive items in USD.

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

Google Phone Sanitizer at 97 USD, 
Google Phone Sanitizer at 78 USD, 
UpCycled Handlebar Bag at 59 USD, 
Google Four Color EDC Flashlight at 59 USD, 
Google Flashlight at 59 USD, 
upCycled Bike Saddle Bag at 49 USD, 
Google Flashlight at 47 USD, 
26 oz Double Wall Insulated Bottle at 24 USD, 
20 oz Stainless Steel Insulated Tumbler at 24 USD, and 
Android 24 oz Contigo Bottle at 23 USD.


Question 3: 

For context, the sentiment score is a number that tells how words elicit emotional responses and opinions whether 'positive', 'negative', or 'neutral'.  For the sake of this exercise, a sentiment score of 0.5 or above is considered a sign of 'positive' feedback on a scale between -1.0 and 1.0.  Find 'Water Bottles' that have a sentiment score above 0.5.

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

#1. 22 oz Water Bottle, 
SKU: GGOEGDHC018299, 
Sentiment Score: 0.9

#2. 22 oz Water Bottle, 
SKU: GGOEGAAX0074, 
Sentiment Score: 0.7

There are 2 Water Bottles that have a sentiment score above 0.5.
