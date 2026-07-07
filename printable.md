---
layout: default
title: "Printable Study Guide"
nav_exclude: true
search_exclude: true
permalink: /printable/
mermaid: true
---

# 🖨️ Printable Study Guide
{: .no_toc }

All chapters combined in order. Use your browser's Print (or Save as PDF) to
get the full guide as one document — each chapter starts on a new page.

---

{% assign chapters = site.pages | where_exp: "p", "p.nav_order and p.nav_order >= 2 and p.nav_order <= 11" | sort: "nav_order" %}
{% for chapter in chapters %}
<div class="print-chapter">
{{ chapter.content }}
</div>
{% endfor %}
