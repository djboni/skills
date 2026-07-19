---
description: Ship a feature — from shared understanding to committed code. Grills, specs, requirements, tickets, implements with TDD, reviews, and commits.
---

# Ship

Orchestrate the full lifecycle of a feature: understand → spec → requirements → tickets → implement → review → commit.

## Conventions

```
.scratch/<title>/
├── issues/           ← tickets (one per file, numbered 01-)
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

### A.5 — User review gate

Present the plan to the user:
- Spec summary
- Requirements summary
- Ticket breakdown with dependencies

**WAIT for explicit approval.** Do not proceed to Phase B until the user confirms.

If the user requests rework, go back to A.1 with the feedback.

### A.6 — Commit the plan

After user approval, commit all planning artifacts:

```bash
git status -s
git add docs/spec/ docs/req/ docs/adr/ CONTEXT.md
git commit -m "docs(<title>): planning — spec, requirements, and tickets"
```

## Phase B — Implementation

Delegate the entire implementation loop to a single **orchestrator** sub-agent. The orchestrator cannot run commands, edit files, implement, or test — it can only delegate to worker sub-agents and coordinate the loop. This keeps the master's context clean across the full ticket set.

### B.1 — Spawn the orchestrator

**MUST spawn a fresh `general` sub-agent.** Record its `task_id` — required for completion verification.

The orchestrator receives everything it needs up front (it cannot read files on its own):

> You are an implementation orchestrator. You delegate all work to worker sub-agents — you never run commands, edit files, implement, or test yourself.
>
> Work the **frontier**: tickets whose blockers are all done. For a linear chain, that is top to bottom.
>
> For each ticket, run this loop:
>
> **1. Implement** — Spawn a fresh `general` sub-agent with:
> > Invoke `/implement` and `/tdd`. Implement the following ticket:
> >
> > [ticket content]
> >
> > Relevant requirements:
> > [EARS requirements referenced by this ticket]
> >
> > Project conventions:
> > - Follow existing code style and patterns
> > - Read `CONTEXT.md` for domain vocabulary
> > - Respect ADRs in the area you are touching
> > - Only ASCII in code. Use uA, kOhm, ->, !=, ..., etc.
> > - Run typechecking and tests regularly, full suite at the end
> > - Do NOT commit — the orchestrator handles commits
> >
> > Return a summary of what was implemented and which files were modified.
>
> Record the implement sub-agent `task_id`.
>
> **2. Review** — Spawn a fresh `general` sub-agent with:
> > You are one agent performing both axes of code review.
> >
> > **Diff:** run `git diff` to see the changes.
> >
> > **Spec:** read `docs/spec/NNNN-<title>.md` and the relevant EARS requirements at `docs/req/<domain>.md`.
> >
> > Ticket:
> > [ticket content]
> >
> > Relevant requirements:
> > [EARS requirements referenced by this ticket]
> >
> > **Standards:** check for Fowler code smells (Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, Refused Bequest). If `CODING_STANDARDS.md` or `CONTRIBUTING.md` exists, those override the baseline. Skip anything tooling already enforces.
> >
> > Report:
> > - **Standards axis**: file/hunk, violation type, severity (hard/judgement), fix suggestion
> > - **Spec axis**: missing requirements, scope creep, wrong implementations — quote the spec line
> >
> > End with: total findings per axis, worst issue per axis. Under 500 words.
>
> Record the review sub-agent `task_id`.
>
> **3. Rework loop** — If the review finds hard violations or missing requirements:
>    1. Resume the **implement sub-agent** by `task_id`:
>       > The review found issues. Fix them:
>       > [review findings]
>       > Do NOT commit.
>    2. Resume the **review sub-agent** by `task_id`:
>       > Re-review the changes. Check that the previous findings are resolved. Run `git diff` to see what changed.
>    3. Repeat until clean (zero hard violations on both axes).
>
>    Always resume the existing agents by `task_id` — never spawn fresh ones for rework.
>
> **4. Commit** — After review passes:
>    ```bash
>    git status -s
>    git add [modified files]
>    git commit -m "<type>(<scope>): <description>"
>    ```
>    Use semantic prefixes: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`. Reference the ticket number in the body if helpful.
>    Update the ticket status in `.scratch/<title>/issues/NN-*.md` to `done`.
>
> Move to the next ticket.
>
> When all tickets are done, return a summary: which tickets were completed, what files were modified per ticket, and any issues encountered.

Master passes:
- All tickets (full content of each `.scratch/<title>/issues/NN-*.md`)
- All referenced EARS requirements (full content of each `docs/req/<domain>.md`)
- Project conventions (from AGENTS.md)

Completion: orchestrator `task_id` recorded AND orchestrator returns with all tickets implemented, reviewed, and committed. If no `task_id` was recorded, the step is not done.

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

---

Feature:
