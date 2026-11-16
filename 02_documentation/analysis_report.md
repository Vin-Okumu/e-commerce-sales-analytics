<h1 align="center">
Analysis Report
</h1>

<p align="center"><b>E-Commerce Sales Analytics: commercial performance, January–June 2026</b></p>

---

## 1. Executive summary

The business generated **₹1.41 bn in net transaction value** from **59,340 orders** placed by **30,840 active customers** between January and June 2026. That is essentially unchanged from the same period in 2025 (₹1.41 bn, −0.07%), so the business is stable but not growing.

Revenue is highly concentrated, which is both its main strength and its main risk:

- **Electronics** accounts for **73%** of revenue.
- **Platinum-tier customers** are 71% of active customers but produce **90%** of revenue.
- **Repeat customers** are 57% of active customers but produce **77%** of revenue.

Operations are steady. 80.3% of orders were delivered, average delivery time is 4.5 days in every month and state, and cancellations and returns together make up about 10% of orders.

The analysis points to **retention and tier progression** as the strongest levers for renewed growth, with **category diversification** as a hedge against reliance on Electronics.

## 2. Scope and approach

**Business question.** *How can the business improve sustainable growth by understanding where sales come from, which products and customers create the most value, where operational problems exist, and what influences repeat purchasing?* The full question set and KPI definitions are in the [analysis charter](analysis_charter.md).

**Data.** The analysis uses three related tables: 250,000 orders, 40,000 customers and 2,000 products, covering June 2024 to June 2026. All values are in Indian rupees. See the [data dictionary](data_dictionary.md) for details.

**Reporting period.** January–June 2026 (the most recent six months), compared with January–June 2025 where relevant.

**Method.**
1. **Profiling** in Python (`01_data_profiling.ipynb`): checked keys, missing values, value ranges and financial reconciliation.
2. **Preparation** in Python (`02_data_preparation.ipynb`): converted data types, derived new fields and removed personal data.
3. **Star-schema model** in Power BI: `Fact_Sales` linked to `Dim_Customers`, `Dim_Products` and `Dim_Date`.
4. **DAX measure layer** (`powerbi/DAX_measure_layer.md`): measures were validated in a test table before any visuals were built.
5. **Six-page report**, with each page answering one business question.

**Key measure definitions**

| Measure | Definition |
|---|---|
| Net Transaction Value (NTV) | Sum of `total_amount` (order value + shipping − coupon discount) |
| Average Order Value (AOV) | NTV ÷ distinct orders |
| Value per Customer | NTV ÷ distinct customers in `Fact_Sales` |
| Repeat Customer Rate | Customers with more than one order in the period ÷ active customers |
| Completed Order Rate | Delivered orders ÷ total orders |
| Coupon Discount Rate | Coupon discounts ÷ gross order value |

---

## 3. Findings

### 3.1 Business performance: is the business growing?

| KPI (Jan–Jun 2026) | Value |
|---|---|
| Net transaction value | ₹1.41 bn |
| Orders | 59,340 |
| Units sold | 74,119 |
| Active customers | 30,840 |
| Average order value | ₹23,812 |
| Average units per order | 1.25 |

- **Revenue is flat year over year.** NTV for January–June 2026 was within 0.1% of the same period in 2025. Monthly NTV stayed between ₹215 M and ₹248 M throughout the two years of data, with no sustained upward or downward trend.
- **February dips because it is shorter.** February 2026 had the fewest orders (9,244), but orders per day (about 330) were in line with January and March. There is no clear seasonal peak in the data.
- **Growth is not coming from any one source.** The number of customers, orders per customer (1.92) and AOV are all stable, so none of them is currently driving growth.

### 3.2 Product performance: which products drive commercial value?

| Category | NTV | Share of NTV | Units sold | Avg order value |
|---|---|---|---|---|
| Electronics | ₹1,026.6 M | 72.7% | 19,755 | ₹64,966 |
| Home | ₹171.8 M | 12.2% | 10,133 | ₹21,178 |
| Sports | ₹111.9 M | 7.9% | 8,598 | ₹16,177 |
| Fashion | ₹79.4 M | 5.6% | 14,911 | ₹6,666 |
| Grocery | ₹9.8 M | 0.7% | 9,796 | ₹1,243 |
| Beauty | ₹9.2 M | 0.6% | 4,961 | ₹2,345 |
| Books | ₹4.4 M | 0.3% | 5,965 | ₹914 |

- **Electronics dominates.** It brings in more revenue than all other categories combined, about six times the next category (Home).
- **Every one of the top 10 products by NTV is an electronics item**, led by Noise Watch V1 (₹46.9 M) and Samsung Mobile V4 (₹43.2 M).
- **Volume and value tell different stories.** Fashion is second by units sold (14,911) but fourth by revenue because of its lower price point. Low-priced lines such as books and grocery rank lowest by revenue, but that reflects their price, not weak demand. Units sold is a fairer measure for them.
- **Coupons have little financial impact.** Coupon discounts totalled ₹14.8 M, or 1.04% of gross order value, and 20% of orders used a coupon.

### 3.3 Customer analysis: who are the most valuable customers?

| Tier | Active customers | NTV | Share of NTV | Value per customer | Orders per customer | Repeat rate |
|---|---|---|---|---|---|---|
| Platinum | 22,047 (71%) | ₹1,272.0 M | 90.0% | ₹57,694 | 2.02 | 60.2% |
| Gold | 4,976 (16%) | ₹95.8 M | 6.8% | ₹19,258 | 1.78 | 51.9% |
| Silver | 3,817 (12%) | ₹45.2 M | 3.2% | ₹11,846 | 1.57 | 41.5% |
| **Total** | **30,840** | **₹1,413.0 M** | | **₹45,818** | **1.92** | **56.5%** |

- **The tier system reflects real behaviour.** Each step up in tier comes with higher spend, more orders and a higher repeat rate. A Platinum customer is worth about **4.9 times** as much as a Silver customer.
- **The 26–35 age group is the most valuable,** producing 43% of NTV. The 18–25 (25%) and 36–45 (24%) groups follow, and customers over 45 contribute only 7%.

### 3.4 Retention and customer behaviour: are customers coming back?

| Customer type | Customers | Orders | NTV share | Value per customer |
|---|---|---|---|---|
| Repeat (2+ orders) | 17,436 (57%) | 45,936 | 77.3% | ₹62,668 |
| One-time | 13,404 (43%) | 13,404 | 22.7% | ₹23,899 |

- **Repeat customers are the core of the business.** They are worth about **2.6 times** as much as one-time customers and produce more than three-quarters of revenue.
- **Repeat behaviour is strongest in the Platinum tier.** Within Platinum, repeat customers produce 78.5% of the tier's value, compared with 68.5% for Gold and 62.0% for Silver.
- **Within each single month, about 11–12% of customers placed more than one order**, and orders per customer stayed between 1.12 and 1.14. The small monthly ups and downs don't form a meaningful trend.

### 3.5 Operations: how efficiently are orders fulfilled?

| Order status | Orders | Share |
|---|---|---|
| Delivered | 47,633 | 80.3% |
| Returned | 2,969 | 5.0% |
| Cancelled | 2,948 | 5.0% |
| Shipped | 2,898 | 4.9% |
| Processing | 2,892 | 4.9% |

- **Fulfilment is consistent.** The completed order rate ranges only from 79.4% (Delhi) to 81.0% (Gujarat) across the 10 states.
- **Delivery time is stable at 4.5 days** in every state. The monthly average moved only between 4.49 and 4.53 days, so the small rise in June doesn't point to a real problem.
- **Cancelled and returned orders account for ₹138.5 M (9.8%) of NTV.** This is the largest operational loss in the data.
- **UPI is the leading payment method** (51% of orders), followed by cash on delivery (33%), debit card (11%) and credit card (4%).
- **Uttar Pradesh (7,772 orders), Haryana (7,628) and Rajasthan (7,497)** are the largest markets by order count.

### 3.6 Growth opportunities: where should management focus?

When the findings are combined, three opportunities stand out:

1. **Convert one-time buyers into repeat buyers.** A repeat customer is worth about ₹38,800 more than a one-time customer, and 13,404 customers bought only once in the period.
2. **Move customers up the tiers.** Gold and Silver together account for 8,793 active customers, but each one is worth only a third (Gold) or a fifth (Silver) of a Platinum customer.
3. **Reduce dependence on Electronics.** Home and Sports are the next largest categories by value and are the natural candidates for cross-selling.

---

## 4. Recommendations

| # | Recommendation | Supporting evidence |
|---|---|---|
| 1 | **Make retention the main growth lever.** Introduce a second-purchase offer or follow-up journey for first-time buyers. | Revenue is flat; repeat customers produce 77% of NTV and are worth 2.6× as much as one-time customers. |
| 2 | **Protect Platinum customers and move Silver and Gold customers up.** Give Platinum priority service, and give lower tiers clear, achievable rewards for reaching the next tier. | Platinum produces 90% of NTV; the Silver repeat rate is only 41.5% against 60.2% for Platinum. |
| 3 | **Lower the concentration risk in Electronics.** Cross-sell Home and Sports products to Electronics buyers. | 73% of NTV and all top-10 products come from one category. |
| 4 | **Investigate cancellations and returns.** Break them down by product, category and payment mode to find avoidable causes. | About 10% of orders and ₹138.5 M of NTV end in a cancellation or return. |
| 5 | **Focus acquisition marketing on customers aged 26–35.** | This group produces 43% of NTV. |
| 6 | **Maintain current delivery performance**, and track delivery time monthly so any deterioration is caught early. | Delivery is stable at 4.5 days across all states. |

## 5. Limitations

- **Revenue, not profit.** The data has no cost-of-goods information, so all findings describe commercial value (revenue). Product profitability can't be assessed.
- **NTV includes every order status.** Cancelled and returned orders are counted in NTV unless the *Status* slicer is applied, so realised revenue is lower than the headline figure.
- **Customer-level totals in the source data don't reconcile.** `total_orders` and `total_spent` in the customer file match the transaction data for only 28.7% of customers. All customer metrics in this report are calculated from the transaction table instead.
- **Ratings have limited use.** Only delivered orders carry ratings (52% of all orders are unrated), and product ratings cluster tightly around 4.4. That makes ratings a weak signal for separating products or measuring customer experience.
- **Charter questions not yet covered.** The dashboard answers the core performance, product, customer, retention and operations questions. The remaining charter questions are candidates for a next phase:
  - discount efficiency (Q3)
  - customer value by tenure and cohort (Q7)
  - the link between delivery time and ratings (Q8)
  - time-of-day and day-of-week demand (Q11)
- **Likely synthetic data.** The distributions are very even: order statuses split 80/5/5/5/5, delivery days are spread almost uniformly from 2 to 7, and every non-Electronics category has exactly 285 products. These patterns suggest generated data, so seasonal and geographic differences should be treated as indicative only.
