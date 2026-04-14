# Developer-Agent Dynamics

How you interact with the agent determines the quality of the output. This document covers the three interaction modes, trust levels, and how to steer agents effectively.

## Three Interaction Modes

### 1. Pair Programming Mode

**You and the agent work together in real time.** You're actively involved — reading output, giving feedback, course-correcting as you go.

**When to use:**
- Complex problems where the solution isn't obvious
- Onboarding to a new codebase or domain
- Architectural exploration ("let's try three approaches and compare")
- Debugging tricky issues where context matters

**What it looks like:**
- You describe the problem, agent proposes an approach
- You refine, agent implements a piece
- You review, give feedback, agent adjusts
- Tight feedback loop, short iterations

**Typical effort split:** 50% agent, 30% human steering, 20% human doing

### 2. Delegation Mode

**The agent works independently. You define the task, the agent executes, you review the result.**

**When to use:**
- Well-defined tasks with clear acceptance criteria
- Established patterns the agent can follow (e.g., "add another CRUD endpoint like the existing ones")
- Tasks where the CLAUDE.md and existing code provide enough context
- Batch work (multiple similar tasks)

**What it looks like:**
- You write a clear plan or task description
- Agent executes end-to-end (code, tests, docs)
- You review the PR/commit when done
- Minimal back-and-forth during execution

**Typical effort split:** 80% agent, 10% human steering, 10% human review

### 3. Review Mode

**The agent has already done work. You're reviewing the output.**

**When to use:**
- Pull request reviews (agent did the code review first, you do the final pass)
- Security audits of agent-generated code
- Code quality assessment
- Validating that agent output meets acceptance criteria

**What it looks like:**
- You read the agent's code review comments
- You examine the diff with business context in mind
- You check for things the agent might miss (naming conventions, customer-specific patterns, production implications)
- You approve, request changes, or flag issues

**Typical effort split:** 70% agent (already done), 30% human review

---

## Trust Levels

Not all tasks deserve the same level of trust. Use this framework to decide how much oversight to apply.

### High Trust — Delegate Freely

The agent can handle these independently with minimal review:

- **Boilerplate and scaffolding** — project setup, file structure, configuration
- **Tests from specifications** — given clear acceptance criteria, agent writes tests
- **Documentation** — API docs, READMEs, inline comments
- **Refactoring within boundaries** — rename, extract method, restructure (when tests exist)
- **Code formatting and linting** — mechanical cleanup
- **Dependency updates** — with test suite validation

### Medium Trust — Pair Program

Work alongside the agent on these. Provide guidance and review each step:

- **New business logic** — the agent doesn't understand your customer's domain as well as you do
- **Architecture changes** — structural decisions need human judgment
- **Complex state management** — subtle bugs hide in state transitions
- **Performance optimization** — requires understanding of production load patterns
- **Third-party integrations** — APIs have quirks that aren't in the docs

### Low Trust — Manual with Agent Assist

Do these yourself. Use the agent for analysis and drafting, but make the final call manually:

- **Security-critical code** — authentication, authorization, encryption, input validation
- **Database migrations** — data loss is irreversible
- **Production configuration changes** — wrong settings cause outages
- **Infrastructure changes** — networking, DNS, certificates
- **Financial calculations** — rounding errors and edge cases matter

```
Trust Level     │ Interaction Mode  │ Review Depth
────────────────┼───────────────────┼──────────────────
High trust      │ Delegation        │ Quick scan
Medium trust    │ Pair programming  │ Thorough review
Low trust       │ Manual + assist   │ Line-by-line audit
```

---

## How to Steer Effectively

Good steering is the difference between an agent that produces great work and one that produces mediocre work. Here's how to do it well.

### 1. Maintain a clear CLAUDE.md

Your CLAUDE.md is the agent's operating manual. It should contain:
- Project context and architecture overview
- Tech stack and conventions
- Workflow commands and when to use them
- Patterns to follow (and anti-patterns to avoid)
- Testing requirements and quality standards

A well-written CLAUDE.md means you can delegate more with less steering.

### 2. Plan before executing

Always run `/plan` (or equivalent) before implementation. Review the plan. Adjust it. A bad plan produces bad code regardless of how capable the agent is.

Good plan review questions:
- Does this break the work into small enough steps?
- Does the agent understand the existing code structure?
- Are there edge cases the plan doesn't account for?
- Is the testing approach thorough enough?

### 3. Give specific instructions

**Vague:** "Add user authentication."

**Specific:** "Add JWT-based authentication using the existing AuthService pattern in `/src/auth/`. Use the same middleware chain as the OrderController. Add tests covering: valid token, expired token, missing token, invalid role."

Specificity doesn't mean micromanaging. It means removing ambiguity so the agent makes the same decisions you would.

### 4. Review output thoroughly

Don't just check if tests pass. Read the code. Ask:
- Does this match our conventions?
- Are there hidden assumptions?
- Would this work at production scale?
- Are the tests actually testing the right things?
- Is the documentation accurate?

---

## Common Mistakes

### Over-delegation without review

**Symptom:** Merging agent PRs after a quick glance. Trusting test results without reading test code.

**Risk:** Subtle bugs, architectural drift, tests that pass but don't verify behavior.

**Fix:** Match review depth to trust level. High-trust tasks get a quick scan. Everything else gets a real review.

### Under-delegation out of distrust

**Symptom:** Writing code manually for tasks the agent handles well. Rewriting agent output instead of giving feedback.

**Risk:** Wasted time, slower delivery, not leveraging the team's capacity.

**Fix:** Start with high-trust tasks. Build confidence. Gradually increase delegation as you learn the agent's strengths and weaknesses for your specific codebase.

### Vague instructions followed by frustration

**Symptom:** "The agent didn't do what I wanted." But the instructions were ambiguous.

**Risk:** Rework, frustration, blaming the tool.

**Fix:** If the agent produces something unexpected, ask: "Could my instructions have been interpreted differently?" Usually the answer is yes. Improve the instructions, not just the output.
