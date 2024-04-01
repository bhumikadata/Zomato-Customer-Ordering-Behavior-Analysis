# Zomato-Customer-Ordering-Behavior-Analysis

# Problem 

Zomato, an online food delivery company, is interested in analyzing customer food ordering behavior to identify customers who exhibit inconsistent patterns over time. 
The company wants to find customers who have placed orders on both weekdays and weekends but with a significant difference in the average order amount between weekdays and weekends.

The task is to write an SQL query to identify such customers. Specifically, the query should find customers who have placed a minimum of 3 orders on both weekdays and weekends each, where the average order amount on weekends is at least 20% higher than the average order amount on weekdays.

![day 26](https://github.com/bhumikadata/Zomato-Customer-Ordering-Behavior-Analysis/assets/131578649/3559b83a-d1f0-4c04-a022-01f01fe80dc0)


# SQL Code

```
WITH cte AS (
  SELECT 
    customer_id, 
    CASE 
      WHEN STRFTIME('%w', order_date) IN ('0','6') THEN 'weekend'
      ELSE 'weekday'
    END AS date_type,
    COUNT(customer_id) AS no_of_orders,
    ROUND(AVG(order_amount), 2) AS avg_order_amount
  FROM 
    orders
  GROUP BY 
    customer_id,
    date_type
),
final AS (
  SELECT
    customer_id,
    MAX(CASE WHEN date_type = 'weekday' THEN no_of_orders END) AS weekday_order,
    MAX(CASE WHEN date_type = 'weekend' THEN no_of_orders END) AS weekend_order,
    MAX(CASE WHEN date_type = 'weekday' THEN avg_order_amount END) AS weekday_avg_amount,
    MAX(CASE WHEN date_type = 'weekend' THEN avg_order_amount END) AS weekend_avg_amount
  FROM 
    cte 
  GROUP BY 
    customer_id  
)
SELECT
  customer_id, 
  weekend_avg_amount,
  weekday_avg_amount, 
  (weekend_avg_amount - weekday_avg_amount) * 100 / weekday_avg_amount AS percent_diff
FROM 
  final 
WHERE 
  weekend_avg_amount > 1.0 * weekday_avg_amount;
```

# Explanation of Code

### Common Table Expressions (CTE): 
Calculates the number of orders and average order amount for each customer on weekdays and weekends.
Subsequent Processing:

### final: 
Aggregates the data from cte to get the maximum number of orders and average order amount for both weekdays and weekends for each customer.

### Output:

Final query selects the customer_id, weekend_avg_amount, weekday_avg_amount, and percent_diff (percentage difference in average order amount between weekends and weekdays) from final table, filtering out customers where the weekend average order amount is at least 20% higher than the weekday average order amount.

# Use Case

This SQL script is useful for Zomato to identify customers who have varying ordering behaviors between weekdays and weekends. By understanding such patterns, Zomato can tailor marketing strategies or offers to these customers to maximize sales and customer satisfaction. For example, Zomato might offer weekend-specific discounts or promotions to encourage more orders from these customers during weekends.
