# SQL-project

```sql
-- Query to select the smallest carton that can fit the total volume of a specific order
select 
    c.carton_id,
    (c.len * c.width * c.height) as carton_vol
from 
    orders.carton c
where 
    (c.len * c.width * c.height) >= (
        select 
            sum(p.len * p.width * p.height * product_quantity) as volume 
        from 
            orders.order_header oh
        inner join 
            orders.order_items oi on oh.order_id = oi.ORDER_ID
        inner join 
            orders.product p on oi.PRODUCT_ID = p.PRODUCT_ID
        where 
            oh.ORDER_ID = 10006
    )
order by 
    (c.len * c.width * c.height) asc
limit 1;

-- Query to get customer details and total order quantity for shipped orders with more than 10 items
select 
    oc.customer_id, 
    concat(customer_fname, ' ', customer_lname) as Customer_full_name, 
    oh.order_id,
    sum(oi.product_quantity) as Total_Order_Qty 
from 
    online_customer oc
inner join 
    order_header oh on oh.customer_id = oc.customer_id
inner join 
    order_items oi on oi.order_id = oh.order_id
where 
    oh.order_status = 'Shipped'
group by 
    oh.order_id
having 
    Total_Order_Qty > 10
order by 
    customer_id;

-- Query to get order details for shipped orders with order_id greater than 10060
select 
    oh.order_id, 
    oc.customer_id, 
    concat(customer_fname, ' ', customer_lname) as Customer_full_name, 
    sum(oi.product_quantity) as product_Qty 
from 
    online_customer oc
inner join 
    order_header oh on oh.customer_id = oc.customer_id
inner join 
    order_items oi on oi.order_id = oh.order_id
where 
    oh.order_status = 'Shipped' 
    and oh.order_id > 10060
group by 
    oh.order_id
order by 
    Customer_full_name;

-- Query to get the most ordered product class (excluding orders from India and USA)
Select 
    pc.product_class_desc, 
    sum(oi.product_quantity) as total_quantity, 
    sum(oi.product_quantity * p.product_price) as total_value 
from 
    order_items oi 
inner join 
    order_header oh on oh.order_id = oi.order_id
inner join 
    online_customer oc on oc.customer_id = oh.customer_id
inner join 
    product p on p.product_id = oi.product_id
inner join 
    product_class pc on pc.product_class_code = p.product_class_code
inner join 
    address a on a.address_id = oc.address_id
where 
    oh.order_status = 'shipped' 
    and a.country not in ('India', 'USA')
group by 
    pc.product_class_desc, pc.product_class_code
order by 
    total_quantity desc
limit 1;
```
