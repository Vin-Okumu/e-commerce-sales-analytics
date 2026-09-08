<h1 align = "center">
Data Preparation Decisions
</h1>

The big question before embarking on data preparation decisions is: What does the profiling exercise tell us? 

We need to first interpret the results from a business/data-modeling perspective.

## A. Dataset structure

The data structure is quite clear

We have:

Dataset	    |Rows	    |Columns	|Grain
------------|-----------|-----------|--------------
Products	|2,000	    |12	        |One row per product
Sales	    |250,000	|20+	    |One row per order
Customers	|40,000	    |15	        |One row per customer

The Sales dataset is especially straightforward because the 250,000 rows translate to 250,000 unique Order_IDs

Therefore, each row represents one order, rather than an order line.

That is a very important finding. It means Order_ID can be used as the transaction/order-level identifier.

And because each order has one Product_ID, our current dataset appears to represent one product per order.

This simplifies the Power BI model considerably.

## Primary keys 

The primary keys are clean as well

We found:

- 2,000 unique Product IDs
- 40,000 unique Customer IDs
- 250,000 unique Order IDs
- zero duplicate rows
- zero duplicate Product IDs
- zero duplicate Customer IDs
- zero duplicate Order IDs

That's excellent.

We can therefore confidently design:

    Customers[Customer_ID]
                 │
                 │ 1
                 ▼
             Sales[Customer_ID]
                 │
                 │
                 │
    Products[Product_ID]
                 │
                 │ 1
                 ▼
             Sales[Product_ID]

This is exactly what we want for Power BI.

## Missingness 

Missingness is not really a "data quality problem." In particular, There are only three meaningful missing fields:

Field	    |Missing	|Interpretation
------------|-----------|--------------------------------------
Coupon_Code	|79.93%	    |Likely no coupon used
Rating	    |51.99%	    |Many orders apparently unrated
Review_Text	|51.99%	    |Many orders apparently have no review

This is an important distinction because:

- For Coupon_Code, it's not advisable to impute the values:

    - A blank coupon code probably means no coupon was used.

    - We'll eventually create something like:

        - Coupon_Used = Yes / No; 
    rather than filling missing codes with an arbitrary coupon.

- For Rating and Review_Text, again, it's not advisable to impute:

    - A missing entry for rating does not mean:

        - Rating = 0

        It means:

        - No rating was provided.

    Same for reviews.

    This distinction becomes particularly important when calculating average ratings in Power BI.

## Categorical Fields 

Our categorical fields are clean

The categorical distributions show no obvious spelling variants such as:

    - Male
    - male
    - MALE

or:

    - Electronics
    - electronics
    - Electronic

That's good.

We have:

    - 7 product categories
    - 33 brands
    - 4 payment methods
    - 5 order statuses
    - 3 coupon codes
    - 2 genders
    - 6 age groups
    - 3 customer tiers

There is therefore no obvious need for aggressive categorical cleaning.

However, we'll still explicitly standardize whitespace and capitalization during preparation because it's cheap and makes the pipeline robust.

## Numerical Data 

Our numerical data is generally valid; several important checks passed:

- No negative original prices
- No negative selling prices
- No invalid discount percentages
- No zero/negative quantities
- Ratings range from 3–5
- Product ratings range from 3.91–4.76

The Sales data also has:

- Quantity: 1–3; which looks plausible.

One thing worth noting, however, is that the product ratings are very tightly clustered around 4.4.

It's not necessarily an error, but it means:

    - Rating may have relatively little discriminatory power at the product level.

    - We should keep it, but we shouldn't assume it will necessarily produce a strong relationship with sales.

## Date Structure 

The date structure is excellent for the project

- Sales covers June 1, 2024 – June 30, 2026

    - That's approximately two years of transaction history.

- Customers registered between June 1, 2023 – May 31, 2024

This is particularly useful; it means we can analyze:

    - monthly trends
    - year-over-year performance
    - seasonality
    - customer tenure
    - cohorts
    - repeat purchasing

- The delivery dates run through July 7, 2026

    - This makes sense because deliveries occur after the final order date.

## Delivery Data 

Delivery data is exceptionally clean

- We found:

    - Invalid delivery dates = 0

- and:

    - Delivery days:
    - Minimum = 2
    - Median = 5
    - Maximum = 7
    - Mean ≈ 4.5 days

- This gives us a very useful operational KPI:

    - Average delivery time ≈ 4.5 days

    - We can derive this during preparation rather than storing it as a permanent raw field.

    - And because there are no negative delivery durations, there is no obvious date-quality issue here.

## Order Time 

Order time is currently stored as text. We correctly identified:

- Order_Time = str

- with values such as:

    - 08:20:00
    - 20:05:00
    - 14:59:00

- This needs conversion.

We'll eventually create:

    - Order_Time → time
    - Order_Hour → integer

    - and potentially:

        - Time_Period such as:

            - Overnight
            - Morning
            - Afternoon
            - Evening

    - But it's still important to keep the original time field as well.

## Referential Integrity 

Referential integrity is perfect. We found:

- Sales Product IDs missing from Products = 0

and:

- Sales Customer IDs missing from Customers = 0

This is excellent. It means we can confidently establish the relationships:

Customers 1 ─────── * Sales * ─────── 1 Products

No orphan transactions need to be dealt with.

## The Financial Reconciliation 

Our strongest finding, perhaps, is the financial reconciliation. It is arguably the most important result in the entire profiling exercise. We established:

$$ Quantity \times Unit\ Price = Order\ Value $$

- with a 100% match within a tiny floating-point tolerance.

- That means Order_Value is not some mysterious independently calculated field.

It is simply:

$$ OrderValue = Quantity \times UnitPrice $$

Excellent.

Then we tested:

$$ TotalAmount = OrderValue + ShippingCost - CouponDiscount $$

and obtained a 100% reconciliation.

That gives us a defensible definition of the transaction economics:

    Gross Order Value + Shipping Cost - Coupon Discount = Total Amount

This is extremely important for the Power BI model.

## Suspicious Results to be Investigated

There is one suspicious financial result we should investigate

Notice this:

    Total_Amount minimum = -21.56

That immediately deserves investigation.

If:

    Total Amount = Order Value + Shipping Cost - Coupon Discount

then a negative total amount means the coupon discount exceeded the order value plus shipping.

That's potentially possible in a synthetic dataset, but from a business perspective it is unusual.

We need to inspect:

sales[sales["Total_Amount"] < 0]

and determine:

    - Which coupon?
    - What order value?
    - What coupon discount?
    - What order status?
    - Is this a handful of records?
    - Is there a systematic reason?

We're not correcting these records just yet, though.

First we have to identify why they exist.

This is exactly why profiling comes before cleaning.

## Data Preparation Expectations
The next question is, perhaps, what should actually happen during data preparation?

Now we can move into the next notebook:

    analysis/
    ├── 01_data_profiling.ipynb
    └── 02_data_preparation.ipynb

Our philosophy remains:

Don't "clean" data that isn't dirty. Transform only where there is an analytical reason.

Our preparation will therefore involve:

#### Products
 - Standardize column names
 - Ensure correct data types
 - Validate pricing relationships
 - Standardize text
 - Potentially create product-level derived fields
#### Sales
 - Convert dates
 - Convert time
 - Create delivery duration
 - Create order-hour variables
 - Create coupon-used indicator
 - Investigate negative total amounts
 - Standardize categorical fields
#### Customers
  - Convert dates
 - Standardize categorical fields
 - Validate customer aggregates
 - Create customer tenure-related fields where appropriate
 - Remove unnecessary PII from the analytical dataset