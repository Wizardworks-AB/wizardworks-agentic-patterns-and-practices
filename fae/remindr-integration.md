# Remindr — The Knowledge Graph

## What Is Remindr?

Remindr is a persistent knowledge graph exposed as an MCP server. It gives agents durable memory across sessions — decisions made today are available tomorrow, to the same agent or a different one.

Without Remindr, every agent session starts from zero. The agent re-discovers the same codebase patterns, re-makes the same design decisions, and repeats the same mistakes. Remindr fixes this by capturing knowledge as it is created and making it searchable later.

## Data Model

### Node Types

| Type | Purpose | Example |
|---|---|---|
| `decision` | Architecture/design decisions with rationale | "Use Dapper instead of EF Core for read queries" |
| `fact` | Verified information about the project | "The API uses JWT tokens with 1-hour expiry" |
| `gotcha` | Pitfalls and non-obvious behavior | "EF Core lazy loading is disabled in this project" |
| `blocker` | Issues preventing progress | "Azure AD B2C config missing for staging" |
| `preference` | Team/project conventions | "Use FluentAssertions over built-in xUnit assertions" |
| `state` | Current status of something | "Migration to .NET 10 is 60% complete" |
| `entity` | Domain entities and their descriptions | "Invoice: core billing document, always linked to a Customer" |
| `plan` | Implementation plans | "Phase 1: Extract service layer, Phase 2: Add caching" |
| `contradiction` | Conflicting information that needs resolution | "Decision A says use REST, Decision B says use gRPC" |

### Edge Types

Edges connect nodes to form the graph. Each edge has a type that describes the relationship:

| Edge Type | Meaning | Example |
|---|---|---|
| `led_to` | A caused or resulted in B | Decision to use CQRS led to creating separate read models |
| `contradicts` | A conflicts with B | "Use REST" contradicts "Use gRPC for internal services" |
| `depends_on` | A requires B | Migration plan depends on .NET 10 GA release |
| `supersedes` | A replaces B | New auth decision supersedes old token strategy |
| `related_to` | A is relevant to B | Caching strategy related to performance requirements |
| `affects` | A impacts B | Database schema change affects API contracts |
| `contributed_to` | A was part of producing B | Sprint 3 work contributed to the v2 release |
| `implements` | A is an implementation of B | UserService implements the user management plan |
| `part_of` | A is a component of B | Auth module is part of the platform architecture |

### Messages

Remindr supports asynchronous inter-agent messaging:

- **send** — Send a message to a specific agent or broadcast to all (`*`)
- **reply** — Reply within an existing message thread
- **inbox** — Check for unread messages

This enables coordination between agents working on the same project without requiring them to run simultaneously.

## Intelligence Features

These features run automatically — no agent action required.

### Deduplication

Before creating a new node, Remindr checks for semantic duplicates using embedding similarity. If an existing node is above the similarity threshold (~0.84), the new node is flagged as a potential duplicate rather than creating a redundant entry.

### Contradiction Detection

When a new `decision` node is created, Remindr compares it against existing decisions. If similarity falls in the range 0.78-0.84 (similar topic but different conclusion), a `contradiction` is flagged. This prevents the knowledge graph from silently accumulating conflicting decisions.

### Auto-Linking

New nodes are automatically linked to semantically related existing nodes. When you record a decision about caching, Remindr finds and links it to existing nodes about performance, data access patterns, and related architectural decisions.

### Confidence Decay

Old, unreferenced nodes gradually lose confidence. A decision made six months ago that has never been referenced or reinforced will decay, making it less prominent in search results. Actively referenced knowledge stays fresh.

## Search

Remindr uses a hybrid search strategy combining multiple signals:

1. **FTS5 full-text search** — keyword matching
2. **Semantic embeddings** — meaning-based similarity (all-MiniLM-L6-v2)
3. **Recency** — newer nodes ranked higher
4. **Edge score** — well-connected nodes ranked higher

The combination means you find relevant results whether you remember the exact terminology or just the concept.

## Two Modes

### Local Mode (stdio)

```
Transport: stdio
Storage: SQLite at ~/.remindr/context.db
Use case: offline development, local experimentation
```

The agent connects to Remindr via stdio. Data lives in a local SQLite file. Good for individual work, offline scenarios, or testing.

### Shared Mode (HTTP)

```
Transport: HTTP via fae-gateway
Storage: Azure SQL
Use case: team collaboration, production
```

The agent connects through fae-gateway to a shared Azure SQL database. All agents and developers on the project share the same knowledge graph. This is the production mode.

## Technology

Built in .NET 10. Storage backend is SQLite (local) or Azure SQL (shared). Embedding model is all-MiniLM-L6-v2 for semantic search and intelligence features.
