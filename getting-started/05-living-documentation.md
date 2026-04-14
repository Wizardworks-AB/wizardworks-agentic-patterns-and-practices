# Living Documentation

## Purpose

Every project needs a place for architecture documentation, decision records, and domain knowledge. But documentation that is written once and never updated is worse than no documentation — it actively misleads.

Living documentation evolves as the project evolves. At Wizardworks, we achieve this through two complementary layers of knowledge.

## Two Layers of Knowledge

### Layer 1: Knowledge Graph (Remindr)

**Automatic and event-driven.**

The knowledge graph captures what happens during work sessions without requiring deliberate documentation effort:

- Decisions made while coding
- Facts discovered about the codebase
- Gotchas and workarounds encountered
- Blockers that need attention
- Messages between agents and team members

This happens naturally as the agent works. When you make an architectural decision during a session, the agent records it. When you discover a quirk in a third-party API, it gets stored. This knowledge is immediately available to anyone (human or agent) working on the same project.

### Layer 2: Knowledge Repo (Files)

**Curated and deliberate.**

Some knowledge deserves more structure and intentionality than a graph entry. Architecture documentation, domain models, glossaries, and decision records belong in a dedicated repository where they can be reviewed, versioned, and discussed via pull requests.

This is the knowledge you sit down and write (or have the agent draft for you), review, and commit.

## Setting Up a Knowledge Repo

Create a new repository following the Wizardworks architecture template:

```bash
# Use the template at:
# https://github.com/Wizardworks-AB/fae-architecture
```

1. Go to [https://github.com/Wizardworks-AB/fae-architecture](https://github.com/Wizardworks-AB/fae-architecture)
2. Click **"Use this template"** to create a new repo (e.g., `acme-architecture`)
3. Clone it alongside your other repos

## Recommended Structure

```
acme-architecture/
├── README.md              # Overview of the project and its architecture
├── CONTEXT.md             # Quick-reference context for agents and humans
├── decisions/             # Architecture Decision Records (ADRs)
│   ├── 001-use-cqrs.md
│   ├── 002-event-sourcing-for-orders.md
│   └── ...
└── docs/
    ├── architecture.md    # System architecture overview
    ├── domain-model.md    # Domain model and entity relationships
    └── glossary.md        # Domain terminology definitions
```

### What goes where

| File/Directory | Content | Updated when |
|---------------|---------|-------------|
| `README.md` | High-level project overview | Major changes to scope or architecture |
| `CONTEXT.md` | Quick-start context for anyone joining the project | As the project evolves |
| `decisions/` | ADRs documenting significant architectural choices | Each significant decision |
| `docs/architecture.md` | System diagrams, component descriptions, integration points | Architecture changes |
| `docs/domain-model.md` | Entities, aggregates, value objects, relationships | Domain model changes |
| `docs/glossary.md` | Domain terms and their definitions | New domain concepts emerge |

## How the Two Layers Complement Each Other

The knowledge graph and the knowledge repo serve different purposes:

| Aspect | Knowledge Graph (Remindr) | Knowledge Repo (Files) |
|--------|--------------------------|----------------------|
| **Captured how** | Automatically during sessions | Deliberately by humans (often drafted by agents) |
| **Granularity** | Fine-grained: individual decisions, facts, gotchas | Coarse-grained: architecture docs, ADRs, glossaries |
| **Lifecycle** | Ephemeral to medium-term | Long-term, version-controlled |
| **Review process** | None — captured as-is | Pull requests, peer review |
| **Best for** | Day-to-day context, session continuity | Onboarding, audits, long-term reference |

Think of it this way: the graph captures what happens moment-to-moment. The repo captures what you want to remember in six months.

## Agent Access

Both layers are accessible to the agent:

- **Knowledge graph** — via the Remindr MCP server (see [Connect to Fae](04-connect-to-fae.md))
- **Knowledge repo** — via file reading, since the repo is a regular Git repository on disk

When an agent starts a session, it can read both the graph (for recent context) and the repo (for stable architecture knowledge). This gives it both the "what just happened" and the "how things are designed" perspectives.
