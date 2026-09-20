# Awesome Chocolates - Sales Analysis Dashboard

An interactive **Power BI** dashboard that analyses sales, profit, costs, boxes and shipments for a chocolate company across **6 countries** (Australia, Canada, India, New Zealand, UK, USA) and **3 product categories** (Bars, Bites, Other).

Built with **Power BI Desktop, DAX and Power Query**.

---

## Dashboard preview

### Overall view - sales people


### Country filter (Australia) - product view


Clicking a country button on the left filters every visual on the page. Here is how the KPIs change between all countries and Australia:

| KPI | All countries | Australia |
|---|---|---|
| Total Sales | $34M | $6M |
| Total Profit | 20.52M | 3.56M |
| Total Shipments | 6K | 1K |
| Total costs | 13.52M | 2.14M |
| Total Boxes | 2M | 326K |
| Profit % (target 60%) | 60.3% | 62.4% |
| Small shipments, LBS % (target 10%) | 10.2% | 11.0% |

---

## Key features

- **KPI cards** for sales, profit, shipments, costs and boxes, each with the latest month value and the month-over-month (MoM) change.
- **Profit % gauge** compared with the 60% target.
- **Metric switcher** (Sales, Boxes, Shipments, costs, Profit, Profit %) that changes the trend chart, built with a field parameter.
- **Interactive trend text** such as "Up by 3.7% vs average" or "Down by 10.7% vs average", written in DAX. It updates when a month is clicked.
- **Custom hover tooltip** on the Total Boxes trend chart, showing that month's boxes and the split by country in a donut chart.
- **Shipment size histogram** and an **LBS gauge**: the share of small shipments (fewer than 50 boxes) against a 10% target.
- **Product and sales person tables** with profit % data bars and green/red target icons. A button switches between the two.
- **Country and category navigation** buttons that filter the whole page.

---

## Key insights

- Overall profit is **60.3%** of sales, just above the 60% target.
- The latest month is weaker: sales are **down 10.8%** on the previous month.
- Products with profit far below target: **Baker's Choco Chips (17.4%)**, **Drinking Coco (26.7%)**, **50% Dark Bites (27.2%)** and **Caramel Stuffed Bars (38.7%)**.
- Sales people are close to each other, with profit % between about **54% and 67%**.
- Australia performs above target on profit (**62.4%**), and its latest month is above its average.

---

## DAX highlights

All measures are documented with explanations in [measures.md](measures.md). A few examples:

```DAX
Total Profit = [Total Sales] - [Total costs]

Profit % = DIVIDE([Total Profit], [Total Sales])

LBS % = DIVIDE([LBS Count], [Total Shipments])
```

```DAX
Boxes Change Text =
VAR _m   = MAX('Calendar'[Start of Month])
VAR _cur = CALCULATE([Total Boxes], REMOVEFILTERS('Calendar'), 'Calendar'[Start of Month] = _m)
VAR _avg = CALCULATE(AVERAGEX(VALUES('Calendar'[Start of Month]), [Total Boxes]), REMOVEFILTERS('Calendar'))
VAR _chg = DIVIDE(_cur - _avg, _avg)
RETURN
IF(_chg >= 0, "Up by ", "Down by ") & FORMAT(ABS(_chg), "0.0%") & " vs average"
```

---

## Data model

| Table | Role |
|---|---|
| `shipments` | Fact table: one row per shipment (sales, costs, boxes, date, product, sales person, country) |
| `calendar` | Date table used for month-over-month calculations |
| `products`, `people`, `locations` | Dimension tables |
| `_Measure` | Holds all DAX measures |
| `Measure Selector` | Field parameter table for the metric switcher |

---

## Repository contents

| File | Description |
|---|---|
| [sales analysis.pbix](sales%20analysis.pbix) | The Power BI project file |
| [ac-sample-data.xlsx](ac-sample-data.xlsx) | Source dataset |
| [measures.md](measures.md) | All DAX measures with explanations |
| [sales_analysis_report.pdf](sales_analysis_report.pdf) | Full project report |

---

## How to open

1. Download `sales analysis.pbix`.
2. Open it in **Power BI Desktop** (free from Microsoft).
3. Click the country and category buttons, and hover over the trend chart to see the tooltip.

If Power BI cannot find the data when you refresh, go to **Home > Transform data > Data source settings > Change Source** and select `ac-sample-data.xlsx` from your computer.

---

## Dataset

Sample data `ac-sample-data` for the fictional company *Awesome Chocolates*, provided as part of a Power BI learning course. The customisations in this project (interactive change text, custom tooltip, metric switcher and layout) are my own work on top of the sample data.

---

## Possible improvements

- Format all MoM values on the KPI cards as percentages.
- Add a year and quarter slicer.
- Add a profit-by-country page and a forecast.
- Publish to the Power BI Service for a live shareable link.

---

## Author

**gunasekhar-49**
