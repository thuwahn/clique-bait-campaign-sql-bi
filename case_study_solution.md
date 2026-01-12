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
| 49.86l              	  | 

### 2. What is the percentage of visits which view the checkout page but do not have a purchase event?

Before answering this question, a base view is created to join relevant event and page information.

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
