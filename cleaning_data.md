What issues will you address by cleaning the data?

Queries:
Below, provide the SQL queries you used to clean your data.




1. As per cleaning hint in the 'cleaning_data.md' file, the unit cost needs to be divided by 1,000,000.  Also, given that revenue is derived from the unit cost multiply by number of units sold, it will need to be converted from the millions as well. Therefore:
   
```sql
SELECT CAST(unit_price AS REAL) / 1000000 AS unit_price
FROM analytics
```
Before:

|unit_price|
|----------|
|249000000 |
|249000000 |
|249000000 |
|199000000 |
|199000000 |

After:

|unit_price|
|----------|
|249       |
|249       |
|249       |
|199       |
|199       |

```sql
SELECT CAST(product_price AS REAL) / 1000000 AS product_price
FROM all_sessions
```
Before:

|product_price|
|-------------|
|2990000      |
|98990000     |
|24990000     |
|76990000     |
|109990000    |

After:

|product_price|
|-------------|
|2.99         |
|98.99        |
|24.99        |
|76.99        |
|109.99       |

```sql
SELECT total_transaction_revenue / 1000000 AS total_transaction_revenue, product_revenue / 1000000 AS product_revenue,
transaction_revenue / 1000000 AS transaction_revenue
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL AND transaction_revenue IS NOT NULL AND product_revenue IS NOT NULL
```
Before:

|total_transaction_revenue|product_revenue|transaction_revenue|
|-------------------------|---------------|-------------------|
|200000000                |120000000      |200000000          |
|169970000                |58656666       |169970000          |
|1015480000               |176400000      |1015480000         |
|1005500000               |60365000       |1005500000         |

After:

|total_transaction_revenue|product_revenue|transaction_revenue|
|-------------------------|---------------|-------------------|
|200                      |120            |200                |
|169                      |58             |169                |
|1015                     |176            |1015               |
|1005                     |60             |1005               |
```



2. The 'date' column importing the CSVs is not in the PostgreSQL date format.  The datatype uploaded is an 'INT', so it will need to be converted from 'INT' to 'VARCHAR' then 'DATE' as follows:

```sql
SELECT TO_DATE(CAST(date AS VARCHAR), 'YYYYMMDD') AS date
FROM analytics
```
Before:

|date|
|----|
|20170705|
|20170705|
|20170705|
|20170705|
|20170705|

After:

|date|
|----|
|2017-07-05|
|2017-07-05|
|2017-07-05|
|2017-07-05|
|2017-07-05|
```

```sql
SELECT TO_DATE(CAST(date AS VARCHAR), 'YYYYMMDD') AS date
FROM all_sessions
```
Before:

|date|
|----|
|20160913|
|20170421|
|20170312|
|20170215|
|20161218|

After:

|date|
|----|
|2016-09-13|
|2017-04-21|
|2017-03-12|
|2017-02-15|
|2016-12-18|
```



3. Noticebly, it appears that there are spaces in front of the names of various products under the sales report.  To remove excess spaces whether leading, trailing, and in between text, it will be as follows:

```sql
SELECT TRIM(from name) AS name
FROM sales_report
```
Before:

|name|
|----|
|Recycled Paper Journal Set|
 | Learning Thermostat 3rd Gen-USA - White|
 | Men's Short Sleeve Hero Tee Charcoal|
|22 oz  Bottle Infuser|
|Android 17oz Stainless Steel Sport Bottle|

After:

|name|
|-------------------|
|Recycled Paper Journal Set|
|Learning Thermostat 3rd Gen-USA - White|
|Men's Short Sleeve Hero Tee Charcoal|
|22 oz  Bottle Infuser|
|Android 17oz Stainless Steel Sport Bottle|



4.  In the 'page_path_level1' column, the '/' was added at the end of the string to keep consistency of page paths:

```sql
SELECT CASE
	WHEN page_path_level1 LIKE '%/' THEN page_path_level1
	ELSE CONCAT(page_path_level1,'/')
	END AS page_path_level1
	FROM all_sessions
	GROUP BY page_path_level1
```
Before:

|page_path_level1|
|----------------|
|/ordercompleted.html|
|/payment.html   |
|/store.html/    |
|/revieworder.html|
|/google+redesign/|
|/asearch.html   |
|/storeitem.html |
|/basket.html    |
|/asearch.html/  |
|/yourinfo.html  |
|/store.html     |

After:

|page_path_level1|
|----------------|
|/ordercompleted.html/|
|/payment.html/  |
|/store.html/    |
|/revieworder.html/|
|/google+redesign/|
|/asearch.html/  |
|/storeitem.html/|
|/basket.html/   |
|/asearch.html/  |
|/yourinfo.html/ |
|/store.html/    |



5.  The time is shown in numerical format, so the data was converted to 'Hour:Minute:Second' format as follows:

```sql
SELECT CAST(TO_TIMESTAMP(time) AS time) AS time
FROM all_sessions
```

Before:

|time|
|----|
|122213|
|268947|
|61402|
|8655|
|0   |

After:

|time|
|------------|
|01:56:53    |
|18:42:27    |
|09:03:22    |
|18:24:15    |
|16:00:00    |


    

   
