---
layout: default
title: "03 — Implement & Manage Semantic Models"
nav_order: 5
description: "Domain 3 (25–30%) — Storage modes, DAX calculations, Direct Lake, relationships, calculation groups, composite models, and optimization for the DP-600 exam."
mermaid: true
---

# Implement & Manage Semantic Models
{: .no_toc }

Domain 3 accounts for **25–30 %** of the DP-600 exam. It spans semantic-model design (storage modes, star schemas, relationships, DAX, calculation groups, composite models) and enterprise-scale optimization (DAX tuning, Direct Lake configuration, incremental refresh).

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## Design and Build Semantic Models

### Choose a Storage Mode

Power BI semantic models support three storage modes. The right choice depends on data volume, latency requirements, and whether Microsoft Fabric is in play.

| Aspect | Import | DirectQuery | Direct Lake |
|---|---|---|---|
| **Data location** | Compressed in-memory (VertiPaq) | Stays in source; queries sent live | Delta tables in OneLake, loaded on demand |
| **Performance** | Fastest queries | Slower — depends on source | Near-Import speed, no copy |
| **Data freshness** | Stale until refresh | Real-time | Near-real-time (framing) |
| **Model size limit** | SKU RAM / Premium capacity | No hard limit | SKU guardrails apply |
| **Transformation layer** | Power Query (M) in dataset | Limited PQ; views in source | Notebooks / Dataflows → Lakehouse |
| **Best for** | Small–mid datasets needing speed | Real-time on relational sources | Fabric-native analytics at scale |

```mermaid
flowchart TD
    A[Start: Choose Storage Mode] --> B{Data in OneLake<br/>Delta tables?}
    B -- Yes --> C{Need real-time<br/>to the second?}
    C -- No --> D[Direct Lake]
    C -- Yes --> E[Direct Lake +<br/>DirectQuery fallback]
    B -- No --> F{Data volume<br/>> capacity RAM?}
    F -- Yes --> G[DirectQuery]
    F -- No --> H{Need real-time?}
    H -- Yes --> G
    H -- No --> I[Import]
```

> 🎯 **Exam Tip:** Direct Lake is the preferred mode for Fabric workloads. It reads Parquet files directly from OneLake — no data copy, no scheduled refresh in the Import sense. Know that it **requires Delta tables in a Lakehouse or Warehouse**.

> ⚠️ **Exam Caveat:** Direct Lake is **only** available in Microsoft Fabric capacities (F SKUs) and Power BI Premium (P SKUs). It is not available in Pro-only workspaces or shared capacity.

---

### Implement a Star Schema

A star schema organises the semantic model around **fact tables** (events / measures) surrounded by **dimension tables** (descriptive attributes). This is the foundation for performant DAX and clean reports.

```mermaid
erDiagram
    DIM_DATE ||--o{ FACT_SALES : "DateKey"
    DIM_PRODUCT ||--o{ FACT_SALES : "ProductKey"
    DIM_CUSTOMER ||--o{ FACT_SALES : "CustomerKey"
    DIM_STORE ||--o{ FACT_SALES : "StoreKey"

    FACT_SALES {
        int DateKey
        int ProductKey
        int CustomerKey
        int StoreKey
        decimal SalesAmount
        int Quantity
    }
    DIM_DATE {
        int DateKey
        date FullDate
        string MonthName
        int Year
    }
    DIM_PRODUCT {
        int ProductKey
        string ProductName
        string Category
    }
    DIM_CUSTOMER {
        int CustomerKey
        string CustomerName
        string Region
    }
    DIM_STORE {
        int StoreKey
        string StoreName
        string City
    }
```

**Star vs Snowflake:**

| Star Schema | Snowflake Schema |
|---|---|
| Dimensions fully denormalised | Dimensions normalised into sub-tables |
| Fewer joins → faster VertiPaq scans | More joins → harder for the engine to optimise |
| Recommended for Power BI | Acceptable at source; flatten before model |

> 🎯 **Exam Tip:** The exam strongly favours **star schemas**. If a question describes a normalised or snowflake source, the correct answer usually involves flattening dimensions in Power Query or the Lakehouse layer before loading into the model.

---

### Implement Relationships

#### Core Relationship Properties

| Property | Options | Notes |
|---|---|---|
| **Cardinality** | One-to-many (1:*), Many-to-one (*:1), One-to-one (1:1), Many-to-many (*:*) | 1:* is the default and preferred |
| **Cross-filter direction** | Single, Both (bi-directional) | Both enables filtering from fact → dimension; use sparingly |
| **Active / Inactive** | One active per path; others inactive | Invoke inactive relationships with `USERELATIONSHIP` |

#### Bridge Tables and Many-to-Many

When a fact table has multiple values per dimension row (e.g., a patient with many diagnoses), insert a **bridge table** between them. Set the bridge-to-fact side as many-to-many and enable bi-directional filtering — or better, use DAX measures with `CALCULATE` + `CROSSFILTER`.

#### Role-Playing Dimensions with USERELATIONSHIP

A Date dimension often plays multiple roles (Order Date, Ship Date, Due Date). Only one relationship can be active. Use `USERELATIONSHIP` in measures for the others:

```dax
Ship Date Sales =
CALCULATE(
    SUM( Sales[SalesAmount] ),
    USERELATIONSHIP( Sales[ShipDateKey], DimDate[DateKey] )
)
```

> ⚠️ **Exam Caveat:** `USERELATIONSHIP` only works inside `CALCULATE` (or `CALCULATETABLE`). It cannot be used standalone. Expect questions that test whether you know this constraint.

---

### Write DAX Calculations

#### Variables and CALCULATE

Variables improve readability and prevent repeated evaluation:

```dax
Profit Margin % =
VAR _Revenue = SUM( Sales[SalesAmount] )
VAR _Cost    = SUM( Sales[CostAmount] )
RETURN
    IF(
        _Revenue = 0,
        BLANK(),
        DIVIDE( _Revenue - _Cost, _Revenue )
    )
```

`CALCULATE` is the most important DAX function — it evaluates an expression under modified filter context:

```dax
All-Region Sales =
CALCULATE(
    SUM( Sales[SalesAmount] ),
    ALL( DimStore[Region] )
)

Region % of Total =
VAR _RegionSales = SUM( Sales[SalesAmount] )
VAR _TotalSales  = CALCULATE( SUM( Sales[SalesAmount] ), ALL( DimStore[Region] ) )
RETURN
    DIVIDE( _RegionSales, _TotalSales )
```

#### Iterator Functions (SUMX, AVERAGEX, MAXX)

Iterators evaluate an expression **row by row** over a table, then aggregate:

```dax
Weighted Avg Price =
SUMX(
    Sales,
    Sales[Quantity] * RELATED( DimProduct[UnitPrice] )
) / SUM( Sales[Quantity] )

Max Line Total =
MAXX( Sales, Sales[Quantity] * Sales[UnitPrice] )
```

> 🎯 **Exam Tip:** Know the difference between `SUM` (aggregator — works on a single column) and `SUMX` (iterator — can evaluate an expression per row). Questions often test whether a scenario needs an iterator.

#### Table Filtering: FILTER, ALL, ALLEXCEPT

| Function | Purpose |
|---|---|
| `ALL( table/column )` | Removes all filters from the specified table or columns |
| `ALLEXCEPT( table, col1, col2 )` | Removes filters from all columns **except** those listed |
| `FILTER( table, expression )` | Returns a table of rows that satisfy the expression (row context) |
| `KEEPFILTERS` | Adds filters without overriding existing context inside CALCULATE |

```dax
Top Category Sales =
CALCULATE(
    SUM( Sales[SalesAmount] ),
    FILTER(
        ALL( DimProduct[Category] ),
        [Total Sales] > 1000000
    )
)
```

> ⚠️ **Exam Caveat:** Using `FILTER` on a large table with millions of rows is a performance anti-pattern. The exam may present this as the "correct but slow" option — prefer column predicates inside `CALCULATE` directly when possible.

#### Windowing Functions (OFFSET, WINDOW, INDEX)

These functions (introduced in late 2022) enable row-relative calculations without complex earlier workarounds:

```dax
Previous Month Sales =
CALCULATE(
    SUM( Sales[SalesAmount] ),
    OFFSET(
        -1,
        ALLSELECTED( DimDate[MonthYear] ),
        ORDERBY( DimDate[MonthYear], ASC )
    )
)
```

| Function | Use Case |
|---|---|
| `OFFSET` | Access a value N rows before/after in a sorted partition |
| `WINDOW` | Define a sliding or absolute range of rows |
| `INDEX` | Access a specific ordinal row position |

#### Information Functions

| Function | Returns | Common Use |
|---|---|---|
| `ISBLANK( value )` | TRUE if value is BLANK | Guard against division errors |
| `HASONEVALUE( column )` | TRUE if exactly one value in filter context | Conditional headers / formatting |
| `SELECTEDVALUE( column, alt )` | The single value in context, or alt | Dynamic titles, parameter captures |

---

### Calculation Groups, Dynamic Format Strings, and Field Parameters

#### Calculation Groups

Calculation groups let you define **reusable DAX transformations** that apply to any measure at evaluation time. They are created through Tabular Editor or XMLA endpoints.

Example — a Time Intelligence calculation group:

```dax
-- Calculation Group: Time Intelligence
-- Calculation Item: YTD
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD( DimDate[FullDate] )
)

-- Calculation Item: PY (Prior Year)
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR( DimDate[FullDate] )
)

-- Calculation Item: YoY %
VAR _Current = SELECTEDMEASURE()
VAR _PY = CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR( DimDate[FullDate] )
)
RETURN
    DIVIDE( _Current - _PY, _PY )
```

| Calculation Groups | Individual Measures |
|---|---|
| One definition applies to **all** measures | Each measure needs its own YTD, PY, YoY copy |
| Maintained centrally via Tabular Editor / XMLA | Maintained individually in Power BI Desktop |
| Can include **dynamic format strings** | Format strings are per-measure |
| Reduces measure proliferation | Can explode to hundreds of measures |

#### Dynamic Format Strings

Attached to a calculation item, a format string expression changes the display format contextually:

```dax
-- Format string for YoY % item
IF(
    ISSELECTEDMEASURE( [Total Sales] ),
    "#,##0.0%",
    "#,##0"
)
```

#### Field Parameters

Field parameters allow **report consumers** to swap dimensions or measures on a visual dynamically. Created in Power BI Desktop via the modelling ribbon, they generate a disconnected table with a DAX expression listing the fields.

> 🎯 **Exam Tip:** Calculation groups require the model to be at **compatibility level 1500+** and are created outside Power BI Desktop (Tabular Editor, XMLA). Field parameters, by contrast, are created directly in Desktop.

---

### Large Semantic Model Storage Format

When enabled, the semantic model can exceed the default per-dataset size limit by storing segments on Premium capacity storage rather than solely in memory.

**When to enable:**

- Models approaching or exceeding the default size limit (e.g., > 10 GB on P1/F64)
- Incremental refresh with many partitions
- Need XMLA read/write endpoint access for third-party tooling

**Implications:**

- Requires **Premium Per User**, Premium capacity (P SKU), or Fabric capacity (F SKU)
- Once enabled, the model can be managed through the **XMLA endpoint** (Tabular Editor, SSMS, ALM Toolkit)
- Enables features such as object-level security, calculation groups via XMLA, and metadata-only deployments

> ⚠️ **Exam Caveat:** Enabling large model storage format is a **one-way setting** — once turned on it cannot be reverted to small format without recreating the dataset.

---

### Design and Build Composite Models

Composite models combine **multiple storage modes** in a single semantic model. Tables can individually be set to Import, DirectQuery, or Direct Lake.

```mermaid
flowchart LR
    subgraph Composite Model
        direction TB
        A[DimDate<br/>Import] --- F[Fact_Sales<br/>Direct Lake]
        B[DimProduct<br/>Import] --- F
        F --- G[Fact_Budget<br/>DirectQuery<br/>SQL DB]
    end
    F -. reads .-> DL[(OneLake<br/>Delta Tables)]
    G -. queries .-> SQL[(Azure SQL<br/>Database)]
```

**Key design rules:**

1. **Import + DirectQuery** — classic composite model; aggregation tables in Import accelerate DQ queries.
2. **Direct Lake + DirectQuery** — Fabric scenario; core facts from Lakehouse, supplemental data from external SQL.
3. **Relationships across storage modes** form a *limited relationship* (DirectQuery semantics apply to that join).

> 🎯 **Exam Tip:** In a composite model, any relationship that crosses storage-mode boundaries is evaluated using **DirectQuery semantics**, even if one side is Import. This can affect performance — the exam tests awareness of this.

---

## Optimize Enterprise-Scale Semantic Models

### Improve Query and Visual Performance

| Technique | Detail |
|---|---|
| **Reduce visual count** | Aim for ≤ 8 visuals per page; each visual fires a separate query |
| **Avoid high-cardinality columns in visuals** | Showing millions of distinct values forces large result sets |
| **Use aggregation tables** | Pre-aggregated Import tables sit in front of DirectQuery detail tables |
| **Set report page type to Tooltip or Drillthrough** | Reduces default-load query count |
| **Use Performance Analyzer** | Capture DAX queries generated by each visual; identify slow ones |

---

### Improve DAX Performance

| Best Practice | Anti-Pattern |
|---|---|
| Use **variables** — evaluated once, reused | Repeating the same sub-expression multiple times |
| Column predicates in `CALCULATE` directly | Wrapping large tables in `FILTER()` |
| Use `KEEPFILTERS` to intersect, not override | Using `FILTER( ALL(...) )` when intersection is intended |
| Avoid `DISTINCTCOUNT` on very high-cardinality columns | — |
| Use `DIVIDE( a, b )` instead of `a / b` | Manual `IF` checks for zero |
| Minimise row-level iteration with `SUMX` over huge tables | Nested iterators (iterator inside iterator) |

```dax
-- Anti-pattern: FILTER on entire table
Bad Example =
CALCULATE(
    SUM( Sales[Amount] ),
    FILTER( Sales, Sales[Region] = "West" )
)

-- Better: direct column predicate
Good Example =
CALCULATE(
    SUM( Sales[Amount] ),
    Sales[Region] = "West"
)
```

> 🎯 **Exam Tip:** The Performance Analyzer in Power BI Desktop shows three timings for each visual: **DAX query**, **visual rendering**, and **other**. For DAX tuning, copy the DAX query and test it in DAX Studio or the Fabric portal query view.

---

### Configure Direct Lake

#### Architecture Overview

```mermaid
flowchart LR
    LH[(Lakehouse<br/>Delta Tables<br/>V-Order Parquet)] -- framing --> DL[Direct Lake<br/>Semantic Model]
    DL -- query --> PBI[Power BI<br/>Reports]
    DL -. fallback .-> DQ[DirectQuery<br/>SQL Endpoint]
```

#### Framing and V-Order

- **Framing** is the process by which the Direct Lake model takes a snapshot of the current Delta table version. A new frame is triggered on refresh or automatically when the model detects new data (depending on settings).
- **V-Order** is a write-time optimisation applied to Parquet files in the Lakehouse that aligns data for fast VertiPaq reads. Always ensure V-Order is enabled for Direct Lake tables.

#### Fallback Behaviour

| Setting | Behaviour |
|---|---|
| **Automatic fallback (default)** | If data exceeds guardrails or unsupported DAX is used, the engine silently falls back to DirectQuery via the SQL endpoint |
| **Manual / disabled fallback** | Queries that cannot be served from Direct Lake will **fail** instead of falling back — useful when you want to guarantee in-memory speed |

Guardrails (per SKU) define thresholds for row count per table, column count, and model size. Exceeding them triggers fallback or failure.

> ⚠️ **Exam Caveat:** If Direct Lake fallback is **disabled** and a query exceeds guardrails, users will see an error — not a slow result. The exam may test your understanding of when to enable vs disable fallback.

#### Direct Lake on OneLake vs SQL Endpoint

| Aspect | Direct Lake on OneLake (default) | Direct Lake via SQL Endpoint |
|---|---|---|
| **Data source** | Delta Parquet files directly | SQL analytical endpoint of Lakehouse/Warehouse |
| **Performance** | Best — no translation layer | Slight overhead from SQL layer |
| **Use case** | Standard Fabric analytics | When you need SQL views, security, or transformations |
| **Fallback target** | SQL endpoint (DirectQuery) | Same SQL endpoint |

> 🎯 **Exam Tip:** Direct Lake **always** reads the Delta files for primary queries. The SQL endpoint is used only as a **fallback** target when DirectQuery mode kicks in. Questions may try to confuse these two paths.

---

### Implement Incremental Refresh

Incremental refresh partitions a table by date so that only recent data is refreshed, while historical partitions are untouched.

**Setup steps:**

1. Create `RangeStart` and `RangeEnd` parameters (type DateTime) in Power Query.
2. Filter the source table to rows between these parameters.
3. Define the refresh policy: archive period (e.g., 3 years), incremental period (e.g., 30 days).
4. Optionally enable **real-time data with DirectQuery** — this adds a DirectQuery partition for the latest data that is always live.

| Configuration | Effect |
|---|---|
| Archive period = 3 years | Historical partitions covering 3 years are loaded once and not refreshed |
| Incremental window = 30 days | Only the most recent 30 days of partitions are refreshed each cycle |
| Detect data changes | Only refresh incremental partitions where source rows changed (requires a `LastModified` column) |
| Real-time + DirectQuery | A DQ partition covers data newer than the latest Import partition |

> ⚠️ **Exam Caveat:** The `RangeStart` and `RangeEnd` parameters **must** be of type DateTime and **must** be named exactly `RangeStart` and `RangeEnd` (case-sensitive). This is a frequent exam trick.

> 🎯 **Exam Tip:** Incremental refresh combined with the **large model storage format** is required when the number of partitions grows large. Without large format, you may hit partition-count limits.

---

## Scenario-Based Quick Reference

| # | Scenario | Answer |
|---|---|---|
| 1 | Data sits in a Fabric Lakehouse and you want the fastest query speed without copying data | **Direct Lake** storage mode |
| 2 | External Azure SQL DB must show real-time data in reports | **DirectQuery** to the SQL DB |
| 3 | Need both Lakehouse facts and live Azure SQL budget data in one model | **Composite model** — Direct Lake + DirectQuery |
| 4 | Date dimension plays Order Date and Ship Date roles | One active relationship; use **USERELATIONSHIP** in measures for the inactive one |
| 5 | Hundreds of measures each need YTD, PY, and YoY variants | Create a **calculation group** with three calculation items |
| 6 | Report visual shows "query exceeded guardrails" error | Direct Lake fallback is **disabled**; either enable fallback or reduce data/columns below SKU limits |
| 7 | Model size approaching 10 GB on P1 / F64 | Enable **large semantic model storage format** |
| 8 | Need to refresh only the last 7 days of a 5-year sales table | Configure **incremental refresh** with 5-year archive, 7-day incremental window |
| 9 | DAX measure runs slowly — wraps entire Sales table in FILTER | Replace `FILTER( Sales, ... )` with a **column predicate** inside `CALCULATE` |
| 10 | Report users want to switch between Revenue, Cost, and Profit on one visual | Implement a **field parameter** |
| 11 | Bridge table connects patients to multiple diagnoses | **Many-to-many** relationship through bridge; consider bi-directional filter or DAX with CROSSFILTER |
| 12 | You want Prior Year to show as a percentage format but Current Year as currency | Use **dynamic format strings** on the calculation group items |
| 13 | Need third-party tool (Tabular Editor) to deploy model metadata | Enable **XMLA read/write endpoint** (requires Premium / Fabric capacity) |
| 14 | Direct Lake model must guarantee no silent performance degradation | **Disable automatic fallback** — queries that exceed guardrails will error instead of falling back to DQ |
| 15 | Incremental refresh partitions keep growing and model won't publish | Enable **large model storage format** to support higher partition counts |

---

*Last updated: {{ "now" | date: "%Y-%m-%d" }}*
