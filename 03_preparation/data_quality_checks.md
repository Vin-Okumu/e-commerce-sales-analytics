<h1 align = "center">
Data Quality Checks
</h1>

Data quality checks are part of the project. We want to explicitly include a **data validation phase** before analysis

Particularly:

For customer consistency, we want to compare: `Customers.total_orders` against: `DISTINCTCOUNT(Sales.order_id)` for each customer.

Also we want to compare: `Customers.total_spent` against the corresponding transaction totals.

For demographic consistency, we want to compare: `Customer age`, `Sales customer age` and `Customer age group`, `Sales customer age group`, 

Regarding **Product pricing consistency** We are interested in checking whether:

$$ OriginalPrice - DiscountAmount \approx SellingPrice $$

For **sales arithmetic**, we want to check whether:

$$ Quantity \times UnitPrice $$

is consistent with: `Order Value`

and investigate how:

`Coupon Discount` and `Shipping Cost` feed into `Total Amount`

This is extremely important because we don't want to build DAX measures based on assumptions about what those fields mean.
