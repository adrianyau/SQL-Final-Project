What issues will you address by cleaning the data?

Queries:
Below, provide the SQL queries you used to clean your data.

1. As per cleaning hint, the unit cost needs to be divided by 1,000,000.  Therefore:

```sql
SELECT unit_price / 1000000 AS revised_unit_price
FROM analytics
```

2. It appears that there is a space in front of the names of various products under the sales report.  To remove the spaces for a linear view:

   ```sql
   SELECT TRIM(from name), name
   FROM sales_report
  ```
