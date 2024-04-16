# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The project is to showcase our SQL fundamentals and knowledge of SQL queries to apply to this mock dataset from a fictitious e-commerce company.  The goal is to improve its original raw data, despite information being inaccurate, inconsistent, duplicated, and missing, by cleaning, transforming, and analyzing, as well as identifying risk areas for our methods of quality assurance, and get meaningful results.  

## Process
1. Five (5) CSV files were downloaded and imported into pgAdmin4.  For sake of simplicity with datatypes and time to upload the CSV files, 'VARCHAR' and 'INTEGER', whether 'INT' or 'BIG INT' depending on the length of numbers, were applied.  Names labeled in the 'Tables' and 'Columns' were case-sensitive and space-sensitive, so the names were typed in lower capitals and words were separate replacing undescores '_' over spaces.

2. The primary key could be identified as the products' SKU between the 'products', 'sales_report', and 'sales_from_sku' tables as they are unique and NOT NULL.  For the 'all_sessions' and 'analytics' tables, it appears that the primary key could be the 'visit_id' column.  Initially, I thought that the 'full_visitor_id' column could be a primary key, but I discovered that every page visit would generate its own unique 'visit_id'.  When I tried to assign the primary key for the 'visit_id', there was an error due to key being duplicated and it could not create a unique index.  In addition, the 'product_sku' column could be found in the 'all_sessions' table, but the foreign key could not be assigned due to duplication.  The 'DISTINCT()' function would have removed any duplicates, but it was suggested not to alter the dataset in the tables to preserve the original raw data for the purpose of this project.

3. As for data cleaning and manipulation, minor corrections could be applied, such as conversion of numeric values to 'date' and 'time' formats in their respective columns, addition [CONCAT()] or deletion [TRIM()] of special characters, or the removal of extra spaces from the names in products descriptions.  Most notably, I feel that the missing information not set for cities and countries could play a factor in the results once I got into the section to answer the five (5) data questions.


## Results
- Duplication is evident in the database with the webpage information.  

## Challenges 
- Time is not on our side, as we are given up to 5 days to complete the SQL Final Project.
- Ambiguity of questions being asked to answer as it depends on our individual responses and the validity of our reasoning to the audience.
- Putting the lectures and modules learned daily from the Bootcamp to use to simulate real-life practice, especially tackling on an enormous dataset.
- Getting used to reading the error messages flagged by pgAdmin 4 and understand what they mean.
- Figuring out how to input SQL code in proper sequence to get the data output.  With long lists of code, it can be difficult to pin down data entry mistakes and typos.

## Future Goals
- Better use of time and efficiencies to gather, sort, and obtain the results under time constraint.
- Learn intermidate and advanced SQL functions to apply different combinations of code to achieve various desired outcomes and better code readability.
- Not only do I like expand and sharpen my understanding of the SQL language, but I would like to gain insight of the data from a business perspective.  For the SQL Final Project in particular, I would have liked to know how an e-commerce operates and understand what sort of information is important and relevant to their stakeholders.
