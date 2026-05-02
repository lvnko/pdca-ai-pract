Write a comprehensive handoff document and save it as `references/handoff-[feature-name]-YYYY-MM-DD.md`.

Before writing, read ALL migration files, schema context files, and prior handoff documents in /references that are related to this session's work. Do not rely on context-window memory alone — verify by reading the files.

Structure the document as follows:

### 1. Session Summary
One paragraph: what was built, what problem it solves, current status (production-ready / needs testing / partial). Be explicit.

### 2. System Architecture (if backend system was added or extended)
Document the system AS IT STANDS NOW, not just the delta this session.
- High-level design and why this approach over alternatives
- All distinct patterns used, with a minimal code example of each
- Data shape conventions (e.g. JSONB delta schema) with concrete examples
- Security model (SECURITY DEFINER, RLS, caller validation)
- Performance bounds and known limits

### 3. Migration / Iteration History
For every migration or iteration applied to reach the current state (across this AND prior sessions), write one entry per migration:
- What it added or changed
- Why it was needed — what gap or bug it was fixing
- What problem it introduced or left unresolved
- Lesson learned (write this as a rule the next developer can follow)
If there were 0 migrations, omit this section.

### 4. Files Created / Modified
Per file: what changed, key sections to read, whether the rest is unchanged.

### 5. DB / RPC / Schema Findings
All triggers, functions, RPCs, views, schema changes:
- Purpose and caller constraints
- Parameters and return shape
- Security model (who can call it, how access is gated)

### 6. Key Decisions Made
Per decision: what was chosen, why, what was explicitly rejected and why.
Only include decisions that are non-obvious or would surprise a future reader.

### 7. Pitfalls & Lessons Learned
Explicit list of things that went wrong or nearly went wrong, how they were discovered, and the rule to avoid them next time.
Write rules as actionable statements, not just descriptions.

### 8. Reference Guide: How to Extend This System
If this session established a reusable pattern, write a decision tree or numbered checklist for the next developer implementing something similar.
Include what to check before starting, what to avoid, and how to verify.

### 9. Known Limitations & Future Extensions
What is deliberately out of scope now and what would be needed to add it.

### 10. Pre-existing Issues (Do Not Investigate)
Pre-existing warnings or bugs unrelated to this session, so the next developer doesn't waste time on them.

### 11. Testing Checklist
Numbered, concrete steps to verify the feature manually end-to-end.
Each step should state the action, the expected result, and what a failure would indicate.

### 12. Exact Next Step
The single most important thing the next developer (or next session) should do first. Be precise: where to go, what to do, what to look for.