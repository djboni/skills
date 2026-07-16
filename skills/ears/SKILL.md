---
name: ears
description: Write requirements using EARS (Easy Approach to Requirements Syntax) patterns. Use when user asks to write requirements, specifications, system behaviour, or mentions EARS.
---

# EARS Requirements

EARS = Easy Approach to Requirements Syntax. Structured patterns for writing clear, consistent textual requirements.
## Generic syntax

```
[While <precondition>,] [When <trigger>,] the <system> shall <response>
```

- Zero or many preconditions
- Zero or one trigger
- One system name
- One `shall` — mandatory, marks the requirement as a requirement
- One or many responses (compound or atomic, preference-driven)

## Patterns

### Ubiquitous (always active, no keyword)

```
The <system> shall <response>
```

Example: The mobile phone shall have a mass of less than XX grams.

### State driven (active while state holds)

```
While <precondition>, the <system> shall <response>
```

Example: While there is no card in the ATM, the ATM shall display "insert card to begin".

### Event driven (triggered by event)

```
When <trigger>, the <system> shall <response>
```

Example: When "mute" is selected, the laptop shall suppress all audio output.

### Optional feature (applies if feature exists)

```
Where <feature>, the <system> shall <response>
```

Example: Where the car has a sunroof, the car shall have a sunroof control panel on the driver door.

### Unwanted behaviour (detect & handle failure)

```
If <trigger>, then the <system> shall <response>
```

Example: If an invalid credit card number is entered, then the website shall display "please re-enter credit card details".

### Complex (combine patterns)

```
While <precondition>, When <trigger>, the <system> shall <response>
```

Example: While the aircraft is on ground, when reverse thrust is commanded, the engine control system shall enable reverse thrust.

## Convention

EARS requirements are stored as `.md` files in `docs/req/`. Subdirectories and file organization is up to the user. Common approaches:

- **By feature** — `login.md` (authentication concerns), `payment.md` (payment concerns), `notifications.md` (notification concerns)
- **By entity** — `user.md` (user lifecycle & data), `order.md` (order lifecycle & data), `invoice.md` (invoice lifecycle & data)
- **By actor/role** — `admin.md` (admin-only concerns), `operator.md` (operator concerns), `customer.md` (customer-facing concerns)
- **By system boundary** — `api-gateway.md` (routing & auth gateway concerns), `billing-svc.md` (billing service concerns), `queue-worker.md` (async worker concerns)
- **By lifecycle phase** — `onboarding.md` (sign-up, setup, first-use), `operations.md` (steady-state usage), `offboarding.md` (deletion, archive, goodbye)

These can be mixed and interleaved within `docs/req/`.

Frontmatter is always present. IDs are optional.

When using IDs:
- **IDs MUST be UNIQUE**
- **Changing the ID of a requirement is FORBIDDEN** We do not want broken links and links that point to the wrong requirement
- IDs can be in any order or sequence in the document
- When creating use the biggest ID+1
- Instead of using sub requirements REQ-LOGIN-001\[abc\] use a plain number and link to the requirement it refines.

```markdown
---
file: login
system: Web Application
stakeholders: Customer, Admin
---

# Login

The system shall mask passwords during entry.

## REQ-LOGIN-001
When "login" is submitted, the system shall validate credentials.
- Rationale: prevent unauthorized access
- Source: ACME-SEC-001

## REQ-LOGIN-002
While too many failed attempts, the system shall lock the account for 5 minutes.
- Rationale: brute force mitigation
- Source: ACME-SEC-002

## REQ-LOGIN-003
If an invalid token is presented, the system shall redirect to the login page.
- Rationale: session integrity
```

## Common requirement fields

These bullet fields can accompany any requirement. All are optional.

- **Rationale** — why this requirement exists. e.g. `prevent unauthorized access`
- **Source** — origin of the requirement. e.g. `ACME-SEC-001`, `GDPR Art. 17`, `stakeholder interview 2026-03`
- **Priority** — importance ranking. e.g. `Must`, `Should`, `Could`, `Won't` (MoSCoW) or `P0`-`P3`
- **Status** — lifecycle stage. e.g. `Draft`, `Reviewed`, `Approved`, `Rejected`
- **Owner** — who owns delivery or sign-off. e.g. `team-auth`, `@jdoe`
- **Risk** — potential impact if wrong. e.g. `Critical`, `High`, `Medium`, `Low`
- **Verification** — how compliance is confirmed. e.g. `Test`, `Inspection`, `Analysis`, `Demo`
- **Links** — related reqs or references. e.g. `REQ-LOGIN-001`, `REQ-PAY-004`

## Reviewer checklist

Verify each requirement against these criteria:

- [ ] Follows correct EARS pattern for its intent
- [ ] `<system>` is consistent across all requirements
- [ ] Every `While` precondition is a valid state, not an action
- [ ] Every `When` trigger is a discrete event, not a condition
- [ ] Every `Where` references a real feature variant
- [ ] Every `If` describes an undesirable situation, not normal flow
- [ ] Every response contains `shall` + testable behaviour (not "shall be robust")
- [ ] Complex requirements only combine patterns when semantics require it
- [ ] No ambiguity: each clause has exactly one interpretation
- [ ] Each requirement is atomic (one behaviour per statement)

## Workflow

1. Identify system (or subsystem) name and stakeholders — ask the user if not provided. Each requirement can reference a different system/subsystem.
2. Elicit required behaviours from the user — ask targeted questions to surface all scenarios
3. Classify each behaviour into a fitting EARS pattern — multiple may apply, preference-driven
4. Write each requirement using the pattern template
5. Review: every clause (While/When/Where/If) has clear semantics, every response is testable
6. Revise: address review findings, update requirements, repeat steps 5-6 until clean
