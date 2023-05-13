What issues will you address by cleaning the data?

--the first thing i need to do is locate the primary key and i have all sorts of duplicates and problem columns.
--once i have these columns identified i can start linking these tables together and building the relationships.
--once they are all connected i can add all the irellevant data im likely to not need to worry about yet

Queries:
Below, provide the SQL queries you used to clean your data.
-- the first thing i did was create a new template from the original tables,
-- a place to store the new clean data

-CREATE TABLE analytics_new AS
SELECT * FROM analytics LIMIT 0;

CREATE TABLE products_new AS
SELECT * FROM products LIMIT 0;

CREATE TABLE analytics_new AS
SELECT * FROM analytics LIMIT 0;

CREATE TABLE sbs_new AS
SELECT * FROM sales_by_sku LIMIT 0;

CREATE TABLE sr_new AS
SELECT * FROM sales_report LIMIT 0;

CREATE TABLE allsessions_new AS
SELECT * FROM all_sessions LIMIT 0;

-- i need a primary key and fullvisitorid looks candidate.
-- using this to check for duplicates
-- this is the query i use to find duplicates,

SELECT "productSKU", COUNT(*)
FROM sales_by_sku
GROUP BY "productSKU"
HAVING COUNT(*) > 1;

-- inserting the cleaned productSKU data into the new sales_by_sku table

INSERT INTO sbs_new
SELECT *
FROM sales_by_sku
order by "productSKU" desc

-- inserting the cleaned productSKU data into the new sales_report table
INSERT INTO sr_new
SELECT "productSKU"
FROM sales_report
ORDER BY "productSKU"

--keeping in mind im ordering things bty productSKU in descending order i can see 
--null values in the ratio column, this code replaces them and shows the old and new
--modified columns
SELECT productSKU,ratio as oldRatio, COALESCE(ratio, 0) AS modified_ratio
FROM sales_report
ORDER BY productSKU DESC;

--i dropped the modified ratio column and just used this code to get rid of null values
SET ratio = coalesce(ratio, 0);

--the first thing i notices was there was a pointless column in analytics and i can just drop it
alter table analytics
drop column userid;

--i notices null vlaues in analytics.csv right away and considered the columns possibly
-- redundant but used this function to see that there was data worth saving, note: i iterated throught the columns, after units_sold i checked bounces
--then revenue

select units_sold, bounces, revenue from analytics
order by units_sold

--this command updated those columns to not contain null values
update analytics
SET units_sold = coalesce(units_sold,0),
    bounces = coalesce(bounces,0),
    revenue = coalesce(revenue,0)

--timeonsite in analytics has null values , i must switch these to zero 
update analytics
set timeonsite = coalesce(timeonsite, 'none')

--got rid of null values in analytics.pageviews
update analytics
set pageviews = coalesce(pageviews,0);

--okay just to recap so you dont lose your place , you created new sales_report and 
--sales_by_sku tables and cleaned the data for them, the products and analytics tables have been cleaned
-- within themselves leaving just the all sessions table for cleaning.

