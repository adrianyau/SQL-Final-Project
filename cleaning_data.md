What issues will you address by cleaning the data?

Queries:
Below, provide the SQL queries you used to clean your data.




1. As per cleaning hint, the unit cost needs to be divided by 1,000,000.  Therefore:
   
   ```sql
   SELECT unit_price / 1000000 AS unit_price
   FROM analytics

   SELECT product_price / 1000000 AS product_price
   FROM all_sessions
   ```

2. The 'date' column importing the CSVs is not in the PostgreSQL date format.  The datatype is an 'INT', so it will need to be converted from 'INT' to 'VARCHAR' then 'DATE' as follows:

   ```sql
   SELECT TO_DATE(CAST(date AS VARCHAR), 'YYYYMMDD') AS date
   FROM analytics

   SELECT TO_DATE(CAST(date AS VARCHAR), 'YYYYMMDD') AS date
   FROM all_sessions
   ```

3. Noticebly, it appears that there are spaces in front of the names of various products under the sales report.  To remove excess spaces whether leading, trailing, and in between text, it will be as follows:

   ```sql
   SELECT TRIM(from name), AS product_description
   FROM sales_report
   ```
   
4. In the 'analytics' table, there were 4,301,122 rows of data, which led to my assumption that there may be duplicate records to attribute to this huge number.  To remove:

   ```sql
   SELECT DISTINCT *
   FROM analytics
   ```

5. Some countries are not set, so NULL was applied as per following:

   ```sql
   SELECT NULLIF(country,'(not set)') AS country
   FROM all_sessions
   ```
   
6. Some cities do not have values.  There are two variables ('(not set)' and 'not available in demo dataset') that do not provide us the information. NULL was applied as per following:

   ```sql
   WITH cte_city AS (
   	SELECT REPLACE(city, 'not available in demo dataset', '(not set)') AS city
   	FROM all_sessions
   )
   SELECT NULLIF(city, '(not set)') AS city
   FROM cte_city
   ```

7. There are some products that cannot be categorized, so values were NULLED as follows:

   ```sql
   WITH v2_product_category_cte AS (
   	SELECT REPLACE(v2_product_category, '${escCatTitle}', '(not set)') AS v2_product_category
   	FROM all_sessions
   )
   SELECT NULLIF(v2_product_category, '(not set)') AS v2_product_category
   FROM v2_product_category_cte
   ```

8. Under 'Channel Grouping', brackets were removed in 'Other' group to align formatting with the rest as follows:

   ```sql
   SELECT REPLACE(channel_grouping, '(Other)', 'Other') AS channel_grouping
   FROM all_sessions

   SELECT REPLACE(channel_grouping, '(Other)', 'Other') AS channel_grouping
   FROM analytics
   ```

9. To nullify a product variant that cannot be set, it is as follows:

    ```sql
    SELECT NULLIF(product_variant, '(not set)') AS product_variant
    FROM all_sessions
    ```

10. In the 'page_path_level1' column, the '/' was added at the end of the string to keep consistency of page paths:

    ```sql
    SELECT CASE
       WHEN page_path_level1 LIKE '%/' THEN page_path_level1
       ELSE CONCAT(page_path_level1,'/')
       END AS page_path_level1
	FROM all_sessions
    ```

11. There was a negative number in the units sold.  It may suggest that there was negative inventory for this product.  To remove the negative:

    ```sql
	SELECT CASE
		WHEN units_sold > 0 THEN units_sold
		ELSE 0
		END AS units_sold
	FROM analytics
    ```

12. 

    

   
