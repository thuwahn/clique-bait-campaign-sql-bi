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

| **product_category**		|**views**		|**cart_adds**	|
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

---

# B. Product Funnel Analysis

#### Using a single SQL query - create a new output table which has the following details:
How many times was each product viewed?
How many times was each product added to cart?
How many times was each product added to a cart but not purchased (abandoned)?
How many times was each product purchased?

```sql
CREATE TABLE product_details AS
SELECT 	page_name AS product_name,
		COUNT(CASE WHEN event_name='Page View' THEN visit_id END) AS views,
		COUNT(CASE WHEN event_name='Add to Cart' THEN visit_id END) AS cart_adds,
		COUNT(CASE WHEN event_name='Add to Cart' AND NOT EXISTS (SELECT 1 
																 FROM joined_tables jt 
																 WHERE jt.visit_id = j.visit_id 
									  							   AND event_name='Purchase') 
			THEN 1 END) AS not_purchases,
		COUNT(CASE WHEN event_name='Add to Cart' AND EXISTS (SELECT 1 
														  	 FROM joined_tables jt 
														  	 WHERE jt.visit_id = j.visit_id 
															   AND event_name='Purchase') 
			THEN visit_id END) AS purchases
FROM joined_tables j
WHERE product_id IS NOT NULL
GROUP BY page_name;

SELECT * 
FROM product_details
ORDER BY product_name;
```

| **product_name**		|**views**|**cart_adds**|**not_purchases**|**purchases**|
|-----------------------|---------|-------------|-----------------|-------------|
|Abalone				|1525	  |932			|233			  |699			|
|Black Truffle			|1469	  |924			|217			  |707			|
|Crab					|1564	  |949	  		|230			  |719			|
|Kingfish				|1559	  |920			|213			  |707			|
|Lobster				|1547	  |968			|214			  |754			|
|Oyster					|1568	  |943			|217			  |726			|
|Russian Caviar			|1563	  |946			|249			  |697			|
|Salmon					|1559	  |938			|227			  |711			|
|Tuna					|1515	  |931			|234			  |697			|

#### Additionally, create another table which further aggregates the data for the above points but this time for each product category instead of individual products.

```sql
CREATE TABLE product_category_details AS
SELECT 	product_category,
		COUNT(CASE WHEN event_name='Page View' THEN visit_id END) AS views,
		COUNT(CASE WHEN event_name='Add to Cart' THEN visit_id END) AS cart_adds,
		COUNT(CASE WHEN event_name='Add to Cart' AND NOT EXISTS (SELECT 1 
															  	 FROM joined_tables jt 
															  	 WHERE jt.visit_id = j.visit_id 
																   AND event_name='Purchase') 
			THEN 1 END) AS not_purchases,
		COUNT(CASE WHEN event_name='Add to Cart' AND EXISTS (SELECT 1 
														  	 FROM joined_tables jt 
														  	 WHERE jt.visit_id = j.visit_id 
															   AND event_name='Purchase') 
			THEN visit_id END) AS purchases
FROM joined_tables j
WHERE product_id IS NOT NULL
GROUP BY product_category;

SELECT * 
FROM product_category_details
ORDER BY product_category;
```

| **product_category**	|**views**|**cart_adds**|**not_purchases**|**purchases**|
|-----------------------|---------|-------------|-----------------|-------------|
|Fish					|4633	  |2789			|674			  |2115			|
|Luxury					|3032	  |1870			|466			  |1404			|
|Shellfish				|6204	  |3792	  		|894			  |2898			|

### 1. Which product had the most views, cart adds and purchases?

```sql
SELECT 	(SELECT product_name FROM product_details ORDER BY views DESC LIMIT 1) || ' - ' || MAX(views) AS max_views,
		(SELECT product_name FROM product_details ORDER BY cart_adds DESC LIMIT 1) || ' - ' || MAX(cart_adds) AS max_cart_adds,
		(SELECT product_name FROM product_details ORDER BY purchases DESC LIMIT 1) || ' - ' || MAX(purchases) AS max_purchases
FROM product_details;
```

| **max_views**		|**max_cart_adds**		|**max_purchases**	|
|-------------------|-----------------------|-------------------|
| Oyster - 1568     | Lobster - 968			|Lobster - 754		|

### 2. Which product was most likely to be abandoned?

```sql
SELECT 	product_name,
		not_purchases AS most_abandoned
FROM product_details 
ORDER BY not_purchases DESC 
LIMIT 1;
```

| **product_name**	|**most_abandoned**	|
|-------------------|-------------------|
| Russian Caviar    | 249				|

### 3. Which product had the highest view to purchase percentage?






