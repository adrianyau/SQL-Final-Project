What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

1. As the products' SKUs are identified as primary keys across the 'products', 'sales_report', and 'sales_by_sku' tables, these tables were joined to show that there were products that do not exist in the database or products could be mislabeled with an non-existent SKU.

```sql
SELECT p.sku, sr.product_sku, s.product_sku, p.name, sr.name
FROM products p
FULL OUTER JOIN sales_report sr
	ON p.sku = sr.product_sku
FULL OUTER JOIN sales_by_sku s
	ON p.sku = s.product_sku
WHERE p.sku IS NULL AND sr.product_sku IS NULL AND sr.product_sku IS NULL
```
