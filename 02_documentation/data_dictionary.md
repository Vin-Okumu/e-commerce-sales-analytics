<h1 align="center">
Data Dictionary
</h1>

This document describes every field in the three analytical tables (`data/processed/`) that feed the Power BI model, and how each maps back to the raw source files (`data/raw/`).

- **Currency:** all monetary values are in Indian rupees (₹).
- **Ranges and counts** below are taken from the processed files as committed to this repository.
- **Naming:** raw files use `Title_Case` column names; `02_data_preparation.ipynb` converts them to `snake_case`. Apart from the renaming, raw and processed values are identical unless a field is marked *Derived* or *Removed*.

---

## Data model

```
Dim_Customers (1) ──── (*) Fact_Sales (*) ──── (1) Dim_Products
                              │
                              │ order_date
                              ▼
                           Dim_Date  (built in Power BI)
```

| Table | Rows | Columns | Grain | Primary key |
|---|---|---|---|---|
| `Fact_Sales` | 250,000 | 32 | One order (each order contains exactly one product) | `order_id` |
| `Dim_Customers` | 40,000 | 10 | One customer | `customer_id` |
| `Dim_Products` | 2,000 | 12 | One product | `product_id` |

Referential integrity: every `customer_id` and `product_id` in `Fact_Sales` exists in the matching dimension table (0 orphan records). 39,914 of the 40,000 customers placed at least one order. All 2,000 products were ordered at least once.

---

## Fact_Sales

Source: `data/raw/sales.csv` (21 columns) plus 11 derived columns.

### Identifiers

| Column | Type | Description | Values / range |
|---|---|---|---|
| `order_id` | text | Unique order identifier. Primary key. | `ORD0000000001` – `ORD0000250000` |
| `customer_id` | text | Customer who placed the order. Foreign key to `Dim_Customers`. | 39,914 distinct |
| `product_id` | text | Product ordered. Foreign key to `Dim_Products`. | 2,000 distinct |

### Dates and times

| Column | Type | Description | Values / range |
|---|---|---|---|
| `order_date` | date | Date the order was placed. Converted from text in preparation. | 2024-06-01 – 2026-06-30 |
| `order_time` | time | Time the order was placed (minute precision). Converted from text. | 00:00:00 – 23:59:00 |
| `delivery_date` | date | Date the order was (or is scheduled to be) delivered. Converted from text. | 2024-06-03 – 2026-07-07 |

### Quantities and money

| Column | Type | Description | Values / range |
|---|---|---|---|
| `quantity` | integer | Units of the product in the order. | 1 – 3 (mean 1.25) |
| `unit_price` | decimal | Price per unit charged. Always equals the product's `selling_price` in `Dim_Products`. | ₹26.13 – ₹136,973.33 |
| `order_value` | decimal | Gross order value. **Rule:** `quantity × unit_price` (holds for 100% of rows). | ₹26.13 – ₹410,919.99 |
| `shipping_cost` | decimal | Shipping charged to the customer. Charged only on orders with `order_value` under ₹500; ₹0 otherwise. | ₹0 – ₹90 |
| `coupon_code` | text | Coupon applied to the order. **Blank (79.93%) means no coupon was used.** | `SAVE10`, `FLAT50`, `DIWALI100` |
| `coupon_discount` | decimal | Rupee discount from the coupon. `SAVE10` = 10% of `order_value`; `FLAT50` = ₹50 flat; `DIWALI100` = ₹100 flat; 0 when no coupon. | ₹0 – ₹41,092 |
| `total_amount` | decimal | Amount the customer pays. **Rule:** `order_value + shipping_cost − coupon_discount` (holds for 100% of rows). Used for the *Net Transaction Value* measure. See the note on negative values below. | −₹21.56 – ₹410,919.99 |
| `payment_mode` | text | Payment method. | `UPI` (51.4%), `COD` (32.8%), `Debit Card` (11.5%), `Credit Card` (4.2%) |

### Order outcome and feedback

| Column | Type | Description | Values / range |
|---|---|---|---|
| `order_status` | text | Current status of the order. | `Delivered` (80.1%), `Cancelled` (5.0%), `Returned` (5.0%), `Shipped` (5.0%), `Processing` (5.0%) |
| `rating` | decimal | Customer's rating of the order. **Blank (51.99%) means no rating was given; it is not a zero.** Only `Delivered` orders have ratings. | 3, 4 or 5 |
| `review_text` | text | Short review comment. Blank whenever `rating` is blank. | 9 fixed phrases, e.g. `Good product`, `Value for money`, `Could be better` |

### Location and demographics (copied onto each order)

| Column | Type | Description | Values / range |
|---|---|---|---|
| `city` | text | Customer's city. Matches `Dim_Customers.city` for 100% of rows. | 39 cities |
| `state` | text | Customer's state. Matches `Dim_Customers.state` for 100% of rows. Uttar Pradesh is recorded as `UP`. | 10 states (see `Dim_Customers.state`) |
| `customer_age` | integer | Customer's age. Matches `Dim_Customers.age` for 100% of rows. | 18 – 75 |
| `customer_age_group` | text | Customer's age band. Matches `Dim_Customers.age_group` for 100% of rows. | `18-25`, `26-35`, `36-45`, `46-55`, `56-65`, `65+` |

### Derived columns (created in `02_data_preparation.ipynb`)

| Column | Type | Derivation | Values / range |
|---|---|---|---|
| `order_hour` | integer | Hour of `order_time`. | 0 – 23 |
| `delivery_days` | integer | `delivery_date − order_date`, in days. | 2 – 7 (mean 4.5, median 5) |
| `coupon_used` | text | `Yes` if `coupon_code` is present, otherwise `No`. | `Yes` (20.1%), `No` (79.9%) |
| `order_year` | integer | Year of `order_date`. | 2024 – 2026 |
| `order_month` | integer | Month number of `order_date`. | 1 – 12 |
| `order_month_name` | text | Month name of `order_date`. | `January` – `December` |
| `order_quarter` | integer | Calendar quarter of `order_date`. | 1 – 4 |
| `order_day` | integer | Day of month of `order_date`. | 1 – 31 |
| `order_day_name` | text | Weekday name of `order_date`. | `Monday` – `Sunday` |
| `registration_date` | date | Customer's registration date, joined from the customer table. | 2023-06-01 – 2024-05-31 |
| `customer_tenure_days` | integer | `order_date − registration_date`, in days. | 1 – 1,125 |

> **Note:** most of the calendar columns are also available from `Dim_Date` in Power BI, which handles time intelligence (month-over-month and year-over-year). The notebook keeps them in the CSV for convenience.

---

## Dim_Customers

Source: `data/raw/customers.csv` (15 columns). Five personal-data columns were removed in preparation.

| Column | Type | Description | Values / range |
|---|---|---|---|
| `customer_id` | text | Unique customer identifier. Primary key. | `CUST00000001` – `CUST00040000` |
| `gender` | text | Customer gender. | `male` (20,003), `female` (19,997) |
| `age` | integer | Customer age. | 18 – 75 (mean 35.3) |
| `age_group` | text | Age band. | `18-25`, `26-35`, `36-45`, `46-55`, `56-65`, `65+` |
| `city` | text | Customer city. | 39 cities |
| `state` | text | Customer state. | `Delhi`, `Gujarat`, `Haryana`, `Karnataka`, `Maharashtra`, `Punjab`, `Rajasthan`, `Tamil Nadu`, `UP`, `West Bengal` |
| `registration_date` | date | Date the customer registered. | 2023-06-01 – 2024-05-31 |
| `customer_tier` | text | Loyalty tier assigned by the business. | `Platinum` (27,349), `Gold` (6,692), `Silver` (5,959) |
| `total_orders` | integer | Lifetime order count, as supplied in the source file. **Does not reconcile with `Fact_Sales`** (see note below). | 0 – 17 |
| `total_spent` | decimal | Lifetime spend, as supplied in the source file. **Does not reconcile with `Fact_Sales`** (see note below). | ₹0 – ₹890,131.55 |

**Removed from the raw file (personal data):** `customer_name`, `email`, `phone`, `date_of_birth`, `pincode`. The raw file in `data/raw/` is left unchanged.

---

## Dim_Products

Source: `data/raw/products.csv` (12 columns). Only the column names were changed.

| Column | Type | Description | Values / range |
|---|---|---|---|
| `product_id` | text | Unique product identifier. Primary key. | `PROD000001` – `PROD002000` |
| `product_name` | text | Product name. Names are reused across product IDs (for example, `Noise Watch V1` may appear more than once), so totals by name can combine several product IDs. | 350 distinct names |
| `category` | text | Product category. | `Electronics` (290 products), and `Beauty`, `Books`, `Fashion`, `Grocery`, `Home`, `Sports` (285 each) |
| `brand` | text | Brand name. | 33 brands, e.g. `Samsung`, `Apple`, `Noise`, `boAt`, `Nike`, `IKEA` |
| `original_price` | decimal | List price before discount. | ₹52.25 – ₹149,658.64 |
| `discount_percent` | integer | Product-level discount, in percent. | 5 – 50 |
| `discount_amount` | decimal | Product-level discount in rupees. Equals `original_price × discount_percent / 100` after rounding. | ₹6.29 – ₹71,289.60 |
| `selling_price` | decimal | Price after the product discount. **Rule:** `original_price − discount_amount` (holds for 100% of rows). | ₹26.13 – ₹136,973.33 |
| `stock_quantity` | integer | Units in stock (a single snapshot with no date attached). | 10 – 1,000 |
| `weight_kg` | decimal | Product weight in kilograms. | 0.21 – 5.00 |
| `avg_rating` | decimal | Average customer rating. Values are tightly clustered around 4.4, so this field does little to separate one product from another. | 3.91 – 4.76 |
| `total_reviews` | integer | Number of reviews. | 15 – 139 |

---

## Known data issues

| Issue | Detail | How the project handles it |
|---|---|---|
| Negative `total_amount` | 5 orders (all with coupon `DIWALI100`, a ₹100 flat discount) have an `order_value` of ₹26–₹36, so the coupon exceeds order value plus shipping. The lowest total is −₹21.56. | Investigated in `02_data_preparation.ipynb` and **kept as-is**. The combined effect on totals is negligible. |
| Customer aggregates don't reconcile | `Dim_Customers.total_orders` and `total_spent` match the order count and spend in `Fact_Sales` for only 28.7% of customers. Across all customers, `total_orders` sums to 200,139 against 250,000 actual orders. | Every customer measure in the Power BI model (orders, value, repeat rate) is calculated from `Fact_Sales`. **Don't use these two columns for analysis.** |
| Blank `coupon_code`, `rating`, `review_text` | 79.93%, 51.99% and 51.99% blank respectively. | Blanks mean "no coupon used" or "not rated", so they are **not imputed**. `coupon_used` flags coupon use explicitly, and averages ignore blanks. |
| No cost data | There is no cost of goods, so margin and profit can't be calculated. | All value measures describe revenue (*Net Transaction Value*), not profit. |
| `Net Transaction Value` includes all statuses | `total_amount` is recorded for cancelled and returned orders too. | Dashboard totals include every order status unless the *Status* slicer is used. |
