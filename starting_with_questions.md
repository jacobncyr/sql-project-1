Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

select totaltransactionrevenue,city
from all_sessions
where city not like 'no data%'
order by totaltransactionrevenue desc


Answer:

--i can select the highest transaction revenues and their corresponding cities 
--from all_sessions and order it descending and the first values with cities are atlantla,
--sunnyvale, tel aviv-yafo, los angeles and sydney. i have to use a filter clause to get rid of the
--entries with no city


**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:

SELECT
  alls.city,
  alls.country,
  avg(alls.productprice * alls.productquantity)as avgvalue
FROM all_sessions AS alls
FULL OUTER JOIN products p ON alls.fullvisitorid = p.sku
GROUP BY alls.city, alls.country
order by avgvalue desc;


Answer:

--i found a few entries of the average value using this query
"no data available in demo dataset"	"Colombia"	530937.500000000000
"no data available in demo dataset"	"United States"	515842.091771340682
"Ann Arbor"				"United States"	404042.553191489362
"no data available in demo dataset"	"Mexico"	278524.590163934426
"Los Angeles"				"United States"	253609.756097560976
"Bengaluru"				"India"	235972.222222222222
"Dallas"				"United States"	233000.000000000000
"no data available in demo dataset"	"Finland"	142142.857142857143

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

WITH country_average AS (
    SELECT
        country AS Name,
        'country' AS Type,
        AVG(p.orderedQuantity) AS average_products_ordered
    FROM
        all_sessions AS als
    JOIN products AS p ON als.productSKU = p.SKU
    GROUP BY country
),
city_average AS (
    SELECT
        city AS Name,
        'city' AS Type,
        AVG(p.orderedQuantity) AS average_products_ordered
    FROM
        all_sessions AS als
    JOIN products AS p ON als.productSKU = p.SKU
    WHERE
        city IS NOT NULL AND city <> '(not set)'
    GROUP BY city
)
SELECT
    Name,
    Type,
    ROUND(average_products_ordered, 0) AS average_products_ordered
FROM
    (
        SELECT * FROM country_average
        UNION
        SELECT * FROM city_average
    ) AS combined
ORDER BY average_products_ordered DESC;

Answer:

--using this query i can see that of the top 10 productcategories ordered,
-- united states is in 6 of them, canada also makes the list once

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

WITH country_average AS (
    SELECT
        country AS Name,
        'country' AS Type,
        v2ProductCategory,
        AVG(p.orderedQuantity) AS average_products_ordered
    FROM
        all_sessions AS als
    JOIN products AS p ON als.productSKU = p.SKU
    GROUP BY country, v2ProductCategory
),
city_average AS (
    SELECT
        city AS Name,
        'city' AS Type,
        v2ProductCategory,
        AVG(p.orderedQuantity) AS average_products_ordered
    FROM
        all_sessions AS als
    JOIN products AS p ON als.productSKU = p.SKU
    WHERE
        city IS NOT NULL AND city <> '(not set)'
    GROUP BY city, v2ProductCategory
)
SELECT
    Name,
    Type,
    v2ProductCategory,
    ROUND(average_products_ordered, 0) AS average_products_ordered
FROM
    (
        SELECT * FROM country_average
        UNION
        SELECT * FROM city_average
    ) AS combined
ORDER BY average_products_ordered DESC;

Answer:

--using a cte we can union tables on countries and cities and they average products ordered, this data includes the oproduct category
--i can see that alot of entries have the same average products ordered which is suspicious and seems unnatural.

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

WITH analytics AS (
    SELECT * FROM analytics
),
allsessions AS (
    SELECT * FROM all_sessions
)
SELECT *
FROM (
    SELECT city, visitnumber, analytics.units_sold * analytics.unit_price as revenuemade
    FROM allsessions
    JOIN analytics USING(fullvisitorid)
) AS combined_data
order by revenuemade desc;

Answer:

-with this query i can see the ammount of visits with the current table of revenue made. 
-if i had a series of these tables that collected this data according to time i would be able to 
-see if the revenue made was increasing the visits, if i had multiple sources of this data i could locate falling revenues.

-city                           visitnumber    revenuemade
-"Palo Alto"				2	249
-"Irvine"				2	55
-"Singapore"				1	7
-"no data available in demo dataset"	2	1
-"Kiev"					31	0
-"Sunnyvale"				1	0
-"no data available in demo dataset"	1	0



