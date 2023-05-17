__Question 1: 
Determine the average time on site for visitors who made a purchase by fullvisitorid__

__SQL Queries:__
```
SELECT fullvisitorid, 
	   AVG(timeonsite) AS avg_time_on_site
FROM all_sessions 
 	 as alls

WHERE transactions > 0
group by fullvisitorid;
```

__Answer:__ 

--this displays two columns, fullvisitorid and avg_time_on_site
fullvisitorid           avg_time_on_site
"0127755228974756313"	318.0000000000000000
"0223683667601132018"	336.0000000000000000
"0343104487250705794"	690.0000000000000000
"0348842070964414318"	197.0000000000000000
"0375962687766031488"	331.0000000000000000


__Question 2:
Identify the visitors who have the highest total transaction revenue__

__SQL Queries:__
```
SELECT fullVisitorID, SUM(transactionrevenue) AS total_transaction_revenue
FROM all_sessions
WHERE transactions > 0
GROUP BY fullVisitorID
ORDER BY total_transaction_revenue DESC
LIMIT 3;
```
__Answer:__
--this only outputs 3 interesting values so i only included them. 
--The fullvisitorid and total_transaction_revenue

"3444642706013409818"	1005500000
"4396927063023774578"	200000000
"7298416213438900925"	169970000

__Question 3: 
Determine the total revenue generated per day by fullvisitorid over a specific date range__

__SQL Queries:__
```
SELECT fullvisitorid, date, SUM(transactionrevenue) AS total_revenue
FROM all_sessions
WHERE transactions > 0
AND date BETWEEN '2015-01-01' AND '2018-01-01'
GROUP BY date, fullvisitorid 
order by total_revenue desc;
```

__Answer:__
--once again i only have 3 interesting values, i recieve an answer showing the date and total revenue 
--between the specified years. all grouped by the fullvisitorid

fullvisitorid		date		total_revenue
"3444642706013409818"	"2016-12-13"	1005500000
"4396927063023774578"	"2017-03-20"	200000000
"7298416213438900925"	"2017-04-05"	169970000

__Question 4: 

determine the number of unique visitors__

__SQL Queries:__
```
SELECT COUNT(DISTINCT fullvisitorid) AS unique_visitors
FROM all_sessions AS als
INNER JOIN analytics AS al USING(fullvisitorid);
```


__Answer:__
--using an aggregation function i can find this
3896


__Question 5: 

retrieve the total number of sessions for each country, order by total_sessions and limit top 5__

__SQL Queries:__
```
SELECT country, SUM(session_count) AS total_sessions
FROM (
  SELECT als.fullvisitorid, als.country, COUNT(*) AS session_count
  FROM all_sessions AS als
  INNER JOIN analytics AS al USING(fullvisitorid)
  GROUP BY als.fullvisitorid, als.country
) as subq
GROUP BY country
order by total_sessions desc limit 5;
```

__Answer:__
country			total_sessions
"United States"		2174
"India"			207
"United Kingdom"	202
"Canada"		144
"Germany"		81