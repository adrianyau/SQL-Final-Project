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

City            Transaction Revenues
San Francisco	1564.32
Sunnyvale	    992.23
Atlanta	        854.44
Palo Alto	    608
Tel Aviv-Yafo	602
New York	    598.35
Mountain View	483.36
Los Angeles	    479.48
Chicago	        449.52
Sydney	        358
Seattle	        358
San Jose	    262.38
Austin	        157.78
Nashville	    157
San Bruno	    103.77
Toronto	        82.16
Houston	        38.98
Columbus	    21.99
Zurich	        16.99

These are the countries with the highest level of transaction revenues on the site.

Country        	Transaction Revenues
United States	13,154,170
Israel"	        602,000
Australia	    358,000
Canada        	150,150
Switzerland    	16,990





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







