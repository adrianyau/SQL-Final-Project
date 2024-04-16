Question 1: 

What is the total number of performance wear categorized between genders?

SQL Queries:

```sql
SELECT v2_product_category, COUNT(v2_product_category) AS num_of_performance_wear
FROM all_sessions
WHERE v2_product_category LIKE '%Performance Wear%'
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

SQL Queries:

Answer:

