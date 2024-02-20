## Basketball SQL Test -These are the questions I answered about the basketball data set using SQL


- Data Dictionary
o event_date – date of the event; date
o opponent – opposing team played during event; text
o date_sold – datetime of the sale; datetime
o section_name – section in the arena; text
o row_name – row of the section; text
o seat_num – seat number in the row; int
o sale_price – price that the seat sold for; money


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
7. Via query, create an additional column named ‘transaction_id’ that creates a transaction
number for seats that were bought together as one purchase
