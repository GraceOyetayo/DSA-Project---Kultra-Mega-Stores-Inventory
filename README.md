# DSA-Project---Kultra-Mega-Stores-Inventory

## Project

Kultra Mega Stores (KMS), headquartered in Lagos, specialises in office supplies and 
furniture. Its customer base includes individual consumers, small businesses (retail), and 
large corporate clients (wholesale) across Lagos, Nigeria. 

## Data Source:

The Business Manager has shared an Excel file containing order data from 2009 to 
2012

## Data Analysis Steps
### Exploratory Data Analysis
- Customers in the dataset are from 13 provinces and 8 regions in the country. Ontario province has the highest number of customer in a province, with 281 customer making 281/5517 percent and Nunuvat has the lowest 38.
  
- A total number 5496 unique orders are in the dataset
-- Total number of unique order placed for each year
  
  - 2009	1394 - highest
    
  - 2010	1393
    
  - 2011	1317 - lowest
    
  - 2012	1392
  
- There are four Customer Segments in the dataset

	--  Consumer	1649

	--  Corporate	3076

	--  Home Office	2032

	--  Small Business	1642




#### Case Scenario I 
1. Which product category had the highest sales? - Technology

`SELECT product_category, 
	  max(sales) as max_sales,
	  round(sum(sales), 2) as sales_per_category
FROM kmscase
GROUP BY product_category
ORDER BY sales_per_category DESC`

ANSWER
Technology	89061.05	5984248.18

The highest sales were made from the Technology category; it is also the category where about 54% of the profit in the last four years has been gotten.

2. What are the Top 3 and Bottom 3 regions in terms of sales? 

`SELECT top 3 
  region, 
	sum(sales) total_sales
from kmscase
group by region
order by total_sales  asc`

ANSWER: 
- Nunavut	116,376.48
- Northwest Territories	800,847.33
- Yukon	975,867.37


`SELECT top 3
region, 
	sum(sales) total_sales
from kmscase
group by region
order by total_sales  desc`

Answer: 
 -   West	3,597,549.28
 -  Ontario 3,063,212.48
 -  Prarie	2,837,304.60

West, Ontario, Prairie has the top sales by region while Nunavut, Northwest Territories, and Yukon has the lowest sales region


3. What were the total sales of appliances in Ontario? 

`select region, 
	sum(sales) as sales
from KMscase
where region = 'Ontario'
and product_sub_category = 'Appliances'
group by region`

Total sales made in the region Ontario is N202,346.84 


4. Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers

`select 
Customer_Name, region, province,
	round(sum(sales),2) as revenue 
from KMSCase
group by  Customer_Name, region, province
order by revenue asc`
 

Andy Reiter	Quebec	Quebec	3.42
John Grady	West	British Columbia	5.06
Muhammed Lee AtlanticNewfoundland	9.37
Roland Murray	Prarie	Manitoba	10.94
Rose O'Brian	West	Alberta	11.08
Julie Kriz	Atlantic	Nova Scotia	11.57
Anne McFarland	West	British Columbia	11.7
Ben Peterman	Ontario	Ontario	13.53
Aaron Bergman	Nunavut	Nunavut	14.76
Grant Thornton	Quebec	Quebec	16.6

As customer name is not unique, region and province are added to the grouping to ensure each customer in the region even if same name is accounted for.
We have a total of 1,538 unique customer

Further querying into these customers shows they only placed one order in the four years period.
This dataset do not show further details into why these customer stopped placing orders as according to order status only one of these orders were returned and most also had short delivery timeline.


5. KMS incurred the most shipping cost using which shipping method?

Delivery Truck	51,971.94
Express Air	7,850.91
Regular Air	48,008.19

The shipping mode where the company spent the most is in delivery truck


#### Case Scenario II 
6. Who are the most valuable customers, and what products or services do they typically 
purchase?


7. Which small business customer had the highest sales?
`select top 10
Customer_Name, region, province, customer_segment,
	round(sum(sales),2) as revenue 
from kmscase
group by  region, province, customer_name, customer_segment
order by revenue desc
WHERE customer_segment = 'Small Business'`

  ANSWER:
  Dennis Kane of Quebec	in the Small Business customer segment with N75,967.59 
  
8. Which Corporate Customer placed the most number of orders in 2009 – 2012?
   
`with customerincorporate as(
select 
Customer_Name, region, province, customer_segment,
	count(distinct order_id) as numberoforders
from kmscase
where customer_segment ='Corporate'
group by  region, province, customer_name, customer_segment
)
select region, province, customer_name, customer_segment, year(order_date), count(distinct order_id) as ord
from allorders
where customer_name in(select customer_name
						from customerincorporate)
						and  customer_segment ='Corporate'
group by region, province, customer_name, year(order_date), customer_segment
order by year(order_date), ord desc
`
ANSWER:
In 2009, Adam Shillingsburg of Alberta, West placed the most orders in the corporate customer segment with 7 orders
In 2010, Doug Bickford of Northwest Territories, placed the most orders in the corporate customer segment with 6 orders
In 2011, Adam Hart of Yukon, Yukon placed the most orders in the corporate customer segment with 5 orders
In 2012, Alan Hwang of Ontario, Ontario placed the most orders in the corporate customer segment with 6 orders

`CREATE VIEW ccorp as
WITH customerincorporate as
(
SELECT 
Customer_Name, region, province, customer_segment,
	count(distinct order_id) as numberoforders
from kmscase
where customer_segment ='Corporate'
group by  region, province, customer_name, customer_segment
)
SELECT region, province, customer_name, customer_segment, year(order_date) as year_of_order, count(distinct order_id) as ord
from allorders
where customer_name in(select customer_name
			from customerincorporate)
	and  customer_segment ='Corporate'
group by region, province, customer_name, year(order_date), customer_segment

SELECT Customer_Name, region, province, customer_segment,
	sum(ord) as numberoforders
from ccorp
group by region, province, customer_name, customer_segment
order by numberoforders desc

ANSWER:
In total, for the four year period, Jonathan Doherty of Saskachewan, Prarie placed the highest number of orders in the corporate customer segment with 14 orders

   
10. Which consumer customer was the most profitable one?

`SELECT top 10
	customer_Name, 
	region, 
	province, 
	customer_segment,
	round(sum(sales),2) as revenue, sum(profit) as pr
from kmscase
group by  region, province, customer_name, customer_segment
order by pr desc`

ANSWER:
*Emily Phan*- Atlantic-New Brunswick, Consumer segment, revenue N97,011.19, profit N28,663.71


10. Which customer returned items, and what segment do they belong to?

They were all in the four customer segments, where 38% of them are corporate customers 

-- Corporate	334

-- Home Office	201

-- Consumer	147

-- Small Business 190
    
    
11. If the delivery truck is the most economical but the slowest shipping method and 
Express Air is the fastest but the most expensive one, do you think the company 
appropriately spent shipping costs based on the Order Priority? Explain your answer 
