# A. USER SEGMENTATION

Create a view that map relevant events to users:

```sql
CREATE VIEW user_events AS
SELECT 	u.user_id,
	    ei.event_name,
	    e.event_time
FROM events e
JOIN event_identifier ei ON ei.event_type = e.event_type
JOIN users u ON u.cookie_id = e.cookie_id
WHERE ei.event_name IN ('Ad Impression', 'Ad Click', 'Purchase');
```

Create a view that maps each user event to the campaign active at the time of the event.

```sql
CREATE VIEW campaign_events AS
SELECT 	u.user_id,
		u.event_name,
	    u.event_time,	    
	    c.campaign_id,
	    c.campaign_name
FROM user_events u
JOIN campaign_identifier c ON u.event_time 
	BETWEEN c.start_date AND c.end_date;
```

Users were segmented into mutually exclusive exposure groups ('Non-exposed', 'Impression-only', 'Clickers'):

```sql
CREATE VIEW user_segmentation AS
SELECT	user_id,
		MIN(event_time) AS event_time,
		campaign_id,
		campaign_name,

		MAX(CASE WHEN event_name = 'Ad Impression' THEN 1 ELSE 0 END) AS has_impression,
		MAX(CASE WHEN event_name = 'Ad Click' THEN 1 ELSE 0 END) AS has_click,
		MAX(CASE WHEN event_name = 'Purchase' THEN 1 ELSE 0 END) AS has_purchase,

		CASE
			WHEN MAX(CASE WHEN event_name = 'Ad Impression' THEN 1 ELSE 0 END) = 0
				THEN 'Non-exposed'
	        WHEN MAX(CASE WHEN event_name = 'Ad Impression' THEN 1 ELSE 0 END) = 1
	            AND MAX(CASE WHEN event_name = 'Ad Click' THEN 1 ELSE 0 END) = 0
	            THEN 'Impression-only'
	        WHEN MAX(CASE WHEN event_name = 'Ad Click' THEN 1 ELSE 0 END) = 1
	            THEN 'Clicker'
	     END AS user_segmentation 
			
FROM campaign_events 
GROUP BY user_id, campaign_id, campaign_name;

SELECT *
FROM user_segmentation;
```

| user_id | event_time  | campaign_id | campaign_name                          | has_impression | has_click | has_purchase | user_segmentation |
|--------:|------------:|------------:|----------------------------------------|---------------:|----------:|-------------:|-------------------|
| 445     | 2020-02-03  | 3           | Half Off - Treat Your Shelf(...)       | 1              | 0         | 1            | Impression-only   |
| 448     | 2020-01-25  | 2           | 25% Off - Living The Lux Life          | 1              | 0         | 1            | Impression-only   |
| 388     | 2020-01-17  | 2           | 25% Off - Living The Lux Life          | 1              | 1         | 1            | Clicker           |
| 443     | 2020-02-24  | 3           | Half Off - Treat Your Shelf(...)       | 1              | 1         | 1            | Clicker           |
| 6       | 2020-01-25  | 2           | 25% Off - Living The Lux Life          | 0              | 0         | 1            | Non-exposed       |
| 384     | 2020-01-03  | 1           | BOGOF - Fishing For Compliments        | 0              | 0         | 1            | Non-exposed       |


# B. FUNNEL METRICS

Funnel metrics are calculated using user-level flags to avoid double counting and to measure conversion from impression → click → purchase accurately.

```DAX
Impression Users =
CALCULATE(
    DISTINCTCOUNT('hqtcsdl user_segmentation'[user_id]),
    'hqtcsdl user_segmentation'[has_impression] = 1
)

Click Users =
CALCULATE(
    DISTINCTCOUNT('hqtcsdl user_segmentation'[user_id]),
    'hqtcsdl user_segmentation'[has_click] = 1
)

Purchase Users =
CALCULATE(
    DISTINCTCOUNT('hqtcsdl user_segmentation'[user_id]),
    'hqtcsdl user_segmentation'[has_click] = 1 &&
    'hqtcsdl user_segmentation'[has_purchase] = 1
)

```



# C. PURCHASE UPLIFT ANALYSIS

**Treated_users**: Users who received at least one Ad Impression

```sql
treated_users AS (
	  SELECT DISTINCT user_id
	  FROM user_events
	  WHERE event_name = 'Ad Impression'
)
```

**Control_users**: Users who never received any Ad Impression

```sql
control_users AS (
	SELECT DISTINCT u.user_id
	FROM users u
	WHERE u.user_id NOT IN (
		SELECT user_id FROM treated_users)
)
```

**Purchase_users**: Users who made at least one Purchase event

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
