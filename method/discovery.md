# Discovery — How agents emerge

---

## The principle

Agents are not chosen from a menu. They are discovered from the project itself.

Every system has natural boundaries — places where responsibilities separate, where failure should be isolated, where change happens at different speeds. These boundaries exist whether you name them or not. Discovery makes them visible.

The result is a set of agents whose scope, identity, and communication contracts come directly from the structure of what you're building. No guessing. No templates. No "pick three agents from this list."

---

## Before you begin

Discovery requires honesty. The most common failure mode is describing the project you wish you were building instead of the one you're actually building. Aspirational scope, imagined users, features that "might be needed later" — all of these produce agents that don't map to reality.

The questions below are designed to cut through that. Answer them as they are, not as you'd like them to be.

---

## Step 1 — What is the project?

**Goal:** Establish the truth of what you're building. Not the pitch. Not the feature list. The truth.

### Questions to answer

**What problem are you actually solving?**
Not "we're building a platform for X." What specific friction exists in someone's day that this project removes? If you can't describe the problem without referencing your solution, you don't understand the problem yet.

**Who uses it, and at what moment in their day?**
Not a persona. A moment. Are they at their desk? On their phone during a commute? In the middle of something urgent? The moment shapes everything — interface decisions, response time requirements, error tolerance.

**What is the core loop?**
The user does X. The system does Y. The user gets Z. Every product has a core loop, and everything else is either supporting that loop or distracting from it. Define the loop in one sentence. If you can't, the project's scope isn't clear enough yet.

**What is explicitly out of scope?**
This is the most important question in Step 1. What you're *not* building defines the project more precisely than what you are building. Name the features you've discussed and rejected. Name the integrations you're not doing yet. Name the users you're not serving.

### What a good Step 1 output looks like

> **Project:** A document translator that takes PDF files in one language and produces translated PDFs that preserve the original layout.
>
> **Problem:** Translators currently re-create layouts manually after translating text. Hours of formatting work per document.
>
> **User moment:** A professional translator at their desk, mid-project, with a stack of documents due this week.
>
> **Core loop:** User uploads a PDF → system extracts text while mapping layout → system translates text → system rebuilds the PDF with translated text in the original layout → user downloads the result.
>
> **Out of scope:** Real-time translation. Handwritten documents. Documents without extractable text (scanned images without OCR). Collaborative editing. User accounts beyond basic authentication.

### What a bad Step 1 output looks like

> "We're building an AI-powered translation platform with support for multiple file formats, real-time collaboration, and enterprise features."

This describes ambition. It doesn't describe a system you can build.

---

## Step 2 — What are the responsibilities?

**Goal:** Identify the obligations the system carries. Not features — obligations. Things that must always be true, regardless of which version you're building.

### Questions to answer

**What must always be true?**
These are your invariants. They don't change between v1 and v10. Examples:
- Uploaded documents must never be accessible to other users.
- Translated text must map back to its original position.
- The system must never silently drop content during translation.

Invariants are the load-bearing walls of the system. You can redesign the rooms around them, but removing them brings the structure down.

**What changes often versus rarely?**
This reveals where stability lives and where volatility lives. The things that change often need flexible boundaries. The things that change rarely need protection from unnecessary change.

| Changes often | Changes rarely |
|--------------|----------------|
| UI components, copy, styling | Data model, auth logic |
| Translation engine (swap providers) | File processing pipeline structure |
| Supported file formats (expand over time) | Core loop sequence |

**What could fail independently without bringing everything down?**
This is the isolation question. If the translation engine goes down, can the user still upload files? If the layout engine fails, does the entire system crash? Each independent failure domain is a candidate for a boundary.

### What a good Step 2 output looks like

A list of obligations with clear failure isolation:

> **Invariants:**
> - Document privacy is absolute — no cross-user access, ever.
> - Layout fidelity — translated document matches original structure.
> - No silent data loss — every element in the source appears in the output, or the system flags what it couldn't process.
>
> **Independent failure domains:**
> - Translation engine failure → user sees "translation unavailable," but uploads and history still work.
> - Layout reconstruction failure → user gets translated text without layout, with a clear warning.
> - Auth failure → nothing works. This is a hard dependency.

---

## Step 3 — Where are the boundaries?

**Goal:** Find the natural seams in the system. This is where agents emerge.

### Questions to answer

**What talks to the outside world?**
Anything that crosses the system boundary — APIs, third-party services, user-facing interfaces, email, webhooks. Each external interface is a boundary candidate because it has different reliability characteristics, different error modes, and different rates of change than internal logic.

**What stays strictly internal?**
Pure business logic. State management. Data transformation. Things that never see the outside directly. Internal components have different rules — they can trust their inputs more, they don't need to handle network failures, and they can be refactored without coordinating with external systems.

**What do you never want to mix?**
This is intuition formalized. Most experienced developers have a visceral reaction to certain combinations:
- Auth logic mixed with business logic.
- Data transformation mixed with presentation.
- Third-party API calls mixed with core algorithms.

That reaction is a boundary signal. Trust it.

**Where would you be afraid to touch without breaking something else?**
Fear of change reveals coupling. If modifying the translation engine makes you worry about the upload flow, those two things are coupled and should be separated. Each area of fear is a boundary that doesn't exist yet but should.

### How boundaries become agents

Each natural boundary becomes a candidate agent. The mapping is direct:

```
Boundary                          → Agent
──────────────────────────────────────────────
User-facing interface             → Frontend Agent
API and external communication    → Backend Agent
Translation processing            → Translation Agent
Document layout and reconstruction→ Layout Agent
Authentication and user management→ Auth Agent
```

Not every boundary needs its own agent. Small boundaries can be absorbed by adjacent agents. The test is: **does this boundary have its own obligations, its own failure modes, and its own rate of change?** If yes, it's an agent. If it's just a module within a larger responsibility, it stays inside an existing agent.

### Validating your boundaries

After identifying candidate agents, verify them against these checks:

**The independence test.** Can this agent do its job without knowing the internal details of other agents? If it needs to know how another agent's data is structured internally, the boundary is in the wrong place.

**The communication test.** Can the interaction between this agent and its neighbors be described as a simple contract? "I send you X, you return Y." If the contract requires sharing internal state, the boundary is in the wrong place.

**The replacement test.** Could you replace this agent's implementation entirely (different language, different approach) without the other agents noticing? If yes, the boundary is clean.

**The failure test.** If this agent fails, do the other agents degrade gracefully or crash? If they crash, the isolation isn't real.

---

## After discovery

Once boundaries are identified and validated, three things happen:

### 1. Souls are drafted

Each agent gets a soul — its permanent identity. The soul comes from the boundary's obligations:

- What does this agent protect?
- What does it never compromise on?
- How does it communicate?
- How does it handle failure?

Use [`souls/_template.md`](../souls/_template.md) as the starting structure.

### 2. Agent prompts are written

Each agent gets a project-specific prompt defining its scope, stack, rules, and communication contracts for this particular project. The agent_prompt is where discovery meets implementation.

### 3. The repo is scaffolded

The project structure reflects the boundaries. Each agent's domain is visible in the file tree. Communication contracts are documented. The architecture is readable before any code exists.

---

## Common mistakes

**Defining agents by technology instead of responsibility.**
"The React agent" and "the Node agent" are technology boundaries, not system boundaries. A Frontend Agent that owns user experience is a responsibility boundary. The technology is an implementation detail within that boundary.

**Too many agents.**
If you have more than 5-7 agents, some of your boundaries are probably modules, not agents. Merge them. A system with 12 agents has a communication problem, not an architecture.

**Too few agents.**
If you have one or two agents, you probably haven't completed Step 3. A single "Backend Agent" that handles auth, data processing, third-party integrations, and business logic has no real boundaries — it's a monolith with a name.

**Skipping Step 1.**
Jumping straight to "where are the boundaries" without understanding the project's truth produces agents that map to your assumptions, not to the system's actual structure. Step 1 exists to prevent this.

**Designing for a future that doesn't exist.**
"We might need a microservices architecture later" is not a reason to create 15 agents today. Discovery maps the system as it is. When the system evolves, discovery runs again.

---

> The agents you need are already inside the project. Discovery is how you find them.
