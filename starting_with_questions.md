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

SELECT alls.city, alls.country,
count(alls.v2productcategory) as categorycount
FROM all_sessions AS alls
FULL OUTER JOIN products p ON alls.fullvisitorid = p.sku
full outer join sales_report sr on p.sku = sr."productSKU"
group by alls.city, alls.country
order by categorycount desc


Answer:

--using this query i can see that of the top 10 productcategories ordered,
-- united states is in 6 of them, canada also makes the list once



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

--query for city
SELECT city, v2productname, categorycount
FROM (
  SELECT
    alls.city,
    alls.v2productname,
    COUNT(alls.v2productname) AS categorycount,
    ROW_NUMBER() OVER (PARTITION BY alls.city ORDER BY COUNT(alls.v2productname) DESC) AS row_num
  FROM
    all_sessions AS alls
  FULL OUTER JOIN products p ON alls.fullvisitorid = p.sku
  FULL OUTER JOIN sales_report sr ON p.sku = sr."productSKU"
  WHERE city NOT LIKE 'no data%'
  GROUP BY alls.city, alls.v2productname
) AS subquery
WHERE row_num = 1
ORDER BY city;

--query for country
SELECT country, v2productname, categorycount
FROM (
  SELECT
    alls.country,
    alls.v2productname,
    COUNT(alls.v2productname) AS categorycount,
    ROW_NUMBER() OVER (PARTITION BY alls.country ORDER BY COUNT(alls.v2productname) DESC) AS row_num
  FROM
    all_sessions AS alls
  FULL OUTER JOIN products p ON alls.fullvisitorid = p.sku
  FULL OUTER JOIN sales_report sr ON p.sku = sr."productSKU"
  WHERE city NOT LIKE 'no data%'
  GROUP BY alls.city, alls.v2productname
) AS subquery
WHERE row_num = 1
ORDER BY city;

Answer:

--using a window function  subquery you can see the top selling item in each city and to see the top selling item and quantity of each country just/
--switch the city column name to country


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







