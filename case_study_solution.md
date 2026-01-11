# A. Digital Analysis

### 1. What is the percentage of visits which have a purchase event?
SELECT ROUND(100*COUNT(visit_id) / 
			(SELECT COUNT(DISTINCT visit_id)::NUMERIC 
			 FROM events), 2) AS purchase_rate
FROM events
JOIN event_identifier USING (event_type)
WHERE event_name='Purchase';
