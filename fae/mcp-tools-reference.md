# Remindr MCP Tools Reference

Complete reference for all MCP tools available to agents through Remindr.

---

## Read Tools

### `context(query, project?)`

Semantic search in the knowledge graph. Returns ranked results with relevance scores.

**Use before starting work on any topic.** This is how you discover what is already known about a subject.

```
context("authentication strategy")
context("caching", project: "acme-web")
```

**When to use:** Before making a decision, before implementing a feature, when you need background on a topic.

---

### `briefing(project?, recentHours?)`

Project summary and orientation. Returns recent decisions, active blockers, unread messages, and project state.

```
briefing()
briefing(project: "acme-web", recentHours: 48)
```

**When to use:** At the start of every session. This is your "what happened since I was last here" tool.

---

### `status(project?)`

Quick overview of current project state. Active work, recent changes.

```
status()
status(project: "acme-web")
```

**When to use:** When you need a quick pulse check without the full briefing.

---

### `why(query, project?)`

Trace decision chains. Follows edges (`led_to`, `supersedes`, `contradicts`) to understand why something was decided. Uses server-side BFS, 1-2 hops.

```
why("why do we use Dapper for read queries")
why("authentication flow", project: "acme-web")
```

**When to use:** When you encounter something that seems wrong or arbitrary and need to understand the reasoning. When you need to trace how one decision led to another.

---

### `get(nodeId)`

Fetch full node content by ID.

```
get("node-abc-123")
```

**When to use:** When you have a specific node ID from search results and need its complete content, rationale, or metadata.

---

### `list(type?, status?, project?)`

List nodes with filters. Browse decisions, blockers, facts by type and status.

```
list(type: "decision")
list(type: "blocker", status: "active", project: "acme-web")
list(type: "gotcha")
```

**When to use:** When browsing knowledge by category rather than searching for something specific.

---

### `blockers(project?)`

List all active blockers.

```
blockers()
blockers(project: "acme-web")
```

**When to use:** Before planning work. Check what is currently blocked so you do not start tasks that depend on unresolved issues.

---

### `inbox(includeRead?)`

Check messages from other agents.

```
inbox()
inbox(includeRead: true)
```

**When to use:** At session start, and periodically during long sessions. Other agents may have sent you questions or updates.

---

## Write Tools

### `decide(decision, rationale, alternatives?, project?)`

Record an architecture or design decision with rationale. Triggers automatic contradiction detection against existing decisions.

```
decide(
  decision: "Use Dapper for read-side queries",
  rationale: "EF Core adds overhead for simple reads. Dapper gives direct SQL control and better performance for reporting queries.",
  alternatives: "EF Core for everything, raw ADO.NET",
  project: "acme-web"
)
```

**When to use:** After making any significant technical decision. Always include rationale and considered alternatives — your future self (or the next agent) will want to know why.

---

### `remember(type, title, content, project?)`

Store knowledge in the graph. Deduplication check runs automatically.

```
remember(
  type: "gotcha",
  title: "EF Core migrations require manual SQL for spatial indexes",
  content: "The EF Core migration generator does not handle spatial indexes correctly. You must add a manual SQL step in the migration for any table with geography columns.",
  project: "acme-web"
)

remember(
  type: "fact",
  title: "API rate limit is 100 requests per minute per tenant",
  content: "Configured in Azure API Management. Applies to all endpoints. Returns 429 with Retry-After header.",
  project: "acme-web"
)
```

**Types:** `fact`, `gotcha`, `plan`, `preference`, `state`, `entity`

**When to use:** Whenever you discover something worth preserving. If you think "someone should know this," record it.

---

### `block(description, project?)`

Register a blocker. Creates a blocker node with active status.

```
block(
  description: "Cannot deploy to staging — Azure AD B2C tenant configuration is missing",
  project: "acme-web"
)
```

**When to use:** When you hit something that prevents progress and needs external resolution.

---

### `resolve(nodeId, resolution)`

Resolve a blocker. Updates status to resolved with resolution text.

```
resolve(
  nodeId: "blocker-xyz-789",
  resolution: "B2C tenant was configured by DevOps team. Connection string added to Key Vault."
)
```

**When to use:** When a previously registered blocker has been resolved. Always document the resolution for future reference.

---

### `forget(nodeId, reason?)`

Mark knowledge as stale. Sets status to stale — does not delete.

```
forget(
  nodeId: "fact-old-123",
  reason: "API was migrated to v2 — this v1 documentation is no longer accurate"
)
```

**When to use:** When information is outdated or no longer relevant. Prefer `forget` over leaving stale data in search results.

---

### `link(from, to, type)`

Create a typed edge between nodes.

```
link(
  from: "decision-abc",
  to: "decision-xyz",
  type: "supersedes"
)

link(
  from: "plan-123",
  to: "decision-abc",
  type: "implements"
)
```

**Edge types:** `led_to`, `contradicts`, `depends_on`, `supersedes`, `related_to`, `affects`, `contributed_to`, `implements`, `part_of`

**When to use:** When you identify a meaningful relationship between two pieces of knowledge that was not automatically detected.

---

### `send(to, subject, content)`

Send an async message to another agent. Use `"*"` for broadcast.

```
send(
  to: "security-reviewer",
  subject: "Review needed: new auth token rotation logic",
  content: "Added refresh token rotation in PR #142. The token storage approach needs security review before merge."
)

send(
  to: "*",
  subject: "Breaking change: API v1 endpoints deprecated",
  content: "All v1 endpoints will return 410 after next deployment. Update any tests or integrations."
)
```

**When to use:** When you need input from another agent that is not currently running, or when you need to broadcast information to all agents.

---

### `reply(threadId, content)`

Reply in an existing message thread.

```
reply(
  threadId: "thread-abc-123",
  content: "Reviewed. The token rotation looks correct but add a test for the race condition when two refreshes happen simultaneously."
)
```

**When to use:** When responding to a message in your inbox.

---

## Additional Tools

### `read_thread(threadId)`

Read full message thread content.

```
read_thread(threadId: "thread-abc-123")
```

**When to use:** When you need the full conversation context of a message thread.

---

### `mark_read(threadId)`

Mark a thread as read.

```
mark_read(threadId: "thread-abc-123")
```

**When to use:** After processing an inbox message that does not require a reply.

---

### `list_agents()`

List all known agents in the system.

```
list_agents()
```

**When to use:** When you need to know who to send a message to, or to see what agents are registered for the project.

---

### `audit(project?, agent?, ...)`

Full audit log with filtering, pagination, and CSV export.

```
audit(project: "acme-web")
audit(agent: "implementer", project: "acme-web")
```

**When to use:** When you need to trace what happened — who created what, when, and why. Useful for debugging and compliance.

---

### `propose(content, project?)`

Submit an observation for review.

```
propose(
  content: "The current caching strategy may cause stale data issues under high concurrency. Consider adding cache invalidation on write.",
  project: "acme-web"
)
```

**When to use:** When you have an observation or suggestion that should be reviewed by a human or senior agent before being acted on.
