## Project Purpose
### Business Objective
Assess the commercial and operational performance of an e-commerce business to identify the products, customers, markets, and operational factors driving growth, while uncovering opportunities to improve customer retention, sales efficiency, and long-term business performance.

The analysis uses Power BI as the primary analytical and visualization platform, with the three datasets (products, customer and sales) integrated into a relational data model.

Core Analytical question: - How can the business improve sustainable growth by understanding where sales come from, which products and customers create the most value, where operational problems exist, and what factors influence repeat purchasing and customer experience?

### Analytical Scope

The analysis focuses on five interconnected areas:

Area	                            | Central Business Question
------------------------------------|---------------------------------------------------------------
Business Performance	            | How is the business performing and where is growth coming from?
Product and commercial performance	|Which products are driving demand, and are pricing and discounting being used effectively?
Customer value	                    |Who are the most valuable customers and what characterizes them?
Retention & Experience	            |Are customers returning, and what factors may influence their experience?
Growth Opportunities	            |Where should management focus its resources?

This gives this project a logical progression; from Performance to Action, through drivers, customers and experience respectively. 

### Business Question 1: How is the Business Performance?

#### Decision being supported: 
 Management needs to understand whether the business is experiencing healthy growth and which factors are contributing to overall performance.

Key questions:

-	How much revenue is the business generating?
-	How many orders and units are being sold?
-	How many customers are purchasing?
-	What is the average order value?
-	How is sales performance changing over time?
-	Which states/cities contribute most to sales?
-	Are there significant periods of unusually high or low demand?

Core KPIs

        Revenue: Given by SUM of Total amount
        Orders: Given by DISTINCTCOUNT of Order ID
        Customers: Given by DISTINCTCOUNT of Customer ID
        Units sold: Given by SUM of Quantity
        Average Order value: Given by Revenue / Orders
        Revenue Growth: Given by (Current Revenue – Previous Revenue)/Previous Revenue

#### Power BI Analysis

-	KPI cards
-	Monthly revenue Trend
-	Monthly order trend
-	AOV trend
-	Revenue by category
-	Revenue by state
-	Revenue contribution by product

#### Expected business insight

We are interested in identifying whether the business’ growth is driven by:
-	More customers
-	More orders per customer
-	Larger orders
-	Partial products/categories
-	Particular geographic markets

#### Potential decision
Management can determine where growth is coming from and whether growth appears broad-based or concentrated in particular products, customers or markets. 

### Business Question 2: Which products are driving commercial performance?

Decision being supported: - Management wants to determine which products and categories deserve commercial attention and which may require intervention. 

#### Key Questions:
-	Which products generate the most revenue?
-	Which products sell the most units?
-	Which categories dominate sales?
-	Which brands perform best?
-	Which products have high ratings but weak sales?
-	Which products have high sales but poor ratings?
-	Which products are heavily discounted?
-	Which products have low sales and high stock levels?

#### Key metrics:
-	Revenue, Units sold, Orders, Average selling price, Discount %, Discount amount, Rating, Reviews, Stock quantity

#### Useful derived metrics:

        Revenue contribution: Given by Product Revenue/Total revenue
        Units sold per order: Given by Total units/Total orders
        Discount-adjusted sales performance: From comparing Discount%, Units sold and Revenue instead of assuming that higher discounting automatically produces better performance.

#### Power BI Analysis
Here we prefer to use a revenue vs rating or revenue vs discount scatter plot 
Additionally, insights from this metric shall be achieved from generating a product performance matrix

                |High sales	                |Low sales
----------------|---------------------------|--------------------------
High ratings	|Star products	            |Growth opportunities
Low ratings	    |Customer-experience risk	|Products requiring review

#### Potential decision: 
Determine which products should be:
-	Promoted
-	Maintained
-	Reviewed
-	Potentially deprioritized
-	Investigated for customer-experience issues

### Business Question 3: Is discounting generating enough demand?
This deserves its own analysis because our data supports both product-level discounts and coupon discounts.

#### Decision being supported
Determine whether promotional activity appears to stimulate demand or simply reduce realized selling prices.

#### Key questions
- How much revenue is being given up through discounts?
- Which categories rely most heavily on discounts?
- Do heavily discounted products sell significantly more?
- Do coupon users have higher AOV?
- Are certain products repeatedly discounted?
- Are discounts associated with higher order volumes?

#### Metrics
        Product discount: Given by the difference between Original price and Selling price
        Coupon discount: Given by sum of coupon discount
        Potentially: effective discount rate will be given by adding product discount to coupon discount and dividing by gross product value.

#### Power BI Analysis
- Discount distribution
- Revenue by discount band
- Units sold by discount band
- AOV: coupon vs non-coupon orders
- Discount % by category
- Discount vs sales scatter plot

#### Expected insight
Identify whether there is evidence that higher discounting leads to higher demand or whether higher discounting leads to relatively little additional demand.

#### Decision
Should inform future promotional strategy and identify products/categories where discounting should be reviewed. 

Importantly, this should be described as discount efficiency and not profitability since the data doesn't include COGS/ product acquisition cost. 

### Business Question 4: Who are the most valuable customers?

#### Decision being supported
Identify the customers and customer segments that contribute most strongly to the business.

#### Key question
- Who generates the most revenue?
- Who purchases most frequently?
- Which customer tiers are most valuable?
- Which age groups generate the most revenue?
- Which geographic segments are most valuable?
- Do high-spending customers have distinctive characteristics?

#### Metrics
- Customer revenue, Orders per customer, AOV, Customer tenure, Customer tier, Revenue contribution

