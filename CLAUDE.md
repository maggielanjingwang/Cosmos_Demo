# Auto-Research — Epic Cosmos AF Platform

## What this is
An agentic pipeline that turns a researcher's question into a Cosmos-ready
variable list: screen literature → surface research gaps → extract & map
variables → interactive checklist → SAP + data-extraction plan. Atrial
fibrillation (AFib) first, then reusable for other cardiovascular conditions.
Reference product: IHME GBD Compare, but cleaner and research-workflow oriented.

## Where things are
- `afib_literature_variable_inventory.xlsx` — the living inventory: 22 papers,
  60 variables (V001–V085), 246 paper–variable links, each variable mapped to
  Cosmos tables/fields. Sheets: Literature_Matrix, Variable_Dictionary,
  Paper_Variable_Link, New_Study_Intake, Lists, Documentation_Sources.
- `papers/` — downloaded full-text PDFs. See `papers/_DOWNLOAD_STATUS.md` for
  what's present and what still needs manual download.
- `Epic_Cosmos_AF_Rate_vs_Rhythm_SAP_v0.3.docx` — the live study SAP (v0.3).
- `00X-*-field-mapping.pdf` — Cosmos field mappings, one per analysis.
- `goal.md` — the 3-layer project narrative + the full Step 0–7 workflow spec.
- `rule.md` — the 10 engineering rules for building the pipeline.

## The workflow, in one line
Step 0 context → 1 question+clarify → 2 screen+quality-gate → 3 synthesis+extract
+contradiction → 4 visualize+select+recommend → 4.5 assumptions → 5 SAP → 6 data
pre-check → 7 Cosmos plan. Checkpoints at Steps 1, 2, 4, 4.5, 6.

## Hard rules (non-negotiable)
1. Output in English.
2. Ask when anything is unclear — never guess on research design or direction.
3. Proposal engine, not execution engine — a human checkpoint at every step;
   never auto-advance through a step that changes the research design.
4. Transparent workflow — emit and render intermediate states, not just a
   final answer. Every step's inputs, outputs, and decision logic are inspectable.
5. Provenance on every artifact — source papers, role in source, confidence,
   verification status. No artifact ships without it.
6. Uncertainty is first-class — never render an unverified mapping or a
   contradicted claim with the same weight as a confirmed one.
7. Clinical vocabulary is the key; Cosmos field names are attributes of it.
8. Keep scientific relevance and data feasibility as two separate scores —
   never merge them into one.
