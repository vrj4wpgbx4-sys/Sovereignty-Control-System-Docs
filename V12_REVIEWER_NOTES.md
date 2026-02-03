\# Sovereignty Control System — v1.2 Reviewer Notes



\## Scope of v1.2



Version \*\*v1.2\*\* strengthens the system's accountability around \*what policy state governed each decision\* by introducing:



\- \*\*Policy bundles\*\*:

&nbsp; - Canonical JSON bundles describing policies and delegations.

&nbsp; - Stored under `policy\_bundles/`.

\- \*\*Bundle registry\*\*:

&nbsp; - Append-only JSONL ledger at `data/policy\_bundles.jsonl`.

&nbsp; - Maps `policy\_bundle\_id` → `policy\_bundle\_hash` + file path + metadata.

\- \*\*Decision ↔ bundle linkage\*\*:

&nbsp; - Each decision record can carry:

&nbsp;   - `policy\_bundle\_id`

&nbsp;   - `policy\_bundle\_hash`

\- \*\*Reviewer-facing tooling\*\*:

&nbsp; - `decision\_replay.py` — list/explain decisions and check log integrity.

&nbsp; - `policy\_bundles\_cli.py` — inspect bundle registry and bundle contents.

&nbsp; - `policy\_bundles\_diff.py` — compare two bundles (once more than one exists).



The goal is that an external reviewer can, \*\*without trust in the operator\*\*, verify:



1\. That the audit log is intact (no tampering).

2\. Which policy bundle governed any given decision.

3\. That the recorded bundle hash corresponds to an actual bundle file.

4\. How policy bundles evolve over time.



---



\## 1. Files and tools added or extended in v1.2



\### 1.1 Policy bundle format



The canonical format is documented in:



\- `docs/POLICY\_BUNDLE\_FORMAT.md`



A concrete example bundle used in v1.2:



\- `policy\_bundles/bundle-2026-01-31-emergency-v1.json`



This bundle encodes:



\- `policy-001` — Sovereign emergency lockdown in CRISIS state.

\- `policy-002` — Guardian emergency lockdown with dual approval.

\- A simple delegation from the Sovereign to the Guardian with constraints.



\### 1.2 Bundle registry



Registry path:



\- `data/policy\_bundles.jsonl`



Format:



\- One JSON object per line.

\- Example entry:



```json

{

&nbsp; "policy\_bundle\_id": "bundle-2026-01-31-emergency-v1",

&nbsp; "policy\_bundle\_hash": "058f15ef83b9935a93395c9ac57027a3957dd21e076dfe36585ef820ed576ae2",

&nbsp; "created\_at": "2026-01-31T16:45:00Z",

&nbsp; "activated\_at": "2026-01-31T16:49:50Z",

&nbsp; "source\_path": "policy\_bundles/bundle-2026-01-31-emergency-v1.json",

&nbsp; "description": "Emergency authority and lockdown governance policies for sovereign owner and guardian approvals.",

&nbsp; "status": "active"

}



