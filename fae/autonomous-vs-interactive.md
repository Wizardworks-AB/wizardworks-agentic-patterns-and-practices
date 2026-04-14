# Autonomous vs Interactive Mode

Fae supports two operating modes — and a hybrid of both — using the same knowledge, skills, and context system. The mode determines how the agent is triggered and where it runs, not what it can do.

## Mode 1: Autonomous (the Fae Product)

The agent runs event-driven in Azure Container Apps, 24/7, without human intervention.

| Aspect | Detail |
|---|---|
| **Trigger** | Teams messages, DevOps webhooks, cron schedules, portal actions |
| **Runtime** | Azure Container Apps |
| **Knowledge repo** | Azure Files / Blob Storage |
| **Code access** | Git clone per task (fresh checkout, clean state) |
| **Coordination** | Remindr knowledge graph + DevOps board |

### Named Agent Types

| Name | Role |
|---|---|
| **Sage** | Event processing, triage, routing |
| **Cobalt** | Implementation (the builder) |
| **Sentinel** | Monitoring, health checks |
| **Critic** | Code review, quality gates |
| **Puck** | Testing, E2E, validation |

Each agent type loads a different skill configuration but shares the same runtime, tools, and knowledge graph.

## Mode 2: Interactive (Developer Use)

A developer runs `claude` locally against the same knowledge repo and customer code.

| Aspect | Detail |
|---|---|
| **Trigger** | Developer starts a session manually |
| **Runtime** | Local machine (Claude Code CLI) |
| **Knowledge repo** | Cloned locally (sibling to customer code) |
| **Code access** | Local working copy |
| **Coordination** | Remindr knowledge graph + DevOps board |

### How It Works

```
~/agents/acme/          ← Client agent repo (where you run claude)
~/code/acme/            ← Customer code repo
```

The developer picks tasks from the DevOps board, runs commands like `/plan`, `/tdd`, `/tdd-implement`, and records knowledge in Remindr — the same knowledge graph the autonomous agent uses.

## Mode 3: Hybrid (Both Simultaneously)

The most powerful mode. Autonomous agents handle routine work while developers handle complex tasks. Both operate on the same project at the same time.

| Aspect | How It Works |
|---|---|
| **Task ownership** | DevOps board is the single source of truth. Each task is assigned to one owner — agent or human. |
| **Knowledge sharing** | Both write to the same Remindr knowledge graph. |
| **Conflict prevention** | One task per session. The board prevents double-assignment. |
| **Code isolation** | Each session works on its own branch. Merges go through normal PR review. |

### No Conflicts

The key insight: there are no conflicts because:

1. **Task-level isolation** — One task, one owner, one branch. No two workers touch the same code.
2. **Board as arbiter** — The DevOps board tracks who owns what. If Cobalt is working on Task A, a developer cannot pick it up (and vice versa).
3. **Knowledge is additive** — Writing to the knowledge graph does not create conflicts. New decisions are checked for contradictions automatically.

## Practical Implications

### Everything Is Shared

What you record in Remindr during an interactive session is immediately visible to the autonomous agent:

```
Interactive session:
  decide("Use optimistic concurrency for inventory updates", ...)

Next autonomous session:
  briefing() → shows the decision you made
  context("concurrency") → finds your decision
```

A decision you make locally will be checked for contradictions when the autonomous agent makes its own decisions. The knowledge graph does not distinguish between sources — knowledge is knowledge.

### Session Isolation

Implementation and review happen in separate sessions with different skill configurations. This is true in both modes:

- The agent that writes code is not the agent that reviews it
- Each session starts fresh with a `briefing()` to catch up on recent changes
- Review sessions load the reviewer skill, not the implementer skill

This separation of concerns ensures genuine independence between implementation and quality review, regardless of whether the work is autonomous or interactive.

### Choosing a Mode

| Situation | Mode |
|---|---|
| New project, building from scratch | Interactive (developer needs to guide early decisions) |
| Mature project, well-defined patterns | Autonomous (agent can follow established patterns) |
| Complex feature + routine tasks in parallel | Hybrid (developer takes the complex work, agent handles routine) |
| Maintenance and bug fixes | Autonomous (well-scoped, pattern-following work) |
| Exploratory design or research | Interactive (requires human judgment at every step) |
