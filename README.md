# 📘 DP-600: Implementing Analytics Solutions Using Microsoft Fabric
### Study Notes Repository

[![Deploy to GitHub Pages](https://github.com/marcogrimaldi29/dp-600-study-notes/actions/workflows/pages.yml/badge.svg)](https://github.com/marcogrimaldi29/dp-600-study-notes/actions/workflows/pages.yml)
[![GitHub](https://img.shields.io/badge/GitHub%20Pages-Live-brightgreen?logo=github)](https://github.com/marcogrimaldi29/dp-600-study-notes)
[![Personal Hub of Marco Grimaldi](https://img.shields.io/badge/Blog-marcogrimaldi29.com-blue?logo=rss)](https://marcogrimaldi29.com)

> - 🎯 **Goal:** Earn the Microsoft Certified: Fabric Analytics Engineer Associate badge
> - 📅 **Notes Version:** 2026
> - 🌐 **Published site:** [📘 DP-600 Study Notes](https://marcogrimaldi29.com/dp-600-study-notes/)
> - ✍️ **Author:** [Marco Grimaldi](https://www.linkedin.com/in/marco-grimaldi29/)
> - 🔗 **Related repos:** [📘 AZ-305 Study Notes](https://marcogrimaldi29.com/az-305-study-notes/) · [📘 DP-700 Study Notes](https://marcogrimaldi29.com/dp-700-study-notes/)
---

## 📋 Exam At-a-Glance

| Detail | Info |
|--------|------|
| 🏅 Certification | Microsoft Certified: Fabric Analytics Engineer Associate |
| 📝 Passing Score | **700 / 1000** |
| 💶 Exam Price | **~$165 USD / ~€155 EUR** *(varies by country; VAT may apply)* |
| ⏱️ Duration | **100 minutes** *(120 min seat time incl. check-in)* |
| ❓ Question Types | MCQ, multi-select, drag-and-drop, case studies |
| 🔁 Renewal | **Annual** via free online assessment on Microsoft Learn |
| 🛡️ Prerequisite | **None** *(recommended: hands-on Fabric & Power BI experience)* |

---

## 📊 Official Domain Breakdown

> ⚠️ **Official ranges** from the Microsoft study guide (updated April 2026)

```mermaid
pie title Exam Domain Weights of the DP-600 (official ranges)
    "Prepare Data (45–50%)" : 47
    "Maintain a Data Analytics Solution (25–30%)" : 27
    "Implement & Manage Semantic Models (25–30%)" : 26
```

| # | Domain | Official Weight | Key Services |
|---|--------|----------------|-------------|
| 1 | Maintain a Data Analytics Solution | **25–30%** | Security, governance, version control, deployment pipelines, XMLA endpoint |
| 2 | Prepare Data | **45–50%** | Lakehouses, warehouses, Dataflow Gen2, notebooks, SQL, KQL, DAX, star schemas |
| 3 | Implement & Manage Semantic Models | **25–30%** | Storage modes, DAX, Direct Lake, relationships, calculation groups, composite models |

> 🔑 **Domain 2 (Prepare Data) carries nearly half the exam weight** — prioritize data preparation and transformation skills.

---

## 🗺 Certification Path

```mermaid
flowchart LR
    PL300["📊 PL-300\nPower BI\nData Analyst\n(Recommended)"]
    DP600["📈 DP-600\nImplementing Analytics\nSolutions Using\nMicrosoft Fabric\n(This Exam)"]
    BADGE["🏅 Fabric Analytics\nEngineer Associate"]

    PL300 -->|Foundation| DP600
    DP600 --> BADGE
```

---

## 🗂️ Repository Structure

```
dp-600-study-notes/
├── README.md                                    ← 📍 You are here
├── 00-fabric-prerequisites.md                   ← Microsoft Fabric fundamentals
├── 01-maintain-data-analytics-solution.md       ← Domain 1 (25–30%)
├── 02-prepare-data.md                           ← Domain 2 (45–50%)
├── 03-implement-manage-semantic-models.md       ← Domain 3 (25–30%)
└── 04-quick-reference-cheatsheet.md             ← Last-minute review & exam traps
```

---

## 📚 Official Learning Resources

| Resource | Link |
|----------|------|
| 📚 Microsoft's DP-600 Certification Learning Paths | [Certification Learning Paths](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/) |
| 📄 Official Exam Page | [DP-600 Exam](https://learn.microsoft.com/en-us/credentials/certifications/exams/dp-600/) |
| 📋 Skills Measured / Study Guide | [Official Study Guide](https://learn.microsoft.com/en-gb/credentials/certifications/resources/study-guides/dp-600) |
| 🧪 Free Practice Assessment | [Practice Assessment](https://learn.microsoft.com/en-us/credentials/certifications/exams/dp-600/practice/assessment?assessment-type=practice&assessmentId=90) |
| 🎬 Exam Readiness Videos | [Exam Readiness Zone](https://learn.microsoft.com/en-us/shows/exam-readiness-zone/preparing-for-dp-600-plan-implement-and-manage-a-solution-for-data-analytics) |
| 📚 Microsoft Fabric Documentation | [Fabric Docs](https://learn.microsoft.com/en-us/fabric/) |
| 🎓 Instructor-Led Course | [DP-600T00-A (4 days)](https://learn.microsoft.com/en-us/training/courses/dp-600t00) |
| 💶 EU Exam Pricing | [Pearson VUE Microsoft](https://home.pearsonvue.com/microsoft) |

---

### ✅ Key Study Tips

- 🎯 The exam tests **"which analytics approach?"** not just **"what does it do?"** — think in trade-offs and constraints
- 📊 Know **semantic model storage modes** deeply — Import, DirectQuery, Direct Lake, and composite models
- 🔄 Know **Dataflow Gen2 vs Notebook vs Pipeline** boundaries — the exam tests which tool fits which scenario
- 🔒 Study **security layers deeply** — workspace, item, row-level (RLS), column-level (CLS), and object-level (OLS) security
- 📐 **DAX is heavily tested** — know CALCULATE, iterators, table filtering, variables, and calculation groups
- ⚡ Understand **Direct Lake** mode — fallback behavior, V-Order, framing, and when it falls back to DirectQuery
- 📖 For case studies: read **business requirements and constraints first**, then eliminate answers

---

## ⚡ Quick Navigation

| File | Topics Covered |
|------|---------------|
| [📘 00 — Fabric Prerequisites](./00-fabric-prerequisites.md) | OneLake, workspaces, lakehouses, warehouses, capacities, Delta Lake |
| [🔒 01 — Maintain Analytics Solution](./01-maintain-data-analytics-solution.md) | Security, governance, version control, deployment pipelines, XMLA |
| [🔄 02 — Prepare Data](./02-prepare-data.md) | Data connections, star schemas, transforms, SQL, KQL, DAX queries |
| [📐 03 — Semantic Models](./03-implement-manage-semantic-models.md) | Storage modes, DAX, Direct Lake, relationships, optimization |
| [⚡ 04 — Quick Reference Cheatsheet](./04-quick-reference-cheatsheet.md) | Key numbers, decision tables, exam traps, final checklist |

---

## 📚 About the Study Notes

These notes are hosted on **GitHub Pages** and published as a searchable website on this URL:

👉 **[📘 DP-600 Study Notes](https://marcogrimaldi29.com/dp-600-study-notes/)**

The site includes full-text search, Mermaid diagram rendering, and mobile-friendly navigation for on-the-go review.

These notes are designed to be a structured, exam-focused summary of the most important concepts and services based on the official [Microsoft DP-600 Study Guide](https://learn.microsoft.com/en-gb/credentials/certifications/resources/study-guides/dp-600) and its criteria.

Additional resources and study notes maintained by me, such as the **[📘 AZ-305 Study Notes](https://marcogrimaldi29.com/az-305-study-notes/)**, the **[📘 DP-700 Study Notes](https://marcogrimaldi29.com/dp-700-study-notes/)**, and more, are also available for those pursuing the Microsoft and Azure certifications at the following Landing Page:

👉 **[🧑‍🏫 Microsoft Study Notes: Central Hub](https://marcogrimaldi29.com/microsoft-study-notes/)**

---

## ✍️ About the Author

Maintained by **[Marco Grimaldi](https://www.linkedin.com/in/marco-grimaldi29/)** — Cloud Consultant, Language Trainer & Lifelong Learner.

🏠 Find more certification guides, study tips, and tech content at **[🌐 marcogrimaldi29.com](https://marcogrimaldi29.com)**

The site is continuously updated and based on my personal study notes and experiences. If you have any feedback, suggestions, or corrections, feel free to [reach out](https://marcogrimaldi29.com/contact/)!

---

## 📈 Analytics

This site uses [Umami](https://umami.is/) for privacy-friendly analytics.

---

## ©️ Credits & Acknowledgements

The [Just the Docs](https://github.com/just-the-docs/just-the-docs) theme is used for a clean, documentation-style layout. Licensed under [MIT](https://opensource.org/license/MIT).

Created with the help of AI. Model used: [Claude Opus 4.6](https://www.anthropic.com/news/claude-opus-4-6). The content has been reviewed and edited by the author for accuracy and clarity, but may contain errors. Always verify against the latest [Microsoft documentation](https://learn.microsoft.com/en-us/fabric/).

> *Not affiliated with or endorsed by Microsoft.*

---
