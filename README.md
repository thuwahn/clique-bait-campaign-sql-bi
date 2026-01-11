# Clique Bait Campaign

## Introduction

Clique Bait is an online platform where marketing campaigns are used to drive user engagement and purchases. This project analyzes user interaction data to understand how campaign exposure affects purchases and assess campaign effectiveness.

## Objective

Evaluate the effectiveness of marketing campaigns by analyzing impression, click, and purchase events to quantify purchase uplift and identify high-performing campaigns for better budget allocation and creative optimization.

## About Dataset

There is a total of 5 datasets:
- **`users`**(user_id, cookie_id, start_date): Customers who visit the Clique Bait website are tagged via their  **`cookie_id`**.
- **`events `**(visit_id, cookie_id, page_id, event_type, sequence_number, event_time): Customer visits are logged in this events table at a **`cookie_id`** level and the **`event_type`** and **`page_id`** values can be used to join onto relevant satellite tables to obtain further information about each event.
- **`event_identifier `**(event_type, event_name): The **`event_identifier `** table shows the types of events which are captured by Clique Bait’s digital data systems.
- **`campaign_identifier`**(campaign_id, products, campaign_name, start_date, end_date): This table shows information for the 3 campaigns that Clique Bait has ran on their website so far in 2020.
- **`page_hierarchy`**(page_id, page_name, product_category, product_id): This table lists all of the pages on the Clique Bait website which are tagged and have data passing through from user interaction events.

**ER Diagram:**

<img width="715" height="347" alt="127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8" src="https://github.com/user-attachments/assets/36389568-2600-48c3-8b78-bd5af2ab682d" />

## User Segmentation

Users were segmented into three mutually exclusive groups to enable uplift and funnel analysis:
- **Non-exposed users**: users who did not receive any campaign impression
- **Impression-only users**: users who received at least one impression but did not click
- **Clickers**: users who clicked on at least one campaign impression

## KPI Framework

The following KPIs were defined to evaluate campaign performance and user conversion behavior:
- Impression rate
- Click-through rate (CTR)
- Purchase conversion rate
- Purchase uplift by user group
- Campaign-level performance comparison

##  Project Structure

| **Folder**              | **Description**                                              |
|-------------------------|--------------------------------------------------------------|
| schema.sql              | Database schema and reference tables |
| case_study_solution.sql | Answers the original case study questions. |
| campaign_analysis.sql   | Data cleaning, user segmentation, and funnel & uplift metric computation using CTEs and conditional aggregation. |
| python                  | Exploratory data analysis and validation, including distribution analysis and time-to-purchase patterns. |
| powerbi                 | One-page management dashboard for campaign performance reporting. |
