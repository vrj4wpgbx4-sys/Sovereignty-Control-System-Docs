# Access & Delegation Governance Model (SCS)

## Purpose

This document defines how the Sovereignty Control System (SCS) governs
**day-to-day access and delegated authority** under normal operating conditions.

It establishes:

- who is permitted to grant access
- who may receive access
- what access applies to
- how access is revoked or expires
- how misuse or unauthorized attempts are handled and preserved

This model complements the Emergency Governance Model and represents
the baseline governance layer for routine operations.

---

## Governance Intent

Most systems treat access as a configuration detail.
SCS treats access as a **governed decision**.

Every access change is:
- explicitly authorized,
- bounded in scope and duration,
- enforced deterministically,
- recorded as forensic evidence,
- and explainable after the fact.

This ensures that access decisions can be reviewed, audited, and
defended independently of the systems that execute them.

---

## Core Principles

1. **Access is explicit**  
   No actor possesses implicit authority.

2. **Delegation is bounded**  
   All access is constrained by scope, purpose, and duration.

3. **Authority is role-based**  
   Decisions depend on the role of the actor, not identity alone.

4. **Misuse is preserved**  
   Unauthorized or denied attempts are recorded as first-class evidence.

5. **Access is explainable**  
   Every decision can be reconstructed through forensic replay.

---

## Roles

### OWNER
- May grant or revoke any access
- May delegate authority
- Retains ultimate control and accountability

### DELEGATE
- May exercise access that has been explicitly granted
- May not grant, extend, or escalate access
- May request additional access through governed processes

### GUARDIAN
- Reserved for recovery, oversight, or succession scenarios
- Not active in this model

---

## Governed Actions

### GRANT_ACCESS

Grants scoped, time-bounded access from an OWNER to a target identity.

**Required context:**
- `target_identity`
- `access_scope`
- `duration`
- `purpose`

---

### REVOKE_ACCESS

Revokes previously granted access prior to expiration.

**Required context:**
- `target_identity`
- `access_scope`
- `reason`

---

### ACCESS_REQUEST

Represents a request by a DELEGATE for access they do not currently possess.

**Required context:**
- `requested_scope`
- `purpose`

---

## Decision Rules (High-Level)

| Action         | Actor Role | Decision |
|---------------|-----------|----------|
| GRANT_ACCESS  | OWNER     | ALLOW    |
| GRANT_ACCESS  | DELEGATE  | DENY     |
| REVOKE_ACCESS | OWNER     | ALLOW    |
| REVOKE_ACCESS | DELEGATE  | DENY     |
| ACCESS_REQUEST| DELEGATE  | EVALUATE |
| ACCESS_REQUEST| OWNER     | ALLOW    |

These rules are evaluated deterministically by SCS policy bundles.

---

## Enforcement

When access is granted:

- An enforcement record is created
- Access is active only within the approved scope and duration
- Enforcement automatically expires when the duration ends

When access is revoked or expires:

- Enforcement is terminated
- The termination event is logged

SCS governs **authority and enforcement metadata**; execution remains
the responsibility of the underlying system.

---

## Forensic Logging

Every access decision produces an append-only audit record containing:

- actor identity and role
- target identity
- action requested
- decision outcome
- governing policy bundle
- enforcement metadata (if applicable)
- cryptographic linkage to prior entries

Denied actions are recorded with the same fidelity as allowed actions.

---

## Forensic Replay

Given a decision identifier, SCS can reconstruct:

- who initiated the action
- what authority was evaluated
- why access was granted or denied
- what scope was affected
- when enforcement began and ended
- which policy governed the decision

Replay is generated directly from recorded logs.
No inference or interpretation is applied.

---

## Integration Boundary

This model is intentionally **system-agnostic**.

SCS does not require:
- direct database access
- shared schemas
- embedded SDKs
- UI integration

External systems consult SCS at **governance decision points** and
respect the resulting ALLOW or DENY outcome.

---

## Summary

The Access & Delegation Governance Model ensures that routine access
within SCS is:

- explicit rather than assumed
- bounded rather than open-ended
- enforceable rather than advisory
- auditable rather than opaque
- explainable after the fact

Together with Emergency Governance, this model establishes SCS as a
forensic-grade authority and access governance system.
