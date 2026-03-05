# Power BI Dashboard — Campaign Funnel & Uplift Analysis

This Power BI report provides a comprehensive view of how marketing campaigns influence user behavior, product interaction, and purchase conversion. 
The dashboard integrates SQL-based user segmentation, funnel metrics, and uplift analysis to evaluate campaign performance across impression, click, 
and purchase stages.

## 1. Dashboard Overview
The Overview page summarizes the entire performance picture across campaigns and products.

<img width="1527" height="856" alt="image" src="https://github.com/user-attachments/assets/c162357c-5ecb-4330-841d-440064f4eb37" />


### 1. Funnel Efficiency

The Overall Purchase Conversion Rate is 86.4%  Once a user shows intent (Clicks), the conversion probability is virtually absolute. This indicates that the Landing Page and Checkout flow are fully optimized.
- **Action**: Stop optimizing the checkout process. Instead, reallocate 100% of the optimization budget to Top-of-Funnel (ToF) activities to drive more Impressions.

### 2. Consumer Behavior
The Half Off and BOGOF campaigns achieved superior conversion rates (86.7% and 84.8% respectively). Customers are far more responsive to "deep discounts" (50%) or "gift-with-purchase" mechanics than to moderate percentage discounts.
- **Action**: Consider adjusting the content of the 25% Off campaign or switching to more attractive promotional formats to maximize conversion efficiency.

### 3. Product Performance
Lobster leads the catalog with a 77.9% purchase rate, followed closely by Oyster and Kingfish. Customers show a distinct tendency to purchase fresh, easy-to-prepare seafood items. Despite being a premium product, Russian Caviar has the lowest purchase rate (73.7%) on this list.
- **Action**: Pivot visuals and marketing content to specifically target the "Lux Life" segment rather than mass audiences.  

### 4. Monthly Performance
While February showed peak performance, March experienced a drastic ~90% drop in impressions and sales. The decline in sales is not due to diminishing ad quality, but stems directly from the massive drop in Impressions. This pattern confirms the business currently relies too heavily on short-term events or temporary campaigns (likely peaking around Tet/Valentine's Day).
- **Immediate Investigation**: Determine if the drop is due to budget exhaustion or a natural decline in market interest post-holiday.
- **Action**: Develop a distributed marketing plan to ensure a baseline of traffic. The goal is to avoid revenue free-fall following peak periods and smooth out these monthly fluctuations.
---

## 2. Campaign Details

This page analyzes the performance of three marketing campaigns run from January to March:

![funnel_uplift_analysis-hình ảnh-1](https://github.com/user-attachments/assets/59d7dd6f-fce5-48d4-98d9-ba7e6ae4512d)

### Campaign Overview
#### 1. The Revenue Star: Half Off - Treat Your Shellf(ish)
It accounted for 82% of total impressions (353/426) and maintained a stellar conversion rate (CR) of 86%. The Click Abandonment rate was virtually zero (only 2 drops out of 308 clicks).

The 50% discount holds strong mass appeal and fits the preferences of the vast majority of users. It trigger impulse buying without raising doubts about product value.

- **Action**:
    - Reallocate the majority of the ad budget (70-80%) to this campaign to maximize revenue.
    - Target Lookalike Audiences based on the behavior of users who clicked or purchased from this campaign to find new high-potential customers.

#### 2. The Potential Candidate: BOGOF - Fishing For Compliments
Despite having the lowest Impressions (66), it achieved the highest Click-Through Rate (87.9%) across the system. 

The keywords "Free" or "Buy 1 Get 1" act as powerful psychological triggers. Even with limited exposure, the high engagement rate proves that customers are highly motivated by the perceived value of a "gift."

- **Action**:
    - Inventory Clearance: Use for new product launches or to clear inventory quickly.
    - Bundling: Bundle with premium products to increase Average Order Value (AOV).
    - Email Marketing: Send exclusive BOGOF offers to the existing customer list to drive repeat purchases and re-engage dormant users.
 
#### 3. The Weak Link: 25% Off - Living The Lux Life
This campaign was the lowest performer across the board (CTR 77.6%, CR 72.4%) and suffered from the highest abandonment rates.

For the premium/luxury customer segment, a 25% discount is not compelling. These customers are more sensitive to "Exclusive Value" than to minor price reductions.

- **Action**:
    - A/B Test Content: Compare the effectiveness of "25% Off" versus "Save 1/4" messaging.
    - Retargeting: Target users who viewed but did not click with the higher-converting BOGOF offer to re-engage them.
    - Conditional Kill: If the above tactics fail to improve metrics, permanently eliminate the campaign to prevent budget wastage on a low-efficiency funnel.

### Monthly Trends
The timeline chart provides definitive proof that the campaign rotation strategy optimized efficiency, but failed on volume maintenance.

| Month | Strategy | Conversion Rate | Result |
|------:|---------:|----------------:|--------|
| January | 25% Off + BOGOF | 76.3% | Moderate performance dragged down by the weak 25% offer. |
| February | Half Off | 86.7% | Performance Spike. Removing the weak link boosted overall efficiency significantly. |
| March | Half Off | 87.0% | Peak Efficiency / Volume Crash. CR hit an all-time high, proving the ad creative is still fresh. The sales drop is strictly due to a lack of Impression volume.|

Contrary to a typical post-holiday slump where engagement metrics fade, the campaign Conversion Rate actually climbed to a record high of 87.0% in March. This divergence —low volume despite high efficiency — confirms that the revenue drop was not driven by ad fatigue or diminishing quality. Instead, it exposes a structural vulnerability: the business is over-indexed on short-term bursts (likely Tet/Valentine’s), leaving it prone to instability when campaign budgets are paused.
- **Action**:
    - **Immediate Investigation**: Audit the ad accounts to distinguish between forced budget caps versus a strategic pause in spending.
    - **Action**: Switch to a consistent marketing plan. Maintain an "Always-On" budget to keep traffic steady and avoid sudden revenue drops.
---

## 3. User Behavior — Segmentation & Journey Flow
The User Behavior dashboard provides a comprehensive view of how different customer segments interact with the brand. 

![funnel_uplift_analysis_3](https://github.com/user-attachments/assets/b771b156-9892-48cb-9941-4b2bf482d961)

### 1. Clickers: 
The Purchase Rate is 98.1% and the Clicker Drop Rate is a negligible 2.4%.

Once a user clicks on an ad, the purchase is virtually guaranteed. This proves that the Ad Copy and Landing Page are perfectly aligned.

### 2. Campaign Behavioral Analysis:
The Half Off campaign has the most frictionless, boasting a record-low 0.6% Drop Rate.
- **Action**: Pour 70% of the budget into "Half Off".
  
The BOGOF campaign excels in brand memory.  It achieved a perfect 100% Conversion Rate for "Impression-only" users (those who saw the ad but didn't click immediately), making it the most powerful tool for planting brand awareness.
- **Action**: Use "BOGOF" creatives for broad targeting (Top of Funnel). It is the most effective at sticking in customers’ minds.
  
The 25% Off generates the highest friction with a 6.6% Drop Rate—11 times higher than the "Half Off" campaign. It actively repels users and has the lowest brand recall efficiency.
- **Action**: Eliminate "25% Off" Immediately. It is dead weight.

### 3. Collapse Data regarding Non-exposed users in March
Non-exposed purchases collapsed from 60 and 61 orders to 12 orders in March.

**Insight**: The revenue drop in March occurred not only in the Paid channel but also in the Organic channel. Paid ads act as a catalyst. In Jan/Feb, high ad visibility drove brand awareness, leading to high organic search volume. When ad impressions were cut in March, brand visibility vanished, causing the organic stream to dry up immediately.
- **Action**: Maintain a baseline budget for Display Ads campaigns to sustain brand presence. This is essential to feed the "Non-exposed" funnel.

--- 

## 4. Product Details — Product Funnel & Performance
The Product Details dashboard provides a granular view of customer engagement from "View" to "Purchase". 
![funnel_uplift_analysis_4](https://github.com/user-attachments/assets/799288a7-8db4-4327-8b54-740319fbeaff)

### 1. The Funnel Bottleneck: Browsing vs. Buying
The biggest drop-off occurs at the initial interest stage. ~40% of users view a product but decide not to add it to their cart. In contrast, once an item is in the cart, the conversion rate is exceptionally high (75.9%).
- **Action**:
    - Focus on Product Pages (PDP): The Checkout is optimized. Shift resources to improve Product Detail Pages.
    - Tactics: Enhance high-resolution imagery, add video content, improve product descriptions, and display social proof (reviews) prominently near the "Add to Cart" button to boost the ATC Rate.

### 2. Portfolio Balance
The "Monthly Purchased Products Heatmap" reveals a surprising trend: Demand is evenly distributed.

Observation: Sales volumes across the top 8 products are remarkably similar, ranging from 1,469 units (Black Truffle) to 1,568 units (Oyster). There is no "long tail" of unsold inventory; every product is a "hero" product.

**Insight**: The specific menu curation is perfectly aligned with the target audience's tastes. Cross-selling should be effortless because customers clearly value the entire range equally.

- **Action**: Implement "Bundle Deals" aggressively. Since customers like everything equally, pre-set bundles (e.g., "The Full Ocean Platter": Crab + Oyster + Salmon) have a high probability of success and will increase Average Order Value (AOV).

### 3. The "May" Anomaly (High Intent)
While volume dropped significantly in April and May (following the March trend), May showed a sudden spike in user intent. The Add-to-Cart (ATC) Rate jumped to 69.8% in May, significantly higher than the Q1 average of ~61%.

**Insight**: Although traffic was low, the few users visiting in May were highly qualified and ready to buy.

- **Action**: Analyze the traffic source for May. Whatever channel brought in those few users delivered the highest quality leads of the year. Replicate that targeting strategy.

### Product-Specific Strategy
#### The "Fresh vs. Cured" Divide
Revisiting the purchase rates with the full dataset confirms the strategy proposed earlier:
|Product Group|Avg. Purchase Rate|Customer Behavior|Strategy|
|------:|---------:|----------------:|--------|
|Fresh Shellfish|High (~77%)|Decisive. Users see it, want it, and buy it with little hesitation.|Lead Magnets. Use these visuals in all Top-of-Funnel ads to capture attention and drive cheap clicks.|
|Luxury Cured|Lower (~73-76%)|Hesitant. Users view and add to cart, but are more likely to abandon at checkout (likely due to price).|Retargeting. Use email flows or dynamic retargeting ads offering a small incentive (e.g., "Free Shipping") specifically for users who abandoned these high-margin items.|

--- 

## 5. Uplift Analysis — Incremental Impact

![funnel_uplift_analysis_5](https://github.com/user-attachments/assets/c9192bff-5aba-4811-a1c4-e4e875e40c9b)

### 1. Ad Effect
Uplift Impression vs No-Impression is 27.96%, showing an ad increases the probability of purchase by nearly 28 percentage points compared to the organic baseline.

**Strategic Implication**: The business is heavily ad-dependent. Without ads, the conversion rate drops significantly (from ~97% down to ~69%). This directly explains the revenue decline in March: when ads were turned off, the 28% growth also disappeared.

### 2.  Campaign Specific Uplift:
- The BOGOF offer is the most persuasive. It takes users with low organic intent (63%) and converts them almost perfectly (98%).

- Half Off boasts the highest Organic Demand (76.3%). Even without advertising, this product still sells well (76%). In this case, advertising acts as a catalyst, pushing the already high conversion rate to a peak of 98.9%.

- While 25% Of advertising does provide a lift (+26.7%), this campaign starts from the lowest baseline (62.3%) and finishes with the lowest result (89.0%).


### 3. Uplift Click vs Impression Only
- **BOGOF (Negative Uplift -2.00%)**: Users who only saw the ad converted at 100%, while those who clicked converted at 98%. The click added no value. The campaign offer is so simple and powerful that visuals alone are sufficient to drive a purchase.

- **25% Off (Positive Uplift +11.55%)**: Users who clicked converted much higher (91.5%) than those who just viewed (80%). This offer is weaker. It requires the user to click, land on the site, and be further convinced by content to make a purchase.

### 4.  Strategic Recommendations
- **Prioritize Half Off for profitability**: Since it has the highest Organic Baseline (76%), you don't need to spend as aggressively to convince people. The product sells itself.
- **Use BOGOF for Market Penetration**: Use this campaign to target "Cold Audiences" (people who don't know the brand). The ad is exceptionally good at turning uninterested strangers into buyers.
- **Click-Based KPIs**: For BOGOF, the "View" is what drives the sale, not the click. Optimizing Cost Per Impression (CPM) is better than Cost Per Click (CPC).





