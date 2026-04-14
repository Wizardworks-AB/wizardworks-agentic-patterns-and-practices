# When to Use Agents, When to Go Manual

Not every task benefits equally from agent involvement. This guide helps you make the right call quickly.

## The Key Question

> **"Can I clearly specify what I want and verify the output?"**
>
> If **yes** -- delegate to the agent.
> If **partially** -- pair with the agent.
> If **no** -- do it manually (agent can assist).

That's the core heuristic. Everything below is a more detailed breakdown.

## Decision Matrix

### ALWAYS Use Agents

These tasks are well-defined, repetitive, or mechanical. Agents handle them faster and more consistently than humans.

| Task | Why agent excels |
|---|---|
| Boilerplate / scaffolding | Pattern-based, no judgment needed |
| Test writing | Systematic coverage from specs |
| Documentation | Comprehensive, consistent format |
| Code review (first pass) | Catches common issues reliably |
| Refactoring | Mechanical transformations with test validation |
| Bug reproduction | Methodical, writes test to prove the bug |

### PREFER Agents

These tasks benefit from agent execution but need human steering or review.

| Task | Why prefer agent | Human involvement |
|---|---|---|
| Feature implementation (with TDD) | Agent follows TDD cycle efficiently | Plan review, acceptance criteria |
| API endpoint creation | Follows established patterns | Design review, edge cases |
| Data model changes | Generates migrations, updates types | Schema review, data implications |
| CI/CD pipeline setup | Knows tooling syntax well | Review security, verify environments |

### PAIR With Agents

These tasks require human judgment throughout. The agent handles the mechanical work while you make the decisions.

| Task | Why pair | Your role |
|---|---|---|
| Complex business logic | Domain knowledge matters | Provide business rules, validate logic |
| Performance optimization | Requires production context | Identify bottlenecks, validate approach |
| Architecture changes | Structural decisions are high-impact | Make decisions, agent implements |
| Debugging tricky issues | Agent is methodical, you have intuition | Guide investigation, interpret results |

### GO MANUAL

These tasks require human judgment, accountability, or presence. The agent can assist (analysis, drafting) but you make the final call and execute.

| Task | Why manual | Agent assists with |
|---|---|---|
| Security-critical code (final review) | Accountability, expertise | First-pass review, vulnerability scanning |
| Production deployments (trigger) | Go/no-go is a human decision | Pipeline setup, pre-deploy checks |
| Customer meetings | Relationship, nuance | Transcription, note structuring |
| Architectural decisions | Strategic judgment | Analysis, option comparison, ADR drafting |

---

## Visual Decision Flow

```
                    ┌─────────────────────────┐
                    │ New task arrives         │
                    └────────┬────────────────┘
                             │
                    ┌────────▼────────────────┐
                    │ Can I specify it clearly │
                    │ AND verify the output?   │
                    └────────┬────────────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
           Yes, fully    Partially       No
              │              │              │
    ┌─────────▼──────┐ ┌────▼─────┐ ┌─────▼──────────┐
    │  DELEGATE       │ │  PAIR    │ │  MANUAL        │
    │  Agent executes │ │  Agent + │ │  You execute   │
    │  You review     │ │  You     │ │  Agent assists │
    └────────────────┘ └──────────┘ └────────────────┘
```

---

## Cost Considerations

Different models have different strengths and price points. Match the model to the task.

| Model tier | Use for | Examples |
|---|---|---|
| **Opus** (highest capability) | Critical reasoning, complex architecture, subtle bugs | Architecture reviews, complex debugging, security analysis |
| **Sonnet** (balanced) | Judgment-requiring tasks, feature work | Feature implementation, code review, business logic |
| **Haiku** (fast, efficient) | Mechanical tasks, high volume | Boilerplate, formatting, simple tests, documentation updates |

The right model depends on the task, not the project. A single project might use all three: Haiku for scaffolding, Sonnet for feature work, Opus for the security review.

---

## Time Considerations

Agent sessions have setup overhead: the agent reads context, loads the plan, orients itself. Factor this in.

```
Task complexity vs. agent benefit:

  Agent benefit
  ▲
  │                              ╱
  │                           ╱
  │                        ╱
  │                     ╱
  │                  ╱
  │               ╱
  │            ╱
  │         ╱
  │      ╱
  │   ╱
  ├──────────────────────────────────►
  │                          Task complexity / duration
  │
  ▼ "Just do it yourself"
  
  Sweet spot: tasks that take 30+ minutes of focused work.
```

### Rules of thumb

| Task duration | Recommendation |
|---|---|
| < 5 minutes | Just do it manually. Agent setup costs more than the task. |
| 5-30 minutes | Could go either way. If the pattern exists, delegate. If novel, manual. |
| 30 min - 2 hours | Agent sweet spot. Delegate or pair. |
| 2+ hours | Definitely use agent. But break it into smaller tasks first. |

---

## Practical Examples

### Example 1: Add a new API endpoint (similar to existing ones)
**Decision:** DELEGATE. Clear pattern, verifiable output.
```
Agent: writes endpoint, tests, docs following existing pattern
You: review PR, check edge cases
```

### Example 2: Optimize a slow database query
**Decision:** PAIR. Requires production context and performance intuition.
```
You: identify the slow query, share execution plan and data volumes
Agent: proposes index changes, rewrites query, benchmarks
You: validate approach against production patterns, approve
```

### Example 3: Fix a critical auth bypass vulnerability
**Decision:** MANUAL with agent assist. Security accountability requires human judgment.
```
Agent: analyze the vulnerability, propose fix, scan for similar issues
You: validate the fix, review for edge cases, test manually, deploy
```

### Example 4: Update documentation after a feature ships
**Decision:** ALWAYS delegate. Mechanical, verifiable, low risk.
```
Agent: updates API docs, README, changelog, inline comments
You: quick scan for accuracy
```
