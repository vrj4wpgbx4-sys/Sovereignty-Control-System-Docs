\# Emergency Governance Model (SCS)



\## 1. Purpose



This document defines the \*\*emergency governance model\*\* for the Sovereignty Control System (SCS), including:



\- Emergency state transitions (`NORMAL → CRISIS`)

\- Emergency actions (`EMERGENCY\_LOCKDOWN`)

\- Enforcement duration and expiration

\- Forensic logging and replay



The goal is to provide \*\*clear, defensible governance\*\* for high-risk operations while producing \*\*forensic-grade evidence\*\* for later review.



---



\## 2. Core Concepts



\### 2.1 System States



SCS distinguishes between two primary governance states:



\- `NORMAL` – standard operating conditions  

\- `CRISIS` – elevated risk state that unlocks emergency authority



State transitions are explicit and auditable. Emergency powers are only available in the appropriate state.



\### 2.2 Roles



The emergency model assumes three primary roles:



\- \*\*OWNER\*\* – Sovereign authority, ultimate control  

\- \*\*GUARDIAN\*\* – Protective authority with constrained emergency rights  

\- \*\*DELEGATE\*\* – Operational authority, no emergency powers



Emergency authority is \*\*never implied\*\*; it is always determined by explicit rules.



---



\## 3. Emergency Actions



\### 3.1 DECLARE\_CRISIS



\*\*Action ID:\*\* `DECLARE\_CRISIS`  

\*\*Purpose:\*\* Transition the system from `NORMAL` to `CRISIS` under defined conditions.



\#### Authority Rules



\- OWNER  

&nbsp; - May declare CRISIS unconditionally.  

\- GUARDIAN  

&nbsp; - May declare CRISIS only with corroboration (not yet implemented in code; currently denied).  

\- DELEGATE  

&nbsp; - Can never declare CRISIS.



\#### Required Context



\- `crisis\_reason` – mandatory, human-readable explanation  

\- `incident\_reference` – mandatory, incident or ticket identifier



If required context is missing, the request is \*\*denied\*\*.



\#### State Transition



\- On ALLOW:

&nbsp; - `system\_state\_before: NORMAL`

&nbsp; - `system\_state\_after: CRISIS`

\- On DENY:

&nbsp; - `system\_state\_after` remains unchanged.



Each decision is recorded as an immutable log entry.



---



\### 3.2 EMERGENCY\_LOCKDOWN



\*\*Action ID:\*\* `EMERGENCY\_LOCKDOWN`  

\*\*Purpose:\*\* Immediately limit damage by suspending delegated authority and restricting operations during a crisis.



\#### Authority Rules



\- OWNER  

&nbsp; - May execute EMERGENCY\_LOCKDOWN when `system\_state == CRISIS`.  

\- GUARDIAN  

&nbsp; - Reserved for future logic (e.g. corroboration). Currently denied.  

\- DELEGATE  

&nbsp; - Can never execute EMERGENCY\_LOCKDOWN.



\#### Required Conditions



\- `system\_state == CRISIS`  

\- `reason\_code` in context (mandatory)  

\- Optional: `incident\_reference`



If conditions are not met, the request is \*\*denied\*\*.



\#### Enforcement



On ALLOW:



\- Enforcement is activated with:

&nbsp; - `enforcement\_id: emergency-lockdown-enforcement-v1`

&nbsp; - Start time: current UTC timestamp

&nbsp; - End time: start time + \*\*6 hours\*\* (default lockdown duration)

&nbsp; - Status: `ACTIVE`



On DENY:



\- Enforcement is not applied.



Enforcement details are recorded in the audit log entry.



---



\## 4. Audit and Forensic Logging



All emergency decisions are logged in an \*\*append-only JSONL file\*\*:



\- Path: `data/emergency\_audit\_log.jsonl`

\- Format: one JSON object per line



Each entry contains at least:



\- `decision\_id` (unique)  

\- `request\_id`  

\- `action\_id`  

\- `identity\_label` and `role`  

\- `system\_state\_before` and `system\_state\_after`  

\- `decision` (`ALLOW` or `DENY`)  

\- `reason` (policy-driven decision explanation)  

\- `context` and `resource`  

\- `requested\_at` and `timestamp`  

\- `policy\_bundle\_id`  

\- Enforcement metadata (for EMERGENCY\_LOCKDOWN):

&nbsp; - `enforcement\_applied`

&nbsp; - `enforcement\_id`

&nbsp; - `enforcement\_start`

&nbsp; - `enforcement\_end`

&nbsp; - `enforcement\_status`



\### 4.1 Hash-Chained Integrity



To protect integrity, each log entry includes:



\- `entry\_hash` – SHA-256 over the entry content plus previous hash  

\- `prev\_hash` – the `entry\_hash` of the prior log entry (or `null` for the first)



This forms a \*\*hash chain\*\*:



```text

entry\_1.entry\_hash = SHA256(json(entry\_1) + "")

entry\_2.entry\_hash = SHA256(json(entry\_2) + entry\_1.entry\_hash)

entry\_3.entry\_hash = SHA256(json(entry\_3) + entry\_2.entry\_hash)

...



