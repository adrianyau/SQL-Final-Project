Question 1: What is the number of performance wear categorized between genders?

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

SQL Queries:

Answer:



Question 3: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
