<h1 align = "center">
Data Modeling Requirements before Analysis
</h1>

Before building the dashboard we want to establish 

                    Dim_Product
                         │
                         │ Product ID
                         ▼
 Dim_Customer ─────── Fact_Sales
    │                    │
    │                    │ Order Date
    │                    ▼
    │                Dim_Date
    │
    └── Customer ID

We also want to create a proper **Date dimension** rather than relying entirely on the raw order date

Potential dimensions:

- `Dim_Date`
- `Dim_Product`
- `Dim_Customer`

with:

- `Fact_Sales`

as the central transaction table.

This is expected to make our DAX measures and time intelligence much cleaner.
