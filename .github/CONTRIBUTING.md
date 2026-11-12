# Contributing to DP-600 Study Notes

Thanks for helping keep these notes accurate. They cover **Microsoft Exam DP-600: Implementing Analytics Solutions Using Microsoft Fabric**, and Microsoft Fabric ships changes almost weekly: features move out of preview, get renamed or change their limits, and the exam's skills outline is revised every so often. Reports from people studying with the notes are the best way to catch what has changed.

> [!IMPORTANT]
> **This repo accepts issues, not pull requests.** Pull requests are turned off. I write every change myself and check it against the Microsoft Fabric documentation, so the notes keep one voice from the prerequisites page through to the appendices. If you know exactly how something should read, put your proposed wording in the issue. It's just as useful.

---

## Contents

- [Ways to Help](#-ways-to-help)
- [Before You Open an Issue](#-before-you-open-an-issue)
- [Opening an Issue](#-opening-an-issue)
- [Out of Scope](#-out-of-scope)
- [What Happens Next](#-what-happens-next)
- [Code of Conduct](#-code-of-conduct)
- [Security](#-security)

---

## 🙌 Ways to Help

Every report fits one of the five problem types in the issue template:

| Type | What it covers | For example |
| --- | --- | --- |
| **Bug** | A broken link, search, a diagram or a page that won't load | A Mermaid decision diagram on the Prepare Data page shows up as plain text, a link to Microsoft Learn returns 404, or search finds nothing for a term that's on the page |
| **Wrong content** | A fact, exam tip or code sample is incorrect | A DAX measure in Appendix B returns the wrong total, or a decision table picks Dataflow Gen2 for a scenario that needs a notebook |
| **Outdated content** | Microsoft Fabric or the exam has changed | A preview feature is now generally available or has a new name, a Direct Lake limit has changed, or the study guide has been revised since the notes were written |
| **Device issue** | The site displays or behaves badly on a specific device or browser | A wide comparison table is cut off on a phone, or the sidebar menu won't open on a tablet |
| **Something else** | Anything the other four don't cover, including suggestions | A topic that would read better as a comparison table, or an exam trap the Quick Reference Cheatsheet is missing |

---

## 🔎 Before You Open an Issue

- **Search the existing issues**, both [open and closed](https://github.com/marcogrimaldi29/dp-600-study-notes/issues?q=is%3Aissue). If someone has already reported it, add a 👍 reaction, or a comment with anything new, rather than opening a duplicate.
- **Check the date.** The notes are written against the **skills measured as of October 19, 2026**. Compare that with the **Skills measured** section of the [official study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600). If it shows a newer date or different domain weights, say so in the issue, so I know which revision to compare against.
- **Check the current docs.** The [Microsoft Fabric documentation](https://learn.microsoft.com/en-us/fabric/) usually confirms whether something has changed, and gives you the official source the issue template asks for. For recent changes, [What's new in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/fundamentals/whats-new) is a quick place to start.
- **One problem per issue.** Separate issues are easier to track, fix and close. Several typos on the same page can share one issue.
- **For a site bug, hard-refresh first** (Ctrl+F5, or Cmd+Shift+R on a Mac), in case your browser is showing an older copy of the page.

---

## 📝 Opening an Issue

1. Open a new issue with the [🐞 Report a problem with the notes](https://github.com/marcogrimaldi29/dp-600-study-notes/issues/new?template=ISSUE_TEMPLATE.md) template.
2. Give it a title that names the page and the problem, for example *"03 Semantic Models: Direct Lake fallback section is out of date"* rather than *"Mistake on page"*.
3. Fill in the sections that apply and delete the rest.

> [!TIP]
> To link straight to a section, open **Table of contents** at the top of the page and click the section, or click the link icon that appears next to a heading when you point at it. Then copy the address from your browser into the template's **Page URL** field.

### What Makes a Report Easy to Fix

- **A link to the exact section**, not just the page
- **The text as the notes have it**, quoted, so I can find it even if the page has changed since
- **An official source** for any content change. Microsoft Learn is best. A report without one may take longer, because I check every change against the documentation before it goes in
- **For code samples:** where you ran it (a Fabric notebook, the SQL analytics endpoint, a warehouse, DAX query view or a KQL queryset) and the error or result you got
- **For bugs and device issues:** the steps to reproduce it, what you expected and what happened instead, your device, operating system and browser, and a screenshot if it shows the problem
- **Your proposed wording**, if you have it. With pull requests turned off, this is the closest thing to suggesting an edit directly

---

## 🚫 Out of Scope

To keep the notes accurate and the issue tracker useful, please don't open issues for:

- **Live exam content.** Questions, answers or topics remembered from a real DP-600 exam are covered by the exam NDA and break Microsoft's [exam and assessment lab security policy](https://learn.microsoft.com/en-us/credentials/support/exam-and-assessment-lab-security-policies). I'll close issues or comments that include them and remove the content.
- **Exam booking, scores or retakes.** [Microsoft Credentials support](https://learn.microsoft.com/en-us/credentials/support/help) can help with those.
- **Help with your own Fabric tenant or project.** To troubleshoot your own capacity, workspace, pipeline, semantic model or report, ask in the [Microsoft Fabric Community](https://community.fabric.microsoft.com/) or on [Microsoft Q&A](https://learn.microsoft.com/en-us/answers/), or contact [Microsoft Fabric support](https://support.fabric.microsoft.com/).
- **Anything private.** Issues are public, so leave out tenant and workspace IDs, connection strings, keys and tokens, and any customer data, including in screenshots.
- **Unofficial sources.** Exam dump sites, third-party practice tests and AI chat answers aren't accepted as the source for a content change.
- **Promotion.** I don't add links to courses, products or services, affiliate links or paid placements.
- **Security vulnerabilities.** Please [report these privately](https://github.com/marcogrimaldi29/dp-600-study-notes/security/advisories/new), as described in the [security policy](SECURITY.md), not in a public issue.

---

## 🔄 What Happens Next

- I maintain the notes on my own, in my spare time, so replies are best effort and there's no fixed response time.
- I check each report against the official documentation. If I need more detail, I'll ask on the issue, so keep an eye on your GitHub notifications.
- When a fix goes in, I close the issue with a note on what changed. The live site updates shortly after.
- If nothing changes, I close the issue with the reason, such as out of scope, can't be verified, or working as intended.
- An issue waiting on a reply may be closed after a while. Comment with the missing details and I can reopen it.

---

## 🤝 Code of Conduct

By opening, commenting on or reacting to issues here, you agree to follow the [Code of Conduct](CODE_OF_CONDUCT.md). Comments that are abusive, spam or share live exam content may be hidden, edited or locked.

---

## 🔒 Security

Found a security problem with the site or this repo? Please don't report it in a public issue. Use [Report a vulnerability](https://github.com/marcogrimaldi29/dp-600-study-notes/security/advisories/new) instead, so only you and I can see it. The [security policy](SECURITY.md) has the details.

---

If the notes helped you, the README's [💚 Support](https://github.com/marcogrimaldi29/dp-600-study-notes#-support) section has a couple of ways to show it. Thanks for helping make them better for the next DP-600 candidate.
