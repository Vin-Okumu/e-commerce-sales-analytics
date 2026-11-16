<h1 align = "center">
Analysis Charter
</h1>

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
Product and commercial performance	| Which products are driving demand, and are pricing and discounting being used effectively?
Customer value	                    | Who are the most valuable customers and what characterizes them?
Retention & Experience	            | Are customers returning, and what factors may influence their experience?
Growth Opportunities	            | Where should management focus its resources?

This gives this project a logical progression; from Performance to Action, through drivers, customers and experience respectively. 

### Business Question 1 — How is the Business Performance?

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

#### Core KPIs

Revenue

$$ Revenue = \sum TotalAmount $$

Orders

$$ Orders = DISTINCTCOUNT(OrderID) $$

Customers

$$ Customers = DISTINCTCOUNT(CustomerID) $$

Units sold

$$ Units = \sum Quantity $$

Average Order Value

$$ AOV = \frac{Revenue}{Orders} $$

Revenue growth

$$ Growth\% = \frac{Current\ Revenue - Previous\ Revenue} {Previous\ Revenue} $$

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

### Business Question 2 — Which products are driving commercial performance?

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

Revenue contribution
$$ Revenue\ Contribution = \frac{Product\ Revenue}{Total\ Revenue} $$
Units per order
$$ Units/Order = \frac{Total\ Units}{Total\ Orders} $$

#### Power BI Analysis
Here we prefer to use a revenue vs rating or revenue vs discount scatter plot 
Additionally, insights from this metric shall be achieved from generating a product performance matrix

---          |High sales	            |Low sales
-------------|--------------------------|--------------------------
High ratings |Star products	            |Growth opportunities
Low ratings	 |Customer-experience risk	|Products requiring review

#### Potential decision: 
Determine which products should be:
-	Promoted
-	Maintained
-	Reviewed
-	Potentially deprioritized
-	Investigated for customer-experience issues

### Business Question 3 — Is discounting generating enough demand?
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
Product discount:

$$ Discount\ Amount = Original\ Price - Selling\ Price $$

Coupon discount:

$$ Coupon\ Discount = \sum CouponDiscount $$

Potentially:

$$ Effective\ Discount\ Rate = \frac{Product\ Discount + Coupon\ Discount} {Gross\ Product\ Value} $$

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

### Business Question 4 — Who are the most valuable customers?

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

#### Customer value framework
Rather than simply ranking customers, we'll classify them based on spend x frequency

For example

Segment	                |Spend	|Frequency |	Interpretation
------------------------|-------|----------|---------------------------
Champions	            |High	|High	   |Protect & retain
High-value occasional	|High	|Low	   |Encourage repeat purchases
Frequent low-value	    |Low	|High	   |Increase basket size
Low-value	            |Low	|Low	   |Lower priority

### Business Question 5 — Does the customer tier system reflect actual customer value?

This is a particularly interesting question because the Customer dataset contains customer tier.

#### Decision being supported

Assess whether the existing customer segmentation/tiering appears aligned with actual purchasing behavior.

#### Questions
- Do higher-tier customers spend more?
- Do they place more orders?
- Do they have higher AOV?
- Are some lower-tier customers behaving like high-tier customers?
- Which tiers are growing fastest?

#### Analysis

Compare:

Tier → Customers → Orders → Spend → AOV

#### Potential visual:

Customer Tier Performance Matrix expected to produce an interesting business insight:

"The Gold tier represents only X% of customers but contributes Y% of total customer spend."

### Business Question 6 — Are customers becoming repeat buyers?

This is one of the central analyses.

#### Decision being supported

Determine whether the business is building a sustainable customer base or relying heavily on one-time transactions.

#### Questions
- How many customers purchase only once?
- How many make repeat purchases?
- What proportion of revenue comes from repeat customers?
- How many orders does the average repeat customer make?
- Does customer value increase with repeat purchasing?

#### Metrics

Repeat customer rate

$$ Repeat\ Customer\ Rate = \frac{Customers\ with\ >1\ Order} {Total\ Customers} $$

Orders per customer

$$ Orders/Customer = \frac{Total\ Orders}{Total\ Customers} $$

#### Power BI analysis
- New vs repeat customer KPI
- Repeat customer trend
- Orders per customer
- Revenue from repeat customers
- Customer purchase-frequency distribution

#### Decision

Identify whether customer retention should be a strategic priority.

### Business Question 7 — How does customer value evolve over time?

Since we have customer registration dates, this becomes particularly useful.

#### Decision being supported

We want to understand whether longer customer relationships translate into greater commercial value.

#### Questions

- How long have customers been with the business?
- Does spending increase with customer tenure?
- Do long-tenure customers order more frequently?
- Which acquisition cohorts generate the most value?

#### Derived metric

Customer tenure

$$ Tenure = OrderDate - RegistrationDate $$

Potential analysis:

Tenure → Orders → Spend → AOV

This shall help answer the question on whether retaining customers actually create greater customer value?

### Business Question 8 — What is the relationship between customer experience and commercial performance?

With the available data this becomes a very interesting analysis given the following available indicators:

- Customer rating
- Review text
- Product average rating
- Delivery date
- Order date
- Order status

#### Questions
- Which products receive the highest/lowest ratings?
- Are highly rated products selling more?
- Do customers experiencing longer delivery times give lower ratings?
- Are cancelled/failed orders associated with poor customer experience?
- Do repeat customers rate their purchases differently from one-time customers?

#### Derived metric

Delivery time

$$ Delivery\ Time = DeliveryDate - OrderDate $$
Potential analysis

Delivery time → Rating

This could uncover an operational/customer-experience relationship.

#### Decision

Here we want to identify potential fulfillment or product-quality problems that may threaten customer retention.

### Business Question 9 — Where are the strongest and weakest markets?
#### Decision being supported

We want to identify geographic markets that deserve expansion, improvement, or closer monitoring.

#### Questions

- Which states generate the most revenue?
- Which have the most customers?
- Where is AOV highest?
- Where are ratings strongest/weakest?
- Which regions have high customer concentration?
- Are some regions experiencing longer delivery times?

#### Metrics
- Revenue
- Orders
- Customers
- AOV
- Repeat customer rate
- Average rating
- Delivery time

#### Power BI

We'll use:

- Filled map, if appropriate
- State ranking
- Revenue by state
- Customer density
- AOV by geography

#### Decision

We'll prioritize markets based on commercial attractiveness rather than simply sales volume.

### Business Question 10 — Where are operational problems affecting sales?

With the Sales dataset we have:

- Order status
- Delivery date
- Order date
- Payment mode
- City/state

#### Questions

- What percentage of orders are successfully fulfilled?
- Where are cancellations concentrated?
- Which markets experience longer delivery times?
- Are particular payment methods associated with unsuccessful orders?
- What proportion of sales value is associated with unsuccessful orders?

#### Metrics

Order completion rate

$$ Completion\ Rate = \frac{Completed\ Orders}{Total\ Orders} $$

Cancellation rate

$$ Cancellation\ Rate = \frac{Cancelled\ Orders}{Total\ Orders} $$

#### Decision

We want to identify operational bottlenecks that could be reducing realized revenue or customer satisfaction.

### Business Question 11 — When are customers purchasing?

This is a supporting analysis rather than one of the central business questions.

From order date and order time, we want to derive:

- Month
- Quarter
- Day of week
- Hour
- Weekend/weekday

#### Questions
- When is demand highest?
- Are there seasonal patterns?
- What days generate the most orders?
- What hours generate the most orders?
- Does purchasing behavior differ by customer segment?

#### Decision

Inform:

- campaign timing
- promotions
- staffing
- fulfillment planning

### Business Question 12 — Where should management focus?

This becomes the synthesis question.

Instead of another descriptive analysis:

We are more interested in what are the most actionable opportunities identified by the data?

We want, then to build an opportunity framework around:

Product opportunities

High demand + high rating + low stock

→ Replenishment priority

Product risks

Low demand + high stock

→ Promotion/inventory review

Customer opportunities

High spend + low purchase frequency

→ Retention/cross-selling opportunity

Customer risks

Large customer base + low repeat rate

→ Retention intervention

Operational risks

Long delivery time + low rating

→ Fulfillment investigation

Promotional risks

High discount + weak incremental demand

→ Discount strategy review

This is where the analysis ultimately becomes decision support.

