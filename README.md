# Customer Churn Case Study

## Case Study Overview
This case study is part of the 2025 UC Berkeley Master of Analytics Google Hackathon. Tasked with a real world business scenario, we worked to create a comprehensive, data-driven recommendation in the form of a slideshow presentation to industry leaders. This repository covers our data cleaning, hypothesis testing, and data visualization processes in creating a robust, data-backed recommendation. 

## Case Study Prompt
#### The Business Scenario
You are the Analytics Task Force for BizGrow, a SaaS platform helping SMBs manage inventory. Until recently, growth was explosive. However, Q3 results just came in, and they are disastrous: Customer Churn has spiked from 4% to 12% in a single quarter.

The Chief Operating Officer has called an emergency meeting. But there appears to be more questions than answers as various teams come up with conflicting points of view:
- **Sales Leadership claims:** "The product is broken; customers are leaving because the new dashboard is too slow."
- **Product Leadership claims:** "Sales is acquiring low-quality customers who go out of business in 3 months. The product is fine."
- **Customer Support team claims:** “We are overwhelmed by tickets and nobody listens to the customer."

The COO has called you in. They do not want a dashboard. They do not want a list of 50 charts. They have one request:
- "We have limited resources. I need you to identify the single biggest leak in our customer
retention and give me a specific, actionable recommendation to fix it. Convince me I’m
making the right decision.”

#### The Data
You have been given access to three raw datasets. Warning: These datasets have real-world flaws (missing values, inconsistent definitions) that you must handle as you build your case.
1. **SalesForce Export:** Contract dates, company size, and industry (Warning: Industry field
is ~30% empty).
2. **Usage Logs:** Daily login activity (Warning: European server logs are corrupted for
September).
3. **Support Tickets:** Unstructured text of customer complaints (Sales vs. Product issues).
---

## Data Cleaning Methods
We used two data cleaning methods, one rigorous and one simple. The methods are briefly described below:

- **Simple Cleaning Method**:
  - Customer Data: direct standardization of dates, NULL columns replaced with "unknown"
  - Usage Logs: missing European September logs filled with August observations (forward filling)
  - Support Tickets: creation timestamps converted to datetime
    
- **Simple Cleaning Method**:
  - Customer Data: direct standardization of dates, flagged NULL columns as missing in a separate column before replacing with "unknown"
  - Usage Logs: flagged missing European September logs before forward filling with August observations, created flags for daily login data
  - Support Tickets: categorized tickets and flagged by issues
 
These methods dealth with the dirty in very different ways, notably with the rigorous method preserving more of the original data and making less assumptions than the simple method. However, both methods yielded data that resulted in the same hypothesis testing results (which are described in the following section), a cross-validation phenomenon that strengthens our hypothesis testing backed recommendation.

---

## Hypothesis Testing
We created four hypotheses to test with the cleaned data in developing a data-driven recommendation. Three hypotheses were based on the teams' initial claims of **dashboard speed**, **customer quality**, and **customer support collapse**, with a fourth hypothesis on **market saturation** to address possible market issues.

#### Simple Data Cleaning Hypothesis Testing
- Dashboard Speed:
  - We measured engagement by observing session_minutes vs churn and whether or not "slow dashboard" complaints were increasing over time by looking at ticket trends
  - Logic: If customers are leaving because the new dashboard is too slow, then **churned customers should have lower engagement** and **dashboard complaints should spike ticket trends**
  - Result: Churned customers actually engaged more with the dashboard and no spiked were observed, **thus the dashboard speed hypothesis was rejected**
  - Visuals: "Simple Data Cleaning/hyp_a_engagement_churn.png" and "Simple Data Cleaning/hyp_a_ticket_trend.png"

- Market Saturation:
  - We observed customer contract discount and value by cohort month as a measure of market saturation
  - Logic: If the market is saturated, then **we should see rising discounts in later cohorts as there is a need to "bribe" the market to gain customers**
  - Result: Contract discount rates remained relatively stable over time, **thus the market saturation hypothesis was rejected**
  - Visuals: "Simple Data Cleaning/hyp_c_discount_trend.png"

- Customer Support Collapse:
  - We observed support ticket resolution hours while grouping by cohort month
  - Logic: If customer support is being overwhelmed by tickets, then **resolution times should increase over time**
  - Result: Ticket resolution times oscillated over time, but averaged out to remain stable overall, **thus the customer support collapse hypothesis was rejected**
  - Visuals: "Simple Data Cleaning/hyp_d_resolution_trend.png"
    
- Customer Quality:
  - We grouped customers into cohorts by onboarding month and observed the initial onboarding score as the quality metric 
  - Logic: If sales started selling to worse-fit customers, then onboarding scores may decline in later cohorts
  - Result: We saw a steady decrease in onboarding score in later cohort months, **thus the customer quality hypothesis was accepted**
  - Visuals: "Simple Data Cleaning/hyp_b_onboarding_trend.png"
    
#### Rigorous Data Cleaning Hypothesis Testing
- Dashboard Speed:
  - We counted how many dashboard-related support tickets each customer filed and compared churned customers vs retained customers
  - Logic: If customers are leaving because the new dashboard is too slow, then **churned customers should have more dashboard complaints** and **customers who complained about the dashboard should churn at higher rates**
  - Result: In reality, churned customers complained LESS about the dashboard, **thus the dashboard speed hypothesis is rejected**
  - Visuals: "Rigorous Data Cleaning/hyp_a_dashboard.png"
 
- Market Saturation:
  - We observed churn by region to see if the market is saturated/churn is higher in certain regions
  - Logic: If the market is saturated in certain regions, then **certain regions should have much higher churn**
  - Result: The churn rates of the two main regions (North America, Europe) were almost identical, **thus the market saturation hypothesis was rejected**
  - Visuals: "Rigorous Data Cleaning/hyp_c_saturation.png"
 
- Customer Support Collapse:
  - We compared the number of tickets submitted, resolution hours, and resolution rates of churned and retained customers to determine whether or not the customer support has collapsed
  - Logic: If customer support is being overwhelmed by tickets, then **churned customers should have more tickets, longer resolution hours, and lower resolution rates**
  - Result: There was no significant difference in the average number of tickets, average resolution hours, and resolution rate between retained and churned customers, **thus the customer support collapse hypothesis was rejected**
  - Visuals: "Rigorous Data Cleaning/hyp_d_support.png"

- Customer Quality:
  - We created a composite customer quality score using whether or not the customer's industry is known, whether or not the customer has logged in, whether or not the customer got a contract discount, and the customer's company size, adding a point for each "good" quality met (4 = known industry, logged in, no discount, large company; 0 = unknown industry, never logged in, discount, small company)
  - Logic: If sales is acquiring low quality customers, then **low quality, low score customers should churn at higher rates than high quality, high score customers**
  - Result: Low quality, low score customers churned at higher rates, notably with customers with a score of 1 churning at 100% and a stark difference in churn rates between known industry customers and unknown industry customers, **thus the customer quality hypothesis was accepted**
  - Visuals: "Rigorous Data Cleaning/hyp_b_customer_quality.png"

As mentioned previously, both the simple and rigorous data cleaning methods yielded the same hypothesis testing results: the acquisition of low quality customers was the main contributer to customer churn

## Miscellaneous Notes
- When running the hypothesis test jupyter notebooks ("Simple Data Cleaning/hypothesis_testing_simple.ipynb" and "Rigorous Data Cleaning/hypothesis_testing_rigorous.ipynb"), run the "Load Data and Calculate Initial Churn Rate" section before each hypothesis test
- The "simple_cleaned_usage_logs.csv" is uploaded as a zip file in the data folder due to file size constraints
- The full deliverable, with the recommendation, is attached as a pdf file ("Customer Churn Case Study Deliverable.pdf")

## Team Members
- Robert Chang
- Joshua Chen
- Derrick Chun
- Aivan Durfee
- Ethan Lam
- Johnathan Wu
