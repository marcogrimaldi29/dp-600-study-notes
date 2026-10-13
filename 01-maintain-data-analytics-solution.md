---
layout: default
title: "01 — Maintain a Data Analytics Solution"
nav_order: 3
description: "Domain 1 (25–30%) — Security & governance, version control, deployment pipelines, XMLA endpoint, and reusable assets for the DP-600 exam."
permalink: /01-maintain-data-analytics-solution/
mermaid: true
---

# 🔒 Maintain a Data Analytics Solution
{: .no_toc }

> - Based on: *Microsoft Fabric documentation* (Microsoft Learn)
> - 📁 [← Back to Home](/dp-600-study-notes/)

Domain 1 accounts for **25–30%** of the DP-600 exam. It spans two broad skill areas: implementing security and governance, and maintaining the analytics development lifecycle across Microsoft Fabric and Power BI.

<details open markdown="block">
  <summary>Table of contents</summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 🛡️ Implement Security and Governance

### 👤 Workspace-Level Access Controls

Every Fabric workspace exposes four built-in roles. The exam expects you to know the precise boundary of each role.

| Capability | Admin | Member | Contributor | Viewer |
|---|:---:|:---:|:---:|:---:|
| Read and explore items | ✅ | ✅ | ✅ | ✅ |
| Create, edit, and delete items | ✅ | ✅ | ✅ | ❌ |
| Publish and update an app | ✅ | ✅ | ❌ | ❌ |
| Share items and grant access | ✅ | ✅ | ❌ | ❌ |
| Manage workspace settings and roles | ✅ | ❌ | ❌ | ❌ |
| Add or remove users (all roles) | ✅ | ❌ | ❌ | ❌ |
| Add Members/Contributors/Viewers | ✅ | ✅ | ❌ | ❌ |

> **Exam Tip:** Members can add other Members, Contributors, and Viewers, but they **cannot** add Admins. Only an Admin can grant Admin access.
{: .note }

> **Exam Caveat:** A Contributor can create and edit items inside the workspace but **cannot** share those items or publish an app. Sharing requires the Member (or Admin) role.
{: .warning }

### 📦 Item-Level Access Controls

Item-level security operates independently of workspace roles and allows granular distribution of content.

- **Sharing** — Any workspace Member or Admin can share a specific item (report, semantic model, dashboard) with users who have no workspace role at all. Share links can optionally grant Build permission or reshare permission.
- **App permissions** — When publishing a Power BI app, you define one or more audiences. Each audience can see a different subset of reports and dashboards. App consumers do not need a workspace role.
- **Build permission on semantic models** — Grants the ability to create new reports on top of a shared semantic model, use Analyze in Excel, or connect via XMLA. Build permission can be granted through sharing, app settings, or directly in the semantic model permissions pane.

> **Exam Tip:** Build permission is the key permission that enables downstream content creation. If a question mentions allowing someone to create reports on an existing semantic model without workspace access, the answer is Build permission.
{: .note }

### 🔐 Row-Level, Column-Level, Object-Level, and File-Level Security

```mermaid
graph TD
    A["Security Layers in Fabric"] --> B["Row-Level Security (RLS)"]
    A --> C["Column-Level Security (CLS)"]
    A --> D["Object-Level Security (OLS)"]
    A --> E["File-Level Access Control"]
    B --> B1["Filters rows (DAX in semantic models, T-SQL in Warehouse / SQL endpoint)"]
    C --> C1["Restricts columns (T-SQL GRANT in Warehouse / SQL endpoint, OneLake security)"]
    D --> D1["Hides tables or columns, including metadata (semantic model OLS)"]
    E --> E1["OneLake security roles (folders, tables, rows, columns)"]
```

Security can be defined at **two layers**: the **data store** (Warehouse / SQL analytics endpoint T-SQL security, or OneLake security roles on a lakehouse) and the **semantic model** (roles with DAX filters and object permissions).

| Feature | RLS | CLS | OLS |
|---|---|---|---|
| **What it restricts** | Rows returned by queries | Specific columns (query fails if it touches them) | Entire tables or columns — data **and** metadata |
| **Semantic model** | DAX filter on a role | Implemented as **OLS on columns** (`metadataPermission: none`) | Role `tablePermission` / `columnPermission` with `metadataPermission: none` |
| **Warehouse / SQL endpoint** | `CREATE SECURITY POLICY` + predicate function | `GRANT SELECT ON t(col1, col2)` / `DENY` | `GRANT` / `DENY` on objects |
| **Lakehouse (OneLake security)** | Row filter in a OneLake security role | Column selection in a OneLake security role | Table/folder scope of a role |
| **Authored in Power BI Desktop?** | ✅ Manage roles UI (also web modeling) | ✅ via **TMDL view** (no dedicated UI) | ✅ via **TMDL view** (no dedicated UI) |
| **User experience** | Data appears filtered; schema is visible | Column errors out | Object behaves as if it doesn't exist |

- **RLS** uses DAX predicates (e.g., `[Region] = USERPRINCIPALNAME()`) to restrict which rows a user sees. You assign users or security groups to roles in the service, and validate with **Test as role** (service) or **View as** (Desktop).
- **OLS** (semantic model) removes tables or columns from what a role can see — clients can't even discover them, and measures that reference them are implicitly secured. Define it in **TMDL view** in Power BI Desktop (a `createOrReplace` role script) or in **Tabular Editor** / XMLA tools.
- **CLS** is the term used for column permissions in the **Warehouse / SQL analytics endpoint** (T-SQL `GRANT`) and in **OneLake security**. In a semantic model, column-level security *is* column-level OLS.
- **File-level access control** uses **OneLake security** roles: an Admin or Member defines roles on a lakehouse that grant Read (or ReadWrite) on specific folders, tables, or schemas, optionally with row- and column-level rules. These are enforced across Spark, the SQL endpoint (user's identity mode), and Direct Lake on OneLake.

> **Exam Caveat:** OLS no longer *requires* an external tool — Power BI Desktop's **TMDL view** can script it. There is still no point-and-click OLS UI in Desktop, so Tabular Editor remains the classic answer. Read the options carefully: "only Tabular Editor/XMLA can do this" is now outdated.
{: .warning }

> **Exam Tip:** Semantic-model RLS and OLS apply **only to Viewers** (and users given Read/Build without a workspace role, e.g., via an app or sharing). Workspace **Admins, Members, and Contributors** have edit permission on the model, so RLS/OLS **never** applies to them — however they open the content. OneLake security roles behave the same way: they only restrict Viewers and Read-only users.
{: .note }

### 🏷️ Sensitivity Labels (Microsoft Purview Information Protection)

Sensitivity labels (Confidential, Highly Confidential, General, etc.) can be applied to Fabric items — semantic models, reports, dashboards, dataflows, and lakehouses. Labels propagate downstream: if a semantic model is labeled **Confidential**, reports built on it inherit that label automatically.

Key points for the exam:

- Labels are defined in the Microsoft Purview portal, not in Fabric.
- Assigning labels in Fabric requires the user to have an appropriate Purview license and the label to be published to them via a label policy.
- Labels persist when data is exported to Excel or PDF (the file carries the label's protection settings).
- Mandatory labeling can be enforced via tenant settings so that users must classify items before saving.

> **Exam Tip:** Sensitivity labels **travel with exports**. A report labeled Highly Confidential will produce an Excel export that retains the Highly Confidential protection. This is a frequent exam topic.
{: .note }

### ✅ Endorsing Items (Promoted vs. Certified)

Endorsement signals to consumers which items are trustworthy and authoritative.

| Aspect | Promoted | Certified | Master data |
|---|---|---|---|
| **Who can apply** | Anyone with **write permission** on the item (incl. Contributors) | Only reviewers authorized by the Fabric admin (security groups), who also have write permission | Only users authorized by the Fabric admin, who also have write permission |
| **Admin configuration required** | No | Yes — tenant setting + certifier groups (can be delegated to **domain admins**) | Yes — *Endorse master data* tenant setting + authorized users |
| **Applies to** | All items except Power BI dashboards | All items except Power BI dashboards | **Data items only** (e.g., lakehouses, semantic models) |
| **Intended use** | "This item is ready for sharing and reuse" | "This item meets organizational quality standards" | "This is the authoritative, single source of truth for this data" |
| **Governance implication** | Low barrier — self-service | High barrier — organizational control | High barrier — core reference data |

> **Exam Caveat:** Certification requires two things: (1) a Fabric admin (or a domain admin, if delegated) has enabled certification and authorized the reviewer, and (2) the reviewer has **write permission** on the item. Any user can *request* certification, but only authorized reviewers can apply it.
{: .warning }

---

## 🔄 Maintain the Analytics Development Lifecycle

### 🔀 Version Control for a Workspace (Git Integration)

Fabric workspaces can connect to a remote Git repository in **Azure DevOps**, **GitHub**, or **GitHub Enterprise** (cloud-hosted only). This enables source control for items such as semantic models, reports, notebooks, lakehouses, warehouses, and pipelines.

```mermaid
flowchart LR
    subgraph Remote["Git Repository"]
        B1["main branch"]
        B2["feature branch"]
    end
    subgraph Fabric["Fabric Workspace"]
        W["Workspace Items"]
    end
    W -- "Commit" --> B1
    B1 -- "Update" --> W
    B2 -- "Pull Request" --> B1
```

**Configuration steps:**

1. Open Workspace settings and navigate to Git integration (only a **workspace Admin** can connect the repository).
2. Connect to an Azure DevOps, GitHub, or GitHub Enterprise repository and select a branch and folder.
3. Items in the workspace are synced to/from the branch. Changes made in the workspace appear as uncommitted; the user commits when ready.
4. To work in isolation, use **Branch out to another workspace** from the Source control pane — it creates a feature branch *and* a feature workspace connected to it.

**Branching strategies the exam expects you to know:**

- **Single branch (main):** Simplest approach. All developers commit to main. Suitable for small teams.
- **Feature branching:** Developers create short-lived branches, make changes, and merge via pull request. Recommended for team collaboration.
- **Environment branching:** Separate branches map to Dev, Test, and Prod workspaces (often combined with deployment pipelines).

> **Exam Tip:** Git integration in Fabric connects **one branch per workspace**. If a question asks how two developers work on different features simultaneously, the answer involves separate feature branches and separate workspaces (**Branch out to another workspace**), or working locally with PBIP files.
{: .note }

### 📁 Power BI Desktop Projects (.pbip)

The PBIP format saves a Power BI project as a folder structure of human-readable text files instead of a single compressed binary file. The report is saved in **PBIR** format (one JSON file per page, visual, and bookmark — now GA and the default) and the semantic model in **TMDL** (one file per table, role, culture, etc.).

| Aspect | .pbix | .pbip |
|---|---|---|
| **Format** | Single compressed binary file | Folder with JSON and metadata files |
| **Version control friendly** | ❌ — binary diffs are meaningless | ✅ — text-based diffs show exactly what changed |
| **Merge conflicts** | Cannot be resolved in standard tools | Resolvable with standard merge tooling |
| **Sensitivity label support** | ✅ | ✅ |
| **Sharing as a file** | Easy — single file | Requires the whole folder |
| **Recommended for Git workflows** | ❌ | ✅ |

**PBIP folder structure:**

```
project/
├── Sales.pbip                      ← entry point (opens report + model)
├── Sales.Report/
│   ├── definition.pbir             ← required; points to the semantic model
│   └── definition/                 ← PBIR: pages/, visuals/, report.json …
├── Sales.SemanticModel/
│   ├── definition.pbism            ← required
│   └── definition/                 ← TMDL: tables/, relationships.tmdl …
│                                     (or model.bim if saved as TMSL)
└── .gitignore                      ← excludes .pbi/localSettings.json & cache.abf
```

> **Exam Caveat:** PBIP is the recommended format for Git-based workflows but is **not** a replacement for PBIX in all scenarios. There is no "upload a folder" action like uploading a PBIX — you publish from Desktop or sync the files through **Fabric Git integration** / Fabric item APIs. PBIP is designed for developer workflows and source control.
{: .warning }

### 🚀 Deployment Pipelines

Deployment pipelines provide a managed promotion path for Fabric content across environments.

```mermaid
flowchart LR
    D["Development"] -->|"Compare & Deploy"| T["Test"]
    T -->|"Compare & Deploy"| P["Production"]
    style D fill:#264653,stroke:#2a9d8f,color:#e9c46a
    style T fill:#264653,stroke:#e9c46a,color:#e9c46a
    style P fill:#264653,stroke:#e76f51,color:#e9c46a
```

**Key concepts:**

- A pipeline has up to **10 stages** (commonly three: Dev, Test, Prod).
- Each stage maps to a Fabric workspace.
- You can **compare stages** to see which items differ, which are new, and which are missing.
- **Deployment rules** allow you to change parameters (data source connection strings, lakehouse references) at each stage so that Test points to test data and Prod points to production data.
- To deploy you must be a **pipeline admin** *and* at least a **Contributor** in both the source and target workspaces. Assigning a workspace to a stage requires workspace **Admin**.
- Pipelines support selective deployment — you can choose specific items rather than deploying everything.
- Most semantic models **auto-bind** to the paired item in the target stage, but a **Direct Lake semantic model does not** — it keeps pointing at the source-stage lakehouse/warehouse until you add a data source rule.

**Deployment rules** are critical for the exam. They allow overriding:

- Data source connection strings (e.g., a SQL Server name)
- Parameter values
- Lakehouse or warehouse bindings

> **Exam Tip:** Deployment rules solve the problem of "how do I point my Test semantic model to a test database without manually editing connections." If a scenario describes promoting content but needing different data sources per environment, the answer is deployment rules.
{: .note }

> **Exam Caveat:** Deployment pipelines deploy **metadata only** — they do not copy data. After deploying a semantic model to Production, you still need to refresh it to load data from the production data source.
{: .warning }

### 🔍 Impact Analysis of Downstream Dependencies

Fabric provides a **lineage view** for every workspace, showing how items relate to each other (data sources, dataflows, lakehouses, semantic models, reports, dashboards).

**Impact analysis** lets you select a specific item and see everything downstream that depends on it. This is essential before making breaking changes.

- If you modify a **lakehouse** table schema, impact analysis shows which dataflows, semantic models, and reports consume that table.
- If you change a **semantic model** measure, impact analysis shows which reports use that measure.
- The lineage view covers cross-workspace dependencies — a report in Workspace B built on a semantic model in Workspace A will appear in the analysis.

> **Exam Tip:** Before renaming a column in a warehouse or lakehouse, always run impact analysis to identify downstream semantic models and reports that reference that column. The exam frequently tests whether you know to check lineage before making schema changes.
{: .note }

### 🔌 XMLA Endpoint for Semantic Models

The XMLA (XML for Analysis) endpoint exposes Power BI semantic models using the same protocol as SQL Server Analysis Services (SSAS). This enables enterprise tooling.

**Read-only XMLA** (available with Power BI Premium, PPU, or Fabric capacity):

- Query semantic models with SSMS, DAX Studio, Excel, or third-party tools.
- Run DMV queries for diagnostics.

**Read/write XMLA** (must be explicitly enabled in tenant or capacity settings):

- Deploy and modify semantic model metadata (partitions, measures, roles, perspectives).
- Tools: **Tabular Editor**, **ALM Toolkit**, **SSMS**, **Visual Studio (Analysis Services projects)**.
- Use cases: CLS/OLS configuration, automated deployments, CI/CD scripting, incremental refresh partition management.

| Tool | Primary Use |
|---|---|
| Tabular Editor | Full model authoring and editing — measures, partitions, roles, CLS, OLS |
| ALM Toolkit | Schema comparison and selective deployment between models |
| SSMS | Ad-hoc DAX/MDX queries, DMVs, process commands |
| DAX Studio | Performance tuning, query profiling, DMV exploration |

> **Exam Caveat:** Read/write XMLA is **disabled by default**. If a question describes a scenario where a developer cannot deploy changes via Tabular Editor, check whether the XMLA read/write setting is enabled at the tenant or capacity level.
{: .warning }

> **Exam Tip:** Changes made via XMLA **do not appear** in the standard Power BI Desktop round-trip. If someone modifies a model through Tabular Editor and then another developer opens the PBIX in Desktop and republishes, the XMLA changes may be overwritten. Also, once a model has been modified or published through the XMLA endpoint, it **can no longer be downloaded as a .pbix** from the service. This is a governance concern the exam may test.
{: .note }

### ♻️ Reusable Assets

#### Power BI Template Files (.pbit)

A PBIT file contains the report layout, semantic model schema, queries, and parameters — but **no data**. When opened, it prompts the user to enter parameter values and then refreshes.

- Use case: standardized report templates distributed across teams.
- The recipient needs appropriate data source credentials.

#### Power BI Data Source Files (.pbids)

A PBIDS file contains pre-configured data source connection information (server, database, type). When opened in Power BI Desktop, it launches directly into the connection experience for that source.

- Use case: simplify getting started with the correct data source — users do not need to manually type server names.

#### Shared Semantic Models

Shared semantic models enable a "single version of truth" pattern. A central team publishes a certified semantic model, and report creators across the organization build reports on top of it using a live connection or DirectQuery connection.

- Consumers need **Build permission** on the semantic model.
- Shared semantic models appear in the **OneLake catalog** (formerly the OneLake data hub) for discovery.
- Combining shared semantic models with endorsement (Certified) and sensitivity labels creates a governed, reusable data layer.

```mermaid
flowchart TD
    DS["Data Sources"] --> SM["Certified Shared Semantic Model"]
    SM --> R1["Sales Report"]
    SM --> R2["Finance Report"]
    SM --> R3["Executive Dashboard"]
    SM --> R4["Analyze in Excel"]
    SM --> R5["Third-Party Tool via XMLA"]
```

> **Exam Tip:** The combination of a **certified shared semantic model** + **Build permission** + **RLS** is the recommended governance pattern for large organizations. Expect the exam to present scenarios where this trio is the answer.
{: .note }

---

## 📋 Scenario-Based Quick Reference

| # | Scenario | Answer |
|---|---|---|
| 1 | A user needs to create reports on a semantic model but has no workspace role. | Grant **Build permission** on the semantic model via sharing. |
| 2 | A developer needs to configure OLS to hide a salary column from most users. | Define the role's column permission (`metadataPermission: none`) in Power BI Desktop's **TMDL view**, or with **Tabular Editor** via the **XMLA read/write endpoint**. There is no point-and-click OLS UI. |
| 3 | The team wants to track changes to a semantic model over time with meaningful diffs. | Use the **PBIP** format and store it in a **Git repository**. |
| 4 | The Test workspace needs to point to a test SQL database after deployment. | Configure **deployment rules** in the deployment pipeline to override the connection string. |
| 5 | A workspace Admin wants to ensure only authorized users can mark items as Certified. | The **Fabric admin** must enable certification and designate certifier security groups in the admin portal (optionally delegated to domain admins). |
| 6 | An exported Excel file from a Highly Confidential report must retain protection. | **Sensitivity labels** propagate to exports automatically — no extra configuration needed. |
| 7 | Two developers want to work on separate features in the same Fabric workspace simultaneously. | Each developer creates a **feature branch** linked to a **separate workspace**, then merges via pull request. |
| 8 | After deploying a semantic model to Production via pipeline, users see no data. | Deployment pipelines copy **metadata only**. A **data refresh** must be triggered in Production. |
| 9 | A workspace Member shares a report with a colleague via a link. The colleague can view it but cannot create new reports from the model. | The share link was granted **without Build permission**. Re-share with Build permission enabled. |
| 10 | A developer modifies partitions via Tabular Editor, but a colleague overwrites the changes by republishing the PBIX. | XMLA changes are **overwritten by PBIX publish**. Use PBIP + Git or only XMLA-based deployments to avoid this. |
| 11 | Before renaming a lakehouse column, the team needs to know which reports will break. | Use **lineage view** and **impact analysis** in the Fabric workspace. |
| 12 | A Contributor tries to share a report but the option is grayed out. | Contributors **cannot share items**. The user needs at least a **Member** role. |
| 13 | The organization wants all Fabric items to be classified before saving. | Enable **mandatory sensitivity labeling** in the Fabric tenant settings. |
| 14 | A team distributes a standardized report layout without data so that each region can connect to its own source. | Distribute a **.pbit template file** with parameters for the data source. |
| 15 | An external tool cannot connect to a semantic model via XMLA. | Verify that **XMLA read (or read/write) is enabled** at the tenant or capacity level and that the user has at least **Read and Build permission**. |
| 16 | The customer master table in a lakehouse must be flagged as the organization's single source of truth. | Apply the **Master data** endorsement (requires admin authorization + write permission). |
| 17 | Viewers of a lakehouse must only read the `Tables/Sales` folder and only rows for their region. | Create a **OneLake security role** scoped to that table with a row-level rule; assign the Viewers to it. |
| 18 | After deploying a Direct Lake semantic model from Dev to Test, Test reports still show Dev data. | Direct Lake models **don't auto-bind** across stages — add a **data source deployment rule** pointing to the Test lakehouse. |
| 19 | What is the minimum access needed to deploy content from Dev to Test? | **Pipeline admin** plus **Contributor** on both the Dev and Test workspaces (assigning a workspace to a stage needs workspace Admin). |

---

*These notes cover the "Maintain a data analytics solution" domain of the DP-600 exam. For full coverage, pair these notes with hands-on practice in a Fabric trial or capacity environment.*

---

[← 00 — Fabric Prerequisites](/dp-600-study-notes/00-fabric-prerequisites/) | [02 — Prepare Data →](/dp-600-study-notes/02-prepare-data/)