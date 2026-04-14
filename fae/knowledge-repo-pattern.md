# Knowledge Repo Pattern

## Two Complementary Knowledge Layers

Fae uses two knowledge systems that serve different purposes and complement each other.

### 1. Knowledge Graph (Remindr)

**Automatic, event-driven.** Grows from agent sessions.

The graph captures knowledge as it is created during work: decisions made, facts discovered, gotchas encountered, blockers hit, messages exchanged. You do not plan what goes in the graph — it grows organically as agents work.

### 2. Knowledge Repo (Files)

**Curated, deliberate.** Architecture docs, glossary, domain model.

The repo is what you would put in a wiki — stable, structured documentation that is deliberately written and maintained. It provides the foundational context that agents need before they start working.

## When to Use Which

| Knowledge Graph (Remindr) | Knowledge Repo (Files) |
|---|---|
| Session-specific insights | Stable documentation |
| Decisions made during work | Architecture overviews |
| Gotchas discovered while coding | Domain models |
| Blockers encountered | Glossaries |
| Inter-agent messages | Onboarding material |
| Evolving state | Context files (CONTEXT.md) |
| Contradictions and tradeoffs | Architecture Decision Records |

**Rule of thumb:** If it was discovered during a session and might change, it goes in the graph. If it is curated documentation that should persist as-is, it goes in the repo.

## Knowledge Repo Structure

Recommended structure for a customer knowledge repo:

```
acme-architecture/
├── README.md                   — Repo overview, how to use it
├── CONTEXT.md                  — Project context for agents (loaded at session start)
├── decisions/                  — Architecture Decision Records (ADRs)
│   ├── 001-use-cqrs.md
│   ├── 002-dapper-for-reads.md
│   └── ...
├── docs/
│   ├── architecture.md         — System architecture overview
│   ├── domain-model.md         — Domain entities and relationships
│   └── glossary.md             — Domain terminology
└── projects/                   — Per-project documentation
    ├── acme-web/
    │   ├── CONTEXT.md
    │   └── api-spec.md
    └── acme-mobile/
        ├── CONTEXT.md
        └── architecture.md
```

### Key Files

**CONTEXT.md** is the most important file. It is loaded by the agent at session start and provides the foundational context: what the project is, what the tech stack is, what conventions to follow, what to watch out for. Think of it as the briefing document you would give a new developer on day one.

**decisions/** contains Architecture Decision Records. These are the deliberate, reviewed, stable version of architectural decisions. Contrast with `decide()` in the graph, which captures decisions as they happen during work — those may later be promoted to a formal ADR in the repo.

**glossary.md** ensures agents use the same terminology as the customer. Domain language matters. If the customer calls it an "Invoice" and the agent writes "Bill," the code becomes confusing.

## How Graph and Repo Complement Each Other

```
During a session:
  Agent discovers that EF Core migrations break spatial indexes
  → remember(type: "gotcha", ...)   → goes in the GRAPH

After the session:
  Developer reviews the gotcha, decides it's important enough to document
  → Writes it up in docs/gotchas.md   → goes in the REPO

Next session:
  Agent loads CONTEXT.md from the repo (stable context)
  Agent calls briefing() from the graph (recent activity)
  Both knowledge layers inform the agent's work
```

The graph is the living, growing memory. The repo is the curated, stable foundation. Together they give agents both deep context and fresh situational awareness.

## Example

The `fae-architecture` repo follows this exact pattern. It contains the architecture documentation for the Fae platform itself — system diagrams, API specs, domain model, and ADRs — while Remindr captures the day-to-day decisions and discoveries made during Fae development.
