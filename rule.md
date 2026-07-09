Good framing. Here is the same set of rules rewritten for a coding project context:

---

## Rules for Building an Automated Research Workflow (Engineering Spec)

**Rule 1: Transparent Workflow and Visualization**

Every pipeline step must be logged and rendered. Do not just return a final result; return the intermediate states too. Think of it like a DAG (directed acyclic graph) where each node's input, output, and decision logic is inspectable. In practice this means:
- Each agent step emits a structured log (step name, inputs, outputs, confidence score if applicable)
- The UI renders the pipeline as a visible flow, not a spinner followed by an answer
- Users can click into any node and see why it produced what it produced

**Rule 2: Human-in-the-Loop at Every Decision Point**

Design the system as a proposal engine, not an execution engine. The agentic workflow should pause at key decision points and surface a structured payload for the researcher to review before continuing. This is not just a UX choice; it is an architectural one. Your state machine should have explicit `PENDING_REVIEW` states between automated steps. Never auto-advance through a step that modifies the research design.

**Rule 3: Full Provenance Tracking on Every Output**

Every generated artifact (variable list, paper match, suggested cohort criterion) must carry metadata about where it came from. Implement a provenance object attached to every output:
```json
{
  "variable_id": "V042",
  "suggested_by": "literature_match",
  "source_papers": ["SANDHU_2023_GWTG_DOAC_DOSING"],
  "role_in_source": "Exposure",
  "confidence": "high",
  "verification_status": "unverified"
}
```
This makes the system auditable and lets the frontend render citations inline rather than as an afterthought.

**Rule 4: Versioned, Reproducible Pipeline Runs**

Every run should be saved as an immutable snapshot. Store the run config (literature DB version, variable dictionary version, user inputs, model/prompt version) alongside the outputs. Think of it like a lockfile. A researcher should be able to re-run a saved session and get the same result, or diff two runs to see what changed and why. Use a run ID as the primary key for all artifacts produced in a session.

**Rule 5: Explicit Uncertainty as a First-Class Data Type**

Do not flatten uncertainty into a binary yes/no. Model it explicitly throughout the pipeline. For the Cosmos availability field in the variable dictionary, for example, map `High / Medium / Low` to a typed enum and propagate it through to the UI as a visual warning, not just a tooltip. Any variable with `verification_status: unverified` should be rendered differently from a confirmed one. The system should never present an unverified mapping with the same visual weight as a confirmed one.

**Rule 6: Domain-Vocabulary-First Data Model**

Your internal data model and API responses should use clinical terminology as the primary key, not database field names. The clinical name is the stable identifier; the Cosmos field mapping is an attribute of it. This means:
```json
{
  "variable_name": "DOAC dose appropriateness",
  "variable_id": "V042",
  "cosmos_source_tables": ["MedicationOrderFact", "MedicationDim"],
  "cosmos_availability": "Medium"
}
```
Not the other way around. This also makes the system easier to extend to databases other than Cosmos later.

**Rule 7: Strict Separation Between Scientific Relevance and Data Feasibility**

These are two separate scoring dimensions and must never be merged into one. A variable can be scientifically critical but have `cosmos_availability: Low`. The system should surface both scores independently so the researcher can make a conscious tradeoff. Do not pre-filter variables by feasibility; let the researcher decide what to deprioritize. Implement this as two separate columns or badges in the output, never a single combined score.

**Rule 8: Tiered API Design for Progressive Complexity**

Design your API so that the simplest input (a plain-language research question) produces a useful output, but power users can pass in a richer structured object to get more precise results. Think of it like function overloading:
- Simple: `POST /workflow { "research_question": "Does early rhythm control reduce stroke in AF patients?" }`
- Advanced: `POST /workflow { "population": {...}, "exposure": {...}, "outcome": {...}, "variable_overrides": [...] }`

The backend should normalize both inputs into the same internal representation before processing. This lets you build a simple UI first and add advanced controls later without rearchitecting.

**Rule 9: Export-Ready Output Formats from Day One**

Build export as a first-class feature, not an afterthought. Every output object should have a `to_csv()`, `to_markdown()`, and `to_json()` representation from the start. Researchers will need to paste variable lists into IRB applications, share methodology tables with collaborators, and copy cohort criteria into manuscripts. If you do not build this early, you will be adding one-off export hacks forever.

**Rule 10: Stateful, Iterative Session Architecture**

Do not build this as a stateless request-response API. Research is iterative. A researcher will change one inclusion criterion and want to see how the variable recommendations shift without re-running everything. Design the session as a mutable state object with a history stack:
- Each user action produces a new state version (like Redux)
- The diff between two states is always computable and renderable
- The user can roll back to any prior state
- The final state can be exported as the canonical run record

This also means your backend needs to be able to do partial re-runs: if the researcher changes only the outcome variable, only the parts of the pipeline that depend on the outcome variable should re-execute.

---

## One-Line Architectural Principle

**The system is a structured proposal engine with a human checkpoint between every automated step, full provenance on every artifact, and a session architecture that treats iteration as the default, not the exception.**