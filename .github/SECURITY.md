# Security Policy

DP-600 Study Notes is a static site built with Jekyll and the Just the Docs theme, and published on GitHub Pages. It has no accounts and no sign-in. Search runs entirely in your browser, against an index that's published with the site, and the site doesn't store anything on your device: no cookies and nothing in your browser's storage. The dark color scheme is fixed, so there's no theme setting to remember. Pages with diagrams load Mermaid from the jsDelivr CDN, and analytics are cookieless, through [Umami](https://umami.is/). The attack surface is small, but security reports are still welcome, and I'd rather hear about a problem privately before it's public.

---

## 🛡️ Supported Versions

There are no releases. Only what's live is maintained.

| Version | Supported |
| --- | --- |
| The live site at [marcogrimaldi29.com/dp-600-study-notes](https://marcogrimaldi29.com/dp-600-study-notes/) and the `master` branch | ✅ |
| Older commits, forks and copies hosted elsewhere | ❌ |

---

## 📮 Reporting a Vulnerability

**Please don't open a public issue or comment.** Report it privately through GitHub instead: open the repo's **Security** tab and choose **Report a vulnerability**, or go straight to the [private report form](https://github.com/marcogrimaldi29/dp-600-study-notes/security/advisories/new). Only you and I can see the report, and we can discuss and fix the problem there before anything is made public.

It helps to include:

- The affected URL, or the file in this repo
- What the problem is and what someone could do with it
- The steps to reproduce it, or a short proof of concept

Please don't include real secrets, credentials or anyone else's personal data in the report, and don't test against anything that isn't yours.

If you can't use GitHub, send me a private message on [LinkedIn](https://www.linkedin.com/in/marco-grimaldi29/) instead, starting it with **"Security: DP-600 Study Notes"** so it's easy to spot.

---

## 🎯 Scope

**In scope:**

- The published site, including its pages, scripts, styles and assets
- This repository's source and its GitHub Actions workflows
- A gem, script or GitHub Action version used here that has a known vulnerability

**Out of scope:**

- **Microsoft Fabric, Power BI or any other Microsoft product.** Report these to the [Microsoft Security Response Center](https://msrc.microsoft.com/create-report).
- **A vulnerability in Jekyll, Just the Docs, Mermaid, Lunr or another dependency itself.** Report it to that project. If this repo uses an affected version, let me know as well.
- **HTTP response headers set by GitHub Pages.** The repo can't change them.
- **Automated scanner output** with no demonstrated impact on this site.
- **An insecure pattern in the notes**, such as a notebook sample that hard-codes a storage account key instead of reading it from Azure Key Vault, or a T-SQL `GRANT` that gives more access than the scenario needs. That's a content issue, so it's welcome as a public issue. See the [contributing guide](CONTRIBUTING.md).

---

## ⏱️ What to Expect

I maintain this repo on my own, so responses are best effort. I'll confirm I've received your report, keep you posted in the private report while I look into it, and let you know when it's fixed. If you'd like to be credited, say so and I'll credit you in the security advisory or the fix.

Dependabot alerts and security updates are turned on for this repo, so known vulnerabilities in its dependencies are usually flagged and patched without a report.
