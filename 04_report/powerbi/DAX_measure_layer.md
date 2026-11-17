<h1 align="center">DAX Measure Layer</h1>

This document explains how the report's semantic model calculates its numbers: how the measures are organised, the core definitions, and the patterns behind the dynamic titles, like-for-like comparisons and KPI colours.

The model holds about 160 measures, so this page shows **one worked example per pattern** rather than every formula. The complete, current definitions live in the semantic model itself, which is saved as plain text (TMDL):

- All measures: [`_Measure.tmdl`](../e-commerce-sales-analytics.SemanticModel/definition/tables/_Measure.tmdl)
- Tables, columns and the date table: [`tables/`](../e-commerce-sales-analytics.SemanticModel/definition/tables/)
- Relationships: [`relationships.tmdl`](../e-commerce-sales-analytics.SemanticModel/definition/relationships.tmdl)

## Contents
1. [Model and build order](#1-model-and-build-order)
2. [Core measures](#2-core-measures)
3. [Like-for-like time comparison](#3-like-for-like-time-comparison)
4. [KPI labels and colours](#4-kpi-labels-and-colours)
5. [Insight titles](#5-insight-titles)
6. [Highlighting the bar that matters](#6-highlighting-the-bar-that-matters)
7. [Growth what-if scenarios](#7-growth-what-if-scenarios)
8. [Validation](#8-validation)

---

## 1. Model and build order

The measures sit on a star schema: `Fact_Sales` (one row per order) joined to `Dim_Customers`, `Dim_Products` and `Dim_Date`. `Dim_Date` is marked as the date table, and its `Month` column sorts by `Month Number`. Schema design is covered in [`data_modeling.md`](../../03_preparation/data_modeling.md).

All measures live in a dedicated, otherwise empty `_Measure` table rather than being scattered across the data tables. The measures were built and checked in layers before any visuals were designed:

```
Star schema → Core commercial → Customer → Product → Operations → Time intelligence → Presentation layer (labels, titles, colours) → Report pages
```

Measures are grouped into display folders:

| Display folder | What it holds |
|---|---|
| *(root)* | Core commercial, customer, product, operations and time-intelligence measures (section 2) |
| `0 Comparison` | `Last Sale Date`, `Comparable Start`, `PY Period Label` and prior-year values, which drive the like-for-like comparison (section 3) |
| `1 Core additions` | Lost orders (cancelled + returned), share-of-total measures (`% of Revenue`, `% of Customers`, …), Electronics and Platinum shares, and display measures scaled to ₹M / ₹K |
| `2 KPI labels` | One text measure per KPI card, for example `NTV YoY Label` → "▼ 0.1% vs Jan–Jun 2025" (section 4) |
| `3 Formatting` | Colour measures used by conditional formatting: KPI arrow colours, the highlighted bar, table cell colours (sections 4 and 6) |
| `4 Titles` | Page headlines and chart titles that state the insight and update with the slicers, plus the footer and "last refreshed" text (section 5) |
| `5 Growth what-if` | Pools, uplift measures and lever measures behind the Growth page's three what-if parameters (section 7) |

Supporting tables: `Growth Levers` (lever list for the Growth chart), `Refresh_Log` (refresh timestamp for the footer) and three what-if parameter tables (`Second Order Conversion`, `Platinum Churn Avoided`, `Home Attach Rate`).

## 2. Core measures

Every other measure builds on these. Each is defined once and reused, so a change to a definition flows through the whole report.

**Commercial**

```dax
Total Orders            = DISTINCTCOUNT ( Fact_Sales[order_id] )
Units Sold              = SUM ( Fact_Sales[quantity] )
Gross Order Value       = SUM ( Fact_Sales[order_value] )
Coupon Discounts        = SUM ( Fact_Sales[coupon_discount] )
Net Transaction Value   = SUM ( Fact_Sales[total_amount] )
Average Order Value     = DIVIDE ( [Net Transaction Value], [Total Orders] )
Average Units per Order = DIVIDE ( [Units Sold], [Total Orders] )
```

**Customer**

```dax
Total Customers     = DISTINCTCOUNT ( Fact_Sales[customer_id] )
Orders per Customer = DIVIDE ( [Total Orders], [Total Customers] )
Value per Customer  = DIVIDE ( [Net Transaction Value], [Total Customers] )

Repeat Customers =
COUNTROWS (
    FILTER ( VALUES ( Fact_Sales[customer_id] ), CALCULATE ( [Total Orders] ) > 1 )
)
Repeat Customer Rate = DIVIDE ( [Repeat Customers], [Total Customers] )
```

Customer counts come from `Fact_Sales`, not from `Dim_Customers[total_orders]`. The pre-computed totals in the customer file match the order data for only 28.7% of customers (see the [processed data README](../../01_data/processed/README.md)).

**Product.** Most product analysis needs no special measures, because the core measures respond to `Dim_Products` filters automatically. Two are added:

```dax
Average Selling Price = DIVIDE ( [Gross Order Value], [Units Sold] )
Coupon Discount Rate  = DIVIDE ( [Coupon Discounts], [Gross Order Value] )
```

`Coupon Discount Rate` measures discounts actually given at checkout, which is not the same as a product's list `discount_percent`.

**Operations.** Status values (`Delivered`, `Shipped`, `Processing`, `Cancelled`, `Returned`) were confirmed during profiling before being hard-coded.

```dax
Completed Orders      = CALCULATE ( [Total Orders], Fact_Sales[order_status] = "Delivered" )
Completed Order Rate  = DIVIDE ( [Completed Orders], [Total Orders] )
Lost Orders           = CALCULATE ( [Total Orders], Fact_Sales[order_status] IN { "Cancelled", "Returned" } )
Lost Order Rate       = DIVIDE ( [Lost Orders], [Total Orders] )
Average Delivery Days = AVERAGE ( Fact_Sales[delivery_days] )
```

## 3. Like-for-like time comparison

The data runs from June 2024 to June 2026. A plain `SAMEPERIODLASTYEAR` gives misleading results at both ends of that range:

- **At the end:** selecting 2026 (Jan–Jun) would compare against the whole of 2025 if the prior-year window weren't capped.
- **At the start:** selecting 2025 would compare twelve months of 2025 against only seven months of 2024 (Jun–Dec).

Two anchor measures fix this:

```dax
Last Sale Date    = CALCULATE ( MAX ( Fact_Sales[order_date] ), REMOVEFILTERS () )
Comparable Start  = EDATE ( CALCULATE ( MIN ( Fact_Sales[order_date] ), REMOVEFILTERS () ), 12 )
```

The prior-year value is capped at 12 months before the last sale. `KEEPFILTERS` makes the cap *intersect* with the slicer instead of replacing it:

```dax
Net Transaction Value PY =
VAR _cap = EDATE ( [Last Sale Date], -12 )
RETURN
    CALCULATE (
        [Net Transaction Value],
        SAMEPERIODLASTYEAR ( Dim_Date[Date] ),
        KEEPFILTERS ( Dim_Date[Date] <= _cap )
    )
```

The current-period side of each KPI comparison is limited to dates on or after `Comparable Start`, so both sides always cover the same months. `PY Period Label` then writes the comparison window into the card ("vs Jan–Jun 2025"), adding "(same months)" whenever the window was trimmed.

## 4. KPI labels and colours

Each KPI card shows its value plus a text measure with the direction, size and period of change:

```dax
NTV YoY Label =
VAR _cap = EDATE ( [Last Sale Date], -12 )
VAR _cs  = [Comparable Start]
VAR _cur = CALCULATE ( [Net Transaction Value], KEEPFILTERS ( Dim_Date[Date] >= _cs ) )
VAR _py  = CALCULATE ( [Net Transaction Value], SAMEPERIODLASTYEAR ( Dim_Date[Date] ), KEEPFILTERS ( Dim_Date[Date] <= _cap ) )
RETURN
    IF (
        NOT ISBLANK ( _py ),
        FORMAT ( DIVIDE ( _cur - _py, _py ), "▲ 0.0%;▼ 0.0%;— 0.0%" ) & " " & [PY Period Label]
    )
```

- The `FORMAT` string's three sections pick the arrow from the sign: positive, negative, zero.
- Counts and values show **% change**, and rates show **percentage-point change**, so a rate moving from 40% to 42% reads "▲ 2.0 pp" rather than "▲ 5.0%".

A matching colour measure reads the arrow and returns a hex code. The card's label uses it through **Conditional formatting → Format style: Field value**:

```dax
NTV YoY Color = SWITCH ( LEFT ( [NTV YoY Label], 1 ), "▲", "#34D399", "▼", "#FF7A6B", "#9AA8C7" )
```

Colour means **good or bad, not up or down**. For the three KPIs where a rise is bad (`One-Time Customers`, `Lost Order Rate`, `Delivery Days`), the mapping is reversed: ▲ is red and ▼ is green.

## 5. Insight titles

Chart titles state the finding rather than the chart type, and they are recalculated for every slicer selection. For example, the category chart's title:

```dax
Top Category Title =
VAR _ranked      = TOPN ( 2, ALLSELECTED ( Dim_Products[category] ), [Net Transaction Value], DESC )
VAR _top         = TOPN ( 1, _ranked, [Net Transaction Value], DESC )
VAR _topValue    = MAXX ( _ranked, [Net Transaction Value] )
VAR _secondValue = MINX ( _ranked, [Net Transaction Value] )
VAR _share       = DIVIDE ( _topValue, CALCULATE ( [Net Transaction Value], ALLSELECTED ( Dim_Products[category] ) ) )
RETURN
    MAXX ( _top, Dim_Products[category] ) & " is " & FORMAT ( _share, "0%" )
        & " of revenue, " & FORMAT ( DIVIDE ( _topValue, _secondValue ), "0" ) & "× the next category"
```

The pattern repeats across the report:
- `ALLSELECTED` ranks within the current slicer selection.
- `TOPN` finds the leader.
- The result is assembled into one sentence.

Page headlines (`Exec Headline`, `Products Headline`, `Retention Headline`, …) follow the same idea and use `SWITCH ( TRUE (), … )` to choose wording, for example "flat", "up" or "down". Text measures are shown in **Card (new)** visuals or bound to visual titles through **fx**. The footer combines `Selected Period Text` and `Last Refreshed Text`, which reads the timestamp stored in `Refresh_Log`.

## 6. Highlighting the bar that matters

Each chart uses one neutral colour, with gold reserved for the one bar the title is talking about:

```dax
Highlight Top Category =
VAR _target = MAXX ( ALLSELECTED ( Dim_Products[category] ), [Net Transaction Value] )
RETURN
    IF ( [Net Transaction Value] = _target, "#F5B800", "#5B71A3" )
```

This is applied as **Data colors → fx → Field value**. Because it is computed rather than fixed, the highlight follows the leader when the slicers change.

## 7. Growth what-if scenarios

The Growth page sizes three levers with what-if parameters:
- converting one-time buyers to a second order
- avoiding Platinum churn
- attaching Home products to Electronics-only buyers

Each lever is *pool × assumption × value*:

```dax
Uplift Second Order = [One-Time Customers] * [Second Order Conversion Value] * [Average Order Value]
```

`Second Order Conversion Value` is the slider's selection (default 10%). `Lever Uplift` picks the right lever for each row of the `Growth Levers` table, so all three can be shown on one bar chart. `Lever Axis Max` sizes the axis for the largest possible uplift (every slider at its maximum), rounded up to ₹10M, so the scale stays fixed while the sliders move and bars can be compared between scenarios.

Platinum measures wrap their tier filter in `KEEPFILTERS`, so the Platinum lever respects the Tier slicer instead of overriding it.

## 8. Validation

Before any report page was built, the core measures were checked on a temporary validation page:
- `Total Orders`, `Units Sold`, `Gross Order Value`, `Coupon Discounts`, `Net Transaction Value` and `Average Order Value`, each tabulated by `Dim_Date[Year]` / `Month`, by `Dim_Products[category]` and by `Dim_Customers[customer_tier]`.
- Each breakdown's rows were checked to add up to the grand total. This confirms that the relationships pass filters correctly.

Presentation measures were then checked against those validated base numbers for every slicer combination kept on each page.
