---
description: Ship a feature — from shared understanding to committed code. Grills, specs, requirements, tickets, implements with TDD, reviews, and commits.
---

# Ship

Orchestrate the full lifecycle of a feature: understand → spec → requirements → tickets → implement → review → commit.

## Conventions

```
.scratch/<title>/
├── issues/           ← tickets (one per file, numbered 01-)
├── handoff.md        ← context preservation
└── MR.md             ← merge request summary

docs/spec/NNNN-<title>.md   ← structured spec (sequential number)
docs/req/<domain>.md        ← EARS requirements
```

Find the next spec number by listing `docs/spec/` and taking the highest `NNNN` prefix + 1. Pad to 4 digits.

## Phase A — Planning

The master does all planning steps directly using the available skills. No sub-agent delegation.

### A.1 — Grilling

Invoke `/grilling` with `/domain-modeling`. Interview the user relentlessly about the topic from their initial message until a shared understanding is reached. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. Ask one question at a time.

For each fact that can be found in the codebase, look it up rather than asking. The decisions, though, are the user's.

When resolved, apply glossary terms to `CONTEXT.md`. Record hard-to-reverse decisions as ADRs in `docs/adr/`.

Do not proceed until the user confirms shared understanding.

### A.2 — Spec

Invoke `/to-spec`. Synthesize the grilling output into a spec. Write to `docs/spec/NNNN-<title>.md` using the next sequential number.

Completion: file exists with Problem Statement, Solution, User Stories, Implementation Decisions, Testing Decisions, Out of Scope.

### A.3 — Requirements

Invoke `/ears`. Write EARS requirements to `docs/req/<domain>.md` — one file per domain touched. Reference the spec at `docs/spec/NNNN-<title>.md` as the source. Use unique requirement IDs.

Run the EARS reviewer checklist before finishing:
- [ ] Every requirement follows correct EARS pattern
- [ ] `<system>` is consistent
- [ ] Every `shall` response is testable
- [ ] No ambiguity, each clause has one interpretation
- [ ] Each requirement is atomic

### A.4 — Tickets

Invoke `/to-tickets`. Write tickets to `.scratch/<title>/issues/` as numbered local files (01-*, 02-*, etc.). Each ticket must reference the EARS requirements it implements by ID. Block by dependency order.

Quiz the user on the breakdown:
- Does the granularity feel right?
- Are the blocking edges correct?
- Should any tickets be merged or split?

Iterate until approved.

### A.5 — Handoff

Write `.scratch/<title>/handoff.md` capturing:
- What was decided and why (link to ADRs)
- Spec path: `docs/spec/NNNN-<title>.md`
- Requirements paths: `docs/req/<domain>.md`
- Ticket list with statuses (all `pending`)
- Current phase and next step

This file is for YOU. After compaction, read it back to restore context.

### A.6 — User review gate

Present the plan to the user:
- Spec summary
- Requirements summary
- Ticket breakdown with dependencies

**WAIT for explicit approval.** Do not proceed to Phase B until the user confirms.

If the user requests rework, go back to A.1 with the feedback.

### A.7 — Commit the plan

After user approval, commit all planning artifacts:

```bash
git status -s
git add docs/spec/ docs/req/ docs/adr/ CONTEXT.md
git commit -m "docs(<title>): planning — spec, requirements, and tickets"
```

## Phase B — Implementation

Work the **frontier**: tickets whose blockers are all done. For a linear chain, that is top to bottom.

### B.1 — Restore context

Read `.scratch/<title>/handoff.md` to restore context after compaction.

### B.2 — Implement

Spawn a fresh `general` sub-agent. Note its `task_id` for potential rework resume.

The sub-agent prompt:

> Invoke `/implement` and `/tdd`. Implement the following ticket:
>
> [paste full ticket content]
>
> Relevant requirements:
> [paste EARS requirements referenced by this ticket]
>
> Project conventions:
> - Follow existing code style and patterns
> - Read `CONTEXT.md` for domain vocabulary
> - Respect ADRs in the area you are touching
> - Only ASCII in code. Use uA, kOhm, ->, !=, ..., etc.
> - Run typechecking and tests regularly, full suite at the end
> - Do NOT commit — the orchestrator handles commits
>
> Return a summary of what was implemented and which files were modified.

Completion: sub-agent returns with code written and tests passing.

### B.3 — Review

Spawn a `general` sub-agent. Note its `task_id` for potential rework resume.

The sub-agent prompt:

> You are one agent performing both axes of code review.
>
> **Diff:** run `git diff` to see the changes.
>
> **Spec:** read `docs/spec/NNNN-<title>.md` and the relevant EARS requirements at `docs/req/<domain>.md`.
>
> Ticket:
> [paste full ticket content]
>
> Relevant requirements:
> [paste EARS requirements referenced by this ticket]
>
> **Standards:** check for Fowler code smells (Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, Refused Bequest). If `CODING_STANDARDS.md` or `CONTRIBUTING.md` exists, those override the baseline. Skip anything tooling already enforces.
>
> Report:
> - **Standards axis**: file/hunk, violation type, severity (hard/judgement), fix suggestion
> - **Spec axis**: missing requirements, scope creep, wrong implementations — quote the spec line
>
> End with: total findings per axis, worst issue per axis. Under 500 words.

Completion: report delivered. Zero hard violations on both axes = pass.

### B.4 — Rework loop

If the review finds hard violations or missing requirements:

1. Resume the **same implement sub-agent** (use `task_id`):
   > The review found issues. Fix them:
   > [paste review findings]
   > Do NOT commit.

2. Resume the **same review sub-agent** (use `task_id`):
   > Re-review the changes. Check that the previous findings are resolved. Run `git diff` to see what changed.

3. Repeat until clean (zero hard violations on both axes).

### B.5 — Commit

After review passes, the master commits:

```bash
git status -s
git add [modified files]
git commit -m "<type>(<scope>): <description>"
```

Use semantic prefixes: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`. Reference the ticket number in the body if helpful.

Update the ticket status in `.scratch/<title>/issues/NN-*.md` to `done`.

Move to the next ticket. Repeat B.1–B.5.

## Phase C — Wrap-up

After all tickets are committed, write `.scratch/<title>/MR.md`:

```markdown
# MR: <title>

## Summary
[1-3 sentence description of the feature]

## Changes
[bulleted list of what was changed and why]

## Requirements satisfied
[link to spec and requirements files]

## Commits
[output of `git log --oneline` for this feature's commits]

## Full diff
[output of `git diff` covering all commits]
```
