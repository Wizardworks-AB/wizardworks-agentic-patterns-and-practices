# AI-First, Human Responsibility

This is the foundational principle behind everything Wizardworks does. It shapes how we staff projects, how we estimate work, and how we think about quality.

## The Principle

**AI agents are the first choice for execution, not a complement. Humans retain full responsibility for the output.**

These two halves are inseparable. Drop the first and you're leaving capability on the table. Drop the second and you're shipping unaccountable work.

## What "AI-First" Means

Agents are the default executor. Before you open an editor and start typing, ask: *can an agent do this?*

| Activity | Agent does it |
|---|---|
| Write implementation code | Yes |
| Write and run tests | Yes |
| Review changes (first pass) | Yes |
| Update documentation | Yes |
| Generate data models and API specs | Yes |
| Scaffold projects and boilerplate | Yes |
| Refactor existing code | Yes |

You don't code manually when an agent can do it. Manual coding is reserved for the cases where an agent genuinely cannot produce the right result — and those cases are shrinking.

## What "Human Responsibility" Means

You own the output. Period.

- "The agent wrote it" is not an excuse for a bug in production.
- "The agent said the architecture was fine" is not an excuse for a bad design.
- You reviewed it. You approved it. You merged it. It's yours.

This is not a philosophical stance — it's a practical one. Agents produce high-quality work, but they lack the full context of customer needs, business constraints, and production realities. The human provides that context and takes accountability for the final result.

## The Architect as Orchestrator

The role of a senior developer at Wizardworks is evolving:

**From:** hands-on-keyboard coder who writes most of the code personally.

**To:** orchestrator who leads customer dialogue, makes architecture decisions, and steers agents through well-written plans and instructions.

This doesn't mean architects stop understanding code. You need deep technical knowledge to review agent output, catch subtle issues, and make the right architectural calls. But the day-to-day activity shifts from writing code to directing agents that write code.

### What orchestration looks like in practice

- Lead customer meetings and translate requirements into clear specs
- Make architecture decisions (agents propose, you decide)
- Write and maintain CLAUDE.md files that guide agent behavior
- Create plans that break work into agent-sized tasks
- Review agent output thoroughly before merging
- Handle the edge cases and judgment calls that agents flag

## The 70-20-10 Rule

A rough guide to how effort is distributed on a typical task:

```
┌─────────────────────────────────────────────────────────────┐
│  70% — Agent produces                                       │
│  Code, tests, docs, refactoring, reviews                    │
├──────────────────────────────────────┐                      │
│  20% — You steer                     │                      │
│  Plans, instructions, decisions      │                      │
├─────────────┐                        │                      │
│  10% — You  │                        │                      │
│  fix/polish │                        │                      │
└─────────────┴────────────────────────┴──────────────────────┘
```

- **70% agent produces:** The agent writes the code, tests, and documentation. It runs the test suite, performs code review, and handles refactoring.
- **20% you steer:** You create the plan, write clear instructions, validate the approach, make architectural decisions, and course-correct when the agent drifts.
- **10% you fix and polish:** You handle the final touches — the edge case the agent missed, the naming that doesn't quite fit, the performance tweak that requires production knowledge.

These percentages vary by task. A greenfield CRUD endpoint might be 90-5-5. A complex migration with legacy constraints might be 40-30-30. The principle stays the same: the agent does the bulk of the execution.

## Amplification, Not Replacement

This approach is not about replacing developers. It's about amplifying them.

One architect can now manage multiple customer projects through agent orchestration. The bottleneck shifts from "how fast can I type" to "how well can I specify what needs to happen and verify it was done correctly."

The skills that matter most:

- **Specification clarity** — Can you describe what you want precisely enough for an agent to build it?
- **Architecture judgment** — Can you make the right structural decisions?
- **Review thoroughness** — Can you catch what the agent got wrong?
- **Customer understanding** — Can you translate business needs into technical direction?

These are the same skills that always mattered for senior developers. AI-First just makes them the *primary* skills instead of secondary ones behind raw coding speed.
