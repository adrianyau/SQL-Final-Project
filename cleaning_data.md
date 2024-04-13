What issues will you address by cleaning the data?

Queries:
Below, provide the SQL queries you used to clean your data.


1. As per cleaning hint, the unit cost needs to be divided by 1,000,000.  Therefore:

```sql
SELECT unit_price / 1000000 AS revised_unit_price
FROM analytics

SELECT product_price / 1000000 AS revised_product_price
FROM all_sessions
```

2. The 'date' column importing the CSVs is not in the PostgreSQL date format.  The datatype is an 'INT', so it will need to be converted from 'INT' to 'CHAR' then 'DATE' as follows:

```sql
SELECT TO_DATE(CAST(date AS VARCHAR(255)), 'YYYYMMDD')
FROM analytics

SELECT TO_DATE(CAST(date AS VARCHAR(255)), 'YYYYMMDD')
FROM all_sessions
```

3. Noticebly, it appears that there are spaces in front of the names of various products under the sales report.  To remove the spaces whether leading, trailing, and in between text, it will be as follows:

   ```sql
   SELECT TRIM(from name), name
   FROM sales_report
   ```
   
4. In the 'analytics' table, there were 4,301,122 rows of data, which led to my assumption that there may be duplicate records to attribute to this huge number.  To remove:

```sql
SELECT DISTINCT *
FROM analytics
```

5. Some countries are not set, so NULL was applied as per following:

   ```sql
   SELECT NULLIF(country,'(not set)') AS country -- NULL if countries are not set.
	FROM all_sessions
   ```
   

   
