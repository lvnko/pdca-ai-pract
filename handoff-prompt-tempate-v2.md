Read ALL migration files, schema context files, and prior handoff documents
in /references related to this session's work BEFORE writing.
Do not rely on context-window memory alone — verify by reading the files.

Then write a comprehensive handoff document and save it as
`references/handoff-[feature-name]-YYYY-MM-DD.md`.

This document is a **knowledge transfer artifact**, not a session log.
Write for a developer cold-starting with only this document — they must
be able to understand the system, reuse the patterns, extend the work,
and avoid the pitfalls you encountered without asking questions.

---

## 1. Session Summary

One paragraph: what was built, what problem it solves, current status
(production-ready / needs testing / partial). Be explicit about readiness.

---

## 2. System Architecture

*(Skip if no backend system was added or extended.)*

Document the system **as it stands now**, not just this session's delta.

- **High-level design:** pattern used and why over alternatives
- **Security model:** SECURITY DEFINER, RLS, caller validation
- **Performance bounds:** known limits and dataset assumptions

For each distinct pattern, include:
- What entities or components use it
- An annotated code snippet usable as a copy-paste template
- Key rules and gotchas in bullets after the snippet

**Data shape conventions:** JSONB schemas, API contracts, type signatures —
with concrete examples for each operation type (CREATE / UPDATE / DELETE /
special cases).

---

## 3. Migration / Iteration History

*(Skip if no migrations this session.)*

Per migration or major iteration (this and prior sessions if building on
earlier work):

**[ID or version]**
- What it added or changed
- Why it was needed — what gap or bug it fixed
- What problem it introduced or left unresolved
- **Lesson learned:** one actionable rule the next developer can follow

---

## 4. Files & Schema Reference

Per file touched: full path, what changed, key sections to read.
For any trigger, RPC, function, or view introduced or modified, include
inline: purpose, parameters, return shape, and who can call it.
For any migration file, include: the schema change made and its
intent. For any RLS policy added or modified, document the role,
the operation (SELECT / INSERT / UPDATE / DELETE), the condition,
and why that access rule exists.

---

## 5. Pitfalls & Lessons Learned

Things that went wrong or nearly went wrong:
- How it was discovered
- The rule to avoid it next time (actionable statement, not just description)

---

## 6. Key Decisions Made

*(One-off decisions only — decisions that feed into a reusable pattern
belong in Section 7 instead.)*

Per decision:
- **Decision:** what was chosen
- **Why:** reasons in bullets
- **Rejected:** what was considered and why it lost

---

## 7. Reference Guide: How to Extend This System

*(Skip if no reusable pattern was introduced.)*

Decision tree or checklist for the next developer applying this pattern:
1. What to read before starting
2. Which pattern to use under which condition
3. What to avoid and why
4. How to verify correctness after implementation

---

## 8. Known Limitations & Future Extensions

What is out of scope and what would be needed to add it.
Point to related files or components as starting points.

---

## 9. Pre-existing Issues (Do Not Investigate)

Pre-existing warnings or bugs unrelated to this session.

---

## 10. Testing Checklist

Per scenario: action → expected result → what a failure indicates.

---

## 11. Exact Next Step

The single most important thing to do first next session.
Precise: where to go, what to do, what to look for.