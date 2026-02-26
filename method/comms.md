# Inter-Agent Communication Protocol

---

## The principle

Agents in block3 do not communicate in real time. They communicate through files committed to the repository. Every message is a markdown document. Every exchange is async, readable, and auditable by any human at any time.

There are no live connections between agents. No shared memory. No message queues. No hidden state.

> If it's not in the repo, it didn't happen.

---

## Why files

- **Auditability.** Every communication is committed to git. The entire history of agent interaction is traceable. No message is lost, overwritten, or invisible.
- **Async by default.** Agents do not depend on each other being "online." One agent writes a file. Another reads it in a later session. The system works across time, not just across space.
- **Human-readable.** The Architect — or any team member — can read the comms directory and understand exactly what agents said to each other. No decoding. No tooling required.
- **Simplicity.** Files are the lowest-common-denominator interface. Every language, every tool, every platform can read and write files. No integration overhead.

---

## Directory structure

```
comms/
├── contracts/              ← agreed interfaces between agents
│   ├── backend-frontend.md
│   └── backend-translation.md
├── handoffs/               ← structured deliveries between agents
│   ├── backend-to-frontend-2025-01-15.md
│   └── frontend-to-backend-2025-01-16.md
└── flags/                  ← blockers, questions, escalations
    ├── frontend-blocked-2025-01-16.md
    └── backend-question-2025-01-15.md
```

### contracts/

Contracts define the interface between two agents. They are written during setup and updated only when both agents (and the Architect) agree. A contract specifies:

- What agent A sends to agent B
- What agent B returns to agent A
- The format of the exchange
- Error conditions and how they are communicated

Contracts are named by the two agents involved: `[agent-a]-[agent-b].md`.

### handoffs/

Handoffs are structured deliveries. When an agent completes work that another agent depends on, it writes a handoff file. A handoff contains:

- What was delivered
- Where it lives in the repo
- What the receiving agent needs to know
- Any deviations from the contract

Handoffs are named: `[from]-to-[to]-[date].md`.

### flags/

Flags are signals that something needs attention. Three types:

- **Blocked.** "I can't proceed because [reason]. I need [what] from [who]."
- **Question.** "I need a decision on [what] before I can continue."
- **Escalation.** "Something is wrong beyond my scope. The Architect needs to look at this."

Flags are named: `[agent]-[type]-[date].md`.

---

## Contract format

```markdown
# Contract: [Agent A] ↔ [Agent B]

## Direction: A → B

### What A sends
- [description of data/artifact]
- Format: [file type, structure, schema]
- Location: [where in the repo]

### What B expects
- [validation rules B applies]
- [required fields or properties]

## Direction: B → A

### What B returns
- [description of response/artifact]
- Format: [file type, structure, schema]
- Location: [where in the repo]

### What A expects
- [validation rules A applies]
- [required fields or properties]

## Error protocol
- If A sends invalid input: [what happens]
- If B cannot complete the work: [what happens]
- If either agent is unavailable: [what happens]

## Last updated: [date]
## Agreed by: [agent A], [agent B], Architect
```

---

## Handoff format

```markdown
# Handoff: [From Agent] → [To Agent]
**Date:** [YYYY-MM-DD]
**Session:** [session ID or reference]

## Delivered
- [list of artifacts delivered]
- Location: [paths in the repo]

## Context
[What the receiving agent needs to know to use this delivery]

## Contract compliance
- [x] Matches contract specification
- [ ] Deviations: [list any deviations and why]

## Open items
- [anything incomplete or requiring follow-up]
```

---

## Flag format

```markdown
# Flag: [blocked | question | escalation]
**From:** [agent name]
**Date:** [YYYY-MM-DD]
**Priority:** [low | medium | high]

## Situation
[What happened or what needs to be decided]

## Impact
[What is affected if this isn't resolved]

## What I need
[Specific action or decision required]

## From whom
[Who can resolve this — another agent or the Architect]
```

---

## Rules

1. **Never bypass the comms directory.** If an agent needs something from another agent, it goes through a handoff or flag. No informal channels.
2. **Contracts before work.** Two agents do not exchange artifacts until their contract is written and agreed upon.
3. **Handoffs are explicit.** "I pushed the code" is not a handoff. A handoff file is a handoff.
4. **Flags are not optional.** If an agent is blocked, it writes a flag. Silently waiting is not acceptable.
5. **The Architect reads everything.** All comms are visible. There is no private channel between agents.
6. **Contracts evolve, not break.** When a contract needs to change, the new version is written and both agents agree before the old one is replaced. No unilateral contract changes.

---

> Communication is structure. If agents can only talk through files, every conversation becomes part of the architecture.
