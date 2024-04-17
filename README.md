# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The project was to showcase our SQL fundamentals and knowledge of SQL queries, and apply to the dataset provided from an e-commerce company.  The goal was to work on the original raw data despite being inaccurate, inconsistent, duplicated, and missed.  Methods were demonstrated via. pgAdmin 4 that we could clean, transform, and analyze data, as well as identify risk areas for quality assurance, and ultimately, get desired outcomes.  

## Process
1. Five (5) CSV files were downloaded and imported into pgAdmin 4.  For the sake of simplicity with datatypes and time to upload the CSV files, 'VARCHAR' and 'INTEGER', whether 'INT' or 'BIG INT' depending on the length of numbers, were applied.  Names labeled in the 'Tables' and 'Columns' were case-sensitive and space-sensitive, so the names were typed in lower capitals, and words were separate replacing undescores '_' over spaces.

2. The primary key could be identified as the products' SKU between the 'products', 'sales_report', and 'sales_by_sku' tables as they were unique and NOT NULL.  For the 'all_sessions' and 'analytics' tables, it appeared that the primary key could be the 'visit_id' column.  Initially, I thought that the 'full_visitor_id' column could be a primary key, but I discovered that multiple page links could be coming from the same visitor.  Instead, I should have thought that every page link visited would generate its own unique 'visit_id'.  When I tried to assign the primary key for the 'visit_id', there was an error due to key being duplicated and it could not create a unique index.  In addition, the 'product_sku' column could also be found in the 'all_sessions' table, but the foreign key could not be assigned due to duplication.  The 'DISTINCT()' function would have removed any duplicates, and it was suggested to not alter the dataset in the tables to preserve the original raw data for the purpose of this project.

3. As for data cleaning and manipulation, minor corrections could be applied, such as the conversion of numeric values to 'date' and 'time' formats in their respective columns; the addition [CONCAT()] or deletion [TRIM()] of special characters; or the removal of extra spaces within the names in products descriptions.  The hint provided in the 'assignment.md' file suggested that the conversion of the unit/product cost divided by 1,000,000 means that this conversion needs to apply to the calculation of revenue as it is derived mathematically from the formula [unit price * quantity].  Noticeably, I felt that the missing information not set or available for the cities and countries could play a factor in the exercises to answer the five (5) data questions.

4. As for the five (5) data questions, I found CTEs to be very useful to provide my answers as I could think in a step-by-step approach through the usage of these virtual tables.  For example, a CTE was created to remove duplication in the 'analytics' table, so that the 'visit_id' could be used in comparison with the 'units_sold' and that could be joined with the 'all_sessions' table find out about the results of various product categories and product names for Questions 3 and 4 respectively.  In addition, nesting a CTE was a great feature when it involved the 'Ranking Window' function to rank the data.

5. As for my own three (3) data questions, I wanted to showcase other SQL skills learned from the Data Analytics Bootcamp during the last two weeks, as well as applying some business accumen working on this mock datset of an e-Commerce company.  For example, I compared the number of different products (e.g., performance wear) between men and women, plus I applied the UNION operator to combine the lists together and display the count of different products between genders.  Another example is taking a step further with the 'Rank Window' function by ranking the 'Top-10 Accessories' list and include the type of currency along with the product price in the same cell.  My last example is learning about sentiment scores and how I could use the SQL query to find out about a specific product and the buyers' emotional response toward their product purchase.

6. As for quality assurance, I felt that it was second nature while working with SQL queries as data was checked and filtered to its intent, such as filtering out null values and duplicate values; filtering out specific set of data for answers; or filtering out irrelevant information that was not important for the task at hand.

## Results
This SQL Final Project proved the old adage of "Garbage In - Garbage Out".  In summary, I felt that the SQL Final Project had stressed the importance of quality data, in the context of e-commerce, to make key business decisions to increase sales, improve operations, and stand out against the competition.  I had outlined three (3) attributes:

1. Duplication of Data:
It was evident in the database because of similar products within web pages.  From researching online, I learned about the term    'Keyword Cannibalization', which means when there are two or more pages on the website that targets similar keywords from the same search, such as 'White T-Shirts' and 'White Tees'.  The SEO, or search engine optimization, would struggle to determine between webpages, so it would not deliver the results to the users.  Hence, the products from the ecommerce would not appear in the top search results, which would lead to loss of customers and revenue.  Furthermore, product names need to be explicit with their descriptions, such as manufacturer's branding, because seeking similar product descriptions across different search engines would likely to prioritize a webpage with more brand recogniztion.  To note, this might me relevant where there were different product naming conventions under the table columns between 'v2_product_name' versus 'name' of the dataset.

2. Incomplete or Missing Data:
There were missing Product SKUs, which did not correspond with the e-commerce sales of certain products.  It could lead to inaccurate information on how to track product information and which products are being sold more successfully than others. Some cities and countries were not provided in the data set, which could skew results searching for sales information by geography.

3. Inconsistent Data:
Specifically, dataset was provided for product price, product quantity, and product revenue, but the calculations for product revenue derived from multiplying product price by product quantity did not match with the product revenue provided.  Further investigation would be needed to ensure data validity.

## Challenges 
- Time is not on our side, as we are given up to 5 days to complete the SQL Final Project.
- Ambiguity of questions being asked to answer as it depends on our individual responses and the validity of our reasoning to the audience.
- Putting the lectures and modules learned daily to use from the Bootcamp to simulate real-life practices that could potentially involve in tackling on an enormous dataset.
- Getting used to reading the error messages flagged by pgAdmin 4 and understand what they mean.
- Figuring out how to input SQL code in proper sequence to get the data output.  With long lists of code, it can be tedious to pin down data entry mistakes and typos.

## Future Goals
- Better use of time and efficiencies to gather, sort, and obtain the results under time constraint.
- Learn intermidate and advanced SQL functions to apply different combinations of code to achieve various desired outcomes and better code readability.
- Not only do I like to expand and sharpen my understanding of the SQL language, but I would like to gain insight of the data from a business perspective.  For the SQL Final Project in particular, I would have liked to know how an e-commerce operates and understand what sort of information is important and relevant to their stakeholders.
