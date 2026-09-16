# Sales Order Health Dashboard

An end-to-end Excel BI project that turns a messy, 100-row raw order export into a fully interactive sales & fulfillment dashboard — covering data cleaning with Power Query, pivot-based analysis, dynamic KPI formulas, and a slicer-driven dashboard.

![Final Dashboard](screenshots/Final_Dashboar_Pic.png)

## 🎥 Live Demo

![Dashboard Demo](Sales_Order_Health.gif)

---

## 📌 Project Overview

The raw dataset contained customer orders with inconsistent country names, mixed date formats, text-formatted prices, missing unit prices, and duplicate/blank rows. The goal of this project was to:

1. Clean and standardize the data so revenue and order numbers could actually be trusted
2. Build a pivot-based analytical layer to answer real business questions (revenue, cancellations, top performers, concentration risk)
3. Package the findings into a single interactive dashboard for non-technical stakeholders

---

## 🧹 1. Data Preprocessing (Power Query)

All cleaning was done in **Power Query** rather than manually, so the pipeline re-runs automatically if the source data changes.

- **Table Helper Technique for missing Unit Price:** Built a small reference/lookup table (`unitPrice`) mapping each Product to its correct price, then used it inside Power Query to backfill any row where `UnitPrice` was missing or invalid — rather than guessing or dropping those rows.
- **M-language transformations:** Used custom M code within Power Query to:
  - Standardize inconsistent `Country` and `Category` values (case, whitespace, abbreviations all merged into one canonical label)
  - Reconcile `Quantity × UnitPrice` against `TotalAmount` to catch mismatches
  - Remove duplicate and blank rows before they could inflate order counts
- Output: a single clean `Cleaned` table used as the source for every pivot table downstream.

---

## 📊 2. Data Modeling & Analysis

- Built multiple **PivotTables** off the cleaned table (`Summary` sheet) breaking revenue and order counts down by Country, Category, Status, and Payment Method.
- Built a dedicated **Status × Category** and **Status × Country** breakdown to isolate exactly where cancellations were concentrated, rather than relying on a single blended number.

---

## 📈 3. Visualization & KPI Techniques

- **Column Helper Technique for highlighting:** Built parallel helper columns (Max / Mid / Min, and Completed vs. Cancelled) next to the raw pivot values, each returning either the real number or `NA()` depending on the condition. Plotting these as separate chart series let bars highlight automatically (e.g., worst-performing category in red, best in a different color) with no manual formatting that would break on refresh.
- **Dynamic KPI formulas:** Iterated through several formula approaches for the headline KPI cards (Top Country, Top Category) — from `INDEX/MATCH` to a more robust `LET` + `FILTER` + `SORTBY` array formula that explicitly excludes blank rows and Grand Total rows before selecting the top value, so the KPI can never accidentally return a total instead of a real category.
- **GETPIVOTDATA & COUNTIFS for slicer-safe metrics:** Used `GETPIVOTDATA` to pull pivot values by name instead of by cell position (so results survive rows shifting when slicers filter the pivot). For metrics that need to stay accurate *regardless* of the Status slicer selection (like Cancellation Rate), formulas were rebuilt against `COUNTIFS` on the raw cleaned table directly, bypassing the pivot/slicer layer entirely.
- **AOV (Average Order Value):** Calculated as Total Revenue ÷ Order Count, used both as a standalone KPI and as a reference point for evaluating a minimum free-shipping order threshold.

---

## 🖼️ Chart Gallery

| Chart | Preview |
|---|---|
| Orders by Status | ![Orders by Status](screenshots/Orders_by_status_by_chart.png) |
| Revenue by Payment Method | ![Revenue by Payment Method](screenshots/Revenue_by_payment_Method.png) |
| Revenue by Country (Map) | ![Revenue by Country](screenshots/Revenue_by_Country_Map.png) |
| Completed vs Cancelled — by Category | ![Completed vs Cancelled by Category](screenshots/Completed_bs_Cancelled_orders_by_Category_column_chart.png) |
| Completed vs Cancelled — by Country | ![Completed vs Cancelled by Country](screenshots/Completed_Vs_Cancelled_Orders_by_Country_bar_chart.png) |
| Cancelled Orders by Category (Pivot) | ![Cancelled by Category](screenshots/Cancelled_by_Category.png) |
| Completed Orders by Category (Pivot) | ![Completed by Category](screenshots/Completed_by_Category.png) |
| Cancelled Orders by Country (Pivot) | ![Cancelled by Country](screenshots/Cancelled_by_country.png) |
| Completed Orders by Country (Pivot) | ![Completed by Country](screenshots/Completed_by_country.png) |
| Country × Category Order Heatmap | ![Country x Category](screenshots/The_Most_country_category_Cancelled.png) |

---

## 🔑 Key Insights

- **Top Country:** Egypt — $3,902 revenue
- **Top Category:** Furniture — $5,190 revenue
- **Cancellation concentration:** Kitchenware and Sportswear carry the highest cancelled quantities among categories; Egypt and Germany lead among countries — not one extreme outlier, but two axes worth investigating operationally.
- **Order health:** ~30% of all orders are Cancelled, ~37% Completed, ~21% Shipped, ~12% Pending — meaning roughly a third of "gross" order volume never converts to real revenue.

---

## 🛠️ Tools Used

- Microsoft Excel — Power Query (M language), PivotTables, Slicers & Timelines, dynamic array formulas (`LET`, `FILTER`, `SORTBY`), `GETPIVOTDATA`, `COUNTIFS`, `INDEX/MATCH`
- Conditional Formatting for heatmaps and threshold-based highlighting

---

## 📁 Repository Structure

```
├── MyThirdDashboard_ISA.xlsx
├── Sales_Order_Health.gif
├── README.md
└── screenshots/
    ├── Final_Dashboar_Pic.png
    ├── The_Most_country_category_Cancelled.png
    ├── Cancelled_by_Category.png
    ├── Completed_by_Category.png
    ├── Cancelled_by_country.png
    ├── Completed_by_country.png
    ├── Orders_by_status_by_chart.png
    ├── Revenue_by_payment_Method.png
    ├── Revenue_by_Country_Map.png
    ├── Completed_Vs_Cancelled_Orders_by_Country_bar_chart.png
    └── Completed_bs_Cancelled_orders_by_Category_column_chart.png
```
