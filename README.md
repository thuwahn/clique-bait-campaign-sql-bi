# Clique Bait Campaign


## Introduction

Clique Bait is an online platform where marketing campaigns are used to drive user engagement and purchases. This project analyzes user interaction data to evaluate campaign effectiveness using both funnel-based conversion metrics and uplift analysis.


## Objective

The goal is to evaluate the effectiveness of marketing campaigns by analyzing user progression through the impression → click → purchase funnel, quantifying the incremental purchase uplift attributable to campaign exposure, and identifying high-performing campaigns to support budget allocation and creative optimization.


## About Dataset

There is a total of 5 datasets:
- **`users`**(user_id, cookie_id, start_date): Customers who visit the Clique Bait website are tagged via their  **`cookie_id`**.
- **`events `**(visit_id, cookie_id, page_id, event_type, sequence_number, event_time): Customer visits are logged in this events table at a **`cookie_id`** level and the **`event_type`** and **`page_id`** values can be used to join onto relevant satellite tables to obtain further information about each event.
- **`event_identifier `**(event_type, event_name): The **`event_identifier `** table shows the types of events which are captured by Clique Bait’s digital data systems.
- **`campaign_identifier`**(campaign_id, products, campaign_name, start_date, end_date): This table shows information for the 3 campaigns that Clique Bait has ran on their website so far in 2020.
- **`page_hierarchy`**(page_id, page_name, product_category, product_id): This table lists all of the pages on the Clique Bait website which are tagged and have data passing through from user interaction events.


**ER Diagram:**

<img width="1292" height="788" alt="image" src="https://github.com/user-attachments/assets/339233e6-5a42-4678-a874-3c7645149468" />



## User Segmentation

Users were segmented into three mutually exclusive exposure groups to support behavioral analysis:

- **Non-exposed users**: users who did not receive any campaign impression
- **Impression-only users**: users who received at least one impression but did not click
- **Clickers**: users who clicked on at least one campaign impression


## Funnel Metrics

### Funnel steps:

- Impression → Click → Purchase

### Metrics:

```
Click-through Rate (CTR) = Click Users / Impression Users

Purchase Conversion Rate = Purchase Users / Click Users

Overall Funnel Conversion Rate = Purchase Users / Impression Users
```

> **Note**  
> Funnel conversion metrics are calculated only for users who received at least one campaign impression.


## Uplift Analysis

Uplift analysis compares purchase behavior across user exposure groups to quantify the incremental impact of campaign exposure.

### Metrics:

```
Purchase rate = Purchase Users​ / Total Users (by user group)

Uplift=  PurchaseRate_treated​ − PurchaseRate_control​ 

Campaign-level uplift comparison
```

## KPI Framework

The following KPIs were defined to evaluate campaign performance:
- Funnel conversion metrics
- Purchase conversion rate
- Purchase uplift by user group
- Campaign-level performance comparison

##  Project Structure

| **Folder**                     | **Description**                                              |
|--------------------------------|--------------------------------------------------------------|
| schema.sql                     | Database schema and reference tables |
| case_study_solution.md         | Answers the original case study questions. |
| campaign_uplift_analysis.md    | Data cleaning, user segmentation, and funnel & uplift metric computation using CTEs and conditional aggregation. |
| funnel_uplift_analysis.pbix    | Power BI dashboard covering campaign funnel performance, user behavior analytics, product-level conversion analysis, and uplift measurements across campaign groups.  |
