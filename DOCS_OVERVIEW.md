\# DOCS\_OVERVIEW.md

Sovereignty Control System  

Documentation Map \& Reading Guide



---



\## Purpose



This document provides a \*\*human-readable map of the documentation set\*\* for the Sovereignty Control System (SCS).



It answers three questions clearly:



1\. What documents exist?

2\. What is each document for?

3\. Who should read which documents, and in what order?



This file introduces \*\*no new behavior\*\*, \*\*no new design\*\*, and \*\*no new requirements\*\*.  

It exists solely to reduce orientation time and reviewer confusion.



---



\## Documentation Philosophy



SCS documentation is intentionally layered.



Each layer serves a different audience and purpose:



\- \*\*Runtime verification\*\*

\- \*\*Reviewer procedure\*\*

\- \*\*Design evolution\*\*

\- \*\*Future conceptual framing\*\*



These layers are explicitly separated to prevent:

\- Scope bleed

\- Accidental commitments

\- Misinterpretation of design documents as behavior



---



\## Canonical Runtime \& Review Documentation (v1.4)



These documents define and verify the \*\*implemented system\*\*.



\### `README.md`

\*\*Audience:\*\* All readers  

\*\*Purpose:\*\* High-level orientation and intent



\- Explains what SCS is

\- States core guarantees

\- Directs reviewers to the proper entry point

\- Does not contain procedural detail



---



\### `docs/REVIEW\_INDEX.md`

\*\*Audience:\*\* External reviewers, auditors  

\*\*Purpose:\*\* Single authoritative entry point for review



\- Defines mandatory runtime anchor (`v1.4-policy-bundle-signatures`)

\- Lists required reviewer documents

\- Enforces scope boundaries

\- Separates runtime review from forward design



This is the \*\*starting point for any serious review\*\*.



---



\### `docs/REVIEWER\_WALKTHROUGH.md`

\*\*Audience:\*\* External reviewers, technical auditors  

\*\*Purpose:\*\* Step-by-step verification procedure



\- Verifies audit log integrity

\- Verifies policy bundle lifecycle and hashing

\- Verifies decision replay behavior

\- Verifies enforcement correlation

\- Demonstrates end-to-end traceability



Completing this walkthrough is sufficient to audit v1.4.



---



\### `docs/EXTERNAL\_REVIEWER\_HANDOFF.md`

\*\*Audience:\*\* Third-party reviewers, compliance teams  

\*\*Purpose:\*\* Self-contained review handoff



\- Explains how to conduct a review without system authors

\- Lists required artifacts and commands

\- Defines what is in scope vs out of scope

\- Restates expected reviewer conclusions



This document exists to make \*\*live explanation unnecessary\*\*.



---



\## Core Data \& Tooling References



These are \*\*artifacts\*\*, not narrative documents.



\### `data/audit\_log.jsonl`

\- Append-only, hash-chained decision log

\- Canonical record of all decisions



\### `data/policy\_bundles.jsonl`

\- Registry of all policy bundles

\- Lifecycle state, hashes, timestamps, signatures



\### `src/policy\_bundles\_cli.py`

\- Inspection and verification of policy bundles

\- Registry integrity checks



\### `src/decision\_replay.py`

\- Read-only decision replay

\- Correlation with enforcement events

\- Observational by design



---



\## Design Evolution Documentation (Non-Binding)



These documents describe \*\*possible future directions\*\*.  

They are \*\*not part of runtime behavior\*\* and \*\*must not be used to judge v1.4 correctness\*\*.



---



\### `docs/V15\_DESIGN\_OVERVIEW.md`

\*\*Audience:\*\* Designers, reviewers, architects  

\*\*Status:\*\* Design-only (backward-safe)



\- Describes structured signatures

\- Describes policy evolution metadata

\- Preserves all v1.4 invariants

\- Introduces no enforcement semantics



Anchor tag: `design-v1.5-anchor`



---



\## v2 Conceptual Documentation (Pre-Design)



These documents define the \*\*conceptual boundary of a future major version\*\*.  

They are intentionally explicit about risk and scope.



---



\### `docs/V2\_CONCEPT\_FRAMING.md`

\*\*Audience:\*\* System architects, governance designers  

\*\*Status:\*\* Concept framing only



\- Explains why v2 would exist at all

\- Draws a hard boundary between v1.x and v2

\- Introduces the idea of interpreted authority



Anchor tag: `design-v2-concept-framing`



---



\### `docs/V2\_RISK\_ANALYSIS.md`

\*\*Audience:\*\* Architects, auditors, reviewers  

\*\*Status:\*\* Risk analysis (design-only)



\- Enumerates risks introduced by interpreted authority

\- Identifies failure modes

\- Defines criteria under which v2 should \*not\* proceed



This document acts as a \*\*design gate\*\*, not a checklist.



---



\### `docs/V2\_GOVERNANCE\_MODEL.md`

\*\*Audience:\*\* Governance designers, reviewers  

\*\*Status:\*\* Conceptual governance model



\- Describes how authority \*could\* be interpreted

\- Emphasizes explainability and reviewer supremacy

\- Explicitly avoids implementation detail



---



\## How to Read This Repository (By Role)



\### External Reviewer

1\. `README.md`

2\. `docs/REVIEW\_INDEX.md`

3\. `docs/REVIEWER\_WALKTHROUGH.md`

4\. `docs/EXTERNAL\_REVIEWER\_HANDOFF.md`

5\. Inspect artifacts directly



Ignore v1.5 and v2 documents.



---



\### System Designer / Architect

1\. Runtime \& reviewer docs (to understand guarantees)

2\. `docs/V15\_DESIGN\_OVERVIEW.md`

3\. `docs/V2\_CONCEPT\_FRAMING.md`

4\. `docs/V2\_RISK\_ANALYSIS.md`

5\. `docs/V2\_GOVERNANCE\_MODEL.md`



---



\### Non-Technical Stakeholder

1\. `README.md`

2\. (Optional) `docs/EXTERNAL\_REVIEWER\_HANDOFF.md`



---



\## Status



\- \*\*Type:\*\* Documentation map

\- \*\*Binding:\*\* None

\- \*\*Runtime Impact:\*\* None

\- \*\*Purpose:\*\* Orientation and clarity



---



End of document.



