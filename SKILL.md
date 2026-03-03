---
name: forced-feedback-loop
description: >-
  Time-boxed forced feedback loop skill for any structured task — research, project work,
  document creation, code development, or analysis. Enforces continuous self-feedback cycles
  that synthesize existing information into new hypotheses. All work is documented in a
  structured work-log with line-count-based incremental reading and relative-coordinate
  cross-referencing. The agent never stops voluntarily before the allocated time expires.
  Use when the user requests sustained, iterative deep work with a time budget — e.g.
  "spend 30 minutes researching X", "work on this project for 1 hour", "iterate on this
  design until time runs out".
metadata:
  author: jho6394
  version: '3.0'
---

# Forced Feedback Loop

A time-boxed execution policy forcing continuous self-improvement cycles. Works for any
structured task: research, code, design, document, project, or analysis.

Three non-negotiable principles:

1. **Never stop before time expires.** Self-termination before deadline = FAIL.
2. **Synthesize new proposals each loop.** Every cycle must produce novelty.
3. **Document everything in work-log.** Nothing stays only in ephemeral context.

---

## 1. Start Contract

Write as Report #0 in `work-log.md` within 1 minute. Required fields:

`task_type` (research/project/document/code/analysis/design/other) | `task_goal` |
`in_scope` | `out_of_scope` | `min_required_minutes` (default: 5) |
`done_definition` (3–5 bullets) | `deliverables` | `as_of_date` | `start_time`

No field may be skipped. Assume reasonable defaults and note assumptions.

---

## 2. Work-Log System

### 2.1 File & Structure

`work-log.md` is the single source of truth. Reports stack newest-on-top.

### 2.2 Report Format

First line is always a line-count header:
```
=== Report #N | lines: <L> | elapsed: <MM:SS> | type: <feedback|milestone|synthesis> ===
```
`<L>` = total lines including this header.

### 2.3 Incremental Reading Protocol

```
1. Read line 1 → parse line-count L of latest report
2. Read lines 1..L → full latest report
3. If more context needed: read line L+1 → parse L2 → read (L+1)..(L+L2)
4. Repeat as needed, stop when sufficient
```

### 2.4 Relative-Coordinate Cross-Referencing

Reference past reports with `K-reports-below` + `line N below` (offset to that
report's header). Never use absolute report numbers inside report bodies.

### 2.5 Writing Rules

- **Append-on-top**: newest first.
- **Immediate-write**: write every thought as it occurs. Batching = FAIL.
- **No deletion**: corrections are new reports referencing the old one.

---

## 3. Domain Work Files

Three files for every task type:

| File | Purpose |
|---|---|
| `working-raw.md` | Unverified ideas, brainstorming, rough plans |
| `proved-archive.md` | Rejected/falsified items with reason + evidence |
| `proved-curated.md` | Validated items, confirmed decisions |

State transitions: new → `working-raw` → tested false → `proved-archive` / tested
valid → `proved-curated`. Items move, never delete.

For domain-specific semantics of these files, load `references/DOMAIN-TABLES.md` § 6.

### 3.1 Decision-Evidence Mapping

Every key decision gets `D#` with linked `E#` entries. Each `E#` requires `fetched_at`
and `method`. No key decision without evidence. Evidence types can be mixed.

For domain-specific evidence types and examples, load `references/DOMAIN-TABLES.md` § 1.

---

## 4. Forced Feedback Loop Engine

### 4.1 Core Loop

After the initial pass (Section 9), enter this loop until time expires:

```
WHILE elapsed_minutes < min_required_minutes:

  STEP 1 — DIAGNOSE
    Read latest 1–3 reports (incremental protocol). Identify:
    weakest decision, largest gap, untested assumption, contradictions.

  STEP 2 — PROPOSE (mandatory novelty)
    Combine 2+ existing findings → at least one NEW proposal.
    Strategies: INVERSION | COMBINATION | ANALOGY | DECOMPOSITION |
    EDGE CASE | TEMPORAL | STAKEHOLDER SHIFT | CONSTRAINT FLIP |
    TECHNOLOGY SWAP
    Log immediately in working-raw.md.

    For domain-specific proposal terminology, load `references/DOMAIN-TABLES.md` § 4.

  STEP 3 — TEST
    Validate or falsify the new proposal and weakest existing decision.
    If tools fail: log gap → lower confidence → try alternative → log "untestable".

    For domain-specific test methods, load `references/DOMAIN-TABLES.md` § 5.

  STEP 4 — UPDATE
    Move tested items to proved-archive or proved-curated.
    Update confidence scores. Write new report to work-log.md.

  STEP 5 — META-ASSESS
    "Am I progressing or circling?" / "Different angle?" / "Expert challenge?"
    If stuck 2 consecutive loops → pivot and log reason.

END WHILE
```

### 4.2 Loop Quality Rules

- Each loop MUST produce material novelty (new evidence, falsification, structural
  revision, confidence change, or architectural decision).
- No-update loop = `LOOP_FAIL` → immediate pivot.
- Minimum 1 new proposal per 2 loops averaged. Zero proposals = FAIL.
- Report types: `feedback` | `milestone` | `synthesis`.

### 4.3 Anti-Stagnation

Perspective shift | Scope zoom | Adversarial thinking | Cross-domain transfer |
Constraint relaxation.

---

## 5. Time Execution Policy

- **Idle waiting is FORBIDDEN.** No sleep, polling, or non-productive padding.
- While `elapsed < min_required_minutes`, MUST NOT: declare complete, ask user to
  stop, produce final summary, or say "this is sufficient."
- Check elapsed time each loop. Log in every report header.

---

## 6. Source Hierarchy

Three tiers: L1 (primary) → L2 (authoritative) → L3 (commentary).
Every key decision needs at least one L1 anchor. L3-only = `[LOW_CONFIDENCE]`.

For domain-specific tier definitions, load `references/DOMAIN-TABLES.md` § 2.

---

## 7. Decision Hygiene

Four-tier tags: **Verified** → **Derived** → **Unverified** → **Deferred**.
No absolute language. Every major decision needs a counter-proposal and a
falsification test. Every decision records at least one rejected alternative.
Deferred items need a `next_action` note.

For domain-specific tag names, load `references/DOMAIN-TABLES.md` § 3.

---

## 8. Uncertainty and Gap Control

Maintain in `proved-curated.md`:
- **Uncertainty Register**: Item | Type (epistemic/aleatory) | Likelihood | Impact | Next Test
- **Data-Gap Register**: Gap ID | Missing Data | Impact | Fallback | Confidence Penalty | Follow-up

If a tool/search fails: run fallback → lower confidence → log `GAP-#`.

---

## 9. Routine Order (Initial Pass)

Complete once before entering the feedback loop:

**A. Ideation** → `working-raw.md`: candidates, uncertainties, failure points.
**B. Evidence** → collect, map to claims. Log gaps.
**C. Validation** → disprove top claims. Move to archive or curated.
**D. First Synthesis** → write synthesis report. Confidence scores.

Then enter the Forced Feedback Loop (Section 4).

---

## 10. Reproducibility

Final report must include: commands/tool calls used, source list with timestamps,
assumptions, key decision points with alternatives, rerun notes.

---

## 11. Absolute Prohibitions

Before starting work and before the FAIL Gate, load and review:
`references/PROHIBITIONS.md`

24 prohibited behaviors across 5 categories (Time, Reasoning, Files, Process,
Communication). Any violation = automatic FAIL. Key highlights:
- No early termination, idle waiting, or quality shortcuts (T1–T4)
- No self-confirmation, fabrication, or skipped falsification (R1–R8)
- No batched writes, deletions, or absolute-coordinate refs (F1–F6)
- No scope drift, silent abandonment, or unchecked FAIL gates (P1–P6)
- No fake confidence, hidden uncertainty, hollow offers, or filler (X1–X6)

---

## 12. Strict FAIL Gate

Mark `INCOMPLETE` if ANY missing:

- [ ] Start contract (§1)
- [ ] Work-log with formatted reports (§2)
- [ ] Three domain work files (§3)
- [ ] Minimum runtime consumed by active work (§5)
- [ ] D#-E# mapping (§3.1)
- [ ] At least one feedback loop completed (§4)
- [ ] At least one novel proposal in loops (§4.2)
- [ ] Uncertainty + gap registers (§8)
- [ ] Reproducibility section (§10)
- [ ] No Absolute Prohibition violated (§11 / `references/PROHIBITIONS.md`)
- [ ] Every report has line-count header
- [ ] Cross-references use relative coordinates

No "conditional pass."

---

## 13. End Block

Write ONLY after `elapsed_minutes >= min_required_minutes`:

```
=== FINAL REPORT | elapsed: <MM:SS> ===
end_time: <system time>
total_feedback_loops: <N>
total_proposals_generated: <N>
total_proposals_validated: <N>
total_proposals_falsified: <N>

## Summary: Confirmed / Uncertain / Follow-up Required
## Key Artifacts
## Biggest Surprise
## Recommendation for Next Session
```

---

## 14. Multi-Session Continuity

When resuming: read `work-log.md` incrementally (§2.3) → read `proved-curated.md` →
create Report #(N+1) → continue feedback loop → use relative coordinates for all refs.
