Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```sql
/* Group cities by sum of transaction revenues ordered by descending */
SELECT city, SUM(total_transaction_revenue::REAL / 1000000) AS total_transaction_revenue
FROM all_sessions
WHERE city != '(not set)' AND city!= 'not available in demo dataset' AND total_transaction_revenue IS NOT NULL
GROUP BY city
ORDER BY total_transaction_revenue DESC

/* Group countries by sum of transaction revenues ordered by descending */
SELECT country, SUM(total_transaction_revenue::REAL / 1000) AS total_transaction_revenue
FROM all_sessions
WHERE country != '(not set)' AND total_transaction_revenue IS NOT NULL
GROUP BY country
ORDER BY total_transaction_revenue DESC
```



Answer:

These are the cities with the highest level of transaction revenues on the site.

San Francisco at 1564.32, Sunnyvale at 992.23, Atlanta at 854.44, Palo Alto	at 608, Tel Aviv-Yafo at 602, New York at 598.35, Mountain View at 483.36, Los Angeles at 479.48, Chicago at 449.52, Sydney at 358, Seattle at 358, San Jose at 262.38, Austin at 157.78, Nashville at 157, San Bruno at 103.77, Toronto at 82.16, Houston at 38.98, Columbus at 21.99, and Zurich at 16.99.

These are the countries with the highest level of transaction revenues on the site.

United States at 13,154,170, Israel at 602,000, Australia at 358,000, Canada at 150,150, and Switzerland at 16,990.





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







