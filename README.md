# Olist E-Commerce Sales & Customer Analytics Dashboard (Power BI)

An interactive, multi-page Power BI report built on the public **Olist Brazilian E-Commerce** dataset. It covers revenue performance, customer behaviour, product mix and delivery quality, and uses Power BI's AI visuals to show what drives low customer reviews.

> **Currency:** all monetary values are in Brazilian Real (R$).
> **Data period:** September 2016 to October 2018. Only January 2017 to August 2018 is used for trends, because the first and last months in the data are almost empty.

---

## Screenshots

| Home | Business Performance |
|---|---|
| ![Home](screenshots/01_home.png) | ![Business Performance](screenshots/02_business_performance.png) |

| Customer Analysis | Product & Sales Analysis |
|---|---|
| ![Customer Analysis](screenshots/03_customer_analysis.png) | ![Product and Sales](screenshots/04_product_sales.png) |

| AI Insights | Category Detail (drill-through) |
|---|---|
| ![AI Insights](screenshots/05_ai_insights.png) | ![Category Detail](screenshots/06_category_detail.png) |

---

## Report pages

| Page | Question it answers | Main visuals |
|---|---|---|
| **Home** | What is the overall business snapshot? | 9 KPI cards (Total Revenue, Total Orders, Total Customers, Repeat Customers, Delivered Orders, Items Sold, Average Order Value, Total Freight, Total Sales incl. Freight), navigation buttons |
| **Business Performance** | Are we growing, and where does revenue come from? | Revenue trend line, 2017 vs 2018 monthly comparison, YoY growth card, waterfall (Product Revenue + Freight = Total Sales), filled map of revenue by state |
| **Customer Analysis** | Who are our customers and how satisfied are they? | Review score distribution, orders by payment type, **Decomposition Tree** (State > Payment type > City), Average Review Score card, on-time vs late delivery review chart |
| **Product & Sales Analysis** | Which products drive revenue? | **Pareto chart** (revenue + cumulative %), **histogram** of item price, price vs sales volume scatter, rating by category |
| **AI Insights** | What drives low reviews? | **Key Influencers** visual, written key finding and recommendation |
| **Category Detail** *(hidden, drill-through)* | How is one category performing? | Category KPIs, monthly revenue trend, top 10 states |

---

## Power BI features used

- **Data modelling:** star-schema style model with a dedicated Date table and one-to-many relationships.
- **DAX:** measures, calculated columns, time intelligence (YTD, prior year, YoY), `SWITCH`, `CALCULATE`, `FILTER`, `DIVIDE`, `SELECTEDVALUE`.
- **AI visuals:** Key Influencers and Decomposition Tree.
- **Advanced charts:** Pareto (combo chart), histogram using calculated bins, waterfall, filled map, scatter, combo chart.
- **Interactivity:** drill-through page, page navigator, button slicers, synced Year slicer across pages, tooltips.
- **Design:** consistent layout, page frame and colour theme across all pages.

---

## Data model

**Source files** (Olist Brazilian E-Commerce dataset, 9 CSV files):

`olist_orders_dataset`, `olist_order_items_dataset`, `olist_order_payments_dataset`, `olist_order_reviews_dataset`, `olist_customers_dataset`, `olist_products_dataset`, `olist_sellers_dataset`, `olist_geolocation_dataset`, `product_category_name_translation`

**Tables in the model:** `Orders`, `Order_Items`, `Payments`, `Reviews`, `Customers`, `Products`, `Sellers`, `Geolocation`, `Seller_Sales_Summary`, `DateTable`, and a small helper table `Bridge` (used for the waterfall chart, not related to other tables).

**Data preparation (Power Query):**
- Joined product category names with the English translation file.
- Loaded and typed all tables, and built a separate Date table.

---

## DAX measures and columns

**Measures**
`Total Revenue`, `Total Orders`, `Delivered Orders`, `Items Sold`, `Average Order Value`, `Total Freight`, `Total Sales Including Freight`, `Avg Item Price`, `Total Customers`, `Repeat Customers`, `Repeat Customer %`, `Avg Review Score`, `Avg Review Score by Category`, `Revenue YTD`, `Revenue PY`, `Revenue YoY`, `Revenue YoY %`, `YoY Growth (Jan-Aug)`, `Cumulative Revenue %`, `Bridge Value`

**Calculated columns**
`Delivery Delay Days`, `Delivery Status` (On Time / Late), `Order Low Review`, `Price Bin`, `State Full`, `Country`, `Month Year`, `Month Short`, `YearMonthNum`

Example (Pareto cumulative share):

```DAX
Cumulative Revenue % =
VAR CurrentRev = [Total Revenue]
VAR CumRev =
    CALCULATE(
        [Total Revenue],
        FILTER(
            ALL(Products[Product_Category_Name_English]),
            [Total Revenue] >= CurrentRev
        )
    )
VAR AllRev =
    CALCULATE([Total Revenue], ALL(Products[Product_Category_Name_English]))
RETURN DIVIDE(CumRev, AllRev)
```

---

## Key insights

- **Late delivery hurts reviews the most.** Orders delivered on time average about 4.3 stars, while late orders average about 2.3.
- **Key Influencers:** orders delivered 5 or more days late are about 7.9x more likely to get a low review, against an average low-review rate of about 10%.
- **Revenue is concentrated by product.** 17 of 73 categories generate about 80% of revenue (Pareto analysis).
- **Revenue is concentrated by place.** São Paulo (SP) is by far the largest state, followed by Rio de Janeiro and Minas Gerais.
- **Growth.** Revenue for January to August 2018 is roughly 137% higher than the same period in 2017.
- **Seasonality.** Black Friday (24 Nov 2017) produced the highest single-day revenue spike.
- **Payments.** Credit card accounts for about three quarters of payments.
- **Repeat customers are rare** (about 3%), which points to a retention opportunity.
- **Freight** adds roughly 15% on top of product revenue.

---

## Repository structure

```
.
├── README.md
├── olist_dashboard.pbix          # Power BI report (Git LFS)
├── screenshots/                  # page images used above
├── data/
│   └── README.md                 # link to the dataset (raw CSVs are not stored here)
└── .gitattributes                # Git LFS tracking rules
```

---

## How to run

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free, Windows).
2. Download the dataset from Kaggle: *Brazilian E-Commerce Public Dataset by Olist*.
3. Open `olist_dashboard.pbix`.
4. If Power BI asks for the data location, go to **Home > Transform data > Data source settings** and point each source to the folder where you saved the CSV files.
5. Click **Refresh**.

---

## Limitations and future work

- Data for late 2016 and the end of 2018 is incomplete, so those months are excluded from trend charts.
- Add a **Seller Performance** page using the `Sellers` and `Seller_Sales_Summary` tables.
- Add a **delivery operations** page (delivery time histogram, late delivery % by state).
- Add bookmarks for metric switching and field parameters.
- Add row-level security and publish to Power BI Service.

---

## Tools

Power BI Desktop · DAX · Power Query (M) · Olist public dataset (Kaggle)

## Author

Harshit Pandey
[LinkedIn](www.linkedin.com/in/harshit-pandey-7ab186281) · [GitHub](https://github.com/Harshit28pan)

## Dataset credit

Olist Brazilian E-Commerce Public Dataset, released by Olist on Kaggle. Used here for learning and portfolio purposes.
