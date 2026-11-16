# Processed Data

This folder holds the three cleaned, analysis-ready tables that the Power BI report (`powerbi/e-commerce-sales-analytics.pbix`) loads. They are produced from the raw files in `data/raw/` by [`Analysis/02_data_preparation.ipynb`](../../Analysis/02_data_preparation.ipynb).

| File | Rows | Columns | Grain | Built from |
|---|---|---|---|---|
| `Fact_Sales.csv` | 250,000 | 32 | One order (one product per order) | `raw/sales.csv` + registration date from `raw/customers.csv` |
| `Dim_Customers.csv` | 40,000 | 10 | One customer | `raw/customers.csv` |
| `Dim_Products.csv` | 2,000 | 12 | One product | `raw/products.csv` |

Full field definitions are in the [data dictionary](../../documentation/data_dictionary.md).

## How the tables relate

```
Dim_Customers.customer_id  1 ──── *  Fact_Sales.customer_id
Dim_Products.product_id    1 ──── *  Fact_Sales.product_id
```

Power BI adds a `Dim_Date` table, joined on `Fact_Sales.order_date`, for time intelligence.

## What was done to the raw data

Preparation follows the principle set out in [`data_preparation_decisions.md`](../../Analysis/data_preparation_decisions.md): *don't clean data that isn't dirty; only transform where there is an analytical reason.* Profiling (`01_data_profiling.ipynb`) found no duplicate rows, no duplicate keys and no orphan records, so the changes are limited to the following.

**All three tables**
- Column names standardised to lower-case `snake_case` (for example `Order_ID` → `order_id`).

**Fact_Sales**
- Converted `order_date` and `delivery_date` from text to dates, and `order_time` from text to a time value.
- Added derived columns:
  - `order_hour`
  - `delivery_days` (delivery date minus order date)
  - `coupon_used` (`Yes`/`No`)
  - `order_year`, `order_month`, `order_month_name`, `order_quarter`, `order_day`, `order_day_name`
- Joined `registration_date` from the customer table and derived `customer_tenure_days` (order date minus registration date).

**Dim_Customers**
- Converted `registration_date` to a date.
- **Removed personal data:** `customer_name`, `email`, `phone`, `date_of_birth` and `pincode`. The raw file is kept unchanged in `data/raw/`.

**Dim_Products**
- No changes besides column names.

**Intentionally not changed**
- Blank `coupon_code`, `rating` and `review_text` values were **not filled in**. A blank means "no coupon" or "not rated", not zero.
- Five orders with a negative `total_amount` (caused by the flat ₹100 `DIWALI100` coupon on orders worth under ₹40) were investigated and **left as-is**.

## Validation results

These checks were run against the processed files and all pass unless noted.

| Check | Result |
|---|---|
| Unique `order_id`, `customer_id`, `product_id` in their own tables | ✅ 100% unique |
| Every `Fact_Sales` customer and product exists in its dimension table | ✅ 0 orphans |
| `order_value = quantity × unit_price` | ✅ 100% of rows |
| `total_amount = order_value + shipping_cost − coupon_discount` | ✅ 100% of rows |
| `selling_price = original_price − discount_amount` | ✅ 100% of rows |
| `unit_price` matches the product's `selling_price` | ✅ 100% of rows |
| Customer age, age group, city and state in `Fact_Sales` match `Dim_Customers` | ✅ 100% of rows |
| `delivery_days` is positive | ✅ 2 – 7 days |
| `Dim_Customers.total_orders` / `total_spent` match `Fact_Sales` | ⚠️ Only 28.7% of customers match. Use `Fact_Sales` for all customer metrics. |

## Regenerating these files

```bash
pip install pandas numpy jupyter
cd Analysis
jupyter notebook 02_data_preparation.ipynb   # Run all cells
```

The notebook reads from `../data/raw/` and overwrites the three CSVs in this folder.
