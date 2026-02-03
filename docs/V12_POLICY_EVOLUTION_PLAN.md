\# v1.2 — Policy Evolution \& Explainability — Planning



\## 1. Release Theme



v1.2 introduces a \*\*policy evolution layer\*\* on top of the existing authority and enforcement engine. The focus is:



\- Treating policy as \*\*versioned, hash-identified bundles\*\*

\- Making policy changes \*\*diffable and reviewable\*\*

\- Improving \*\*explainability\*\* so reviewers can see:

&nbsp; - \*“Which policy bundle was in force for this decision?”\*

&nbsp; - \*“What changed between bundle X and bundle Y?”\*

&nbsp; - \*“Why did the engine reach this outcome under those policies?”\*



This builds on v1.0 (integrity and provenance) and v1.1 (replay and correlation).



---



\## 2. Objectives



v1.2 aims to deliver:



1\. \*\*Policy Bundle Concept\*\*

&nbsp;  - Define a “policy bundle” as a canonical snapshot of all active policies and related configuration at a point in time.

&nbsp;  - Give each bundle a \*\*stable identifier\*\* and a \*\*cryptographic hash\*\*.



2\. \*\*Bundle Hashing\*\*

&nbsp;  - Define a canonical serialization format for policy bundles.

&nbsp;  - Compute a \*\*bundle hash\*\* (e.g., SHA-256) over that serialization.

&nbsp;  - Ensure the hash is:

&nbsp;    - Deterministic

&nbsp;    - Independent of file ordering and incidental whitespace



3\. \*\*Decision ↔ Policy Bundle Link\*\*

&nbsp;  - Each decision should reference:

&nbsp;    - `policy\_bundle\_id` (human-oriented, e.g., `bundle-2026-01-31T...`)

&nbsp;    - `policy\_bundle\_hash` (cryptographic checksum)

&nbsp;  - This link should appear in:

&nbsp;    - The \*\*decision record\*\* (engine)

&nbsp;    - The \*\*audit log\*\* entry

&nbsp;    - The \*\*replay / explain\*\* output



4\. \*\*Policy Diff Capability\*\*

&nbsp;  - Provide a way to compare two bundles:

&nbsp;    - Additions / removals of policies

&nbsp;    - Changes within a given policy (conditions, thresholds, roles, etc.)

&nbsp;  - Expose this as a \*\*CLI entry point\*\* (e.g., `policy\_bundles diff <from> <to>`).



5\. \*\*Explainability Enhancements\*\*

&nbsp;  - Extend decision replay / explain output to show:

&nbsp;    - Which policy bundle and hash were active

&nbsp;    - Which specific policy (or policies) matched and why

&nbsp;  - Target: a reviewer can understand the decision without reading raw JSON.



---



\## 3. Out of Scope (for v1.2)



To avoid scope creep, v1.2 does \*\*not\*\* attempt to:



\- Redesign the entire policy language or DSL.

\- Introduce a full policy editor UI.

\- Implement multi-tenant policy isolation.

\- Implement automated, AI-driven policy suggestions or refactoring.



These can be considered for v1.3+.



---



\## 4. Current Baseline (Pre-v1.2)



As of v1.1:



\- \*\*Policies\*\* are effectively embedded in:

&nbsp; - Code-level logic (e.g., emergency lockdown rules)

&nbsp; - Policy-related JSONL logs (`delegations.jsonl`, `policy\_change\_log.jsonl`)

\- \*\*Decisions\*\*:

&nbsp; - Reference policy IDs (e.g., `\["policy-001"]`)

&nbsp; - Are recorded with hash-chained audit log entries and `decision\_correlation\_id`

\- \*\*Replay \& Correlation\*\*:

&nbsp; - `decision\_replay.py` can list, explain, and correlate decisions with enforcement

&nbsp; - `decision\_correlation\_id` ties together:

&nbsp;   - Decision

&nbsp;   - Audit log entry

&nbsp;   - Enforcement events



What is missing:



\- A first-class notion of a \*\*policy bundle\*\* with a hash.

\- A stable, auditable link between:

&nbsp; - Concrete decisions and

&nbsp; - The full configuration state that was in force.



---



\## 5. Core Concepts for v1.2



\### 5.1 Policy Bundle



A \*\*policy bundle\*\* is a snapshot of governance configuration including:



\- All active policy definitions (e.g., policy-001, policy-002, …)

\- Delegation rules relevant to decisions

\- Any other state required for deterministic evaluation



Conceptually:



\- Stored as a structured document (e.g., JSON or YAML) in a well-defined path.

\- Versioned and tracked in Git like code.



\### 5.2 Bundle Identifier and Hash



Each bundle has:



\- `policy\_bundle\_id` (human-oriented)

&nbsp; - Example: `bundle-2026-01-31T16-30Z-emergency-lockdown-v1`

\- `policy\_bundle\_hash` (SHA-256 of canonical representation)

&nbsp; - Example: `8e3c12cf...`



The hash:



\- Provides tamper-evidence for the policy state at decision time.

\- Allows external tools to verify the bundle content offline.



\### 5.3 Bundle Registry / Index



A simple registry structure that maps:



\- `policy\_bundle\_id` → file path + hash + metadata

\- Optionally:

&nbsp; - Activation timestamp

&nbsp; - Change summary / reviewer note



This can be a JSONL or JSON index (e.g., `data/policy\_bundles.jsonl`).



\### 5.4 Policy Diff



Given two bundle IDs:



\- `bundle\_A` and `bundle\_B`

\- Compute semantic diff:

&nbsp; - Added policies

&nbsp; - Removed policies

&nbsp; - Modified policies (fields changed, thresholds, conditions)



Output targeted at reviewers:



\- High-level summary (what changed)

\- Optionally a detailed JSON patch for tooling.



---



\## 6. Proposed Changes by Layer



\### 6.1 Data / Artifacts



New or updated artifacts:



\- `data/policy\_bundles.jsonl` (or similar)

&nbsp; - Tracks registered bundles with:

&nbsp;   - `policy\_bundle\_id`

&nbsp;   - `policy\_bundle\_hash`

&nbsp;   - `activated\_at`

&nbsp;   - `description` / `change\_summary`

\- Canonical bundle files

&nbsp; - e.g., `policy\_bundles/bundle-<id>.json`

\- Documentation:

&nbsp; - `docs/POLICY\_BUNDLE\_FORMAT.md`

&nbsp; - `docs/POLICY\_DIFF\_EXAMPLES.md` (optional)



\### 6.2 Engine / Decision Records



Extend the engine’s \*\*decision record\*\* with:



\- `policy\_bundle\_id: str`

\- `policy\_bundle\_hash: str`



Rules:



\- At evaluation time, the engine (or caller) must know:

&nbsp; - Which bundle is “currently active”

\- These fields should be:

&nbsp; - Pure metadata (do not alter decision outcome)

&nbsp; - Required for recording and replay



\### 6.3 Audit Logger



\- Include `policy\_bundle\_id` and `policy\_bundle\_hash` in each audit entry.

\- Maintain hash-chaining as in v1.0.

\- Preserve backward compatibility:

&nbsp; - Older entries may not have these fields.



\### 6.4 Replay \& Explain CLI



Extend existing replay capabilities to:



\- Show `policy\_bundle\_id` and `policy\_bundle\_hash` in:

&nbsp; - `list`

&nbsp; - `explain`

&nbsp; - `--json` output

\- Add a new mode (or subcommand) to:

&nbsp; - Show a summarized explanation:

&nbsp;   - Which bundle was active

&nbsp;   - Which policy from that bundle matched





\### 6.5 Policy Diff CLI



Introduce a focused CLI entry point, for example:



\- `policy\_bundles.py` with commands:

&nbsp; - `list` — list known bundles and hashes

&nbsp; - `show <bundle\_id>` — show bundle metadata

&nbsp; - `diff <bundle\_a> <bundle\_b>` — human-readable diff



Integration:



\- May be stand-alone initially.

\- Later may be cross-linked from decision replay output:

&nbsp; - e.g., “To see what changed since this bundle, run: `policy\_bundles diff <bundle\_id> <new\_bundle\_id>`”.



---



\## 7. Migration \& Backward Compatibility



\### 7.1 Existing Logs



\- Existing audit and enforcement logs:

&nbsp; - Will not have `policy\_bundle\_id` or `policy\_bundle\_hash`.

\- Replay tooling should:

&nbsp; - Detect absence and display a clear message

&nbsp; - Avoid breaking on older entries



\### 7.2 Incremental Adoption



Plan for incremental rollout:



1\. Introduce bundle format and registry.

2\. Start recording bundle ID + hash on new decisions.

3\. Only then layer on policy diff tooling.



---



\## 8. v1.2 Execution Plan (Steps)



Proposed sequence of steps for v1.2:



1\. \*\*Step 1 — Define Bundle Format and Registry\*\*

&nbsp;  - Write `docs/POLICY\_BUNDLE\_FORMAT.md`

&nbsp;  - Create a simple example bundle + registry entry



2\. \*\*Step 2 — Wire Policy Bundle into Decision Metadata\*\*

&nbsp;  - Extend decision records with `policy\_bundle\_id` and `policy\_bundle\_hash`

&nbsp;  - Thread these fields into:

&nbsp;    - Audit logger

&nbsp;    - Enforcement metadata (if needed)

&nbsp;    - Replay JSON



3\. \*\*Step 3 — Update Replay / Explain Output\*\*

&nbsp;  - `decision\_replay.py` shows bundle ID + hash

&nbsp;  - `--json` output includes them for tooling



4\. \*\*Step 4 — Implement Bundle Diff Tool\*\*

&nbsp;  - Stand-alone CLI for listing and diffing bundles

&nbsp;  - Simple, reviewer-readable output



5\. \*\*Step 5 — Documentation \& Review\*\*

&nbsp;  - Document how to:

&nbsp;    - Register a new bundle

&nbsp;    - Activate it

&nbsp;    - Verify a decision against the bundle

&nbsp;    - Run diffs for reviews



Each step should be \*\*tagged\*\* similarly to v1.1, for example:



\- `v1.2-step-1-policy-bundle-format`

\- `v1.2-step-2-bundle-wiring`

\- …



---



\## 9. Ready-to-Use Next Action



Once this plan is accepted, the next concrete action for implementation is:



> v1.2 Step 1 — Define bundle format and registry:

> - Create `docs/POLICY\_BUNDLE\_FORMAT.md` describing:

>   - Bundle structure

>   - Example JSON

>   - How `policy\_bundle\_id` and `policy\_bundle\_hash` are derived



This will be the first executable step once you are ready to move from planning to implementation.



