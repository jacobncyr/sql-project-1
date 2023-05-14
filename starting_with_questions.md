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

--link tables producs and all_sessions together
--select * from all_sessions as alls full outer join products p on alls.fullvisitorid = p.sku
-- all_sessions has the session and totalrevue
-- that links to products by the sku to fullvisitorid which shows which city
take break

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







