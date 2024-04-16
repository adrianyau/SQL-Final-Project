What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

1. As the products' SKUs are identified as primary keys across the 'products', 'sales_report', and 'sales_by_sku' tables, these tables were joined to show that there were products that do not exist in the database or products could be mislabeled with an non-existent SKU.

```sql
SELECT p.sku, sr.product_sku, s.product_sku, p.name, sr.name
FROM products p
FULL OUTER JOIN sales_report sr
	ON p.sku = sr.product_sku
FULL OUTER JOIN sales_by_sku s
	ON p.sku = s.product_sku
WHERE p.sku IS NULL AND sr.product_sku IS NULL AND sr.product_sku IS NULL
```

2. Some information on cities and countries or not set or available in the dataset, so it can be difficult to determine item and sales information without knowing where the products are purchased, or where is it specifically purchased within a certain country.

``` sql
SELECT city, country
FROM all_sessions
WHERE city = '(not set)' OR city = 'not available in demo dataset' OR country = '(not set)'
```

3. In the 'analytics' table where it tracks website information data, duplications can occur because user can double-click on a page link, re-click on a page link if the website does not respond, or even clicking back and forth between page links.  To remove such duplications, DISTINCT() function was used:

 ```sql
SELECT *
FROM analytics
/* Total rows: 4,301,122 */

 SELECT DISTINCT *
FROM analytics
/* Total rows: 1,739,308 */
```

4. 
