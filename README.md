# Cosmos Demo — AFib Research Platform

An interactive, single-file web app that turns a research question into an **Epic
Cosmos–ready variable list and data query plan** for atrial fibrillation (AFib)
research. It walks through the full workflow — literature screening, evidence
synthesis, a variable taxonomy, an SAP draft, and a Cosmos extraction plan — with
a human checkpoint at every step.

The app is a self-contained HTML file (no server, no build, no internet needed).

## How to view it

### Option A — GitHub Pages (recommended, renders the app)
GitHub serves `.html` files as raw text, so open it through **Pages**, not the
raw file view.

1. In this repo: **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Set **Branch: `main`** and **folder: `/ (root)`**, then **Save**.
4. Wait ~1 minute, then open:

   - **Latest app:** https://maggielanjingwang.github.io/Cosmos_Demo/
     (the root redirects to the newest version)
   - Specific version: https://maggielanjingwang.github.io/Cosmos_Demo/afib_research_platform_v3.html

### Option B — No setup (instant preview)
Use the HTML preview proxy — works without enabling Pages:

- https://htmlpreview.github.io/?https://github.com/maggielanjingwang/Cosmos_Demo/blob/main/afib_research_platform_v3.html

### Option C — Local
Download `afib_research_platform_v3.html` and double-click it to open in any
browser.

> Tip: a plain GitHub link like
> `github.com/.../afib_research_platform_v3.html` only shows the source code.
> Use Option A or B to actually run the app.

## What's in the repo

| File | Purpose |
|------|---------|
| `index.html` | Redirects to the latest app version (for the Pages root URL) |
| `afib_research_platform_v3.html` | **Latest** — the interactive platform |
| `afib_research_platform_v2.html`, `_v1.html` | Earlier versions |
| `afib_research_console_v0.html` | Original single-page prototype |
| `goal.md` | Project overview + full Step 0–7 workflow spec |
| `rule.md` | Engineering rules for the platform |
| `CLAUDE.md` | Operating contract / project summary |

## The workflow (inside the app)

`Home` overview → **Step 0** context → **Step 1** question & clarify →
**Step 2** literature screening & quality → **Step 3** synthesis & contradiction
detection → **Step 4** variable taxonomy & selection → **Step 4.5** assumptions →
**Step 5** SAP draft → **Step 6** data pre-check → **Step 7** Cosmos data plan.

Selections carry through: confirm papers in Step 2, pick variables in Step 4, and
they populate the SAP, pre-check, and Cosmos plan. Export your variable list as
CSV or JSON from Step 4.

---
Prototype for discussion. Built from the AFib literature-to-variable inventory and
the Epic Cosmos Data Dictionary.
