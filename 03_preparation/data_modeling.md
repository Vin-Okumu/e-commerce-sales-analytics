<h1 align = "center">
Data Modeling Requirements before Analysis
</h1>

Before building the dashboard we want to establish the dimensions as follows:


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

This we expect to make our DAX measures and time intelligence much cleaner.


Our expectation is that the entire project can follow the following logic

                    BUSINESS OBJECTIVE
                           │
                           ▼
                How healthy is the business?
                           │
                           ▼
                 Where does growth come from?
                    /                \
                   /                  \
                  ▼                    ▼
             PRODUCTS              CUSTOMERS
                │                      │
                ▼                      ▼
         What drives demand?     Who creates value?
                │                      │
                ▼                      ▼
          Pricing/discounts       Retention
          Inventory              Segmentation
          Ratings                Tenure
                │                      │
                └──────────┬───────────┘
                           ▼
                     OPERATIONS
                           │
                           ▼
                 Delivery / Status
                           │
                           ▼
                    CUSTOMER EXPERIENCE
                           │
                           ▼
                 GROWTH OPPORTUNITIES
                           │
                           ▼
                    MANAGEMENT ACTION
