Here is the updated version:

---

## What We Are Trying to Do

### In One Sentence

We want to use Epic Cosmos, a large electronic health record database, to study real-world treatment patterns and outcomes among patients with newly diagnosed atrial fibrillation (AFib). The central question is: rhythm control vs. rate control, which is better? Beyond that, we want to automate and productize the entire research pipeline so it can be quickly reused for other cardiovascular conditions.

---

### Layer 1: The Core Research Project (SAP file)

We have already drafted a complete Statistical Analysis Plan (v0.3). This is a real study intended for publication.

**Research question:** From 2018 onward, how have rhythm-control strategies (antiarrhythmic drugs, cardioversion, catheter ablation) vs. rate-control strategies (beta-blockers, digoxin, etc.) trended among incident AF patients in the US? How do the two strategies differ in anticoagulation quality and clinical outcomes?

**4 specific aims:**

1. Describe quarterly trends in rhythm control vs. rate control from 2018 onward
2. Track AF phenotype progression (paroxysmal to persistent to permanent)
3. Evaluate anticoagulation quality (OAC/DOAC use) across treatment strategies
4. Compare 1-year and 3-year outcomes (mortality, stroke, rehospitalization) by strategy

**Data source:** Epic Cosmos patient-level data. The study population consists of adults first diagnosed with AF during an ED visit or hospitalization (ICD-10 codes I48.0, I48.11, I48.19, I48.21), excluding atrial flutter, requiring a clean 365-day lookback period. Expected sample size exceeds 110,000 patients.

---

### Layer 2: Research Infrastructure (xlsx file)

We have built a living workbook called a "literature-to-variable inventory," which contains:

- **22 seed studies** drawn from authoritative registries such as GWTG-AFIB, NCDR, and FinACAF
- **60 standardized research variables** (V001 through V085), covering cohort definition, AF phenotype, management strategies, anticoagulation, ablation, LAAO, and outcomes
- **246 paper-variable linkages**, with each variable mapped to specific tables and fields within the Cosmos data dictionary

The purpose of this system is to let any researcher, given a new research question, quickly identify which variables are needed, what research gap in each paper and synthesize the research gap or unmet needs in order to discovery a new research question, where to find them in the database, and which prior studies have done similar analyses.

---

### Layer 3: Automated Workflow and Visualization

We are planning to build an agentic workflow that connects the two layers above:

1. A researcher inputs a research question
2. The system automatically screens the literature library and extracts relevant variables
3. The system outputs a visual variable checklist that researchers can interact with
4. The selected variables feed directly into Epic Cosmos data extraction

Our reference product is the IHME GBD Compare visualization tool, though we want something cleaner and more oriented toward the research workflow.

---

### Summary: Our Three Priorities

| Layer | What | Current Status |
|-------|------|---------------|
| Core research | Real-world study of AF rhythm vs. rate control | SAP v0.3 complete, awaiting data access |
| Research infrastructure | Literature-variable taxonomy mapped to Cosmos fields | Excel has 22 papers and 60 variables |
| Tool platform | Dynamic visualization and automated literature screening workflow | In development |

Our broader ambition is to get AFib right first, then replicate the entire framework (literature matrix, variable taxonomy, automated pipeline) for other cardiovascular conditions. We are not just trying to publish one paper. We are building a reusable research infrastructure.


-----
Step 0: Project Context Initialization
Step 1: Research Question Input + Follow-up Clarification
Step 2: Literature Screening + Quality Gating + List Confirmation
Step 3: Evidence Synthesis + Variable Extraction + Contradiction Detection
Step 4: Overview Visualization + Variable Selection + Research Question Recommendation
Step 4.5: Assumption Registry Confirmation
Step 5: SAP Generation
Step 6: Data Quality Pre-check
Step 7: Epic Cosmos Data Plan + Visualization

# Auto-Research Workflow for Medical Researchers
### Epic Cosmos AF Research Platform — Design Specification

---

## Overview

This document defines the end-to-end automated research workflow for medical doctors and researchers using the Epic Cosmos database. The system is designed as a **structured proposal engine**: automation handles screening, synthesis, and generation tasks, while the researcher retains full control at every decision point.

The current primary use case is atrial fibrillation (AFib) research, with the architecture built for extension to other cardiovascular conditions.

---

## Step 0: Project Context Initialization

Before the researcher inputs any question, the system loads the appropriate domain context.

- Select disease area (current: AFib; future: HF, CAD, etc.)
- Load the corresponding literature library, variable dictionary, and Cosmos field mappings
- Set researcher role preference (clinician / epidemiologist / data scientist), which governs output language complexity and visualization depth
- Output: a fully initialized project session with a unique `run_id` and locked dependency versions (literature DB version, variable dictionary version, Cosmos field mapping version)

> **Engineering note:** Every session is versioned from Step 0. All downstream artifacts inherit this version context so the run is fully reproducible.

---

## Step 1: Research Question Input and Clarification

The researcher inputs an initial research question in free text or structured form.

**1a. Follow-up Question Engine**

The system asks targeted clarifying questions to resolve ambiguity:
- What is the target population? (e.g., incident AF only, or all AF?)
- What is the primary exposure of interest?
- What is the primary outcome?
- What is the time horizon?
- Are there known subgroups of interest?

**1b. Scope Confirmation**

The system returns a structured interpretation of the research question for the researcher to confirm or edit before proceeding:

```json
{
  "population": "Adults with incident AF, first diagnosed in ED or inpatient setting, 2018 onward",
  "exposure": "Rhythm control vs. rate control strategy",
  "comparator": "Rate control only",
  "outcome": "Stroke, mortality, AF hospitalization at 1 year",
  "subgroups": ["AF phenotype", "HF status", "OAC eligibility"],
  "time_horizon": "1-year and 3-year follow-up"
}
```

> **Rule:** The system never advances to Step 2 without explicit researcher confirmation of the scoped question. This is a hard `PENDING_REVIEW` checkpoint.

---

## Step 2: Literature Screening, Quality Gating, and List Confirmation

**2a. Automated Literature Screening**

The system searches the literature library using the confirmed research scope and returns a ranked list of relevant papers with match rationale per paper.

**2b. Quality Gating**

Before passing papers to synthesis, the system automatically tags each paper:

| Tag | Definition |
|-----|-----------|
| `study_design` | RCT, observational, registry, meta-analysis |
| `data_source_tier` | Tier 1 (GWTG, NCDR, FinACAF), Tier 2 (single EHR system), Tier 3 (single center) |
| `evidence_level` | High / Medium / Low |
| `sample_size` | Reported enrollment |
| `recency_flag` | Published within last 2 years |

The researcher sets a quality threshold filter. Papers below the threshold are retained in the list but visually downweighted and excluded from synthesis by default.

**2c. Literature List Confirmation**

The researcher reviews the filtered list, can manually include or exclude papers, and confirms the final set before synthesis begins.

> **Rule:** No paper enters the synthesis pipeline without appearing in the confirmed list. The confirmed list is saved as an immutable artifact of the run.

---

## Step 3: Evidence Synthesis, Variable Extraction, and Contradiction Detection

**3a. Per-Paper Extraction**

For each confirmed paper, the system extracts:
- Study design and population
- Exposure, comparator, and primary outcome
- All variables used (by role: Exposure / Outcome / Adjustment / Subgroup)
- Key figures and tables with structured summaries
- Explicitly stated research gaps or unsolved clinical problems
- Author conclusions

**3b. Contradiction Detection**

The system actively identifies conflicts across papers, not just supporting evidence. Every extracted claim is checked against claims from other papers in the confirmed list.

```json
{
  "claim": "Early rhythm control reduces ischemic stroke risk",
  "supporting_papers": ["OATES_2025", "TEPPO_2023"],
  "contradicting_papers": ["FOX_2022"],
  "consensus_level": "moderate",
  "contradiction_flag": true,
  "implication": "Potential research gap: heterogeneity by geography or care setting"
}
```

Contradictions are highlighted prominently in the output. They are not averaged away. Contradicted claims are the primary source of research gap signals fed into Step 4.

**3c. Provenance on Every Artifact**

Every extracted variable, claim, and gap carries a provenance object:

```json
{
  "variable_id": "V042",
  "variable_name": "DOAC dose appropriateness",
  "suggested_by": "literature_match",
  "source_papers": ["SANDHU_2023_GWTG_DOAC_DOSING"],
  "role_in_source": "Exposure",
  "cosmos_availability": "Medium",
  "verification_status": "unverified",
  "confidence": "high"
}
```

> **Rule:** No variable or claim appears in any downstream output without a provenance object. Unverified mappings are rendered with a distinct visual indicator, never with the same weight as confirmed ones.

---

## Step 4: Overview Visualization, Variable Selection, and Research Question Recommendations

**4a. Literature and Variable Overview Visualization**

The system renders an interactive dashboard with the following supported chart types:
- Star/radar chart: variable coverage across papers
- Sankey diagram: how papers connect to variable roles (Exposure, Outcome, Adjustment, Subgroup)
- Heatmap: variable x paper matrix, color-coded by role
- Timeline: publication year distribution and evidence accumulation over time
- Gap map: unsolved problems clustered by clinical domain

All visualizations support download as SVG/PNG. Underlying raw data is always exportable as Excel.

**4b. Variable Selection Interface**

The researcher selects variables through two supported modes:

- **Checkbox mode:** Browse the full variable list filtered by category, Cosmos availability, or evidence level. Check/uncheck to include or exclude.
- **Chat mode:** Describe what is needed in plain language ("I want variables related to anticoagulation quality and stroke outcomes"), and the system highlights the relevant variables for confirmation.

Both modes produce the same structured output: a confirmed variable selection list with roles assigned.

**4c. Research Question Recommendations**

Based on synthesis results, the system surfaces potential research questions the researcher may not have considered. Each recommendation includes a structured score:

| Dimension | Definition |
|-----------|-----------|
| `novelty_score` | How many existing papers already answer this question |
| `feasibility_score` | Whether Cosmos data can support this question |
| `clinical_impact_score` | Derived from research gap frequency and clinical domain priority |
| `sample_size_estimate` | Rough estimate of eligible patients in Cosmos based on literature comparators |

**Warning system:** If the researcher's original question has already been directly answered by existing papers, the system issues a prominent warning:

> "This research question appears to have been addressed by [PAPER_X, PAPER_Y]. Consider the extension opportunities listed below, or refine your population, era, or exposure to differentiate your contribution."

> **Rule:** Recommendations are always presented as options, never as instructions. The researcher decides.

---

## Step 4.5: Assumption Registry Confirmation

Before SAP generation, the system extracts every key assumption embedded in the confirmed research scope and variable selection, and presents them as an explicit checklist.

Each assumption is tagged:

| Tag | Meaning |
|-----|---------|
| `verifiable_in_cosmos` | Can be checked with a pilot query |
| `domain_expert_judgment` | Requires clinical input, not data |
| `guideline_dependent` | Tied to a specific guideline version |

Example:

```
[ ] Incident AF is defined using a 365-day clean lookback period
    Tag: verifiable_in_cosmos
    Risk: Sites with short enrollment history may not support this lookback

[ ] Sotalol is classified as a rhythm-control agent in the primary analysis
    Tag: domain_expert_judgment
    Risk: Some analyses treat sotalol as dual-purpose; sensitivity analysis recommended

[ ] OAC eligibility uses CHA2DS2-VASc >= 2 threshold
    Tag: guideline_dependent (2023 ACC/AHA guideline)
    Risk: 2024 ESC guideline uses CHA2DS2-VA; cross-guideline sensitivity may be needed
```

The researcher confirms, modifies, or flags each assumption. This confirmed registry is saved as a versioned artifact and is referenced in both the SAP and the Cosmos data plan.

> **Rule:** The assumption registry is not optional. It is the bridge between the researcher's intent and the system's implementation. It also serves as documentation for IRB submissions and peer review.

---

## Step 5: SAP Generation

Using the confirmed scope, variable selection, and assumption registry, the system generates a full Statistical Analysis Plan draft.

The SAP includes:
- Working title and project metadata
- Study overview and primary hypothesis
- Specific aims table (Aim, Objective, Primary Hypothesis)
- Study design and case structure
- Study population and enrollment criteria (with ICD-10 code table)
- Definitions and key variables (organized by domain)
- Exposure group definitions
- Primary and secondary outcomes
- General analysis considerations (descriptive stats, missing data handling)
- Analysis objectives and tasks
- Appendix shells: table/figure shells, variable mapping, data gaps and sensitivity analyses

The SAP is generated in structured Markdown and is exportable as Word (.docx) for manuscript submission workflows.

> **Rule:** The SAP is a draft. It is clearly labeled as system-generated and requires researcher review and sign-off before use. The system does not present it as final.

---

## Step 6: Data Quality Pre-check

Before generating the Cosmos data extraction plan, the system runs a feasibility check on every selected variable.

For each variable:
- Check historical Cosmos completeness against the `cosmos_availability` field in the variable dictionary
- Flag variables where completeness is below a configurable threshold (default: 70%)
- Suggest fallback variables or proxy definitions where available
- Generate an overall data plan feasibility score

Output is a structured pre-check report:

| Variable | Cosmos Availability | Completeness Risk | Recommended Action |
|----------|-------------------|-------------------|-------------------|
| V042 DOAC dose appropriateness | Medium | High (requires weight + creatinine) | Add sensitivity analysis excluding dose-ineligible patients |
| V023 Heart failure phenotype | Medium | Moderate (EF may need local mapping) | Validate echo data availability in pilot query |
| V051 Heart rate control | High | Low | Proceed as planned |

> **Rule:** Researchers see this report before the data plan is finalized. No variable is silently dropped. Every feasibility concern is surfaced explicitly with a recommended action.

---

## Step 7: Epic Cosmos Data Plan and Visualization

Using the confirmed variables, SAP, assumption registry, and pre-check report, the system generates a structured Cosmos data extraction plan.

The plan includes:
- Cohort construction query logic (step-by-step enrollment criteria with Cosmos table references)
- Variable-to-table mapping for every selected variable
- Recommended analytic populations (primary cohort, subgroup cohorts, sensitivity cohorts)
- Missing data handling strategy per variable
- Suggested pilot query scope for feasibility validation before full extraction

**Visualization:**
- Flowchart: patient enrollment funnel (Step 1 through final analytic cohort)
- Table: variable x Cosmos source table matrix
- Timeline: data extraction sequence and dependency order

All outputs are exportable as Excel (raw data) and PDF (formatted plan document).

---

## Complete Workflow Summary

```
Step 0:   Project Context Initialization
          Load domain, literature DB, variable dictionary, Cosmos mappings, researcher role

Step 1:   Research Question Input and Clarification
          Follow-up questions, scope structured output, researcher confirmation [CHECKPOINT]

Step 2:   Literature Screening, Quality Gating, List Confirmation
          Automated screening, evidence level tagging, researcher confirms final paper list [CHECKPOINT]

Step 3:   Evidence Synthesis, Variable Extraction, Contradiction Detection
          Per-paper extraction, contradiction flagging, provenance on every artifact

Step 4:   Overview Visualization, Variable Selection, Research Question Recommendations
          Interactive dashboard, checkbox/chat variable selection, novelty/feasibility scoring [CHECKPOINT]

Step 4.5: Assumption Registry Confirmation
          Explicit assumption checklist, researcher sign-off, saved as versioned artifact [CHECKPOINT]

Step 5:   SAP Generation
          Full Statistical Analysis Plan draft, exportable as Markdown and Word

Step 6:   Data Quality Pre-check
          Per-variable feasibility check, completeness risk flags, researcher review [CHECKPOINT]

Step 7:   Epic Cosmos Data Plan and Visualization
          Cohort construction logic, variable-table mapping, enrollment funnel visualization
```

---

## Core Architectural Principles

**Proposal engine, not execution engine.** The system proposes at every step. The researcher decides.

**Provenance on everything.** No artifact is output without traceable source metadata.

**Uncertainty is a first-class data type.** Unverified mappings, low-availability variables, and contradicted claims are never hidden. They are surfaced with explicit visual distinction.

**Sessions are versioned and reproducible.** Every run is a lockfile. Any run can be replayed or diffed against another run.

**Iteration is the default.** The session architecture maintains a history stack. Changing one variable or one assumption triggers partial re-execution of only the affected downstream steps, not a full restart.

**Domain vocabulary first.** All outputs use clinical terminology as the primary identifier. Database field names are attributes, not keys.