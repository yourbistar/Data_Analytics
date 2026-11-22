# Business Performance Dashboard — Architecture & UX Specification

**Phase:** 3 — Dashboard Architecture and UI/UX Design
**Status:** Approved architecture reference; the four report pages have since been implemented in the existing PBIP project.
**Canvas basis:** Existing PBIR page size, 1280 × 720 px (16:9).

## 1. Existing report assessment

The PBIP contains one report bound to its semantic model. It now has four implemented pages: Executive Overview, Revenue Analysis, Regional Performance, and Product & Channel Analysis. Continue extending this report in place; do not create a second report or rebuild the PBIP.

The report references the built-in `CY24SU10` theme. Keep the existing report and theme resource architecture when making future visual updates; the palette and typography below document the approved design language.

## 2. Semantic model inventory used by this design

| Model object | Fields used in the report | Filter reach |
|---|---|---|
| `dim_date` | `Year`, `Quarter`, `YearQuarter`, `MonthName`, `YearMonth`, `Date` | Filters sales and monthly targets through active date relationships. |
| `dim_region` | `RegionName` | Filters sales and monthly targets. |
| `dim_product` | `ProductName`, `Category` | Filters sales only. |
| `dim_channel` | `ChannelName` | Filters sales only. |
| `fact_sales` | Existing measures listed below | Sales fact relates to date, region, product, and channel. |
| `fact_monthly_target` | Existing measures listed below; monthly target records are by month and region | Target fact relates to date and region only. |

Active one-way many-to-one relationships connect `fact_sales` to `dim_date`, `dim_region`, `dim_product`, and `dim_channel`; `fact_monthly_target` connects to `dim_date` and `dim_region`. This supports consistent date/region filtering. Product/category and channel filtering cannot filter targets. No target by product, category, or channel exists, so Page 4 is actual-revenue analysis only.

### Existing measures to reuse

| Existing measure | Intended use |
|---|---|
| `[Total Revenue]` | Revenue KPIs and actual-revenue charts. |
| `[Revenue Target]` | Target KPI and target series. |
| `[Target Attainment %]` | Target attainment KPI and regional attainment comparison. |
| `[Revenue Variance]` | Revenue variance KPI/chart. |
| `[Revenue Variance %]` | Optional relative variance detail. |
| `[Current Month Revenue]` | Selected-month revenue KPI. |
| `[Previous Month Revenue]` | Contextual comparison. |
| `[Month-over-Month Growth %]` | Selected-month growth KPI and monthly growth series. |
| `[Year-to-Date Revenue]` | YTD KPI. |
| `[Previous Year Revenue]`, `[Year-over-Year Growth %]` | YoY comparison across the available synthetic 2023–2025 actual history. |
| `[Current Month Target]`, `[YTD Target]` | Selected-month and YTD target KPIs. |
| `[Revenue % of Total]` | Revenue contribution. This existing measure removes product, region, and channel filters in its denominator, so it represents share of total business revenue for the selected date context, not share within a selected region/channel. Label and tooltip it accordingly. |
| `[Revenue Rank by Product]` | Product ranking. |
| `[Total Orders]`, `[Units Sold]`, `[Average Order Value]`, `[Revenue per Unit]` | Optional tooltip/detail metrics where relevant. |

No additional measure or dimension is required for the proposed report. Do not create duplicate calculations. The available `Month-over-Month Growth %` can be grouped by category or region, but only when the visual has a monthly date context. It is not a meaningful single KPI across an unconstrained multi-month selection.

## 3. Page architecture and layout

Use a consistent page frame: 32 px outer margins, 16 px gutters, a 64 px title/header band, a compact filter row, and aligned visual edges. Keep the page inside the existing 1280 × 720 canvas so readers do not need to scroll. The specifications below are approximate placements in pixels, measured from the top-left of the canvas; allow minor adjustments for Power BI visual chrome.

### Page 1 — Executive Overview

**Purpose:** Give executives a fast read on revenue, plan attainment, recent growth, and business mix.

| Placement (x, y, w, h) | Visual | Mapping and notes |
|---|---|---|
| 32, 20, 1216, 44 | Page title and context | Title: “Business Performance”. Optional small subtitle bound to selected Year/YearMonth. |
| 32, 76, 1216, 44 | Filter row | Year and Region slicers. Add a compact YearMonth slicer for the selected-month KPI cards; set its visual interactions to leave the full-year trend visuals unfiltered. |
| 32, 132, 288, 96 | KPI card | `[Total Revenue]`; currency format. |
| 336, 132, 288, 96 | KPI card | `[Revenue Target]`; currency format. |
| 640, 132, 288, 96 | KPI card | `[Target Attainment %]`; percentage format. |
| 944, 132, 304, 96 | KPI card | `[Month-over-Month Growth %]`; explicitly label as selected-month growth. Use the YearMonth slicer for this card only. |
| 32, 244, 592, 220 | Line and clustered column combo | Axis `dim_date[YearMonth]`; columns `[Total Revenue]` and `[Revenue Target]`; line `[Target Attainment %]` or omit line if the scale impairs readability. Keep target comparison at monthly/region granularity. |
| 640, 244, 608, 220 | Line chart | Axis `dim_date[YearMonth]`; value `[Total Revenue]`. It remains at year context when YearMonth is selected for KPI cards. |
| 32, 480, 592, 208 | Horizontal bar chart | Axis `dim_region[RegionName]`; value `[Total Revenue]`; descending sort. Target/actual comparison is not required in this chart. |
| 640, 480, 608, 208 | Horizontal bar chart | Axis `dim_product[Category]`; value `[Total Revenue]`; tooltip `[Revenue % of Total]`. Label contribution as share of total business revenue under the selected date context. |

### Page 2 — Revenue Analysis

**Purpose:** Explore monthly and quarterly revenue performance against plan and examine changes over time.

| Placement (x, y, w, h) | Visual | Mapping and notes |
|---|---|---|
| 32, 20, 1216, 44 | Page title | “Revenue Analysis”. |
| 32, 76, 1216, 44 | Filter row | Year and Region slicers; YearMonth selection can target KPI cards while monthly trend visuals retain the selected year. |
| 32, 132, 288, 88 | KPI card | `[Current Month Revenue]` for selected YearMonth. |
| 336, 132, 288, 88 | KPI card | `[Month-over-Month Growth %]` for selected YearMonth. |
| 640, 132, 288, 88 | KPI card | `[Revenue Variance]` for selected month and region. |
| 944, 132, 304, 88 | KPI card | `[Revenue Variance %]` for selected month and region. |
| 32, 236, 800, 250 | Line and clustered column combo | Axis `dim_date[YearMonth]`; values `[Total Revenue]`, `[Revenue Target]`; optional `[Revenue Variance]` tooltip. Monthly targets are only compared at month or coarser grain. |
| 848, 236, 400, 250 | Line chart | Axis `dim_date[YearMonth]`; value `[Month-over-Month Growth %]`; use zero-centered reference line if available. |
| 32, 502, 592, 186 | Column chart | Axis `dim_date[YearQuarter]`; value `[Total Revenue]`; chronological sort supplied by `YearQuarterSort`. |
| 640, 502, 608, 186 | Line chart | Axis `dim_date[YearMonth]`; value `[Total Revenue]`; legend `dim_region[RegionName]`. Keep regional series count at four. |

`[Previous Year Revenue]` and `[Year-over-Year Growth %]` compare periods where prior-year actual data is available. The source covers synthetic actuals from 2023 through 2025.

### Page 3 — Regional Performance

**Purpose:** Compare regions on actual revenue, target attainment, and monthly trajectory.

| Placement (x, y, w, h) | Visual | Mapping and notes |
|---|---|---|
| 32, 20, 1216, 44 | Page title | “Regional Performance”. |
| 32, 76, 1216, 44 | Filter row | Year and YearMonth slicers. Region may be a slicer, but default to all regions to preserve comparison. |
| 32, 132, 592, 250 | Horizontal bar chart | Axis `dim_region[RegionName]`; value `[Total Revenue]`; descending sort. Tooltip `[Revenue % of Total]` shows each region's share of total business revenue for the selected date context. The sorted bars also expose top and bottom revenue performers without a new ranking measure. |
| 640, 132, 608, 250 | Horizontal bar chart | Axis `dim_region[RegionName]`; value `[Target Attainment %]`; add 100% reference line and sort by attainment. |
| 32, 398, 800, 290 | Line chart | Axis `dim_date[YearMonth]`; values `[Total Revenue]`; legend `dim_region[RegionName]`. |
| 848, 398, 400, 290 | Horizontal bar chart | Axis `dim_region[RegionName]`; value `[Month-over-Month Growth %]`; requires selected YearMonth context. Use zero-centered reference line and signed percentage labels to show positive/negative regional growth. |

The top and bottom regions are identified by sorting the revenue or attainment bars. A separate “top/bottom” KPI would need a ranking measure that does not currently exist, so it is deliberately omitted rather than introducing duplicate/business logic.

### Page 4 — Product & Channel Analysis

**Purpose:** Understand the composition of actual revenue across the available products, categories, and sales channels.

| Placement (x, y, w, h) | Visual | Mapping and notes |
|---|---|---|
| 32, 20, 1216, 44 | Page title | “Product & Channel Analysis”. |
| 32, 76, 1216, 44 | Filter row | Year, YearMonth, Region, Category, and Channel. All are meaningful for actual revenue. Category and Channel filter actuals only. |
| 32, 132, 592, 248 | Horizontal bar chart | Axis `dim_product[ProductName]`; value `[Total Revenue]`; tooltip `[Revenue Rank by Product]`, `[Units Sold]`, and `[Average Order Value]`; sort descending by revenue. |
| 640, 132, 608, 248 | Bar chart | Axis `dim_product[Category]`; value `[Total Revenue]`; tooltip `[Revenue % of Total]`. Explain the measure’s total-business denominator in tooltip/help text. |
| 32, 396, 592, 292 | Line chart | Axis `dim_date[YearMonth]`; value `[Total Revenue]`; legend `dim_product[Category]`; optional tooltip `[Month-over-Month Growth %]`. |
| 640, 396, 608, 292 | Horizontal bar chart | Axis `dim_channel[ChannelName]`; value `[Total Revenue]`; tooltip `[Revenue % of Total]`, `[Total Orders]`, and `[Average Order Value]`; descending sort. |

The existing `[Month-over-Month Growth %]` may be used in category/month detail, but not as a category-level scalar across many months. There is no category-specific YoY history in the current source data.

## 4. Filter and interaction strategy

- **Year:** Main date filter on all pages, using `dim_date[Year]`. The current source covers 2023 through 2025.
- **YearMonth:** Use `dim_date[YearMonth]`, sorted by `YearMonthSort`. On pages with full-year trend charts, configure the YearMonth slicer to affect selected-month KPI/growth visuals but not the full-year trend charts. On charts without that interaction override, the slicer filters both actuals and monthly targets through `dim_date`.
- **Region:** Use `dim_region[RegionName]`. It filters actuals and targets consistently and should be synced on Pages 1–3. Include it on Page 4 for regional actual-revenue breakdown.
- **Product Category and Sales Channel:** Use `dim_product[Category]` and `dim_channel[ChannelName]` on Page 4 only. They filter sales actuals and should not be offered as report-wide filters beside target comparisons.
- **Cross-highlighting:** Keep interactions among actual-revenue charts within a page. Disable Category/Channel filtering of target visuals if such visuals are ever placed on a page with these selectors; target records have no category/channel grain and must not look like a category/channel-specific plan.
- **Tooltips and accessibility:** Provide descriptive titles, units, and concise tooltip definitions. Use visual titles/labels and patterns/markers in addition to color. Set a logical tab order and meaningful alt text during implementation.

## 5. Theme and formatting

- **Canvas:** Warm white/light neutral background (`#F7F8FA`); white visual surfaces. Use no heavy outlines; separate regions with whitespace and subtle neutral dividers only where necessary.
- **Palette:** Actual revenue deep blue (`#1769AA`); target muted amber (`#C58A16`); favorable variance green (`#247A55`); unfavorable variance red (`#B54745`); primary text charcoal (`#202B36`); secondary text slate (`#5B6773`). Keep category series distinct but restrained and test contrast against white.
- **Meaning:** Use consistent color mapping (actual always blue, target always amber; favorable/unfavorable only for variance status). Do not encode meaning by color alone; include explicit legends, labels, or signs.
- **Typography:** Segoe UI or the existing Power BI default sans-serif. Page headings 22–26 pt semibold, KPI values 24–30 pt, visual titles 12–14 pt, axis/labels at least 10–11 pt where canvas permits.
- **Numbers:** Currency measures display as dollars with thousands separators and two decimals for detailed charts; compact KPI labels may use display units (K/M) only if the visual retains an exact tooltip. Percentages use one decimal place. Revenue and target share a currency style.
- **Layout:** Align all cards/charts to the 16 px grid, maintain consistent padding and visual title positions, avoid unnecessary borders, and prevent legends/labels from crowding data.

## 6. Implementation limitations and guardrails

1. The PBIR report contains four implemented pages and their visual definitions. Preserve the existing report and page structure when making future changes.
2. Actuals cover synthetic dates in 2023–2025. Previous-year measures require a comparable prior-year period in the selected filter context.
3. Targets are monthly by region. They are not defined by product/category/channel, so do not compare a category or channel’s revenue to the same unfiltered target as if it were a segment target.
4. `[Revenue % of Total]` intentionally removes product, region, and channel filters in its denominator. It is a business-wide contribution share within the selected date context; it will not recalculate as a within-region/channel share.
5. Target values are recorded on the first of each month. Present targets at month or coarser granularity, not as a daily target series.
6. The CSV Power Query sources use the shared `pDataFolder` parameter. Each developer must set it to the local repository data folder before refresh.
7. This document captures approved design intent; the PBIR and TMDL definitions contain the implemented report and model.

## 7. Implementation sequence (completed)

1. Extend the existing PBIR report: rename Page 1 and add Pages 2–4.
2. Apply the shared theme, page frame, and consistent filter behavior.
3. Build Page 1 and verify actual/target and growth interactions.
4. Build Pages 2–3 around monthly and regional analysis.
5. Build Page 4 as actuals-only product/category/channel analysis.
6. Validate each page against the existing model measures and source grain; do not add model calculations unless separately reviewed and approved.
