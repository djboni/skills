---
description: Security audit — scan repo frameworks, research vulnerabilities, verify against OWASP
---

# Security Audit

Orchestrate a security audit across the current repository. The results directory is `./docs/security-audit/!`date +%Y-%m-%d`/`, with each step written to a named file inside it.

## Step 1 — Reconnaissance (read-only sub-agent)

Spawn a single `explore` agent with this task:

> Thoroughly explore the repository at the current working directory. Identify every framework, library, and major dependency used — check package.json, requirements.txt, Cargo.toml, go.mod, and any other dependency manifests. For each framework found, note: name, version constraint, and what it is used for in the project. Also identify the production start command (check scripts, Makefile, docker-compose, README). Return a structured summary with all findings.

Wait for this agent to complete before proceeding.

## Step 2 — Vulnerability research (two read-only sub-agents, parallel)

After step 1 completes, spawn these two `general` agents **in parallel**:

**Agent A — Framework vulnerabilities:**
> Using the following list of frameworks and versions: `[paste step 1 findings here]`, search the internet for known vulnerabilities, CVEs, and security advisories for each framework. Focus on recent (last 12 months) and critical/high severity issues. For each vulnerability found, note: CVE ID, affected framework+version, severity, brief description, and recommended mitigation. Return a structured report.

**Agent B — OWASP Top 10:**
> Search for the latest OWASP Top 10 (current year). For each item in the Top 10, return: the category name, a brief description, common attack vectors, and general prevention strategies. Return a structured summary.

Wait for both agents to complete. Write the combined results of both agents to `02-research.md`.

## Step 3 — Start production server (master agent)

1. Build the frontend if a build step exists (e.g. `npm run build` in `frontend/`).
2. Install backend dependencies if needed.
3. Start the production server in the background. Capture the exact command used and the port it listens on. If the server fails to start, write the error to `03-server.md` and **stop** — do not proceed to step 4.

## Step 4 — Verification (parallel sub-agents)

Spawn multiple `general` agents in parallel, each verifying a specific area. Distribute work so no agent handles more than 2–3 frameworks or one OWASP category. Each agent receives: the framework list, the vulnerability findings from step 2, the OWASP Top 10 from step 2, and the running server details.

Each verification agent reads the relevant source files and checks for:
- The specific CVEs and vulnerabilities found in step 2
- The OWASP Top 10 categories relevant to its assigned area
- Common misconfigurations and insecure defaults

Each agent returns a list of findings, each tagged with: OWASP category, severity (critical/high/medium/low/info), affected file(s), line number(s), and a concrete remediation suggestion.

After all verification agents complete, the master agent aggregates their results into a final report that includes:
- Summary by severity
- OWASP Top 10 mapping
- Prioritized remediation plan
- List of files that need changes with line references

Write the final report to `04-findings.md`.

## Step 5 — Cleanup

Stop the production server started in step 3.
