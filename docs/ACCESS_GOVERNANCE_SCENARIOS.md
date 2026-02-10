# Access Governance Scenarios (SCS)

## Purpose

This document provides a plain-language walkthrough of how the
Sovereignty Control System (SCS) governs **day-to-day access and
delegated authority**.

It is written for:
- auditors
- reviewers
- governance committees
- both technical and non-technical stakeholders

All scenarios described below are derived from executed code paths,
recorded audit entries, and forensic replay output.

---

## System Context

Before any access decision occurs, SCS assumes:

- an explicit identity and role for each actor
- a defined resource or scope to which access applies
- a deterministic decision engine that evaluates each request against policy
- an append-only, hash-linked access audit log
- a forensic replay mechanism capable of reconstructing decisions later

This model operates under normal system conditions and complements
the Emergency Governance Model.

---

## Scenario 1: OWNER Grants Scoped Access

### Situation

An OWNER needs to grant limited, time-bounded access to a collaborator
in order to perform routine operational work.

### Action Requested

- **Action:** `GRANT_ACCESS`  
- **Actor:** Ronald (OWNER)  
- **Target Identity:** Alice  
- **Access Scope:** `READ_ONLY`  
- **Duration:** 4 hours  
- **Purpose:** Operational support  

### Decision Outcome

- **Decision:** ALLOW  
- **Reason:** Owner authority confirmed; access granted  
- **Policy Bundle:** `access-governance-v1`  

### Enforcement

- **Applied:** YES  
- **Enforcement ID:** `access-grant-…` (unique per grant)  
- **Start Time:** Decision timestamp (UTC)  
- **End Time:** Start time + 4 hours  

During this window, Alice holds `READ_ONLY` access strictly within the
bounds defined by the governing policy.

### Forensic Evidence

The access audit log records:

- the acting identity and role  
- the target identity  
- the access scope  
- the approved duration and stated purpose  
- the decision outcome  
- the enforcement window  
- cryptographic linkage to prior audit entries  

This record can be replayed at any time to explain why access was
granted and under what conditions.

---

## Scenario 2: DELEGATE Attempts to Grant Access (Denied)

### Situation

A DELEGATE attempts to grant elevated access to another party, which
is not permitted under the access governance model.

### Action Requested

- **Action:** `GRANT_ACCESS`  
- **Actor:** Bob (DELEGATE)  
- **Target Identity:** Charlie  
- **Access Scope:** `ADMIN`  
- **Duration:** 8 hours  
- **Purpose:** Improper escalation attempt  

### Decision Outcome

- **Decision:** DENY  
- **Reason:** Only OWNER may grant access  
- **Policy Bundle:** `access-governance-v1`  

### Enforcement

- **Applied:** NO  
- **Enforcement ID:** None  
- **Start / End:** Not applicable  

SCS does not silently ignore this attempt. The denied action is fully
recorded as evidence of an unauthorized escalation attempt.

### Forensic Evidence

The access audit log records:

- the attempted action  
- the actor identity and role  
- the requested scope and stated purpose  
- the denial reason  
- confirmation that no enforcement was applied  

This ensures that improper or unauthorized access attempts are
preserved for later audit or review.

---

## Forensic Replay

For any recorded access decision, SCS supports forensic replay by
`decision_id`.

Replay output includes:

- the action and decision outcome  
- the acting identity and role  
- the target identity  
- the access scope, duration, and purpose  
- the governing policy bundle  
- the enforcement window (if applicable)  
- the evidence source (audit log)  

Replay is generated directly from recorded audit entries.
No interpretation or inference is applied.

---

## Why These Scenarios Matter

Together, these scenarios demonstrate that SCS:

- permits legitimate, bounded access grants by an OWNER  
- denies and records improper attempts by DELEGATEs  
- treats denied actions as first-class forensic evidence  
- preserves a clear, auditable history of access decisions  
- can explain access changes long after execution  

These properties define a **forensic-grade access governance system**.

---

## Summary

SCS treats access as an explicit, governed decision rather than an
assumed system property.

By combining deterministic decision-making, time-bounded enforcement,
and forensic replay, SCS provides a trustworthy foundation for
day-to-day access and delegation governance.
