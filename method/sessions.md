# Sessions — Log Format and Conventions

---

## The principle

Every session produces a log. The log is committed to the repo. The system watches itself build.

Session logs are not an afterthought. They are first-class citizens of the block3 method. A project's session log history is a complete record of how the system was built — what was decided, what was tried, what worked, and what didn't.

---

## What a session is

A session is a single working period between the Architect and one or more agents. It has:

- A **brief** — what the session should accomplish (written before the session starts)
- A **duration** — a defined working period, not an open-ended exploration
- A **log** — what actually happened (written when the session ends)

Sessions are the atomic unit of progress in block3. Work happens in sessions. Between sessions, the state is the repo.

---

## Session brief

The brief is written by the Architect (or an orchestrator) before the session starts. It becomes the agent's `custom.md` — the third layer of its identity stack.

### Brief format

```markdown
# Session Brief
**Date:** [YYYY-MM-DD]
**Agent:** [agent name]
**Session ID:** [sequential or descriptive ID]

## Objective
[What this session should accomplish — one clear statement]

## Context
[Current project state relevant to this session]
[What was delivered in the last session]
[Any blockers or flags to be aware of]

## Scope
[What the agent should do in this session]
[What the agent should NOT do in this session]

## Success criteria
[How we'll know this session achieved its goal]
[Measurable or verifiable conditions]
```

### Rules for briefs

- **One objective.** A session has one goal. If there are two goals, there are two sessions.
- **Explicit scope.** The agent should know what it is and isn't supposed to do before it starts.
- **Success is defined, not assumed.** "Improve the API" is not a success criterion. "API handles error responses with structured JSON for all 4xx/5xx codes" is.

---

## Session log

The log is written at the end of the session. It records what actually happened, not what was supposed to happen.

### Log format

```markdown
# Session Log
**Date:** [YYYY-MM-DD]
**Agent:** [agent name]
**Session ID:** [matches the brief]

## Objective
[Restated from the brief]

## What was done
- [Concrete actions taken, in order]
- [Files created, modified, or deleted]
- [Decisions made during the session]

## What was delivered
- [Artifacts produced]
- [Location in the repo]
- [Status: complete | partial | blocked]

## Decisions made
- [Any architectural or implementation decisions]
- [Reasoning behind non-obvious choices]

## Issues encountered
- [Problems faced during the session]
- [How they were resolved, or why they weren't]

## What's next
- [What the next session should address]
- [Dependencies or blockers for the next session]
- [Flags raised for the Architect or other agents]

## Quality check
- [ ] Delivered artifacts meet the quality floor
- [ ] No regressions introduced
- [ ] Session scope was respected (no unscoped work)
- [ ] Handoffs written for any cross-agent deliveries
```

---

## Directory structure

```
sessions/
├── briefs/
│   ├── 001-backend-api-setup.md
│   ├── 002-frontend-layout.md
│   └── 003-backend-auth.md
└── logs/
    ├── 001-backend-api-setup.md
    ├── 002-frontend-layout.md
    └── 003-backend-auth.md
```

- Brief and log share the same session ID.
- Files are named: `[ID]-[agent]-[short-description].md`.
- IDs are sequential. The ordering tells the story of how the project was built.

---

## Conventions

### Session naming

Use a three-digit sequential ID followed by the agent name and a short description:
- `001-backend-project-scaffold`
- `002-frontend-design-system`
- `003-backend-auth-endpoints`

### Session cadence

There is no prescribed cadence. Some projects run multiple sessions a day. Others run one per week. The rule is: each session has a clear objective and produces a log. The gap between sessions doesn't matter — the repo captures the state.

### Multi-agent sessions

If a session involves multiple agents, each agent produces its own log. The Architect's log coordinates them:

```
sessions/logs/
├── 004-architect-sprint-review.md
├── 004-backend-api-refactor.md
└── 004-frontend-component-update.md
```

Same session ID, different perspectives.

### Session review

The Architect reviews session logs before writing the next brief. This is not optional. The review ensures:
- Delivered work matches the objective
- Quality floor was met
- No unscoped work was done
- Issues raised are addressed in the next session
- The project state is accurately reflected

---

## Why this matters

Session logs solve three problems:

1. **Continuity.** Agents don't remember between sessions. The log is how the next session knows what happened in the last one. Without logs, every session starts from scratch.

2. **Accountability.** The log records what was done, not what was planned. If the delivered work doesn't match the objective, the log makes that visible. No hiding.

3. **Learning.** Over time, the session history reveals patterns — which types of sessions succeed, which struggle, which agents are consistently efficient, where bottlenecks form. The logs make the process improvable.

---

> The system watches itself build. Every session is a record. Every record is a lesson.
