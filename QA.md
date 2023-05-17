What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

When i was doing the QA process i had to check for duplicate records, missing values
badly formatted values and incorrect data formats. I should have done a few calculations to verify the 
integrity of the data, that is something i did not do. Data accuracy could be a potential problem.

to check for duplicate values i used soemthing like:
SELECT currencycode, AVG(transactionrevenue) AS avg_revenue_per_transaction
FROM all_sessions
WHERE transactions > 0
GROUP BY currencycode;

to find null values my queries looked like this:
SELECT COUNT(*) AS missing_values_count
FROM all_sessions
WHERE column_name IS NULL OR column_name = '';

the things i did not do are calculation verifications.

I used a query like this to inspect random values along the way.
SELECT *
FROM all_sessions
ORDER BY RAND()
LIMIT 10;