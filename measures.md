# DAX Measures – Awesome Chocolates Sales Analysis

All measures used in the Power BI dashboard, grouped by purpose.

**Tables used:** `shipments` (fact table), `calendar` (date table), `_Measure` (holds the measures), `Measure Selector` (calculated table for the metric switcher).

---

## 1. Base measures

These are the building blocks. Every other measure uses them.

### Total Sales
Adds up the sales amount of all shipments.
```DAX
Total Sales = SUM(shipments[Sales])
```

### Total costs
Adds up the cost of all shipments.
```DAX
Total costs = SUM(shipments[costs])
```

### Total Boxes
Adds up the boxes shipped.
```DAX
Total Boxes = SUM(shipments[Boxes])
```

### Total Shipments
Counts the number of shipments (one row = one shipment).
```DAX
Total Shipments = COUNTROWS(shipments)
```

### Total Profit
Profit = sales minus costs.
```DAX
Total Profit = [Total Sales] - [Total costs]
```

### Profit %
Profit as a share of sales (profit margin).
```DAX
Profit % = DIVIDE([Total Profit], [Total Sales])
```

---

## 2. Latest month measures

These show the numbers for the most recent month in the data. They power the KPI cards ("This Month" values).

### latest Date
Finds the last month start date in the calendar.
```DAX
latest Date = LASTDATE('calendar'[Start of Month])
```

### Total Sales Latest Month
Total sales for the latest month only.
```DAX
Total Sales Latest Month =
    var ld = [latest Date]
RETURN
    CALCULATE([Total Sales], 'calendar'[Start of Month] = ld)
```

### Total Cost Latest Month
Total costs for the latest month only.
```DAX
Total Cost Latest Month =
    VAR ld = [latest Date]
RETURN
    CALCULATE([Total costs], 'calendar'[Start of Month] = ld)
```

### Total Profit Latest Month
Total profit for the latest month only.
```DAX
Total Profit Latest Month =
    VAR ld = [latest Date]
RETURN
    CALCULATE([Total Profit], 'calendar'[Start of Month] = ld)
```

### Total Boxes Latest Month
Total boxes for the latest month only.
```DAX
Total Boxes Latest Month =
    VAR ld = [latest Date]
RETURN
    CALCULATE([Total Boxes], 'calendar'[Start of Month] = ld)
```

### Total Shipments Latest Month
Number of shipments in the latest month only.
```DAX
Total Shipments Latest Month =
    VAR ld = [latest Date]
RETURN
    CALCULATE([Total Shipments], 'calendar'[Start of Month] = ld)
```

---

## 3. Latest month-over-month (MoM) change %

Compare the latest month with the month before it. Used in the KPI cards (the red/green "MoM %" values).

### Latest MoM Sales Change %
```DAX
Latest MoM Sales Change % =
    var ld = [latest Date]
    var this_month_sales = [Total Sales Latest Month]
    var Prev_month_sales = CALCULATE([Total Sales], 'calendar'[Start of Month] = EDATE(ld, -1))
RETURN
    DIVIDE(this_month_sales - Prev_month_sales, Prev_month_sales)
```

### Latest MoM Cost Change %
```DAX
Latest MoM Cost Change % =
    VAR ld = [latest Date]
    VAR this_month_cost = [Total Cost Latest Month]
    VAR Prev_month_cost = CALCULATE([Total costs], 'calendar'[Start of Month] = EDATE(ld, -1))
RETURN
    DIVIDE(this_month_cost - Prev_month_cost, Prev_month_cost)
```

### Latest MoM Profit Change %
```DAX
Latest MoM Profit Change % =
    VAR ld = [latest Date]
    VAR this_month_profit = [Total Profit Latest Month]
    VAR Prev_month_profit = CALCULATE([Total Profit], 'calendar'[Start of Month] = EDATE(ld, -1))
RETURN
    DIVIDE(this_month_profit - Prev_month_profit, Prev_month_profit)
```

### Latest MoM Boxes Change %
```DAX
Latest MoM Boxes Change % =
    VAR ld = [latest Date]
    VAR this_month_boxes = [Total Boxes Latest Month]
    VAR Prev_month_boxes = CALCULATE([Total Boxes], 'calendar'[Start of Month] = EDATE(ld, -1))
RETURN
    DIVIDE(this_month_boxes - Prev_month_boxes, Prev_month_boxes)
```

### Latest MoM Shipments Change %
```DAX
Latest MoM Shipments Change % =
    VAR ld = [latest Date]
    VAR this_month_shipments = [Total Shipments Latest Month]
    VAR Prev_month_shipments = CALCULATE([Total Shipments], 'calendar'[Start of Month] = EDATE(ld, -1))
RETURN
    DIVIDE(this_month_shipments - Prev_month_shipments, Prev_month_shipments)
```

---

## 4. MoM change % for any selected month

Unlike the "Latest" measures above, these follow whatever month is in the filter context (for example a month on a chart axis). They compare that month with the previous month using `PREVIOUSMONTH`.

### Total sales (prev Month)
Sales of the month before the selected one.
```DAX
Total sales (prev Month) = CALCULATE([Total Sales], PREVIOUSMONTH('calendar'[Date]))
```

### MOM sales change %
```DAX
MOM sales change % =
        var this_month = [Total Sales]
        var prev_month = [Total sales (prev Month)]
RETURN
    DIVIDE(this_month - prev_month, prev_month)
```

### MOM Costs change %
```DAX
MOM Costs change % =
        var this_month = [Total costs]
        var prev_month = CALCULATE([Total costs], PREVIOUSMONTH('calendar'[Date]))
RETURN
    DIVIDE(this_month - prev_month, prev_month)
```

### MOM Profit change %
```DAX
MOM Profit change % =
        var this_month = [Total Profit]
        var prev_month = CALCULATE([Total Profit], PREVIOUSMONTH('calendar'[Date]))
RETURN
    DIVIDE(this_month - prev_month, prev_month)
```

### MOM Boxes change %
```DAX
MOM Boxes change % =
        var this_month = [Total Boxes]
        var prev_month = CALCULATE([Total Boxes], PREVIOUSMONTH('calendar'[Date]))
RETURN
    DIVIDE(this_month - prev_month, prev_month)
```

### MOM Shipment change %
```DAX
MOM Shipment change % =
        var this_month = [Total Shipments]
        var prev_month = CALCULATE([Total Shipments], PREVIOUSMONTH('calendar'[Date]))
RETURN
    DIVIDE(this_month - prev_month, prev_month)
```

---

## 5. Small-shipment analysis (LBS)

Used in the sales person table (the "LBS %" column with the red/green icons).

### LBS Count
Counts shipments with fewer than 50 boxes.
```DAX
LBS Count = CALCULATE([Total Shipments], shipments[Boxes] < 50)
```

### LBS %
Share of shipments that are small (under 50 boxes).
```DAX
LBS % = DIVIDE([LBS Count], [Total shipments])
```

---

## 6. Target indicator

### Profit Target Indicater
Returns a code for the icon/colour in the table:
- **2** = profit % is above the target (green tick)
- **1** = profit % is within 10% below the target
- **0** = profit % is well below the target (red cross)

```DAX
Profit Target Indicater =
    if([Profit %] > [Profit Target], 2,
        if([Profit %] > 0.9 * [Profit Target], 1, 0))
```

---

## 7. Interactive text on the trend chart

### Boxes Change Text
Shows text like "Up by 3.9% vs average" or "Down by 10.6% vs average". It compares the selected month's boxes with the average monthly boxes. When you click a point on the line chart, the text updates for that month. With nothing selected, it shows the latest month.

```DAX
Boxes Change Text =
VAR _m    = MAX('Calendar'[Start of Month])
VAR _cur  = CALCULATE([Total Boxes], REMOVEFILTERS('Calendar'), 'Calendar'[Start of Month] = _m)
VAR _avg  = CALCULATE(AVERAGEX(VALUES('Calendar'[Start of Month]), [Total Boxes]), REMOVEFILTERS('Calendar'))
VAR _chg  = DIVIDE(_cur - _avg, _avg)
RETURN
IF(_chg >= 0, "Up by ", "Down by ") & FORMAT(ABS(_chg), "0.0%") & " vs average"
```

---

## 8. Measure Selector (calculated table)

This is a **table**, not a measure. It lets the user switch the visuals between Sales, Boxes, Shipments, costs, Profit and Profit % with the tab buttons at the top of the dashboard (field parameter).

```DAX
Measure Selector = {
    ("Sales", NAMEOF('_Measure'[Total Sales]), 0),
    ("Boxes", NAMEOF('_Measure'[Total Boxes]), 1),
    ("Shipments", NAMEOF('_Measure'[Total Shipments]), 2),
    ("costs", NAMEOF('_Measure'[Total costs]), 3),
    ("Profit", NAMEOF('_Measure'[Total Profit]), 4),
    ("Profit %", NAMEOF('_Measure'[Profit %]), 5)
}
```

---

## Notes
- `Profit Target` is used by the indicator measure above and is defined separately in the model (a what-if parameter or fixed value).
- The tooltip page ("trend ToolTip") reuses `Total Boxes` in a card and a donut chart, so it needs no extra measures.
