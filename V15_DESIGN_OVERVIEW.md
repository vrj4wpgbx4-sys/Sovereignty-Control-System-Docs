# V15_DESIGN_OVERVIEW.md
Sovereignty Control System — v1.5 Design (Concept Only)

---

## 1. Purpose and Scope

This document defines the **design intent for v1.5** of the Sovereignty Control System (SCS).

v1.5 is explicitly **design-only**.

It introduces:
- No code
- No runtime behavior
- No policy enforcement changes
- No reinterpretation of historical records

This document exists to describe how future versions of SCS *may* improve **governance clarity and reviewer comprehension** while strictly preserving all v1.x guarantees.

It is a **design contract**, not an implementation plan.

---

## 2. Context: What v1.4 Established

As of v1.4, SCS provides a stable and review-ready governance foundation:

- Policy bundles are immutable, hash-addressed artifacts
- Policy bundles move through explicit, non-destructive lifecycle states
- Decisions reference exact policy bundle IDs and hashes
- Audit logs are append-only and hash-chained
- Decision replay is deterministic and observational
- Policy bundle signatures exist but are informational only

v1.4 is considered complete and correct as a runtime system.

---

## 3. Non-Negotiable Invariants

All v1.5 design concepts must preserve the following invariants without exception:

1. **No Retroactive Policy Application**  
   Historical decisions are never re-evaluated against newer policy bundles.

2. **Append-Only Audit History**  
   Audit records remain immutable, hash-chained, and offline-verifiable.

3. **Explicit Policy Anchoring**  
   Every decision references a specific `policy_bundle_id` and `policy_bundle_hash`.

4. **Non-Destructive Policy Lifecycle**  
   Policy bundles and registry entries are never modified or deleted.

5. **Replay Is Observational Only**  
   Replay tooling does not re-run authority logic or alter interpretation.

6. **No Hidden Authority**  
   All authority sources are visible in recorded artifacts.

Any design that violates these constraints is out of scope for v1.5.

---

## 4. Problem Statement

Although v1.4 is stable and auditable, several reviewer-facing gaps remain:

### 4.1 Unstructured Signatures

Policy bundle signatures currently exist as opaque or loosely defined data.  
There is no standardized way to express:
- Who signed
- In what role
- With what intent
- Over what scope

This limits their usefulness for reviewers.

---

### 4.2 Under-Specified Policy Evolution

Policy lifecycle transitions are recorded, but:
- The *intent* behind new bundles is not captured
- There is no standardized change summary
- Reviewers must manually infer “what changed” by inspecting policy content

---

### 4.3 Reviewer Cognitive Load

The system is reviewable, but review effort is higher than necessary:
- Policy lineage must be reconstructed manually
- Endorsement context is implicit
- There is no narrative layer explaining evolution

v1.5 addresses these gaps **as design only**, without introducing enforcement logic.

---

## 5. v1.5 Design Goals

The goals of v1.5 are to:

1. Make policy evolution **human-legible**
2. Make signatures **interpretable but non-authoritative**
3. Reduce reviewer effort without increasing system complexity
4. Remain fully backward-compatible with v1.4 artifacts
5. Preserve replay and audit semantics exactly as-is

---

## 6. Structured Signatures (Design Only)

### 6.1 Current v1.4 Conceptual Shape

In v1.4, signatures are optional and opaque:

```json
"signatures": [
  {
    "raw": "implementation-defined or opaque"
  }
]
