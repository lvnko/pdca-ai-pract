# Claude Code Strategy Cheatsheet
> Compiled from strategy session — covers quota management, model selection, commands, and handoff workflow.

---

## 1. Understanding Quota

- **Session limit:** 5-hour rolling window from your first prompt
- **Weekly limit:** Separate cap that resets weekly — both dimensions burn simultaneously
- **How tokens compound:** Every message resends the full conversation history. A 30-message session means message 30 carries the weight of all 29 before it — this is the #1 cause of fast quota drain
- **Context rot:** As context grows, response quality degrades before you even hit the hard limit — responses become inconsistent and Claude starts ignoring earlier constraints
- **Sessions panel:** Old sessions sitting in the sessions list do NOT consume tokens. Only the currently active session costs anything

---

## 2. Commands Reference

| Command | What it does | When to use |
|---|---|---|
| `/compact` | Compresses conversation into a summary, preserves task state | Mid-task when context is large, between phases, before quota reset resumes |
| `/clear` | Wipes conversation history entirely, project files stay | Starting a completely unrelated new task |
| `/model` | Switch models mid-session without losing context | Anytime you need to change model for cost or capability reasons |
| `/status` | Shows remaining session usage (CLI only, not VS Code extension) | Periodically to avoid surprise quota walls |

### `/compact` vs `/clear` — the decision rule

> **Still working on the same task?** → `/compact`
> **Genuinely done, moving to something unrelated?** → `/clear`
> **Never use `/clear` mid-task** — you will lose all context and burn tokens re-orienting

### VS Code note
`/status` is CLI-only. In VS Code, use the **"View usage"** link in the quota warning banner, or check **Settings → Usage** in claude.ai — this shows both session and weekly limits at a glance.

---

## 3. Model Selection Strategy

### By development phase

| Phase | Model | Reason |
|---|---|---|
| Planning — scope, architecture, file list | **Opus** | Deep reasoning, but short focused exchange keeps cost controlled |
| Execution — writing code, editing files | **Sonnet** | Fast, capable, fraction of Opus cost |
| Routine edits — renaming, formatting, simple fixes | **Haiku** | Cheapest, sufficient for mechanical work |
| Hard blocker / genuinely stuck | **Opus** | Worth the cost when Sonnet has failed |
| Writing tests | **Haiku or Sonnet** | Usually formulaic |
| Code review / typecheck pass | **Sonnet** | Needs judgment but not deep reasoning |
| Generating handoff summary | **Haiku** | Pure extraction, no reasoning needed |
| Absorbing handoff doc (session start) | **Sonnet** | Needs to map relationships correctly before executing |

### Core principle
> **Opus for thinking. Sonnet for doing. Haiku for mechanical work.**

---

## 4. Handling Integration Problems (The Sonnet Thrashing Trap)

### The problem
Sonnet iterating through multiple failed approaches isn't a capability problem — it's a **diagnosis problem**. Letting it thrash burns tokens without resolution.

### The 2-attempt rule
Give Sonnet a maximum of **2 attempts** on any integration or debugging problem. If unresolved after 2 tries, stop and call Opus for diagnosis.

### The surgical Opus pattern
```
Sonnet fails twice
    ↓
/model opus
"Do not write any code yet. Diagnose only: what is the root 
cause of [problem]? List 2-3 most likely causes ranked by probability."
    ↓
Opus returns short diagnosis (cheap)
    ↓
/model sonnet
"Based on that diagnosis, implement fix for cause #1 only."
    ↓
Works → done
Doesn't work → back to Opus for re-diagnosis, not more Sonnet thrashing
```

> Use Opus as a **consultant you call in briefly**, not a developer you leave running all day.

---

## 5. Session Workflow Template

```
[Start of session]
/status or check usage in Settings         ← know your budget
attach handoff-[feature]-[date].md         ← load prior context
/model sonnet
"Read handoff.md and confirm understanding 
before I give you the next task."          ← verify absorption

[Planning a new feature]
/model opus
"Plan [feature], list files you'll touch 
and what you'll do in each."               ← cheap planning exchange
Review and correct the plan in plain English

[Execution]
/model sonnet                              ← stays on Sonnet for coding

[Between phases]
/compact                                   ← preserve state, free tokens
/model sonnet                              ← continue next phase

[Integration problem — Sonnet fails twice]
/model opus  →  diagnose only
/model sonnet  →  execute fix

[End of session]
/model haiku
[paste handoff prompt — see Section 6]     ← generate handoff doc

[Next unrelated task]
/clear                                     ← fresh start
```

---

## 6. Handoff Document Prompt

Use this at the end of every session. Run on **Haiku**.

### LITE Version:
```
The dev result is tested and is passed.

Please write a handoff document in markdown and save it as `handoff-[feature-name]-[YYYY-MM-DD].md`.

Structure it exactly as follows:

## Session Summary
One paragraph: what was the goal of this session and what was achieved.

## Files Created / Modified
List every file touched with its full path and a one-line description of what changed.

## DB / RPC / Schema Findings
List any database schema details, RPC signatures, JSONB shapes, cascade rules, or API contracts discovered or confirmed during this session.

## Key Decisions Made
For each decision: what was decided AND why (include rejected alternatives if relevant).

## Pre-existing Issues (Do Not Investigate)
List any known pre-existing errors, warnings, or technical debt unrelated to this session's work. Claude should not spend time on these next session.

## Exact Next Step
One precise, unambiguous instruction: the first thing Claude should do next session, including file name, function name, or component name where applicable.

Be specific and technical throughout. Avoid vague prose.
This document will be used to cold-start the next Claude Code session.
```

### COMPREHENSIVE Version
```
Write a comprehensive handoff document and save it as
`references/handoff-[feature-name]-YYYY-MM-DD.md`.

Before writing, read ALL migration files, schema context files, and prior
handoff documents in /references that are related to this session's work.
Do not rely on context-window memory alone — verify by reading the files.

Structure the document as follows:

### 1. Session Summary
One paragraph: what was built, what problem it solves, current status
(production-ready / needs testing / partial). Be explicit.

### 2. System Architecture (if backend system was added or extended)
Document the system AS IT STANDS NOW, not just the delta this session.
- High-level design and why this approach over alternatives
- All distinct patterns used, with a minimal code example of each
- Data shape conventions (e.g. JSONB delta schema) with concrete examples
- Security model (SECURITY DEFINER, RLS, caller validation)
- Performance bounds and known limits

### 3. Migration / Iteration History
For every migration or iteration applied to reach the current state
(across this AND prior sessions), write one entry per migration:
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
Explicit list of things that went wrong or nearly went wrong,
how they were discovered, and the rule to avoid them next time.
Write rules as actionable statements, not just descriptions.

### 8. Reference Guide: How to Extend This System
If this session established a reusable pattern, write a decision tree
or numbered checklist for the next developer implementing something similar.
Include what to check before starting, what to avoid, and how to verify.

### 9. Known Limitations & Future Extensions
What is deliberately out of scope now and what would be needed to add it.

### 10. Pre-existing Issues (Do Not Investigate)
Pre-existing warnings or bugs unrelated to this session, so the next
developer doesn't waste time on them.

### 11. Testing Checklist
Numbered, concrete steps to verify the feature manually end-to-end.
Each step should state the action, the expected result, and
what a failure would indicate.

### 12. Exact Next Step
The single most important thing the next developer (or next session)
should do first. Be precise: where to go, what to do, what to look for.
```

---

## 7. CLAUDE.md Auto-Compact Reminder

Add this to your `CLAUDE.md` to make Claude prompt you at phase boundaries:

```
After completing each phase or major task, always end your response with:
"✅ Phase complete. Please run /compact before proceeding to the next phase."
```

---

## 8. Quick Reference — Warning Signs

| Signal | Action |
|---|---|
| Quota warning banner appears | Run `/compact` immediately, don't wait for the wall |
| Sonnet tries same problem twice with no fix | Switch to Opus for diagnosis only |
| Claude responses getting vague or repetitive | Context rot — run `/compact` |
| Starting a completely new feature | `/clear` + fresh session |
| Resuming after quota reset | `/compact` first, then re-anchor with one sentence briefing |

---

## 9. Key Numbers to Know

- **92k tokens freed** = roughly a 70,000-word novel worth of context — a significant compact result
- **Avoid using the final 20%** of context window for complex tasks — quality degrades before the hard cutoff
- **5-hour rolling window** resets from your first prompt, not from a fixed clock time
- Weekly limit and session limit are **independent** — both burn simultaneously

---

*Last updated: based on strategy session covering quota management, model selection, VS Code extension behaviour, and handoff workflow.*
