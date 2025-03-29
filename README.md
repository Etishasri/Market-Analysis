# `Market Analysis`
- This repository contains SQL queries and analysis on an e-commerce grocery dataset, covering ordering patterns, product distribution, and user behavior. Explore insights on top products, reorder trends, and shopping habits. 🚀

## Task

```sql
use project_orders; 

select * from aisles; 
select * from departments; 
select * from order_products_train; 
select * from orders; 
select * from products; 

select count(*) from order_products_train;
```

## Analysis

### 1. What are the top 10 aisles with the highest number of products?
```sql
select aisles.aisle_id, aisle, count(Product_id) as product_count 
from aisles  
join products on aisles.aisle_id = products.aisle_id  
group by aisles.aisle_id  
order by count(product_id) desc 
limit 10;
```

### 2. How many unique departments are there in the dataset?
```sql
select count(distinct department) as unique_department 
from departments;
```

### 3. What is the distribution of products across departments?
```sql
select departments.department_id, department, count(product_id) as product_distribution 
from products  
join departments on products.department_id = departments.department_id 
group by departments.department_id  
order by count(product_id) desc;
```

### 4. What are the top 10 products with the highest reorder rates?
```sql
select o.product_id, p.product_name, count(o.reordered) as total_reorders 
from order_products_train o 
join products p on o.product_id = p.product_id 
where o.reordered = 1 
group by o.product_id, p.product_name 
order by total_reorders desc 
limit 10;
```

### 5. How many unique users have placed orders in the dataset?
```sql
select count(distinct user_id) as unique_users 
from orders;
```

### 6. What is the average number of days between orders for each user?
```sql
select user_id, avg(days_since_prior_order) as average_no_of_days 
from orders  
where days_since_prior_order is not null  
group by user_id  
order by average_no_of_days asc;
```

### 7. What are the peak hours of order placement during the day?
```sql
select order_hour_of_day, count(order_id) as order_count 
from orders  
group by order_hour_of_day  
order by count(order_id) desc;
```

### 8. How does order volume vary by day of the week?
```sql
select order_dow as day_of_week, count(order_id) as total_orders 
from orders 
group by order_dow 
order by day_of_week;
```

### 9. What are the top 10 most ordered products?
```sql
SELECT  
    p.product_name,  
    COUNT(opt.product_id) AS total_orders 
FROM  
    order_products_train opt 
JOIN  
    products p  
ON  
    opt.product_id = p.product_id 
GROUP BY  
    p.product_id, p.product_name 
ORDER BY  
    total_orders DESC 
LIMIT 10;
```

### 10. How many users have placed orders in each department?
```sql
select d.department_id, d.department as department_name, count(distinct o.user_id) as user_count 
from order_products_train opt 
join orders o on opt.order_id = o.order_id 
join products p on opt.product_id = p.product_id 
join departments d on p.department_id = d.department_id 
group by d.department_id, d.department 
order by user_count desc;
```

### 11. What is the average number of products per order?
```sql
select avg(product_count) as avg_products_per_order from ( 
    select order_id, COUNT(product_id) as product_count 
    from order_products_train 
    group by order_id 
) subquery;
```

### 12. What are the most reordered products in each department?
```sql
select d.department_id, d.department as department_name, p.product_id, p.product_name, sum(op.reordered) as total_reorders 
from order_products_train op 
join products p on op.product_id = p.product_id 
join departments d on p.department_id = d.department_id 
where op.reordered = 1 
group by d.department_id, d.department, p.product_id, p.product_name 
order by d.department_id, total_reorders desc;
```

### 13. How many products have been reordered more than once?
```sql
select count(distinct product_id) as products_reordered_more_than_once 
from order_products_train o  
where reordered > 1;
```

### 14. What is the average number of products added to the cart per order?
```sql
select avg(product_count) as avg_products_per_order from (
    select count(product_id) as product_count, order_id 
    from order_products_train 
    group by order_id
) subquery;
```

### 15. How does the number of orders vary by hour of the day?
```sql
select order_hour_of_day as hour_of_day, count(order_id) as total_orders 
from orders 
group by order_hour_of_day 
order by hour_of_day;
```

### 16. What is the distribution of order sizes (number of products per order)?
```sql
select product_count as number_of_products, count(*) as order_count 
from ( 
    select order_id, count(product_id) as product_count 
    from order_products_train 
    group by order_id 
) subquery 
group by product_count 
order by product_count;
```

### 17. What is the average reorder rate for products in each aisle?
```sql
select a.aisle_id, a.aisle as aisle_name, avg(op.reordered) as avg_reorder 
from order_products_train op 
join products p on p.product_id = op.product_id 
join aisles a on a.aisle_id = p.aisle_id 
group by a.aisle_id 
order by avg_reorder;
```

### 18. How does the average order size vary by day of the week?
```sql
select o.order_dow as day_of_week, avg(op.product_count) as avg_order_size 
from ( 
    select order_id, count(product_id) as product_count 
    from order_products_train 
    group by order_id 
) op 
join orders o on op.order_id = o.order_id 
group by o.order_dow 
order by day_of_week;
```

### 19. What are the top 10 users with the highest number of orders?
```sql
select user_id, count(order_id) as total_order 
from orders 
group by user_id 
order by total_order desc 
limit 10;
```

### 20. How many products belong to each aisle and department?
```sql
select a.aisle, d.department, count(p.product_id) 
from products p 
join aisles a on a.aisle_id = p.aisle_id 
join departments d on d.department_id = p.department_id 
group by a.aisle, d.department, a.aisle_id, d.department_id 
order by a.aisle_id, d.department_id;
```

## Attributes Information

### 1. Aisles Dataset:
- `aisle_id`: Unique identifier for the aisle. (Primary Key)
- `aisle`: Name of the aisle.

### 2. Departments Dataset:
- `department_id`: Unique identifier for the department. (Primary Key)
- `department`: Name of the department.

### 3. Order Products Prior Dataset:
- `order_id`: Unique identifier for the order. (Primary Key)
- `product_id`: Unique identifier for the product.
- `add_to_cart_order`: Order in which the product was added to the cart.
- `reordered`: Indicates whether the product was reordered in this order (1 for reordered, 0 for not reordered).

### 4. Orders Dataset:
- `order_id`: Unique identifier for the order. (Primary Key)
- `user_id`: Unique identifier for the user.
- `eval_set`: Evaluation set (prior, train, test).
- `order_number`: Order sequence number for the user.
- `order_dow`: Day of the week the order was placed.
- `order_hour_of_day`: Hour of the day the order was placed.
- `days_since_prior_order`: Number of days since the last order.

### 5. Products Dataset:
- `product_id`: Unique identifier for the product.
- `product_name`: Name of the product.
- `aisle_id`: Identifier for the aisle the product belongs to. (Primary Key)
- `department_id`: Identifier for the department the product belongs to.
