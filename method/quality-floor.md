# Quality Floor

---

## The principle

There is a minimum standard below which nothing ships. This standard is defined before work begins, not after work is reviewed.

The quality floor is not aspirational. It is not a "best practice checklist." It is the hard line between acceptable and unacceptable. Anything above the floor can be discussed, prioritized, or deferred. Anything below the floor does not ship.

---

## Why a floor, not a ceiling

Ceilings are aspirational — "we should aim for 100% test coverage." Floors are operational — "nothing ships without tests for its public interface."

The difference matters. Aspirational targets create guilt when missed. Operational floors create clarity: this passed or it didn't. No ambiguity.

A quality floor does not prevent excellence. It prevents negligence.

---

## Defining the floor

The quality floor is defined per project during setup. It should be written as a checklist where every item is binary — yes or no, passes or doesn't.

### Structure

```markdown
# Quality Floor — [Project Name]

## Code
- [ ] Every public function has at least one test
- [ ] No unhandled errors in any execution path
- [ ] No hardcoded secrets, credentials, or environment-specific values
- [ ] Dependencies are pinned to specific versions

## Architecture
- [ ] Agent boundaries are respected — no cross-boundary violations
- [ ] Communication between agents follows the defined contracts
- [ ] No agent accesses another agent's internal state directly

## Documentation
- [ ] Every delivered artifact has a corresponding entry in the session log
- [ ] Contracts are updated when interfaces change
- [ ] Handoffs describe what was delivered and where it lives

## Delivery
- [ ] All session success criteria are met before marking complete
- [ ] No regressions — existing functionality still works
- [ ] Partial deliveries are flagged as partial, never as complete
```

### Rules for the floor

1. **Binary.** Every item passes or fails. No "mostly passes." No "passes for now."
2. **Measurable.** Every item can be verified without subjective judgment. "Code is clean" is not a floor item. "No function exceeds 50 lines" is.
3. **Stable.** The floor doesn't change mid-sprint. It's set during project setup and revised only at explicit review points.
4. **Known.** Every agent knows the quality floor before starting work. It's part of the project setup, not a surprise during review.

---

## Who checks the floor

### Self-check

Every agent checks its own output against the quality floor before delivering. The session log includes a quality check section for this purpose.

### Architect review

The Architect verifies the floor on review. If an agent's self-check says "passed" but the Architect finds a violation, that's a process problem — either the floor is unclear or the agent doesn't understand it. Both are fixable.

### Automated checks (when applicable)

Where possible, floor items should be automated — linters, test runners, type checkers. Automated checks remove subjectivity and catch violations before human review.

---

## When something fails the floor

1. **It does not ship.** No exceptions. No "we'll fix it later." Below the floor means below the floor.
2. **The agent fixes it.** The same agent that produced the work addresses the violation. Not a different agent. Not the Architect.
3. **The fix is logged.** The session log records what failed, why, and how it was fixed. This builds a history of common floor violations, which can inform future prevention.

---

## Raising the floor

The quality floor can be raised — never lowered. When the team's capability grows, when tooling improves, or when the project matures, the floor moves up.

Raising the floor is an architectural decision made by the Architect. It's announced before it takes effect, and all agents are updated.

Examples of raising the floor:
- v1: "Every public function has at least one test." → v2: "Every public function has tests for happy path and primary error case."
- v1: "No hardcoded secrets." → v2: "All configuration is loaded from environment variables with validation at startup."

---

## The floor is not the goal

The floor is the minimum. It exists to prevent the worst outcomes, not to describe the best ones. Good work exceeds the floor. Great work makes the floor feel conservative.

But the floor is non-negotiable. The goal can flex. The floor cannot.

---

> The floor answers one question: "Can we ship this without being embarrassed?" If the answer is no, it stays until it's yes.
