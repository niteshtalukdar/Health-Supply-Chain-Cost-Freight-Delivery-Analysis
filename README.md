# Cost, Freight and Delivery Analysis for Health Supply Chain

An Excel analysis of 10,324 HIV and malaria medicine shipments that USAID delivered to 43 countries between 2006 and 2015. The project looks at what it cost to deliver these medicines, what drove that cost, how reliable delivery was, and how drug prices changed over time.

Everything is built in Excel with formulas, PivotTables, and Goal Seek. No add-ins, macros, or Power Query are needed to open or follow the work.

## The questions

1. **Where does the money go?** Landed cost and freight as a share of product value, by shipment mode, product group, and country.
2. **Does shipping more make each kilogram cheaper?** Freight cost per kg by shipment size, and when air freight is worth paying for over ocean.
3. **How reliable is delivery?** On-time rate and lead time by mode, year, and vendor.
4. **How did drug prices change?** Price trend for the most-shipped HIV drugs, and generic vs. branded prices.

## Data

**Source:** "Supply Chain Shipment Pricing Data", U.S. Agency for International Development (USAID), Supply Chain Management System (SCMS) project. Licence: Creative Commons Attribution.

- Copy used: [Kaggle](https://www.kaggle.com/datasets/divyeshardeshana/supply-chain-shipment-pricing-data)
- Original: https://data.usaid.gov/d/a3rc-nmf6

The data has one row per shipment line, with product, vendor, country, shipment mode, dates, value, weight, freight, and insurance.

## Workbook layout

| Tab | What it holds |
|---|---|
| README | Project summary and list of Excel skills used |
| Raw_Data | Original data, never edited |
| Clean_Data | Cleaned Excel Table. Grey headers are copied from raw, green headers are formula columns |
| Item_Weights | Median kg per pack for each item, used to split shared freight bills |
| Cleaning_Log | Every fix, the rows it touched, and checks that totals still match the raw file |
| Pivot_Cost, Pivot_Delivery, Pivot_Prices | PivotTables used to explore the data |
| 1_Landed_Cost | Q1: totals and freight % by mode, product group, and country |
| 2_Freight_per_kg | Q2: cost per kg by shipment size, plus an air vs. ocean break-even model |
| 3_Delivery | Q3: on-time %, lead time, vendor scorecard, and vendor lookup |
| 4_Prices | Q4: price trend, price lookup, generic vs. branded |
| Dashboard | KPIs and charts with Year, Mode, and Product Group filters |
| Dash_Data | Calculations that feed the dashboard |
| Findings | Five findings and their limits |

## How I cleaned the data

The raw file had several problems. Each one is logged on the Cleaning_Log tab with the number of rows it affected. The main ones:

- **Mixed date formats.** Dates came in two formats (1/23/08 and 2-Jun-06), and Excel misreads some of them. I imported date columns as text and rebuilt them with `DATE`, `MID`, and `FIND`.
- **Freight stored as references.** 2,445 lines said "See ASN-93 (ID#:1281)" instead of a number, meaning the freight was billed on another line. I pulled out the ID, looked up that line's freight with `INDEX/MATCH`, and split the shared bill across the shipment.
- **Splitting shared bills fairly.** I split shared freight by estimated weight, not by value, because heavy, cheap items like oral liquids would be undercharged by a value split. I checked the weight estimate against recorded shipment weights: the median ratio was 1.01.
- **Freight included in the product price.** 1,442 lines had no separate freight bill. I kept them in all totals instead of setting freight to zero, and estimated the hidden freight separately.
- **Text in number columns.** Freight and weight columns mixed numbers with notes. I split each into a status column and a number-only column.
- **Missing insurance.** 287 lines had no insurance value. I estimated it from the median insurance rate of the other lines.
- **Possible duplicates.** 12 rows looked like repeats. I flagged them instead of deleting, since they had different IDs.

Checks on the Cleaning_Log tab confirm that row count, total value, total packs, and unique IDs all match between the raw and clean data.

## Findings

**1. Freight added 4.5% to the value of the goods.**
By mode, it was 7.3% for air, 3.8% for air charter, 3.1% for ocean, and 2.2% for truck. Total landed cost came to about $1.70 billion on $1.63 billion of product value.

**2. Bigger shipments cost much less per kg.**
Air shipments under 100 kg cost $36.05 per kg. Air shipments over 10,000 kg cost $1.62 per kg. Ocean and truck show the same pattern, so combining small orders can lower freight cost.

**3. Ocean was the cheaper choice for most generic medicines.**
For 2,000 to 9,999 kg shipments, air cost $3.53 more per kg than ocean. Using a holding cost of 20% per year and the 73.5-day gap in the data, air only pays for itself on goods worth more than about $88 per kg. Generic medicines in this range were worth about $70 per kg, so ocean was cheaper for them. The break-even value updates on its own when you change the inputs, and Goal Seek can be used to check it.

**4. The reported on-time rate is likely too high.**
On paper, 88.5% of lines arrived on time. But 61% arrived exactly on the scheduled date, which suggests some scheduled dates were updated after delivery. Removing those lines gives 70.4%, so the real rate likely sits between 70% and 89%. Lines shipped from regional warehouses were on time less often (82.8%) than lines shipped directly from vendors (94.7%).

**5. Drug prices fell, but branded drugs still cost more.**
The five most-shipped first-line HIV drugs, which make up 44% of HIV drug spending, got 42% cheaper per pack from 2008 to 2015 (using 2015 quantities for both years). For the same drug, dose, and form, branded versions cost a median of 2.8 times the generic price.

## Limits

- The data has no ship date, so transport time can't be separated from vendor production and paperwork time.
- The air vs. ocean model counts only the cost of holding stock, not the cost of a clinic running out of medicine, which is the usual reason to fly. That cost can be entered on the tab to see its effect.
- Lead time can only be measured for lines shipped directly from vendors, since warehouse lines have no purchase order date.
- Prices are those negotiated for a large donor programme, not retail prices.
- Groups with fewer than 30 lines are marked in red and should be read with care.

## Excel skills used

- **Cleaning:** Text Import Wizard, Excel Tables, filters, `LEFT`, `MID`, `FIND`, `TRIM`, `VALUE`, `DATE`, `IF`, `AND`, `OR`, `ISNUMBER`, `IFERROR`, `COUNTIF` for duplicate checks
- **Analysis:** `INDEX/MATCH` (including two-way lookups), `SUMIFS`, `COUNTIFS`, `AVERAGEIFS`, `MINIFS`, `MAXIFS`, `MEDIAN(IF())` array formulas, `LOOKUP` for banding, `RANK`, PivotTables
- **Reporting:** Data Validation dropdowns, conditional formatting, Goal Seek, charts, a filterable dashboard



## Author

**Nitesh Talukdar**
[LinkedIn](https://www.linkedin.com/in/niteshtalukdar/) 
