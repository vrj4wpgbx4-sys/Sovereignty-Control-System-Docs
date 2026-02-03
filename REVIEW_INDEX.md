# REVIEW_INDEX.md
Sovereignty Control System  
Reviewer Entry Point

---

## Purpose

This index provides a clear, authoritative starting point for external reviewers evaluating the Sovereignty Control System (SCS).

It establishes:
- The mandatory review anchor
- The canonical reviewer documents
- The scope boundary between runtime behavior and forward-looking design

All documents referenced here are read-only and verifiable using repository artifacts alone.

---

## Mandatory Review Anchor (Runtime Review)

All runtime and policy reviews **must** anchor to the immutable release tag:

```bash
git checkout v1.4-policy-bundle-signatures


Everything *after* that — which is where the v1.5 and v2 links live — **never makes it into the file**.

That’s why:
- `git status` says clean
- You feel like nothing is changing
- We keep re-reviewing the same top section

---

## Let’s break the loop (one decisive move)

We are going to do this **once**, cleanly.

### 🔴 Do this exactly:

1. Open `docs/REVIEW_INDEX.md`
2. **Select ALL** (Ctrl+A)
3. **Delete everything**
4. Paste **this entire file**, from `# REVIEW_INDEX.md` down to `End of index.`  
   (the full version below)
5. Save

---

## ✅ THE FULL FILE (this is longer — scroll)

**Paste *all* of this, not just the top:**

```markdown
# REVIEW_INDEX.md
Sovereignty Control System  
Reviewer Entry Point

---

## Purpose

This index provides a clear, authoritative starting point for external reviewers evaluating the Sovereignty Control System (SCS).

It establishes:
- The mandatory runtime review anchor
- The canonical reviewer documents
- The scope boundary between implemented behavior and forward-looking design

All documents referenced here are read-only and verifiable using repository artifacts alone.

---

## Mandatory Review Anchor (Runtime Review)

All runtime and policy reviews **must** anchor to the immutable release tag:

```bash
git checkout v1.4-policy-bundle-signatures
