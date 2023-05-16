What issues will you address by cleaning the data?

--the first thing i need to do is locate the primary key and i have all sorts of duplicates and problem columns.
--once i have these columns identified i can start linking these tables together and building the relationships.
--once they are all connected i can add all the irellevant data im likely to not need to worry about yet

Queries:
Below, provide the SQL queries you used to clean your data.
__-- the first thing i did was create a new template from the original tables,
-- a place to store the new clean data__

```CREATE TABLE analytics_new AS
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
```
__-- i need a primary key and fullvisitorid looks candidate.
-- using this to check for duplicates
-- this is the query i use to find duplicates__

```
SELECT "productSKU", COUNT(*)
FROM sales_by_sku
GROUP BY "productSKU"
HAVING COUNT(*) > 1;
```
__-- inserting the cleaned productSKU data into the new sales_by_sku table__
```
INSERT INTO sbs_new
SELECT *
FROM sales_by_sku
order by "productSKU" desc
```
__-- inserting the cleaned productSKU data into the new sales_report table__
```
INSERT INTO sr_new
SELECT "productSKU"
FROM sales_report
ORDER BY "productSKU"
```
__--keeping in mind im ordering things bty productSKU in descending order i can see 
--null values in the ratio column, this code replaces them and shows the old and new
--modified columns__
```
SELECT productSKU,ratio as oldRatio, COALESCE(ratio, 0) AS modified_ratio
FROM sales_report
ORDER BY productSKU DESC;
```
__--i dropped the modified ratio column and just used this code to get rid of null values__
SET ratio = coalesce(ratio, 0);

__--the first thing i notices was there was a pointless column in analytics and i can just drop it__
alter table analytics
drop column userid;

__--i notices null vlaues in analytics.csv right away and considered the columns possibly
-- redundant but used this function to see that there was data worth saving__
```
select units_sold, bounces, revenue from analytics
order by units_sold;
```
__--this command updated those columns to not contain null values__
```
update analytics

SET units_sold = coalesce(units_sold,0),
    bounces = coalesce(bounces,0),
    revenue = coalesce(revenue,0)
```
__--timeonsite in analytics has null values , i must switch these to zero__
```
update analytics
set timeonsite = coalesce(timeonsite, 'none')
```
__--got rid of null values in analytics.pageviews__
update analytics
```
set pageviews = coalesce(pageviews,0);
```

__--okay just to recap so I dont lose my place , you created new sales_report and 
--sales_by_sku tables and cleaned the data for them, the products and analytics tables have been cleaned
-- within themselves leaving just the all sessions table for cleaning.

--the unit price in analytics needs to be divided by 1000000 like the description says__
update analytics
```
set unit_price = unit_price / 1000000
```

__--checking totaltransactionrevenue in all_sessions for unique values other than null__
```
SELECT totaltransactionrevenue
FROM all_sessions
WHERE totaltransactionrevenue IS not NULL;
```
__--checking for null columns i found the productrefund ammount only had null values.__
```
SELECT productrefundamount
FROM all_sessions
WHERE productrefundamount IS not NULL;
```
__-- Removing the null column__
```
alter table all_sessions
drop column productrefundamount;
```
__--this queary reveals that itemquantity is a null column, it only hass null values__
```
SELECT itemquantity
FROM all_sessions
WHERE itemquantity IS no NULL;
```
__--checking itemquanity and itemrevenue both reveal null columns. removing with: __
```
alter table all_sessions
drop column itemquantity;

alter table all_sessions
drop column itemrevenue;
```

__--while checking totaltransactions non null values i see that i need to convert to bigint
-- Add a new bigint column__
```
ALTER TABLE all_sessions
ADD COLUMN new_totaltransactionrevenue bigint;
```
__--Update the new column with converted values__
```
UPDATE all_sessions
SET new_totaltransactionrevenue = CAST(totaltransactionrevenue AS bigint);
```
__--Drop the old text column__

```
ALTER TABLE all_sessions
DROP COLUMN totaltransactionrevenue;
```
__--Rename the new column to the original column name__
```
ALTER TABLE all_sessions
RENAME COLUMN new_totaltransactionrevenue TO totaltransactionrevenue;
```
__-- null values removed from totaltransactionrevenue__ 
```
UPDATE all_sessions
SET totaltransactionrevenue = COALESCE(totaltransactionrevenue, 0);
```
__--notice removing the null values from transactions would be a similar operation__
```
UPDATE all_sessions
SET transactions = COALESCE(transactions, 0);
```
__--timeonsite seems to be an integer value. you cant have negative time values so if its null its zero.__
```
UPDATE all_sessions
SET timeonsite = COALESCE(timeonsite, 0);
```
__--sessionqualitydim seems to be a scale of some sort so it needs to have its null values zeroed.__
```
UPDATE all_sessions
SET sessionqualitydim = COALESCE(sessionqualitydim, 0);
```
__--this command shows me transactionrevenue has information, its not alot of information but ill keep it.
-- ill also convert the null to zero.__
```
select transactionrevenue 
from all_sessions 
where transactionrevenue is not null

UPDATE all_sessions
SET transactionrevenue = COALESCE(transactionrevenue, 0);
```

__--using code like this i can quickly check all columns for non null values.
--if i find only null values the column can be discarded__
```
select productrevenue
from all_sessions
where  productrevenue is not null;
```
__--searchkeyword is the last null column i removed it with __
```
ALTER TABLE all_sessions
DROP COLUMN searchkeyword;
```
__--this handy 2 lines converts my charactervarying to zero if there is a null value,
--a further modification and i can quickly remove null values in numeric columns__
```
ALTER TABLE all_sessions
ALTER COLUMN productquantity TYPE integer USING (COALESCE(NULLIF(productquantity, ''), '0')::integer);
```
__--productrevenue was screwed up dont forget to get it from a new import and fix it ****************************************************************__

__--currencycode can have its null values replaced with a'no data' string__
```
UPDATE all_sessions
SET currencycode = COALESCE(currencycode, 'no data');
```
__--transactionid is alphanumeric so i will replace the nulls with 'no data'__
```
UPDATE all_sessions
SET transactionid= COALESCE(transactionid, 'no data');
```
__--pagetitle is text so i will coalesce with 'no data' for null values__
```
UPDATE all_sessions
SET pagetitle = COALESCE(pagetitle, 'no data');
```
__--ecommerceaction_option  is text so i will coalesce with 'no data' for null values__
```
UPDATE all_sessions
SET ecommerceaction_option = COALESCE(ecommerceaction_option, 'no data');
```
```
__--starting again today i built some utility code to uncomment as i need to check columns__
--utility code to help find non-alphanumeric values
--SELECT fullvisitorid
--FROM all_sessions
--WHERE fullvisitorid ~ '[^a-zA-Z0-9]'

--utility code to find letters in numeric columns
--SELECT fullvisitorid
--FROM all_sessions
--WHERE fullvisitorid ~ '[[:alpha:]]';

--utility code to find numbers in text columns
--SELECT *
--FROM your_table
--WHERE your_column ~ '[[:digit:]]';

--utility to find the longer strings with "not" in my data and replace with no data
--select country
--from all_sessions
--where country LIKE '%not%'
```
{{--not's were found in country = (not set) and city =not available in demo dataset, (not set)
--this code is used to fix them__
```
UPDATE all_sessions
SET country = REPLACE(country, 'not', 'no data')
WHERE column_name LIKE '%not%'

UPDATE all_sessions
SET city = REPLACE(city, 'not', 'no data')
WHERE city LIKE '%not%'
```
__--also had to fix the productvariant column__
```
UPDATE all_sessions
SET productvariant = REPLACE(productvariant, 'not', 'no data')
WHERE productvariant LIKE '%not%'
```
```
__--i can uncomment and iterate down the list of tables quickly with this code to check for distinct values__
select distinct v2productcategory
--pagetitle, 
--pagepathlevel1,
--currencycode 
from all_sessions
```
__--it revealed that the v2productcategory contains 2 unnaceptable values  "${escCatTitle}" and "(not set)"
--this code gets rid of them__
```
UPDATE all_sessions
SET productvariant = REPLACE(REPLACE(productvariant, '${escCatTitle}', ''), '(not set)', 'no data')
WHERE productvariant LIKE '%${escCatTitle}%' OR productvariant LIKE '%(not set)%';
```
__--that didnt work correctly so i replaced with null values and converted the null values __
```
UPDATE all_sessions
SET v2productcategory = NULL
WHERE v2productcategory LIKE '${escCatTitle}' OR v2productcategory LIKE '(not set)';

UPDATE all_sessions
SET v2productcategory = 'no data'
WHERE v2productcategory is null;
```
__-- my graphs have no primary keys. while assigning them i am locating duplicate values in my sales_report productSKU
--this code removes the duplicates by assigning ctid's to them and deleting all but the minimum one
-- then the final output shows that there is no duplicates__
```
UPDATE sales_report
SET "productSKU" = "productSKU" || '_'
WHERE ctid IN (
    SELECT ctid
    FROM (
        SELECT ctid,
               ROW_NUMBER() OVER (PARTITION BY "productSKU" ORDER BY ctid) AS row_num
        FROM sales_report
    ) sub
    WHERE row_num > 1
);


SELECT "productSKU", COUNT(*) as duplicate_count
FROM sales_report
GROUP BY "productSKU"
HAVING COUNT(*) > 1;
```
__--starting with the small and simple table, working my way up with complexity i am setting the primary keys
--handling problems associated with primary keys__
```
ALTER TABLE sales_by_sku
ADD PRIMARY KEY (productSKU);

ALTER TABLE sales_report
ADD PRIMARY KEY (productSKU);

```
__-- i am running into a performance issue when working with the analytics column so i made a new table with just
-- the problem column to work on just that__
```
CREATE TABLE tempanalytics AS
SELECT *
FROM analytics;
```
__--while trying to set primary keys on the all_sessions and analytics tables i get duplicates errors this code
--removes duplicates__
```
DELETE FROM analytics
WHERE ctid NOT IN (
    SELECT MIN(ctid)
    FROM analytics
    GROUP BY "fullvisitorid"
);

DELETE FROM all_sessions
WHERE ctid NOT IN (
    SELECT MIN(ctid)
    FROM analytics
    GROUP BY "fullvisitorid"
);

```
__-- i can now set primary key__
```
ALTER TABLE analytics
ADD PRIMARY KEY ("fullvisitorid");
ALTER TABLE all
ADD PRIMARY KEY ("fullvisitorid");
```