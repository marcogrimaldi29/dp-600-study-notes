---
layout: default
title: "Appendix D — Spark SQL Reference"
nav_order: 10
description: "DP-600 Spark SQL syntax appendix — Lakehouse tables, Delta operations (MERGE/OPTIMIZE/VACUUM), temp views, time travel, and the exam caveats that separate it from T-SQL."
permalink: /08-appendix-spark-sql-reference/
mermaid: true
---

# ✨ Appendix D — Spark SQL Reference & Exam Caveats
{: .no_toc }

> - Based on: *Apache Spark SQL & Delta Lake in Fabric* (Microsoft Learn)
> - 📁 [← Back to Home](/dp-600-study-notes/)

Syntax reference for **Spark SQL** as tested on DP-600. Spark SQL runs in **Notebooks and Spark job definitions** against **Lakehouse** Delta tables. The exam tests where Spark SQL applies (vs T-SQL), Delta-specific commands, and the read/write boundaries — not authoring big ETL scripts from scratch.
{: .fs-5 .fw-300 }

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 🧭 1 — Where Spark SQL Shows Up in Fabric

| Surface | Spark SQL? | Notes |
|---------|-----------|-------|
| **Notebook** | ✅ | `%%sql` magic cell, or `spark.sql("...")` in Python |
| **Spark Job Definition** | ✅ | Batch script execution |
| **Lakehouse** | ✅ | Reads/writes managed & external Delta tables |
| **Warehouse** | ❌ | Uses **T-SQL**, not Spark |
| **SQL analytics endpoint** | ❌ | T-SQL read-only surface over the Lakehouse |
| **KQL Database** | ❌ | Uses KQL |

> **Exam Caveat:** Spark SQL **writes** to the Lakehouse (unlike the read-only SQL analytics endpoint). If a scenario needs to **transform and persist** Lakehouse Delta tables, the answer is a **Notebook (Spark SQL / PySpark)** or a Dataflow — *not* the SQL endpoint. See [Appendix C — SQL](/dp-600-study-notes/07-appendix-sql-reference/) for the T-SQL side.
{: .warning }

---

## 🏗️ 2 — Creating Tables (Managed vs External)

```sql
-- Managed (data lives in the Lakehouse-managed Tables/ area)
CREATE TABLE sales USING DELTA AS
SELECT * FROM staging_sales;

-- External / unmanaged (you control the path)
CREATE TABLE ext_sales
USING DELTA
LOCATION 'Files/curated/sales';
```

| Table type | Data location | `DROP TABLE` deletes data? |
|------------|---------------|----------------------------|
| **Managed** | Lakehouse `Tables/` (engine-owned) | ✅ Yes — metadata **and** files |
| **External** | A path you specify (`LOCATION`) | ❌ No — drops metadata only |

> **Exam Caveat:** Dropping a **managed** table deletes the underlying files; dropping an **external** table leaves the data in place. This managed-vs-external distinction (and who owns the files) is a recurring exam question.
{: .warning }

> **Exam Tip:** In Fabric, `USING DELTA` is the default and preferred format — Delta enables ACID, time travel, and Direct Lake. Tables must be **Delta (V-Order optimized)** to be consumed by a Direct Lake semantic model.
{: .note }

---

## 🔺 3 — Delta Lake DML: MERGE, UPDATE, DELETE

Unlike the T-SQL Warehouse, Spark SQL on Delta **supports `MERGE`** — the upsert workhorse.

```sql
MERGE INTO target t
USING updates u  ON t.Id = u.Id
WHEN MATCHED THEN UPDATE SET t.Amount = u.Amount
WHEN NOT MATCHED THEN INSERT (Id, Amount) VALUES (u.Id, u.Amount)
WHEN NOT MATCHED BY SOURCE THEN DELETE;
```

| Command | Purpose |
|---------|---------|
| `MERGE INTO` | Upsert (insert + update + optional delete) |
| `UPDATE ... SET ... WHERE` | In-place row updates |
| `DELETE FROM ... WHERE` | Row deletes |
| `INSERT INTO / OVERWRITE` | Append / replace |

> **Exam Caveat:** `MERGE`, `UPDATE`, and `DELETE` on individual rows are **Delta/Spark capabilities** — the classic scenario "upsert changed rows into a Lakehouse table" points to a **Notebook**, not the SQL analytics endpoint (read-only) and historically not the Warehouse.
{: .warning }

---

## 🧹 4 — Delta Maintenance: OPTIMIZE, VACUUM, Z-ORDER

| Command | What it does | Exam angle |
|---------|--------------|-----------|
| `OPTIMIZE t` | Compacts many small files into fewer large ones | Fixes the "small files problem" |
| `OPTIMIZE t ZORDER BY (col)` | Co-locates data by column for faster filters | Query performance tuning |
| `VACUUM t RETAIN 168 HOURS` | Deletes files no longer referenced (default 7-day retention) | Reclaims storage |
| `DESCRIBE HISTORY t` | Shows the Delta transaction log versions | Auditing / time travel |

> **Exam Caveat:** `VACUUM` permanently removes old data files, which **breaks time travel** beyond the retention window. Running `VACUUM RETAIN 0 HOURS` can destroy the ability to roll back — the default 7-day retention exists to protect time travel. This trade-off is exam-favourite.
{: .warning }

> **Exam Tip:** The **small-files problem** (slow reads from thousands of tiny Parquet files) is solved with `OPTIMIZE` (compaction) and, for filter-heavy queries, `ZORDER BY`. Fabric also applies **V-Order** on write to boost Direct Lake/Power BI read speed.
{: .note }

---

## 🕰️ 5 — Time Travel

Delta keeps a versioned log, so you can query the past.

```sql
SELECT * FROM sales VERSION AS OF 12;
SELECT * FROM sales TIMESTAMP AS OF '2026-06-01T00:00:00';

-- Restore a table to an earlier version
RESTORE TABLE sales TO VERSION AS OF 12;
```

> **Exam Tip:** Time travel answers "recover from a bad load" or "compare to last week" scenarios — as long as `VACUUM` hasn't purged those versions. `VERSION AS OF` uses the integer log version; `TIMESTAMP AS OF` uses a point in time.
{: .note }

---

## 👁️ 6 — Views, Temp Views & the `%%sql` Magic

```python
# In a PySpark cell — register a DataFrame as a queryable view
df.createOrReplaceTempView("recent_sales")
```
```sql
%%sql
-- Now query it with Spark SQL in a separate cell
SELECT Category, SUM(Amount) FROM recent_sales GROUP BY Category;
```

| Object | Scope | Persisted? |
|--------|-------|-----------|
| **Temp view** (`createOrReplaceTempView`) | Current Spark session | ❌ |
| **Global temp view** (`global_temp.`) | All sessions on the cluster | ❌ |
| **View** (`CREATE VIEW`) | Catalog (metastore) | Definition only |
| **Table** (`saveAsTable`) | Lakehouse | ✅ Data persisted |

> **Exam Caveat:** A **temp view is session-scoped** — it vanishes when the Spark session ends and is invisible to the SQL analytics endpoint or Power BI. To expose data to downstream tools you must write a **managed/external Delta table** (`saveAsTable` / `CREATE TABLE`), not a temp view.
{: .warning }

---

## 🔤 7 — Spark SQL vs T-SQL (Key Differences)

| Aspect | Spark SQL (Lakehouse) | T-SQL (Warehouse) |
|--------|-----------------------|-------------------|
| Upsert | ✅ `MERGE` | Historically limited |
| Create-from-query | `CREATE TABLE ... USING DELTA AS SELECT` | `CREATE TABLE AS SELECT` (CTAS) |
| Bulk load | `spark.read...saveAsTable` | `COPY INTO` |
| String concat | `concat()` / `||` | `+` or `CONCAT()` |
| Top N | `... LIMIT 5` | `SELECT TOP 5` |
| Identifier quoting | Back-ticks `` `col` `` | Brackets `[col]` |
| Case sensitivity | Case-insensitive by default | Depends on collation |

> **Exam Caveat:** `LIMIT` (Spark SQL) vs `TOP` (T-SQL), and back-ticks vs brackets, are surface-specific. Mixing `SELECT TOP 5` into a Spark cell or `LIMIT` into a Warehouse query is a syntax-error distractor the exam uses.
{: .warning }

---

## ⚠️ 8 — Spark SQL Exam Traps (Rapid Fire)

1. **Spark SQL runs in Notebooks/Spark jobs**, writing Lakehouse Delta — not in the Warehouse or SQL endpoint.
2. **Managed table drop deletes files; external table drop keeps them.**
3. **Delta supports `MERGE`/`UPDATE`/`DELETE`** — the upsert answer for Lakehouse tables.
4. **`OPTIMIZE` fixes small files; `ZORDER BY` speeds filtered reads.**
5. **`VACUUM` breaks time travel** past the retention window (default 7 days).
6. **Temp views are session-scoped** — write a Delta table to expose data downstream.
7. **`LIMIT` not `TOP`; back-ticks not brackets** in Spark SQL.
8. **`USING DELTA` is required** for Direct Lake consumption (plus V-Order).
9. **Spark SQL and PySpark are interchangeable** within a Notebook (`spark.sql()` bridges them) — see [Appendix E — PySpark](/dp-600-study-notes/09-appendix-pyspark-reference/).
10. **Time travel needs the versions to still exist** — `VERSION AS OF` / `TIMESTAMP AS OF`.

> **Exam Tip:** For "which tool writes/transforms Lakehouse data" questions, remember the hierarchy: **Notebook (Spark)** for pro-code transforms and upserts, **Dataflow Gen2** for low-code, **Pipeline Copy** for bulk movement, **SQL endpoint** for read-only T-SQL queries.
{: .note }

---

[← Appendix C — SQL Reference](/dp-600-study-notes/07-appendix-sql-reference/) | [Appendix E — PySpark Reference →](/dp-600-study-notes/09-appendix-pyspark-reference/)
