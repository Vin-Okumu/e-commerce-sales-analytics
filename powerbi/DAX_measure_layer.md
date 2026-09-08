<h1 align = "center">
Building DAX Measure Layer
</h1>

At this point we have completed the data/modeling layer. The next step is to build the semantic/measure layer—the DAX measures that will power the entire report.

We are building this deliberately rather than jumping straight into visuals

Our expected sequence shall be 

    STAR SCHEMA
        ↓
    MEASURE TABLE
         ↓
    CORE COMMERCIAL METRICS
         ↓
    PRODUCT METRICS
         ↓
    CUSTOMER METRICS
         ↓
    OPERATIONS METRICS
         ↓
    TIME/GROWTH METRICS
         ↓
    REPORT PAGES

### Dedicated Measures Table
First things first, we create a dedicated table specifically for measures

We create a table with one column `_Measure` and one dummy variable and name it `_Measure`

We then hide the `_Measure` column from report view.

The purpose is to give us a clean place to store all DAX measures instead of scattering them across `Fact_Sales` `Dim_Products` and `Dim_Customers`

### Core Commercial Measures
We build the following first

##### Total Orders
    Total Orders =
    DISTINCTCOUNT(Fact_Sales[Order_ID])

##### Units Sold
    Units Sold =
    SUM(Fact_Sales[Quantity])

##### Gross Order Value
    Gross Order Value =
    SUM(Fact_Sales[Order_Value])

##### Shipping Revenue
    Shipping Revenue =
    SUM(Fact_Sales[Shipping_Cost])

##### Coupon Discounts
    Coupon Discounts =
    SUM(Fact_Sales[Coupon_Discount])

##### Net Transaction Value
    Net Transaction Value =
    SUM(Fact_Sales[Total_Amount])

##### Average Order Value
    Average Order Value =
    DIVIDE(
        [Net Transaction Value],
        [Total Orders]
    )

##### Average Units per Order
    Average Units per Order =
    DIVIDE(
        [Units Sold],
        [Total Orders]
    )

### Customer Metrics
In here we are going to add

    Total Customers =
    DISTINCTCOUNT(Fact_Sales[Customer_ID])

Then:

    Orders per Customer =
    DIVIDE(
        [Total Orders],
        [Total Customers]
    )

And:

    Value per Customer =
    DIVIDE(
        [Net Transaction Value],
        [Total Customers]
    )

These three measures are particularly useful because they allow us to distinguish growth through more customers from growth through customers buying more.

### Product Metrics

We won't need a ton of product-specific measures since the existing measures will automatically respond to product filters

For instance, if we put:

`Dim_Products[Category]` on a visual alongside: `[Net Transaction Value]`

Power BI automatically calculates transaction value by category.

Likewise:

`Dim_Products[Brand]` on a visual alongside `[Units Sold]`

will give units sold by brand. Therefore, we'll just have a couple of product metrics

##### Average Selling Price
    Average Selling Price =
    DIVIDE(
        [Gross Order Value],
        [Units Sold]
    )

##### Coupon Discount Rate
    Coupon Discount Rate =
    DIVIDE(
        [Coupon Discounts],
        [Gross Order Value]
    )

Notice that this is not the same thing as the product's `Discount_Percent`. Instead we're measuring coupon discounts actually given relative to gross order value.

That's a much more useful commercial metric.

### Operations Metrics
From our data profiling, we created a variable called `delivery_days` so in our DAX, we create a measure 

##### Average Delivery Days
    Average Delivery Days =
    AVERAGE(Fact_Sales[Delivery_Days])

and:

##### Median Delivery Days
    Median Delivery Days =
    MEDIAN(Fact_Sales[Delivery_Days])

We'll also eventually create:

    Completed Orders
    Cancelled Orders
    Returned Orders
    Cancellation Rate
    Return Rate

But only after the exact values contained in `Fact_Sales[order_status]`

For instance, the dataset may contain `Completed`, `Cancelled`, `Returned`, but we shouldn't hard-code those labels without verifying them.

### Time Intelligence

At this point we want to exploit our `Dim_Date`.

We first create

##### Previous Month Value
    Previous Month Value =
    CALCULATE(
        [Net Transaction Value],
        DATEADD(
            Dim_Date[Date],
            -1,
            MONTH
        )
    )

##### MoM Growth %
    MoM Growth % =
    DIVIDE(
        [Net Transaction Value] - [Previous Month Value],
        [Previous Month Value]
    )

Then we create teh annual evuivalents
    Previous Year Value =
    CALCULATE(
        [Net Transaction Value],
        DATEADD(
            Dim_Date[Date],
            -1,
            YEAR
        )
    )

and

    YoY Growth % =
    DIVIDE(
        [Net Transaction Value] - [Previous Year Value],
        [Previous Year Value]
    )

### DAX Validation
Before creating our report pages, let's test our measures

We'll create a temporary page and call it DAX Validation then add a table containing:

    Dim_Date[Year]
    Dim_Date[Month]

and:

    [Total Orders]
    [Units Sold]
    [Gross Order Value]
    [Coupon Discounts]
    [Net Transaction Value]
    [Average Order Value]

We'll then add another table containing:

    Dim_Products[Category]

and repeat the metrics. Then:

    Dim_Customers[Customer_Tier]

to another.

What we're testing here is whether the relationships actually propagate filters correctly.

#### We are not building the dashboard just yet

And this is intentional

We want to first build a semantic model and only organize the actual dashboard around our business questions once our base measures are validated. 

Just to recap, our intended report shall be organized as follows

##### Page 1 — Executive Overview
    How is the business performing?

##### Page 2 — Product Performance
    Which products/categories are driving commercial value?

##### Page 3 — Customer Analysis
    Who are our most valuable customers and segments?

##### Page 4 — Retention & Customer Behavior
    Are customers returning and increasing their value?

##### Page 5 — Operations
    Where are fulfillment and transaction problems occurring?

##### Page 6 — Growth Opportunities
    Where should management focus next?



