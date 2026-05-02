Please write a comprehensive handoff document in markdown and save it as `handoff-[feature-name]-[YYYY-MM-DD].md`.

This document is a **knowledge transfer artifact** — not just a session log. It must be useful to a developer who has zero context on this session: they should be able to understand the system, reuse the patterns, extend the work, and avoid the pitfalls you encountered.

---

Structure it exactly as follows:

## Session Summary

Two to three paragraphs:
1. What was the goal and what was delivered (include version numbers, migration numbers, or PR references if applicable).
2. What the system does at a high level, and why the approach was chosen.
3. Production status: is it ready? What remains?

---

## Architecture: [System Name]

### High-Level Design

Explain the core design — not just what it does, but **why this architecture was chosen** over alternatives. Include:
- The architectural pattern (e.g. trigger-based, event-driven, hook-based)
- Why this pattern was selected (what constraints or properties it satisfies)
- Key trade-offs accepted

### [Major Subsystem or Pattern A]

- Explain what it covers (which tables, components, routes)
- Include a **representative code snippet** (SQL, TypeScript, etc.) that can be used as a template
- Annotate the snippet with inline comments on non-obvious parts
- List key rules and gotchas in bullet points after the snippet

### [Major Subsystem or Pattern B]

(Same format as above. Add as many subsystems as needed.)

### Data Contracts / Shape Conventions

Document any JSONB shapes, API contracts, type signatures, or data formats that future developers must follow exactly. Include rules for each operation type (CREATE / UPDATE / DELETE / special cases).

---

## Implementation Details

### [Key Feature or Component]

- How it works end-to-end
- Include relevant code snippets (functions, hooks, helpers) with brief annotations
- Note any non-obvious behaviors or edge cases

(Add subsections for each significant feature.)

---

## Design Decisions & Rationale

For each major decision made during this session:

**Decision:** [What was decided, in one sentence]

**Why:**
- Bullet list of reasons (constraints, performance, simplicity, UX, etc.)

**Rejected alternative:** [What was considered but not chosen, and why it was rejected]

---

## Testing Checklist

A numbered list of exact browser or system tests to verify the feature is working correctly. Each item should be specific enough that a developer can run it without asking questions:
- What to click / call / query
- What the expected URL, output, or state should be
- What to verify

---

## Known Limitations & Future Extensions

### [Subsystem Name]

**Not implemented (out of scope for this session):**
- List each limitation with a note on why it was deferred

**If extending in future:**
- Point to related files, components, or patterns to reuse
- Suggest the right extension points

---

## Reference: How to [Repeat This Pattern for New Cases]

A decision tree or step-by-step checklist for the next developer who needs to apply the same pattern to a new entity, table, route, or feature.

Format as:
1. If [condition] → use [approach] (link to the relevant pattern above)
2. If [condition] → use [approach]
...

Then include a **migration / implementation checklist** with checkboxes:
- [ ] Step one
- [ ] Step two
- ...

---

## Pre-Existing Issues (Do Not Investigate)

List any known pre-existing errors, warnings, or tech debt that exist in the codebase and are **unrelated** to this session. Future Claude should not spend time on these.

---

## Exact Next Steps

A numbered list of precise, unambiguous actions:
1. What to verify first (include file path, route, or query to run)
2. What to test second
3. What to archive or communicate

Each step should be specific enough that a developer can execute it without additional context.

---

## Style Notes

- Be **specific and technical** throughout. No vague prose.
- Include **actual code snippets** for any pattern that will be reused — templates are more useful than descriptions.
- Capture **conclusions from struggles**: if something took a long time to figure out, the finding deserves a dedicated section or callout.
- Write for a developer who is cold-starting with only this document.
- This document will be used to brief the next Claude Code session.