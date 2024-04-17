What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

1. As the products' SKUs are identified as primary keys under the 'products' tables, it was joined with the 'all_sessions' table to cross-reference and see if there were valid products.  In addition, some product SKUs do not exist under the 'products' table, and the product names and product categories are not aligned.

```sql
SELECT p.sku, als.product_sku AS unidentified_sku, als.v2_product_name, als.v2_product_category
FROM all_sessions als
LEFT JOIN products p
	ON als.product_sku = p.sku
WHERE p.sku IS NULL
GROUP BY p.sku, als.product_sku, v2_product_name, v2_product_category
LIMIT 10
```

|sku|unidentified_sku|v2_product_name                    |v2_product_category|
|---|----------------|-----------------------------------|-------------------|
|NULL|10 31023        |YouTube Unstructured Cap - Charcoal|YouTube            |
|NULL|10 52233        |Sports Water Bottle                |Drinkware          |
|NULL|10 52237        |24oz USA Made Aluminum Bottle      |Drinkware          |
|NULL|9180751         |Android 24 oz Contigo Bottle       |(not set)          |
|NULL|9180752         |Android 24 oz Contigo Bottle       |(not set)          |
|NULL|9180760         |Electronics Accessory Pouch        |(not set)          |
|NULL|9180761         |Large Zipper Top Tote Bag          |(not set)          |
|NULL|9180788         |Insulated Bottle                   |(not set)          |
|NULL|9180797         |Google 22 oz Water Bottle          |(not set)          |
|NULL|9180801         |Engraved Ceramic Google Mug        |(not set)          |

Take product SKU 9180751 "Android 24 oz Contigo Bottle" as an example.
```sql
SELECT product_sku, v2_product_name, v2_product_category, page_title
FROM all_sessions
WHERE product_sku ='9180751'
```
It appears that searching the product in the 'all_sessions' table as per page title does not correlate with the product.

|product_sku|v2_product_name|v2_product_category                |page_title|
|-----------|---------------|-----------------------------------|----------|
|9180751    |Android 24 oz Contigo Bottle|(not set)                          |Google Laptop and Cell Phone Stickers|
|9180751    |Android 24 oz Contigo Bottle|(not set)                          |Android Baby Essentials Baby Set|



2. Some information on cities and countries or not set nor available in the dataset, so it can be difficult to determine item and sales information without knowing where the products are purchased, or where is it specifically purchased within a certain country.  For example, a country could have different cities, and cities could have the same names in different countries (e.g., Vancouver, BC, CAN, and Vancouver, WA, USA).  Therefore, WHERE() clause was used to filter out missing city and country information.

``` sql
SELECT city, country
FROM all_sessions
WHERE city != '(not set)' OR city != 'not available in demo dataset' OR country != '(not set)'
LIMIT 5
```
|city|country |
|----|--------|
|(not set)|Taiwan  |
|not available in demo dataset|United States|
|not available in demo dataset|United States|
|not available in demo dataset|United States|
|London|United States|

Therefore, WHERE() clause was used to filter out missing city and country information.
```sql
SELECT city, country
FROM all_sessions
WHERE city != '(not set)' AND city != 'not available in demo dataset' AND country != '(not set)'
LIMIT 5
```
|city|country |
|----|--------|
|London|United States|
|Sydney|Australia|
|Philadelphia|United States|
|Sunnyvale|United States|
|Mountain View|United States|

However, it would be helpful if we have additional information on the geographical locations, such as 'provinces', 'states', and 'districts' to narrow down the details (e.g., is there a city called London in the United States, or is it London, England, UK?)



3. In the 'analytics' table where it tracks website information data, there was a ton of redudant data.  To remove such duplications, the DISTINCT() function was used:

 ```sql
SELECT *
FROM analytics
/* Total rows: 4,301,122 */

 SELECT DISTINCT *
FROM analytics
/* Total rows: 1,739,308 */
```

4. There are some products that cannot be set or categorized.  The WHERE() clause should filter out the uncategorized products.

   ```sql
   SELECT v2_product_category
   FROM all_sessions
   WHERE v2_product_category = '${escCatTitle}' OR v2_product_category = '(not set)'
   GROUP BY v2_product_category
   ```
   |v2_product_category|
|-------------------|
|(not set)          |
|${escCatTitle}     |



5. Under 'product_quantity', 'product_price, and 'product_revenue', the calculation of the product revenue ('product_quantity' * 'product_price) does not equal to the 'product_revenue' on the 'all_sessions' table.

   ```sql
   SELECT (product_quantity * product_price / 1000000) AS calc_product_revenue, product_revenue / 1000000 AS product_revenue
   FROM all_sessions
   WHERE (product_quantity * product_price) != product_revenue
   ```
