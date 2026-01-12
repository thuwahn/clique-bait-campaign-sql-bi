# A. Digital Analysis

### 1. What is the percentage of visits which have a purchase event?

```sql
SELECT ROUND(100*COUNT(visit_id) / 
			(SELECT COUNT(DISTINCT visit_id)::NUMERIC 
			 FROM events), 2) AS purchase_rate
FROM events
JOIN event_identifier USING (event_type)
WHERE event_name='Purchase';
```

| **purchase_rate**       |
|-------------------------|
| 49.86              	  | 

### 2. What is the percentage of visits which view the checkout page but do not have a purchase event?

Before answering this question, a base view is created to join relevant event and page information:

```sql
CREATE VIEW joined_tables AS
SELECT 	e.visit_id,
    	e.cookie_id,
    	e.page_id,
    	p.page_name,
    	ei.event_name,
    	e.event_time
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier ei ON e.event_type = ei.event_type;
```

Using the base view above to calculate the required metrics and answer the question:

```sql
WITH checkout_purchase_cte AS (
	SELECT 	COUNT(CASE WHEN page_name='Checkout' THEN visit_id END) AS checkouts,
			COUNT(CASE WHEN event_name='Purchase' THEN visit_id END) AS perchases
	FROM joined_tables
)
SELECT ROUND(100*(checkouts-perchases)/checkouts::NUMERIC,2) AS checkout_abandonment_rate
FROM checkout_purchase_cte;
```

| **checkout_abandonment_rate**|
|------------------------------|
| 15.50             	  	   | 

### 3. What are the top 3 pages by number of views?
```sql
SELECT 	page_name, 
		COUNT(*) AS views
FROM joined_tables
WHERE event_name='Page View'
GROUP BY page_name
ORDER BY COUNT(*) DESC
LIMIT 3;
```

| **page_name**		|**views**		|
|-------------------|---------------|
| All Products      | 3174			|
| Checkout	        | 2103			|
| Home Page	        | 1782			|

### 4. What is the number of views and cart adds for each product category?

```sql
SELECT 	product_category, 
		COUNT(CASE WHEN event_name='Page View' THEN visit_id END) AS views,
		COUNT(CASE WHEN event_name='Add to Cart' THEN visit_id END) AS cart_adds
FROM joined_tables
WHERE product_category IS NOT NULL
GROUP BY product_category
ORDER BY views DESC;
```

| **product_category**		|**views**		|**views**		|
|---------------------------|---------------|---------------|
| Shellfish      			| 6204			|3792			|
| Fish	        			| 4633			|2789			|
| Luxury	        		| 3032			|1870			|

### 5. What are the top 3 products by purchases?

```sql
SELECT 	page_name AS product_name, 
		COUNT(*) AS purchases
FROM joined_tables
WHERE event_name='Add to Cart'
  AND product_id IS NOT NULL
  AND visit_id IN (SELECT visit_id
  				   FROM joined_tables
				   WHERE event_name='Purchase') 
GROUP BY page_name
ORDER BY purchases DESC
LIMIT 3;
```

| **product_name**	|**purchases**	|
|-------------------|---------------|
| Lobster	        | 754			|
| Oyster	        | 726			|
| Crab		        | 719			|


# B. Product Funnel Analysis





