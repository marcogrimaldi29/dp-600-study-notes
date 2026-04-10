---
layout: home
title: DP-600 Study Notes
nav_order: 1
description: "DP-600 Microsoft Fabric Analytics Engineer Associate — complete study notes covering all three exam domains with Mermaid diagrams, semantic model patterns, DAX references, and exam caveats."
permalink: /
mermaid: true
---

# 📘 DP-600 Study Notes
{: .no_toc }

**Microsoft Fabric Analytics Engineer Associate**
{: .fs-5 .fw-300 }

[Start Studying →](/dp-600-study-notes/00-fabric-prerequisites){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View on GitHub](https://github.com/marcogrimaldi29/dp-600-study-notes){: .btn .fs-5 .mb-4 .mb-md-0 target="_blank" }

---

> 🏠 These notes are maintained by **[Marco Grimaldi](https://www.linkedin.com/in/marco-grimaldi29/)** and based on the **[official Microsoft documentation](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/)**.
> Find more certification guides, study tips, and tech content at **[🌐 marcogrimaldi29.com](https://marcogrimaldi29.com)**.
> *Not affiliated with or endorsed by Microsoft. Always verify against the latest Microsoft documentation.*

---

## 🎯 Exam Overview

| Detail | Value |
|--------|-------|
| 🏅 Certification | **Microsoft Certified: Fabric Analytics Engineer Associate** |
| 📝 Passing Score | **700 / 1000** |
| 💶 Price | **~€126 EUR** *(varies by country, VAT may apply)* |
| ⏱️ Duration | **100 minutes** *(120 min seat time incl. check-in)* |
| ❓ Question Types | MCQ, multi-select, drag-and-drop, case studies |
| 🔁 Renewal | **Annual** — free online assessment on Microsoft Learn |
| 🛡️ Prerequisite | **None** *(recommended: experience with Microsoft Fabric & Power BI)* |
| 📚 Languages | English, Japanese, Chinese (Simplified), German, French, Spanish, Portuguese (Brazil) |

---

## 📊 Domain Weights

```mermaid
pie title DP-600 — Official Exam Domain Weights
    " Prepare Data (45–50%)" : 50
    " Maintain a Data Analytics Solution (25–30%)" : 25
    " Implement & Manage Semantic Models (25–30%)" : 25
```

| # | Domain | Weight | Key Focus Areas |
|---|--------|--------|----------------|
| 1 | [Maintain a Data Analytics Solution](./01-maintain-data-analytics-solution/) | **25–30%** | Security & governance, version control, deployment pipelines, XMLA endpoint |
| 2 | [Prepare Data](./02-prepare-data/) | **45–50%** | Data connections, star schemas, transforms, SQL, KQL, DAX queries |
| 3 | [Implement & Manage Semantic Models](./03-implement-manage-semantic-models/) | **25–30%** | Storage modes, DAX calculations, Direct Lake, relationships, optimization |

---

## 🗂️ Notes Index

<div style="display:grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap:1rem; margin: 1.5rem 0;">

<div style="border:1px solid #5f6368; border-radius:8px; padding:1rem; background:#2d2f31;">
<h3 style="margin-top:0;">📘 Prerequisites</h3>
<p>Microsoft Fabric architecture: OneLake, workspaces, lakehouses, warehouses, capacities, and core analytics concepts.</p>
<a href="./00-fabric-prerequisites/" class="btn btn-outline fs-5">Read →</a>
</div>

<div style="border:1px solid #7c4dff; border-radius:8px; padding:1rem; background:#2d2f31;">
<h3 style="margin-top:0;">🔒 Domain 1 — Maintain Analytics Solution</h3>
<p><strong>25–30%</strong> of exam. Security, governance, version control, deployment pipelines, XMLA endpoint, reusable assets.</p>
<a href="./01-maintain-data-analytics-solution/" class="btn btn-outline fs-5">Read →</a>
</div>

<div style="border:1px solid #b388ff; border-radius:8px; padding:1rem; background:#2d2f31;">
<h3 style="margin-top:0;">🔄 Domain 2 — Prepare Data</h3>
<p><strong>45–50%</strong> of exam. Data connections, star schemas, transforms, SQL, KQL, DAX, Visual Query Editor.</p>
<a href="./02-prepare-data/" class="btn btn-outline fs-5">Read →</a>
</div>

<div style="border:1px solid #f4b400; border-radius:8px; padding:1rem; background:#2d2f31;">
<h3 style="margin-top:0;">📐 Domain 3 — Semantic Models</h3>
<p><strong>25–30%</strong> of exam. Storage modes, DAX calculations, Direct Lake, relationships, calculation groups, optimization.</p>
<a href="./03-implement-manage-semantic-models/" class="btn btn-outline fs-5">Read →</a>
</div>

<div style="border:1px solid #e57373; border-radius:8px; padding:1rem; background:#2d2f31;">
<h3 style="margin-top:0;">⚡ Quick Reference Cheatsheet</h3>
<p>Key numbers, decision matrices, DAX patterns, service comparisons, exam traps, and pre-exam checklist.</p>
<a href="./04-quick-reference-cheatsheet/" class="btn btn-outline fs-5">Read →</a>
</div>

</div>

---

## 🧠 How to Use These Notes

These notes are structured to follow the **official DP-600 study guide** domain order. The recommended reading flow:

```mermaid
flowchart LR
    PRE["📘 Prerequisites\n(Fabric fundamentals)"]
    D1["🔒 Domain 1\nMaintain Analytics\nSolution\n25–30%"]
    D2["🔄 Domain 2\nPrepare Data\n45–50%"]
    D3["📐 Domain 3\nSemantic Models\n25–30%"]
    SHEET["⚡ Cheatsheet\n(last-minute)"]

    PRE --> D1 --> D2 --> D3 --> SHEET
```

### 💡 Study Tips

- 🎯 The exam tests **"which analytics approach?"** — think in trade-offs between Import, DirectQuery, Direct Lake, and composite models
- 📐 **DAX is heavily tested** — know CALCULATE, iterators, table filtering, variables, and calculation groups
- 🔒 Know **security layers** — workspace, item, row-level (RLS), column-level (CLS), and object-level (OLS)
- ⚠️ Each section has **`Exam Caveats`** callouts — these are high-frequency exam traps
- 🔄 Each domain ends with a **quick-reference scenario table** — great for final review

---

## 📄 Official Resources

| Resource | Link |
|----------|------|
| 🎓 Microsoft Certification Path | [Fabric Analytics Engineer Associate](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/) |
| 📋 Skills Measured Guide | [Official Study Guide](https://learn.microsoft.com/en-gb/credentials/certifications/resources/study-guides/dp-600) |
| 🧪 Free Practice Assessment | [Practice Test](https://learn.microsoft.com/en-us/credentials/certifications/exams/dp-600/practice/assessment?assessment-type=practice&assessmentId=90) |
| 📚 Microsoft Fabric Documentation | [Fabric Docs](https://learn.microsoft.com/en-us/fabric/) |
| 🎬 Exam Readiness Videos | [Exam Readiness Zone](https://learn.microsoft.com/en-us/shows/exam-readiness-zone/preparing-for-dp-600-plan-implement-and-manage-a-solution-for-data-analytics) |
| 💶 EU Exam Booking | [Pearson VUE Microsoft](https://home.pearsonvue.com/microsoft) |

---

## 📚 About the Study Notes

These notes are hosted on **GitHub Pages** and published as a searchable website on this URL:

👉 **[📘 DP-600 Study Notes](https://marcogrimaldi29.com/dp-600-study-notes/)**

The site includes full-text search, Mermaid diagram rendering, and mobile-friendly navigation for on-the-go review.

These notes are designed to be a structured, exam-focused summary of the most important concepts and services based on the official **[Microsoft DP-600 Study Guide](https://learn.microsoft.com/en-gb/credentials/certifications/resources/study-guides/dp-600)** and its criteria.

---

## ✍️ About the Author

These notes are maintained by **[Marco Grimaldi](https://www.linkedin.com/in/marco-grimaldi29/)** — Cloud Consultant, Language Trainer & Lifelong Learner.

📍 **Find more content at [🌐 marcogrimaldi29.com](https://marcogrimaldi29.com)**

> The website is continuously updated and based on my personal study notes and experiences. If you have any feedback, suggestions, or corrections, feel free to [reach out](https://marcogrimaldi29.com/contact/)**!

---

## 📈 Analytics

This site uses [Umami](https://umami.is/) for privacy-friendly analytics.

---

## ©️ Credits & Acknowledgements

The [Just the Docs](https://github.com/just-the-docs/just-the-docs) theme is used for a clean, documentation-style layout. Licensed under [MIT](https://opensource.org/license/MIT).

Created with the help of AI. Model used: [Claude Opus 4.6](https://www.anthropic.com/news/claude-opus-4-6). The content has been reviewed and edited by the author for accuracy and clarity, but may contain errors. Always verify against the latest [Microsoft documentation](https://learn.microsoft.com/en-us/fabric/).

> *Not affiliated with or endorsed by Microsoft.*
