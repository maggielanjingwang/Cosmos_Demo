# Cosmos Demo — AFib Research Platform

An interactive, single-file web app that turns a research question into an **Epic
Cosmos–ready variable list and data query plan** for atrial fibrillation (AFib)
research. It walks through the full workflow — literature screening, evidence
synthesis, a variable taxonomy, an SAP draft, and a Cosmos extraction plan — with
a human checkpoint at every step.

The app is a self-contained HTML file (no server, no build, no internet needed).

- **Latest app:** https://maggielanjingwang.github.io/Cosmos_Demo/afib_research_platform_v4.html


## What's in the repo

| File | Purpose |
|------|---------|
| `index.html` | Redirects to the latest app version (for the Pages root URL) |
| `afib_research_platform_v4.html` | **Latest** — the interactive platform |
| `afib_research_platform_v3.html`, `_v2.html`, `_v1.html` | Earlier versions |
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
