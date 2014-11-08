SQL-Three
=========
Similarly, if we want to just return rows in which department not only contains and E but also starts with E: 
  Select * from courses_will where Department like ‘E%’;

Last one. Say we want the Professor and Number where the Number doesn’t equal 50.
  Select Professor, Number from courses_will where Number != 50;

Finally, the like operator is case insensitive. However, it may be different in different programs. So this command works fine:
  Select * from courses_will where department like ‘economics’;

How about the range of prices paid, rounded to the neartest dollar? 
  Select round(max(price)-min(price)) from items_ordered;

What if we only wanted to look at transactions in which the price was no greater than $100? 
  select sum(price), count(*), customerid from items_ordered where price<=100 GROUP BY customerid;

What if we want to look at the price range for each item, but we only want to look at items whose average price is less than $100?
  select item, max(price)-min(price) from items_ordered GROUP BY item having avg(price)<100;

What if we wanted to do the same exercise as above but restrict ourselves to items that also do not start with the letter L?
  select item, max(price)-min(price) from items_ordered where item NOT Like 'L%' GROUP BY item having avg(price)<100;

But what if we want to look at these things when both price<100 and customerid = 10499? 
  Select price, item, customerid from items_ordered where (price<100) AND (customerid = 10499);
  
Say we want to take within customer average prices, but we only want to include items that start with L or cost more than $200. Furthermore, we only want to look at customers who average price paid is less than $100. Convoluted, I know.
  Select avg(price), customerid from items_ordered where (item like ‘L%’) or (price > 200) group by customerid having avg(price)<100;

Say we want to select data on all of the things in the list except for the tents and lanterns. How can we do that? 
  Select * from items_ordered where (item not like ‘tent’) and (item not like ‘lantern’);
  
Let’s say we want to pull up everything from items_ordered where the item is either a pillow, shovel, lantern, or canoe.
  select * from items_ordered where item IN (‘Pillow’, ‘Shovel’, ‘Lantern’, ‘Canoe’);
  Select * from items_ordered where item like (‘Pillow’ or ‘Shovel’ or ‘Lantern’ or ‘Canoe’);
  
The basic ordering is approximately (from low to high): 
  Numbers (0-9), Uppercase Letters(A-Z), Lowercase Letters(a-z)

Only including people whose last name come before Jones in the alphabet, return the number of people from each state in customers ordered alphabetically. Only return states with more than 1 person.
  Select state, count(state) from customers where (lastname between ‘A’ and ‘Jones’) group by state having count(state)>1 order by state desc;

Using items_ordered, return the total quantity purchased, by each customer,  excluding items that start with the letter ‘L’. Only return customers who have purchased more than 2 and less than 7 total items. Finally, order the customers by quantity from low to high. 
  select customerid, sum(quantity) from items_ordered where item not like 'L%' group by customerid having sum(quantity) between 3 and 6 order by sum(quantity) asc;
  
Using only items purchased both in 1999 and after the 15th day of a given month, return the average price of each item sorted by avg(price). 
  select item, avg(price) from items_ordered where order_date like '%99' and order_date between 16 and 30 group by item order by avg(price);
  
So, let’s go back to our items_ordered and customers tables. Let’s return the name of the customer who purchased each item. 
  Select item, firstname, lastname from items_ordered inner join customers on items_ordered.customerid = customers.customerid;
  
(doesn't work, as we can't use aggregate functions in where clauses)
  select order_date from items_ordered where price = (select max(price) from items_ordered);

Step 1: Select avg(price) as avgp, firstname, lastname from items_ordered inner join customers on items_ordered.customerid = customers.customerid group by customers.customerid;

Step 2: Select max(avgp), firstname, lastname from (Select avg(price) as avgp, firstname, lastname from items_ordered inner join customers on items_ordered.customerid = customers.customerid group by customers.customerid);

Write a query to determine which customer purchased the most expensive item, excluding any items that cost over $1000. 
  Select max(price), firstname, lastname from items_ordered inner join customers on customers.customerid = items_ordered.customerid where price<1000;

Let’s find the number of transactions that were between $0-$10, $10-$20, $20-$30, etc. So, write a query that outputs a range (we can index the range, say, by the lower bound) and the number of transactions in that range.
  Select count(*), r from (select round((price-5)/10)*10 as r from items_ordered) group by r order by r;  

Let’s find the total number of sales per month. I.E., return a table with two values: Month and Total Sales
  Select sum(quantity), t from (select *,trim(order_date,’1234567890-’)  as t from items_ordered) group by t;

Write a query to find the state with the highest total amount paid, conditional on having at least 5 transactions. 
  Select max(sump) from (Select sum(price) as sump, state from items_ordered inner join customers on items_ordered.customerid = customers.customerid group by state having count(state)>=5);

Write a query to find the number of each item sold per state. Order the results by item, and secondarily by state.
  Select sum(quantity), state, item from items_ordered inner join customers on items_ordered.customerid = customers.customerid group by item, state order by item, state;



