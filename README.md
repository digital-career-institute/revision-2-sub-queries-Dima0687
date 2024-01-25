# java-DB-sub-queries

1. Return all customers listed in the orders table who placed orders on the most recently recorded day. 

2. Select all product names and prices that have unit price which is bigger than price of product with name 'Carnarvon Tigers'

3. Select customer ids and ther contact names that made order that was shipped to Brazil

4. Select customer ids who ordered more than 20 items of product with name = 'Tofu' on a single order.

5. Select only discounted products names, dicount value, category of it, and shipped date of order where this product occurs. Use join + sub query structure.

6. Select orders for customers that come from London and are shipped via United Package. Remember to use sub queries (not joins). 


```SQL
-- Return all customers listed in the orders table who placed orders on the most recently recorded day.

SELECT DISTINCT
    c.customer_id,
    c.company_name
FROM
    customers c
JOIN
    orders o ON c.customer_id = o.customer_id
WHERE
    o.order_date = (SELECT MAX(order_date) FROM orders);
	
-- Select all product names and prices that have unit price which is bigger than price of product with name 'Carnarvon Tigers'

SELECT
    product_name,
    unit_price
FROM
    products
WHERE
    unit_price > (SELECT unit_price FROM products WHERE product_name = 'Carnarvon Tigers');

-- Select customer ids and ther contact names that made order that was shipped to Brazil

SELECT DISTINCT
	c.customer_id,
	c.contact_name,
	o.ship_country
FROM order_details od
RIGHT JOIN orders o ON od.order_id = o.order_id
RIGHT JOIN customers c ON c.customer_id = o.customer_id
WHERE o.ship_country ILIKE 'Brazil';

-- Select customer ids who ordered more than 20 items of product with name = 'Tofu' on a single order.

SELECT DISTINCT
	o.customer_id,
	p.product_name,
	SUM(od.quantity) AS sum_of_quantity
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN products p ON od.product_id = p.product_id
WHERE p.product_name ILIKE 'Tofu'
GROUP BY o.customer_id, p.product_name
HAVING SUM(od.quantity) > 20;

-- Select only discounted products names, dicount value, category of it, 
-- and shipped date of order where this product occurs. Use join + sub query structure.

SELECT
	p.product_name,
	od.discount,
	ct.category_name,
	o.shipped_date
FROM products p
JOIN order_details od ON p.product_id = od.product_id
JOIN categories ct ON p.category_id = ct.category_id
JOIN 
	(SELECT DISTINCT order_id, shipped_date FROM orders) o ON od.order_id = o.order_id
WHERE od.discount > 0;

-- This one is also possible and without sub query
SELECT
	p.product_name,
	od.discount,
	ct.category_name,
	o.shipped_date
FROM products p
JOIN order_details od ON p.product_id = od.product_id
JOIN categories ct ON p.category_id = ct.category_id
JOIN orders o ON od.order_id = o.order_id
WHERE od.discount > 0;

-- Select orders for customers that come from London and are shipped via United Package. 
-- Remember to use sub queries (not joins).

SELECT
	*
FROM orders
WHERE customer_id IN (
	SELECT customer_id
	FROM customers
	WHERE city ILIKE 'London'
) AND ship_via IN (
	SELECT shipper_id
	FROM shippers
	WHERE company_name ILIKE 'United Package'
);

```
