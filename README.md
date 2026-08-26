<h1 align = "center">
E-Commerce Commercial Analytics <br>

<p align = "center">
<img src = "images/Cover.png" width = "900" height = "400">
</p>

## Project Overview

For any e-commerce company, one of the overarching questions is usually: How can we improve profitability and sustainable growth by understanding what drives sales, which products and customers create the most value, and where are we losing opportunities?

Such a question brings about several analytical dimensions that can be segmented into six business areas:

1.	Revenue & profitability
2.	Product performance
3.	Customer behavior & value
4.	Customer retention
5.	Operational/market opportunities
6.	Strategic growth

For this e-commerce data analytics project, we are most interested in answering the following 12 interconnected questions:

## Business Questions

### Business performance
-	Is the business growing, and is that growth profitable?
-	Which periods, categories and markets are driving financial performance?
### Product
-	Which products generate the most revenue?
-	Which products generate the most profit?
-	Are our best-selling products also our most profitable products?
-	Which products/categories represent growth opportunities or value-destruction risks?
### Customers
-	Who are our highest-value customers?
-	Which customer segments contribute the most revenue and profit?
-	What behaviors distinguish high-value customers from the rest?
### Retention
-	Are customers returning after their initial purchase?
-	Are we building a sustainable customer base or continually replacing lost customers?
### Commercial strategy
-	Where should management focus resources to improve profitability and long-term growth?

## Repository Structure

    | e-commerce-commercial-analytics/
    │
    ├── data/
    │   ├── raw/
    │   │   ├── sales.csv
    │   │   ├── products.csv
    │   │   └── customers.csv
    │   │
    │   └── processed/
    │       └── README.md
    │
    ├── powerbi/
    │   └── ecommerce_analysis.pbix
    │
    ├── documentation/
    │   ├── analysis_charter.md
    │   └── data_dictionary.md
    │
    ├── screenshots/
    │   ├── executive_overview.png
    │   ├── product_performance.png
    │   ├── customer_analysis.png
    │   ├── retention_analysis.png
    │   └── growth_opportunities.png
    │
    ├── README.md
    └── .gitignore

## Dataset

- Sales data: contains transactional records for customer purchases. Each row represents a single order placed on the platform and links customers with products through Customer_ID and Product_ID. This table is ideal for revenue analysis, sales trends, payment analysis, delivery performance, and dashboard development.

- Product data: stores information about products available on the e-commerce platform. It includes product details, pricing, discounts, inventory, ratings, and brand information. Each product is uniquely identified by Product_ID and is connected to sales.csv for transaction analysis.

- Customer data: contains demographic information, contact details, registration history, and purchasing statistics for customers. Each record represents one unique customer and is linked to the sales.csv table through the Customer_ID column. This table is useful for customer segmentation, demographic analysis, customer lifetime value (CLV), and regional sales analysis.

## Tools

- Power BI
- Power Query
- DAX
- Git/GitHub

## Dashboard

Screenshots of the major Power BI pages.

## Key Findings

Brief summary of the most important findings.

## Recommendations

Summary of recommended business actions.

## Project Structure

Explanation of the repository folders.