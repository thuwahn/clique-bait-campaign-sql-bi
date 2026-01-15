# A. USER SEGMENTATION

- Funnel-relevant events mapped to users:

```sql
WITH user_events AS (
    SELECT 	u.user_id,
        	ei.event_name,
        	e.event_time
    FROM events e
    JOIN event_identifier ei ON ei.event_type = e.event_type
    JOIN users u ON u.cookie_id = e.cookie_id
    WHERE ei.event_name IN ('Ad Impression', 'Ad Click', 'Purchase')
)
```

- Users were segmented into mutually exclusive exposure groups ('Non-exposed', 'Impression-only', 'Clickers'):

```sql
user_segmentation  AS (
	SELECT	u.user_id,
			c.campaign_id,
			c.campaign_name,

			MAX(CASE WHEN u.event_name = 'Ad Impression' THEN 1 ELSE 0 END) AS has_impression,
			MAX(CASE WHEN u.event_name = 'Ad Click' THEN 1 ELSE 0 END) AS has_click,
			MAX(CASE WHEN u.event_name = 'Purchase' THEN 1 ELSE 0 END) AS has_purchase,

			CASE
	            WHEN MAX(CASE WHEN u.event_name = 'Ad Impression' THEN 1 ELSE 0 END) = 0
	                THEN 'Non-exposed'
	            WHEN MAX(CASE WHEN u.event_name = 'Ad Impression' THEN 1 ELSE 0 END) = 1
	             	AND MAX(CASE WHEN u.event_name = 'Ad Click' THEN 1 ELSE 0 END) = 0
	                THEN 'Impression-only'
	            WHEN MAX(CASE WHEN u.event_name = 'Ad Click' THEN 1 ELSE 0 END) = 1
	                THEN 'Clicker'
	        END AS user_segmentation 
			
	FROM user_events u
	JOIN campaign_identifier c ON u.event_time BETWEEN c.start_date AND c.end_date
	GROUP BY u.user_id, c.campaign_id, c.campaign_name
)
```

| user_id | campaign_id | campaign_name                          | has_impression | has_click | has_purchase | user_segmentation |
|--------:|------------:|----------------------------------------|---------------:|----------:|-------------:|-------------------|
| 445     | 3           | Half Off - Treat Your Shelf(...)       | 1              | 0         | 1            | Impression-only   |
| 448     | 2           | 25% Off - Living The Lux Life          | 1              | 0         | 1            | Impression-only   |
| 388     | 2           | 25% Off - Living The Lux Life          | 1              | 1         | 1            | Clicker           |
| 443     | 3           | Half Off - Treat Your Shelf(...)       | 1              | 1         | 1            | Clicker           |
| 6       | 2           | 25% Off - Living The Lux Life          | 0              | 0         | 1            | Non-exposed       |
| 384     | 1           | BOGOF - Fishing For Compliments        | 0              | 0         | 1            | Non-exposed       |


# B. FUNNEL METRICS

## 1. Funnel metrics by user group

```sql
campaign_funnel_metrics AS (
	SELECT 	campaign_id,
        	campaign_name,

			COUNT(DISTINCT user_id) AS impression_users,
			COUNT(DISTINCT CASE WHEN has_click = 1 THEN user_id END) AS click_users,
			COUNT(DISTINCT CASE WHEN has_click = 1 AND has_purchase = 1 THEN user_id END) AS purchase_users,

			COUNT(DISTINCT CASE WHEN has_click = 1 THEN user_id END)::FLOAT 
				/ NULLIF(COUNT(DISTINCT user_id), 0) AS ctr,
			COUNT(DISTINCT CASE WHEN has_click = 1 AND has_purchase = 1 THEN user_id END)::FLOAT
				/ NULLIF(COUNT(DISTINCT CASE WHEN has_click = 1 THEN user_id END),0) AS purchase_cr,
			COUNT(DISTINCT CASE WHEN has_click = 1 AND has_purchase = 1 THEN user_id END)::FLOAT
				/ NULLIF(COUNT(DISTINCT user_id), 0) AS overall_funnel_cr		
			
    FROM user_segmentation
    WHERE has_impression = 1
	GROUP BY campaign_id, campaign_name
)
SELECT *
FROM campaign_funnel_metrics;
```

| campaign_id | campaign_name | impression_users | click_users | purchase_users | ctr | purchase_cr | overall_funnel_cr |
|------------|---------------|------------------|-------------|----------------|------|-------------|-------------------|
| 1 | BOGOF - Fishing For Compliments | 59 | 50 | 49 | 0.8475 | 0.9800 | 0.8305 |
| 2 | 25% Off - Living The Lux Life | 91 | 71 | 65 | 0.7802 | 0.9155 | 0.7143 |
| 3 | Half Off - Treat Your Shelf(ing) | 352 | 307 | 305 | 0.8722 | 0.9935 | 0.8665 |

## 2. Overall funnel metrics

```sql
funnel_metrics AS (
	SELECT 	SUM(impression_users) AS impression_users,
			SUM(click_users) AS click_users,
			SUM(purchase_users) AS purchase_users,

			ROUND(SUM(click_users) / NULLIF(SUM(impression_users),0)::NUMERIC, 4) AS ctr,
			ROUND(SUM(purchase_users) / NULLIF(SUM(click_users),0)::NUMERIC, 4) AS purchase_cr,
			ROUND(SUM(purchase_users) / NULLIF(SUM(impression_users),0)::NUMERIC, 4) AS overall_funnel_cr			
			
	FROM campaign_funnel_metrics
)
SELECT *
FROM funnel_metrics;
```

| impression_users | click_users | purchase_users | ctr | purchase_cr | overall_funnel_cr|
|------------------|-------------|----------------|------|--------------|-------------------|
| 502 | 428 | 419 | 0.8526 | 0.9790 | 0.8347 |

# C. PURCHASE UPLIFT ANALYSIS

- **Treated_users**: Users who received at least one Ad Impression

```sql
treated_users AS (
	  SELECT DISTINCT user_id
	  FROM user_events
	  WHERE event_name = 'Ad Impression'
)
```

 - **Control_users**: Users who never received any Ad Impression

```sql
control_users AS (
	SELECT DISTINCT u.user_id
	FROM users u
	WHERE u.user_id NOT IN (
		SELECT user_id FROM treated_users)
)
```

- **Purchase_users**: Users who made at least one Purchase event

```sql
purchase_users AS (
    SELECT DISTINCT user_id
    FROM user_events
    WHERE event_name = 'Purchase'
)
```

## 1. Purchase rate by treatment group

```sql
purchase_rate AS (
    SELECT	'Treated' AS user_group,
			COUNT(DISTINCT t.user_id) AS total_users,
			COUNT(DISTINCT p.user_id) AS purchasers,
			ROUND(COUNT(DISTINCT p.user_id)::NUMERIC
				/ COUNT(DISTINCT t.user_id),4) AS purchase_rate
	FROM treated_users t
	LEFT JOIN purchase_users p ON t.user_id = p.user_id

	UNION ALL

	SELECT
		'Control' AS user_group,
		COUNT(DISTINCT c.user_id),
		COUNT(DISTINCT p.user_id),
		ROUND(COUNT(DISTINCT p.user_id)::NUMERIC 
		/ COUNT(DISTINCT c.user_id),4) AS purchase_rate
	FROM control_users c
	LEFT JOIN purchase_users p ON c.user_id = p.user_id
)
SELECT * 
FROM purchase_rate;
```

| user_group | total_users | purchasers | purchase_rate |
|------------|-------------|------------|----------------|
| **Treated** | 442 | 439 | **0.9932** |
| **Control** | 58 | 45 | **0.7759** |

## 2. Purchase uplift: Treated - Control

```sql
uplift_analysis AS (
    SELECT	MAX(CASE WHEN user_group = 'Treated' THEN purchase_rate END) AS treated_purchase_rate,
			MAX(CASE WHEN user_group = 'Control' THEN purchase_rate END) AS control_purchase_rate,
			MAX(CASE WHEN user_group = 'Treated' THEN purchase_rate END)
			-
			MAX(CASE WHEN user_group = 'Control' THEN purchase_rate END)
				AS purchase_uplift
	FROM purchase_rate
)
SELECT * 
FROM uplift_analysis;
```

| treated_purchase_rate | control_purchase_rate | purchase_uplift |
|-----------------------|------------------------|------------------|
| **0.9932** | **0.7759** | **0.2173** |
