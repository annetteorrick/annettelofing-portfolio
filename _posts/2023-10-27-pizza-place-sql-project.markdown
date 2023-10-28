---
layout: post
title:  "SQL Server Pizza Place Project"
date:   2023-10-27 14:21:39 -0500
categories: SQL Project
---


# Pizza Place - SQL 

## 💡 Overview: 
A pizza place wants to determine from their sales information how to better increase their overall income and find actionable insights for the next year. 

The data I analyzed came from [Maven Analytics Data Playground](https://www.mavenanalytics.io/data-playground) under "Pizza Place Sales". 

## 📈 Data Summary: 
A year's worth of sales from a fictitious pizza place, including the date and time of each order and the pizzas served, with additional details on the type, size, quantity, price, and ingredients.

## 🙋🏻‍♂️ Questions for Pizza Place Analysis 
1. How many orders do we have each day? Are there any peak hours?
2. How many pizzas are typically in an order? Do we have any bestsellers?
3. How much money did we make this year? Can we identify any seasonality in the sales?

### 📊 [Tableau Dashboard](https://public.tableau.com/app/profile/annette5795/viz/PizzaPlace-BestSellingPizzasbyCategory/PizzaPlaceSales2015) 


## ✅ Conclusions (Tl;DR version, scroll down for more in depth explanations) 

### Total Gross Income for 2015 
$ 817,860.05 

### Best Sellers by Pizza Category 
The Chicken category was the best performing category, selling 10,815 total pizza's in 2015. 

### Best Sellers by Pizza 
The Classic Deluxe Pizza was the best performing pizza, selling 2,416 total pizza's in 2015. 

### Most popular season for Pizza Place Sales 


### Number of pizza's typically sold in an order 


### Peak Hours 
The most amount of pizzas were sold during the **12:00 pm hour**. 

The **1:00pm hour** was second, and in the evening the **6:00 pm hour** was the third best selling hour. 



### QUESTION 3, PART 1: How much money did we make this year? 
```
SELECT ROUND(SUM(price * quantity), 2) AS 'Gross Income For 2015'
FROM PizzaPlace.dbo.pizzas 
INNER JOIN PizzaPlace.dbo.order_details 
ON PizzaPlace.dbo.pizzas.pizza_id = PizzaPlace.dbo.order_details.pizza_id; 
```
#### Total Gross Income for 2015 
$ 817,860.05 

To find this number, I took the price of each pizza times the quantity of pizza's sold, and added all of those numbers together. 
Then I rounded it to 2 decimal places to display in a money-friendly format. 

### QUESTION 3, PART 2: Can we identify any seasonality in the sales? 

```
SELECT 
CASE 
WHEN date BETWEEN '2015-01-01' AND '2015-02-28' THEN 'Winter'
WHEN date BETWEEN '2015-03-01' AND '2015-05-31' THEN 'Spring'
WHEN date BETWEEN '2015-06-01' AND '2015-08-31' THEN 'Summer'
WHEN date BETWEEN '2015-09-01' AND '2015-11-30' THEN 'Fall'
WHEN date BETWEEN '2015-12-01' AND '2015-12-31' THEN 'Winter'
END AS Seasons,
ROUND(SUM(price * quantity), 2) AS 'Gross Income Per Season'
FROM PizzaPlace.dbo.pizzas
INNER JOIN PizzaPlace.dbo.order_details
ON PizzaPlace.dbo.order_details.pizza_id = PizzaPlace.dbo.pizzas.pizza_id
INNER JOIN PizzaPlace.dbo.orders 
ON PizzaPlace.dbo.order_details.order_id = PizzaPlace.dbo.orders.order_id 

GROUP BY CASE 
WHEN date BETWEEN '2015-01-01' AND '2015-02-28' THEN 'Winter'
WHEN date BETWEEN '2015-03-01' AND '2015-05-31' THEN 'Spring'
WHEN date BETWEEN '2015-06-01' AND '2015-08-31' THEN 'Summer'
WHEN date BETWEEN '2015-09-01' AND '2015-11-30' THEN 'Fall'
WHEN date BETWEEN '2015-12-01' AND '2015-12-31' THEN 'Winter'
END; 
```

To find the seasonality of the sales, I used a case statement to seperate the seasons in the year with the date stamp. 
I used the same statement from part 1 of question 3 to find the sum of the sales, and grouped them by season. 
The output shows you how much money was made during each season of 2015. 


### QUESTION 2, Part 1: How many pizzas are typically in an order?
```
SELECT order_id, COUNT(quantity) AS PizzaCountPerOrder
FROM PizzaPlace.dbo.order_details 
GROUP BY order_id
Order by order_id
```
To find this answer, I used a COUNT of the quantity of pizza's to count each pizza per order, and then grouped it by the unique order id. 
The output shows shows the number of pizzas for each order for all of 2015. 


### Question 2, Part 2: Do we have any bestsellers?
```
SELECT order_details.pizza_id, COUNT(quantity) AS BestSellersBySize
From PizzaPlace.dbo.order_details
INNER JOIN PizzaPlace.dbo.pizzas
ON pizzaplace.dbo.order_details.pizza_id = pizzaplace.dbo.pizzas.pizza_id

GROUP BY order_details.pizza_id
Order by BestSellersBySize DESC 
``` 

#### Overcoming a hiccup in this project... 
I first started to answer this question using the 'pizza_id'. After writing this script, I realized that it was sorting the best sellers by the pizza and pizza size within the order id. The output was including the size of the pizza, so instead of it showing which pizza was the best seller overall, it was showing which pizza was best seller according to size. 

When I was thinking through this... this is what I was commenting out in my code as I was thinking of the best way to solve this: 
```
-- I want to display the pizza type with it's name, not the pizza id with the size.
-- Trying to display best sellers as name, joining 3 tables together to display name of pizza instead of pizza type
-- It's now showing pizza type id instead of pizza size with type 
```

#### To find the pizza type without the pizza size, I needed to join 3 tables together. 
The pizza_types table had the pizza name, but I needed to join that table with the order_details table so I could count the order_id and then group them by the pizza name. 

```
SELECT name AS PizzaName, Count(order_id) AS PizzasPerType
FROM PizzaPlace.dbo.order_details 
INNER JOIN PizzaPlace.dbo.pizzas 
ON PizzaPlace.dbo.order_details.pizza_id = pizzaplace.dbo.pizzas.pizza_id 
INNER JOIN Pizzaplace.dbo.pizza_types 
ON PizzaPlace.dbo.pizzas.pizza_type_id = pizzaplace.dbo.pizza_types.pizza_type_id 

GROUP BY name
ORDER BY PizzasPerType DESC
``` 

I have now found the Best Selling Pizza! Yay, hiccup gone! 
**The Classic Deluxe Pizza** 🍕 is the best selling pizza! 

#### But... what if they asked me to sort by the Best Selling **category** of pizzas? 
Well, then I started playing around with the category of the pizzas. 

``` 
SELECT category, name AS PizzaName, Count(order_id) AS PizzasPerType
FROM PizzaPlace.dbo.order_details 
INNER JOIN PizzaPlace.dbo.pizzas 
ON PizzaPlace.dbo.order_details.pizza_id = pizzaplace.dbo.pizzas.pizza_id 
INNER JOIN Pizzaplace.dbo.pizza_types 
ON PizzaPlace.dbo.pizzas.pizza_type_id = pizzaplace.dbo.pizza_types.pizza_type_id 

GROUP BY category, name 
ORDER BY category, PizzasPerType DESC; 
```

Overall, it was almost the same, but now we were selecting and grouping by the Category, and not the individual pizza name. 

