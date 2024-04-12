What issues will you address by cleaning the data?

Queries:
Below, provide the SQL queries you used to clean your data.

1. As per cleaning hint, the unit cost needs to be divided by 1,000,000.  Therefore:

```sql
SELECT unit_price / 1000000 AS revised_unit_price
FROM analytics
```

2. The 'date' column importing the CSVs are not in the PostgreSQL format.  The datatype is an 'INT', so it will need to be formmated as follows:

```sql
SELECT TO_DATE(CAST(date AS VARCHAR(255)), 'YYYYMMDD')
FROM analytics
```

3. It appears that there is a space in front of the names of various products under the sales report.  To remove the spaces for a linear view:

   ```sql
   SELECT TRIM(from name), name
   FROM sales_report
  ```
