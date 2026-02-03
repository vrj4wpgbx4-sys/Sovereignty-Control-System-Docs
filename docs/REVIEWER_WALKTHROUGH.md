# REVIEWER_WALKTHROUGH.md
Sovereignty Control System  
Canonical Review Guide — v1.4 (Policy Bundles, Signatures, Replay Context)

---

## 1. Purpose

This document provides a complete, procedural walkthrough for **external reviewers** to independently verify the integrity, auditability, and determinism of the Sovereignty Control System (SCS) as of **v1.4**.

The walkthrough is designed so that:

- No trust in system operators or authors is required
- No privileged access is required
- No live system access is required
- Verification is performed entirely from recorded artifacts

All claims are validated through **observable, immutable evidence**.

---

## 2. What the System Claims (Reviewer Context)

SCS is a governance and authority engine whose core claim is:

> **Authority is explicit, policy-bound, recorded, and reviewable after the fact without reinterpretation.**

Specifically, SCS claims that:

- Decisions are deterministic
- Policy state is immutable once referenced
- Audit history is append-only and tamper-evident
- Enforcement is downstream of recorded decisions
- Historical records are never re-evaluated

This walkthrough exists to verify those claims.

---

## 3. Mandatory Review Anchor

All runtime and policy reviews **must** anchor to the immutable v1.4 release tag:

```bash
git checkout v1.4-policy-bundle-signatures
