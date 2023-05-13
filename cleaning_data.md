What issues will you address by cleaning the data?





Queries:
Below, provide the SQL queries you used to clean your data.
--the first thing i did was create a new template from the original tables,
--a place to store the new clean data

--CREATE TABLE analytics_new AS
--SELECT * FROM analytics LIMIT 0;

CREATE TABLE products_new AS
SELECT * FROM products LIMIT 0;

CREATE TABLE analytics_new AS
SELECT * FROM analytics LIMIT 0;

CREATE TABLE sbs_new AS
SELECT * FROM sales_by_sku LIMIT 0;

CREATE TABLE sr_new AS
SELECT * FROM sales_report LIMIT 0;
