Policy Bundle Format

Sovereignty Control System — v1.3

Purpose

This document defines the canonical format and lifecycle semantics for policy bundles in the Sovereignty Control System.

A policy bundle is a cryptographically identifiable snapshot of all governance policy inputs required to make deterministic decisions at a point in time. Policy bundles enable:

Verifiable linkage between decisions and the policies in force

Tamper-evident governance review

Deterministic replay and explainability

Human-readable diffs between policy states

Explicit reasoning about policy evolution over time

This capability builds directly on:

v1.0 — Audit log integrity and provenance

v1.1 — Decision replay and enforcement correlation

v1.2 — Policy bundle anchoring and reviewer tooling

Version v1.3 formalizes policy bundle lifecycle and retirement semantics.

Core Principles

Policy bundles are designed around the following rules:

Snapshot, not delta
A bundle represents a complete policy state, not an incremental change.

Deterministic serialization
The same bundle content must always produce the same cryptographic hash.

Human + machine usable
Bundles must be readable by humans and verifiable by tools.

Decision metadata only
Referencing a bundle does not alter decision logic; it documents governing context.

Immutable once referenced
A bundle must never be modified after it is referenced by a decision.

Lifecycle-aware governance
Bundles progress through explicit lifecycle states that constrain their use.

Terminology

Policy Bundle
A structured document containing all policies and governance inputs in force at a given time.

policy_bundle_id
A human-oriented identifier for a bundle (stable across time).

policy_bundle_hash
A SHA-256 hash over the canonical serialized bundle content.

Bundle Registry
An append-only ledger mapping bundle IDs to hashes and lifecycle metadata.

Lifecycle State
A declared status that governs whether a bundle may be used for new decisions.

Bundle File Format
File Type

Format: JSON

Encoding: UTF-8

One bundle per file

Recommended path:

policy_bundles/<policy_bundle_id>.json

Required Top-Level Fields

Each policy bundle file must contain the following fields:

{
  "policy_bundle_id": "string",
  "description": "string",
  "policies": [ ... ],
  "delegations": [ ... ],
  "created_at": "ISO-8601 timestamp"
}

Field meanings

policy_bundle_id
Human-readable identifier. Must match the registry entry.

description
Plain-language explanation of the bundle’s purpose and scope.

policies
Array of policy definitions governing authority decisions.

delegations
Array of delegation definitions (may be empty).

created_at
Timestamp indicating when the bundle content was authored.

Bundle Registry Format

Policy bundle lifecycle is managed outside the bundle file itself via the registry:

data/policy_bundles.jsonl


Each line is a single JSON object representing one bundle.

Required Registry Fields
{
  "policy_bundle_id": "string",
  "policy_bundle_hash": "sha256-hex",
  "source_path": "string",
  "created_at": "ISO-8601 timestamp",
  "status": "draft | active | retired | superseded",
  "description": "string"
}

Optional Lifecycle Fields
{
  "activated_at": "ISO-8601 timestamp",
  "retired_at": "ISO-8601 timestamp",
  "superseded_by": "policy_bundle_id"
}

Lifecycle States (v1.3)

Policy bundles move through explicit lifecycle states. These states are normative and enforced by tooling.

draft

Bundle is defined but must not govern real decisions.

Intended for review, testing, or staging.

Replay tools may display draft bundles, but decisions must not reference them.

active

Bundle may govern new decisions.

Exactly one bundle per governance domain is typically active at a time.

Decisions recorded during this period may reference this bundle.

retired

Bundle must not govern new decisions.

Retained for historical review and replay.

Decisions made while the bundle was active remain valid and reviewable.

superseded

Bundle has been replaced by another bundle.

superseded_by must reference the successor policy_bundle_id.

Semantically equivalent to retired, but explicitly points forward.

Decision Referencing Rules

The following rules apply system-wide:

New decisions may only reference active bundles.

Historical decisions remain valid even if their governing bundle is later retired or superseded.

Replay tooling must surface lifecycle state when displaying decisions.

Replay tooling must not rewrite or reinterpret historical decisions based on later lifecycle changes.

A reviewer should be able to determine:

“Was this bundle valid at the time the decision was made?”

without ambiguity.

Hashing & Verification

The policy_bundle_hash is computed over the canonical serialized bundle file.

Registry tools and replay tools rely on this hash for integrity verification.

Lifecycle changes do not affect the bundle hash.

Any change to bundle content requires a new bundle ID and hash.

Relationship to Decision Replay

When a decision references a policy bundle, replay tools must show:

policy_bundle_id

policy_bundle_hash

The bundle’s lifecycle state at replay time

Any retirement or supersession metadata (if applicable)

This ensures that reviewers can distinguish:

What governed the decision then

What is considered valid governance now

Non-Goals

This document does not define:

How bundles are authored or approved

How policy logic is evaluated

Cryptographic signatures or authorship proofs

Legal invalidation of historical decisions

Those concerns are intentionally deferred to later versions.

Summary

Policy bundles are immutable snapshots of governance state.

Lifecycle metadata:

Governs when a bundle may be used

Preserves why historical decisions remain reviewable

Makes policy evolution explicit and auditable

With v1.3, the Sovereignty Control System can now answer:

“Which policies governed this decision, and were they valid at the time?”

with precision and integrity.