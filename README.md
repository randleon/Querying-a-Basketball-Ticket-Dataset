## Basketball Tickets SQL Test


Data Dictionary

o event_date – date of the event; date

o opponent – opposing team played during event; text

o date_sold – datetime of the sale; datetime

o section_name – section in the arena; text

o row_name – row of the section; text

o seat_num – seat number in the row; int

o sale_price – price that the seat sold for; currency(in USD $)




1. Write a query that calculates the sum of seats sold, sum of revenue and average sale price,
grouped by event date and opponent.

2. Via query, create an additional column named ‘bowl_level’ based on the following constraints:

    a. Any section_name starting with FLR is labeled as ‘Floor’

    b. Any section_name starting with VIP is labeled as ‘VIP’

    c. Any section_name that is in the lower level (single and double digit section_names) are
labeled as ‘Lower’

    d. Any section_name that is in the middle level (triple digit section_names starting with 1)
are labeled as ‘Middle’

    e. Any section_name that is in the upper level (triple digit section_names starting with 2)
are labeled as ‘Upper’


3. Write a query that calculates the sum of seats sold, min sale price, max sale price, and average
sale price, grouped by sale date and bowl_level, for any one event date in the data set. (the
event date does not matter)


4. Write a query that calculates the total sum of seats sold, sum of seats sold within 7 days of the
event, and the sum of seats sold the day of the event, grouped by event date and opponent.


5. Via query, create an additional column named ‘days_out_grouping’ that calculates categorical
groupings for how many days before an event a sale took place. The specifics of the groupings
are up to the user but they should serve to help sales analysis.


6. Write a query that calculates the sum of seats sold, sum of revenue, min sale price, max sale
price, and average sale price, grouped by days_out_grouping, for the event date used in
question 4.






SELECT * FROM basketball LIMIT 10;

-- Question (1)
-- Write a query that calculates the sum of seats sold, sum of revenue and
-- average sale price, grouped by event date and opponent.

SELECT event_date, COUNT(seat_num) as NumSeatsSold,
ROUND(SUM(sale_price), 2) as TotalRevenue,
ROUND(AVG(sale_price),2) as AverageSalePrice, opponent
FROM basketball
GROUP BY event_date, opponent
ORDER BY event_date;


--Question (2)
--Via query, create an additional column named ‘bowl_level’ 
--based on the following constraints:
-- a. Any section_name starting with FLR is labeled as ‘Floor’
-- b. Any section_name starting with VIP is labeled as ‘VIP’
-- c. Any section_name that is in the lower level (single and double digit section_names) are
-- labeled as ‘Lower’
-- d. Any section_name that is in the middle level (triple digit section_names starting with 1)
-- are labeled as ‘Middle’
-- e. Any section_name that is in the upper level (triple digit section_names starting with 2)
--are labeled as ‘Upper’

ALTER TABLE basketball
DROP COLUMN bowl_level;

SELECT * FROM basketball;

ALTER TABLE basketball
ADD bowl_level VARCHAR (20);

UPDATE basketball 
SET bowl_level=subquery.bowl_level
FROM (
    SELECT section_name,
    CASE WHEN section_name SIMILAR TO 'FLR%%' THEN 'Floor'
         WHEN section_name SIMILAR TO 'VIP%%' THEN 'VIP'
		 WHEN section_name SIMILAR TO '__' THEN 'Lower'
		 WHEN section_name SIMILAR TO '1__' THEN 'Middle'
		 WHEN section_name SIMILAR TO '2__' THEN 'Upper'
         ELSE 'Unknown' END
         AS bowl_level
FROM basketball
ORDER BY opponent
) AS subquery
WHERE basketball.section_name = subquery.section_name;

SELECT * FROM basketball;

--Question (3)
--Write a query that calculates the sum of seats sold, min sale price, 
--max sale price, and average sale price, grouped by sale date and bowl_level,
--for any one event date in the data set
--(the event date does not matter)


SELECT * FROM basketball
LIMIT 3;

--I Chose the date of October 28th, 2021

SELECT event_date,
COUNT(seat_num) as SumOfSeatsSold,
ROUND(MIN(sale_price), 2) as MinSalePrice,
ROUND(MAX(sale_price), 2) as MaxSalePrice,
ROUND(AVG(sale_price), 2) as AvgSalePrice
FROM basketball
WHERE event_date = '2021-10-28'
GROUP BY event_date, bowl_level;

--Question (4)
--Write a query that calculates the total sum of seats sold, 
--sum of seats sold within 7 days of the event, 
--and the sum of seats sold the day of the event, 
--grouped by event date and opponent.

SELECT
opponent as opponent,
event_date as GameDate,
COUNT(seat_num) as TotalNumOfSeatsSold,
COUNT(CASE WHEN event_date='2021-10-28' THEN seat_num ELSE null END) as SoldDayOfEvent,
SUM(COUNT(*)) OVER (ORDER BY basketball.event_date::date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS past7
FROM basketball
GROUP BY event_date, opponent;

--Question 5
--Via query, create an additional column named "days_out_grouping"
--that calculates categorical groupings for how many days before an event a sale took place.
--The specifics of the groupings are up to the user 
--but they should serve to help sales analysis.

--------------------------------------------------
ALTER TABLE basketball
DROP COLUMN basketball;
--------------------------------------------------
SELECT * FROM basketball;
--------------------------------------------------
ALTER TABLE basketball
ADD days_out_grouping VARCHAR(60)

UPDATE basketball 
SET days_out_grouping=subquery.days_out_grouping
FROM (
    SELECT event_date, date_sold, event_date - date_sold AS days_out_grouping
FROM basketball
) AS subquery
WHERE basketball.date_sold = subquery.date_sold;

SELECT event_date, date_sold, days_out_grouping FROM basketball
LIMIT 20;

SELECT DISTINCT(days_out_grouping) FROM basketball;
SELECT DISTINCT(date_sold) FROM basketball;
SELECT DISTINCT(event_date) FROM basketball;

--Question 6
--Write a query that calculates 
--the sum of seats sold, 
--sum of revenue, 
--min sale price, 
--max sale price, 
--and average sale price, 
--grouped by days_out_grouping, 
--for the event date used in question 4.

SELECT
COUNT(event_date) as TotalNumOfSeatsSold,
date_sold,
event_date,
ROUND(SUM(sale_price), 2) as SumOfRevenue,
ROUND(MIN(sale_price), 2) as MinPrice,
ROUND(MAX(sale_price), 2) as MaxPrice,
ROUND(AVG(sale_price), 2) as AvgPrice,
days_out_grouping
FROM basketball
WHERE event_date = '2021-10-28'
GROUP BY days_out_grouping, date_sold, event_date
ORDER BY date_sold
LIMIT 20;

--Question 7
--Via query, create an additional column named 
--"transaction_id" that creates a transaction number 
--for seats that were bought together as one purchase

ALTER TABLE basketball 
DROP COLUMN transaction_id;
--------------------------------------------------
SELECT * FROM basketball ;
--------------------------------------------------
ALTER TABLE basketball 
ADD transaction_id INT;
--------------------------------------------------
select date_sold, 
    row_number() over(
        order by date_sold
    ) as transaction_id
from basketball 
group by date_sold;

UPDATE basketball  
SET transaction_id=subquery.transaction_id
FROM (
    select date_sold, 
    row_number() over(
        order by date_sold
    ) as transaction_id
from basketball 
group by date_sold
) AS subquery
WHERE basketball .date_sold = subquery.date_sold;

SELECT date_sold, transaction_id FROM basketball 
LIMIT 20;

select * from basketball;

7. Via query, create an additional column named ‘transaction_id’ that creates a transaction
number for seats that were bought together as one purchase
