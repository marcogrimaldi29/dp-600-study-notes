---
layout: default
title: "00 — Fabric Prerequisites"
nav_order: 2
description: "Microsoft Fabric fundamentals — OneLake, workspaces, lakehouses, warehouses, capacities, Delta Lake, and core analytics concepts needed for the DP-600 exam."
permalink: /00-fabric-prerequisites/
mermaid: true
---

# 📘 Fabric Prerequisites
{: .no_toc }

> - Based on: *Microsoft Fabric documentation* (Microsoft Learn)
> - 📁 [← Back to Home](/dp-600-study-notes/)

Background knowledge you need before diving into DP-600 exam topics. This page covers the foundational Fabric concepts that every Analytics Engineer question assumes you already understand.

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 🏗️ Microsoft Fabric Architecture Overview

Microsoft Fabric is a **SaaS analytics platform** that unifies data engineering, data warehousing, data science, real-time intelligence, data integration, and business intelligence into a single product. Instead of stitching together separate Azure services, Fabric provides one integrated experience built on a shared foundation.

### 📌 Workloads at a Glance

| Workload | Purpose |
|---|---|
| **Data Engineering** | Spark notebooks, lakehouses, data pipelines |
| **Data Warehouse** | Full T-SQL warehouse with DML/DDL |
| **Data Science** | ML models, experiments, notebooks with MLflow |
| **Real-Time Intelligence** | KQL databases, eventstreams, Real-Time dashboards |
| **Data Factory** | Dataflows Gen2, pipelines (orchestration), Copy job, Mirroring |
| **Databases** | SQL database in Fabric (OLTP), Cosmos DB in Fabric (NoSQL) |
| **Power BI** | Semantic models, reports, dashboards, paginated reports |

```mermaid
graph TD
    Fabric["Microsoft Fabric (SaaS)"]
    Fabric --> DE["Data Engineering"]
    Fabric --> DW["Data Warehouse"]
    Fabric --> DS["Data Science"]
    Fabric --> RTI["Real-Time Intelligence"]
    Fabric --> DF["Data Factory"]
    Fabric --> DB["Databases"]
    Fabric --> PBI["Power BI"]
    DE --> OL["OneLake (ADLS Gen2)"]
    DW --> OL
    DS --> OL
    RTI --> OL
    DF --> OL
    DB --> OL
    PBI --> OL
```

All workloads read from and write to **OneLake**, which is the key architectural differentiator. There is no data duplication across workloads — one copy of data serves all engines.

> **Exam Tip:** Questions often test whether you know which workload owns a given item. For example, a *KQL database* belongs to Real-Time Intelligence, not Data Engineering.
{: .note }

---

## 🗄️ OneLake

OneLake is Fabric's **single, unified data lake** for the entire tenant. Think of it as "OneDrive for data." Under the hood it runs on **Azure Data Lake Storage Gen2** with a hierarchical namespace enabled.

### 💎 Key Characteristics

- **One copy of data** — all Fabric workloads access the same physical files, eliminating silos and redundant copies.
- **Delta Parquet format** — tables are stored as Delta Lake (Parquet files plus a `_delta_log` transaction log).
- **Shortcuts** — virtual pointers to data in external locations (other OneLake paths, ADLS Gen2, Amazon S3, Google Cloud Storage) without copying.
- **Hierarchical namespace** — organizes data as workspace → item → folders, addressable with ADLS Gen2 paths such as `abfss://<workspace>@onelake.dfs.fabric.microsoft.com/<item>.<itemtype>/Tables/<table>`.
- **Automatic discovery** — every Fabric item that produces data lands in OneLake automatically.

> **Exam Caveat:** Shortcuts do **not** copy data. They create a reference. If the source is deleted or access is revoked, the shortcut breaks. Expect scenario questions about this.
{: .warning }
---

## 📂 Workspaces

A workspace is the **primary container** for all Fabric items (lakehouses, warehouses, notebooks, reports, pipelines, etc.). Every workspace is assigned to exactly one capacity.

### 👤 Workspace Roles

| Role | Permissions |
|---|---|
| **Admin** | Full control — manage membership, delete the workspace, manage all items |
| **Member** | Create, edit, delete items; share items; cannot manage workspace settings or membership |
| **Contributor** | Create, edit, delete items; cannot share items or manage membership |
| **Viewer** | View items only; cannot edit, create, or delete |

> **Exam Tip:** The difference between **Member** and **Contributor** is sharing. Members can share items with other users; Contributors cannot. This distinction appears frequently in permission-related questions.
{: .note }

### 🔗 Capacity Assignment

Every workspace must be backed by a capacity (or trial/PPU for Power BI-only workloads). Workspace admins assign the workspace to a specific capacity, which determines available compute and the SKU feature set.

---

## 🏠 Lakehouses

A lakehouse combines the flexibility of a data lake with the query power of a warehouse. It is the central storage item for Data Engineering workloads.

### 🧩 Anatomy of a Lakehouse

- **Tables section** — managed Delta Lake tables queryable via Spark and the SQL analytics endpoint.
- **Files section** — unstructured or semi-structured files (CSV, JSON, images) stored in OneLake but not registered as tables.
- **SQL analytics endpoint** — an auto-generated, **read-only** T-SQL endpoint that lets you query Delta tables using SQL without Spark. You can create **views, functions, and stored procedures** and apply **SQL security (RLS, CLS, OLS, dynamic data masking)**, but you **cannot create tables or run INSERT, UPDATE, or DELETE**.

> **Exam Caveat:** The SQL analytics endpoint is **read-only for data**. You cannot run DML or create tables against a lakehouse via T-SQL. If a question asks you to perform T-SQL writes, the answer involves a warehouse, not a lakehouse endpoint.
{: .warning }

### ✅ When to Use a Lakehouse

- Unstructured or semi-structured data that needs flexible exploration.
- Spark-based data engineering transformations (PySpark, Spark SQL).
- Data science workloads that read Delta tables via notebooks.
- Scenarios where you want one storage layer serving both engineers and analysts.

---

## 🏢 Warehouses

A Fabric warehouse is a **full relational data warehouse** with complete T-SQL DDL and DML support. Data is still stored as Delta Parquet in OneLake, but the engine provides a traditional SQL experience.

### 🔧 Key Capabilities

- **Full DML** — INSERT, UPDATE, DELETE, MERGE (GA), COPY INTO.
- **Full DDL** — CREATE TABLE, ALTER TABLE, CREATE VIEW, CREATE SCHEMA, stored procedures.
- **Surrogate keys** — `BIGINT IDENTITY` columns (system-managed; unique but not sequential).
- **Cross-database queries** — query tables across warehouses and lakehouse SQL endpoints in the same workspace using three-part naming.
- **Security** — column-level security, row-level security, dynamic data masking, object-level permissions.

```mermaid
graph TD
    Q["Need to write data via T-SQL?"]
    Q -->|Yes| WH["Use Warehouse"]
    Q -->|No| Q2["Primary engine is Spark?"]
    Q2 -->|Yes| LH["Use Lakehouse"]
    Q2 -->|No| Q3["Need multi-table transactions?"]
    Q3 -->|Yes| WH
    Q3 -->|No| LH
```

### ⚖️ Lakehouse vs Warehouse Comparison

| Feature | Lakehouse | Warehouse |
|---|---|---|
| Storage format | Delta Parquet in OneLake | Delta Parquet in OneLake |
| Write via T-SQL | No (read-only SQL endpoint) | Yes (full DML/DDL) |
| Write via Spark | Yes | No |
| Cross-database queries | As a source (read) | Full support |
| Row/column-level security | Yes — T-SQL RLS/CLS on the SQL endpoint, or OneLake security roles | Yes (T-SQL RLS/CLS) |
| Best for | Engineers, data scientists | Analysts, SQL-centric teams |

> **Exam Tip:** Both lakehouse and warehouse store data as Delta Parquet in OneLake. The difference is the **engine and write path**, not the storage format.
{: .note }

---

## 💰 Capacities and Licensing

Fabric compute is metered through **Capacity Units (CUs)**. Every operation — Spark jobs, SQL queries, dataflows, Power BI refreshes — consumes CUs from the assigned capacity.

### 💲 SKU Overview

| SKU | CUs | Power BI equivalent | Free-license viewers can consume Power BI content? |
|---|---|---|---|
| F2 / F4 | 2 / 4 | — | ❌ (viewers need Pro/PPU) |
| F8 | 8 | A1 | ❌ |
| F16 | 16 | A2 | ❌ |
| F32 | 32 | A3 | ❌ |
| **F64** | **64** | **P1** (8 v-cores) | ✅ |
| F128 | 128 | P2 | ✅ |
| F256+ | 256+ | P3–P5 | ✅ |
| Trial | 64 | — | ✅ (acts like F64) |

- **F SKUs** — purchased through Azure, pay-as-you-go or reserved. **Every F SKU runs every Fabric workload** — smaller SKUs just have less compute. Can be scaled and **paused** to stop billing.
- **P SKUs** — legacy Power BI Premium per-capacity, **being retired**; **P1 = F64**, P2 = F128. New deployments should use F SKUs.
- **PPU (Premium Per User)** — a per-user Power BI license, **not** a Fabric capacity: it can't run lakehouses, warehouses, or notebooks.
- **Trial capacity** — free 60-day trial with F64-equivalent capacity.

> **Exam Caveat:** The F64 line is about **licensing, not features**. On **F64 or larger** (or any P SKU), users with only a Free license and the **Viewer** role can consume Power BI content. **Below F64**, every Power BI consumer needs a **Pro or PPU** license — even though the capacity still runs all Fabric workloads. Also, F SKUs can be **paused**; P SKUs cannot.
{: .warning }

---

## 🔺 Delta Lake Fundamentals

Delta Lake is the default table format across all of Fabric. Understanding it is non-negotiable for the DP-600.

### ⚙️ Core Properties

- **ACID transactions** — every write is atomic; readers never see partial writes. The `_delta_log` folder contains JSON commit files that track each transaction.
- **Time travel** — query previous versions of a table using `VERSION AS OF` or `TIMESTAMP AS OF` in Spark SQL. Useful for auditing and rollback.
- **Schema enforcement** — writes that do not match the table schema are rejected by default, preventing corrupt data.
- **Schema evolution** — optionally allow new columns by setting `.option("mergeSchema", "true")` on a write operation.

### 🚀 Fabric-Specific Optimizations

- **V-Order** — a Fabric-specific write-time optimization that sorts and compresses Parquet files for faster reads, especially by **Power BI Direct Lake** (and, to a lesser degree, the SQL engines). It adds write cost and gives Spark no inherent read benefit.
- **Optimize Write** — bin-packs data before writing to reduce the small-file problem.
- **Resource profiles** — predefined Spark configuration sets. **New workspaces default to `writeHeavy`**, which has **V-Order disabled** (`spark.sql.parquet.vorder.default=false`). Switch to **`readHeavyForPBI`** (V-Order on, Optimize Write on) for tables consumed by Direct Lake, or `readHeavyForSpark` for Spark-read workloads.

> **Exam Tip:** V-Order is **not** on by default in new workspaces. If a scenario asks how to speed up a **Direct Lake** model over Spark-written tables, enable V-Order (session setting, table property `delta.parquet.vorder.enabled`, or the `readHeavyForPBI` profile) and run `OPTIMIZE ... VORDER` on existing tables.
{: .note }

---

## 📊 Power BI in Fabric

Power BI is both a standalone workload and the analytics layer that sits on top of every other Fabric item.

### 🧱 Key Items

- **Semantic model (dataset)** — the data model that defines tables, relationships, measures, and calculations. This is what reports query against.
- **Report** — interactive visualizations built on a semantic model.
- **Dashboard** — a single canvas of pinned tiles from one or more reports.
- **Paginated report** — pixel-perfect, print-ready reports (SSRS-style) for invoices, statements, and operational documents.

### 🔌 Integration with Fabric Items

- **Direct Lake mode** — a new storage mode where Power BI reads Delta Parquet files directly from OneLake without import or DirectQuery. Combines import-speed performance with DirectQuery-level freshness.
- **No more default semantic models** — since **September 5, 2025**, creating a lakehouse, warehouse, or mirrored database no longer auto-creates a "default" semantic model, and existing ones were decoupled into independent semantic models by November 30, 2025. You now **create a semantic model explicitly** and choose its tables.
- **SQL analytics endpoint** — Power BI can also use DirectQuery against a lakehouse SQL endpoint or warehouse.

> **Exam Caveat:** Direct Lake is **not** the same as DirectQuery. Direct Lake reads columnar data from Parquet files in memory — it does not issue SQL queries to a source. Fallback to DirectQuery only applies to **Direct Lake on SQL analytics endpoint** (and can be disabled); **Direct Lake on OneLake never falls back** — an unsupported or over-guardrail query errors instead. See Domain 3 for the full OneLake-vs-SQL comparison.
{: .warning }

---

## 🔑 Key Concepts for Analytics Engineers

### 📐 Star Schema Basics

The star schema is the recommended modeling pattern for analytical workloads in Fabric and Power BI.

- **Fact tables** — store measurable events (sales transactions, page views, shipments). Contain foreign keys and numeric measures. Typically large and narrow.
- **Dimension tables** — store descriptive attributes (customer name, product category, date). Contain surrogate keys and text/category columns. Typically small and wide.
- **Relationships** — fact tables reference dimension tables via foreign keys, forming a star shape.

### 🔄 Slowly Changing Dimensions (SCD)

| Type | Behavior | Implementation |
|---|---|---|
| **SCD Type 1** | Overwrite old value | UPDATE the dimension row in place |
| **SCD Type 2** | Keep full history | Add new row with effective/expiry dates and a current flag |
| **SCD Type 3** | Track limited history | Add a "previous value" column alongside the current value |

> **Exam Tip:** SCD Type 2 is the most commonly tested. Know that it requires **surrogate keys** (not natural keys) because the same business entity will have multiple rows.
{: .note }

### ✅ Data Modeling Best Practices

- Prefer **star schemas** over flat/wide tables for Power BI performance.
- Avoid bi-directional relationships unless absolutely necessary.
- Use **integer surrogate keys** for joins instead of large text business keys.
- Keep fact tables narrow — push descriptive attributes into dimensions.
- Define explicit **date dimensions** rather than relying on auto date/time.

---

## 📋 Scenario Quick Reference

| Scenario | Recommended Item | Why |
|---|---|---|
| Ingest raw CSV/JSON and transform with Spark | Lakehouse | Spark-native, files section for raw landing |
| SQL analysts need to write stored procedures | Warehouse | Full T-SQL DDL/DML support |
| Data scientists need to train ML models on tabular data | Lakehouse | Spark notebooks read Delta tables natively |
| Build a star schema with row-level security via T-SQL | Warehouse | RLS and fine-grained SQL security |
| Create a Power BI report with near-real-time data from OneLake | Lakehouse + Direct Lake | Direct Lake reads Delta files with no import lag |
| Reference external S3 data without copying | Lakehouse shortcut | Shortcuts point to external storage without data movement |
| Orchestrate a multi-step ELT pipeline | Data Factory pipeline | Pipelines coordinate activities across items |
| Stream IoT events and query with KQL | Real-Time Intelligence | Eventstreams and KQL databases handle streaming data |
| Need cross-database SQL joins across items | Warehouse | Cross-database queries with three-part naming |
| Store unstructured images alongside tabular data | Lakehouse (Files section) | Files section holds any format; tables hold Delta only |
| Operational app needs high-frequency row updates (OLTP) | SQL database in Fabric | Transactional engine, auto-replicated to OneLake for analytics |
| Replicate an Azure SQL / Snowflake / Cosmos DB source for reporting with minimal setup | Mirroring | Free, continuous CDC replica in OneLake with a SQL analytics endpoint |

---

[← Back to Home](/dp-600-study-notes/) | [01 — Maintain a Data Analytics Solution →](/dp-600-study-notes/01-maintain-data-analytics-solution/)