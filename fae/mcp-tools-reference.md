# Remindr MCP Tools Reference

Complete reference for all 22 MCP tools available to agents through the Remindr server. Tool signatures are based on the actual implementation in `src/Remindr.Server/Tools/`.

> **Note:** Most tools accept an optional `project` parameter. If omitted, it falls back to the `X-Default-Project` header configured in `.mcp.json`. You can set this once and forget about it for single-project agents.

---

## Read Tools

### `context`

Semantic search in the knowledge graph. Returns ranked results using hybrid FTS5 + embedding similarity + recency scoring.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | yes | What you need context about |
| `project` | string | no | Falls back to default project |

```
context(query: "authentication strategy")
context(query: "caching approach", project: "acme-web")
```

**When to use:** Before making a decision, before implementing a feature, when you need background on a topic. This is your primary "what do we already know about this?" tool.

---

### `briefing`

Project summary and orientation. Returns active projects, open blockers, recent decisions, stale items, and unread messages.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | string | no | Falls back to default project |
| `recentHours` | int | no | Hours of activity to include (default: 24). Ignored when sinceLastSession is true |
| `sinceLastSession` | bool | no | If true, shows activity since this agent's last briefing (default: false) |
| `timezone` | string | no | IANA timezone for display (e.g., "Europe/Stockholm") |

```
briefing()
briefing(sinceLastSession: true)
briefing(project: "acme-web", recentHours: 48, timezone: "Europe/Stockholm")
```

**When to use:** At the start of every session. Use `sinceLastSession: true` to see exactly what changed since you were last active.

---

### `status`

Current project state — active decisions, open blockers, recent changes.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | string | no | Falls back to default project |

```
status()
```

**When to use:** Quick pulse check without the full briefing.

---

### `why`

Trace decision chains — follows `led_to`, `supersedes`, `contradicts` edges using server-side BFS (1-2 hops).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | yes | Decision or topic to trace |
| `project` | string | no | Falls back to default project |

```
why(query: "why do we use Dapper for read queries")
```

**When to use:** When you encounter something that seems wrong or arbitrary and need to understand the reasoning chain.

---

### `get`

Fetch full untruncated node content by ID. Use when `context()` truncates a result.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `nodeId` | string | yes | Node ID to retrieve |

```
get(nodeId: "node-abc-123")
```

**When to use:** When you have a node ID from search results and need the complete content, rationale, or metadata.

---

### `list`

List nodes with filters.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | no | `decision, fact, blocker, gotcha, preference, state, entity, plan` |
| `status` | string | no | `active, resolved, superseded, stale` |
| `project` | string | no | Falls back to default project |

```
list(type: "decision")
list(type: "blocker", status: "active", project: "acme-web")
```

**When to use:** When browsing knowledge by category rather than searching for something specific.

---

### `blockers`

List all active blockers.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | string | no | Omit to see blockers across all projects |

```
blockers()
```

**When to use:** Before planning work. Check what's currently blocked.

---

### `inbox`

Check messages from other agents.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `includeRead` | bool | no | If true, returns all threads (default: false — unread only) |

```
inbox()
inbox(includeRead: true)
```

**When to use:** At session start, and periodically during long sessions.

---

### `read_thread`

Read full thread content — all messages, untruncated. Auto-marks the thread as read.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `threadId` | string | yes | Thread ID to read |

```
read_thread(threadId: "thread-abc-123")
```

**When to use:** When you see a thread in your inbox and need the full conversation.

---

### `list_agents`

List all known agents in the system. No parameters.

```
list_agents()
```

**When to use:** When you need to know who to send a message to.

---

## Write Tools

### `decide`

Record an architecture or design decision with rationale. **Automatically detects contradictions** with existing decisions — if a conflict is detected (embedding similarity 0.78-0.84), a `contradiction` node is created with edges to both decisions.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `decision` | string | yes | What was decided |
| `rationale` | string | yes | Why this was decided |
| `project` | string | no | Falls back to default project |
| `alternatives` | string[] | no | Alternatives that were considered |
| `supersedes` | string | no | Node ID of previous decision this replaces |
| `relatedTo` | string[] | no | Node IDs this relates to |

```
decide(
  decision: "Use Dapper for read-side queries",
  rationale: "EF Core adds overhead for simple reads. Dapper gives direct SQL control.",
  alternatives: ["EF Core for everything", "raw ADO.NET"],
  project: "acme-web"
)

decide(
  decision: "Switch from REST to gRPC for internal service communication",
  rationale: "Latency requirements demand binary protocol. REST adds too much serialization overhead.",
  supersedes: "decision-node-old-rest",
  alternatives: ["Keep REST with MessagePack", "GraphQL"]
)
```

**When to use:** After making any significant technical decision. Always include `rationale` and `alternatives`. Use `supersedes` when explicitly replacing a previous decision.

---

### `remember`

Store knowledge in the graph. **Auto-deduplicates** via embedding similarity — if a near-duplicate exists (similarity > 0.84), the new node is rejected with a message showing the existing one. Also **auto-links** to semantically related existing nodes.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `type` | string | yes | `fact`, `gotcha`, `preference`, `state`, `entity`, `plan` |
| `title` | string | yes | Short summary (1 line) |
| `content` | string | yes | Full detail |
| `project` | string | no | Falls back to default project |
| `relatedTo` | string[] | no | Node IDs to link |

**Important:** Do NOT use type `decision` (use `decide()` instead) or `blocker` (use `block()` instead). These have dedicated tools with extra logic.

```
remember(
  type: "gotcha",
  title: "EF Core migrations require manual SQL for spatial indexes",
  content: "The migration generator does not handle spatial indexes. Add manual SQL step for geography columns.",
  project: "acme-web"
)

remember(
  type: "preference",
  title: "Customer uses Swedish date format in all reports",
  content: "All date formatting in reports must use sv-SE culture (yyyy-MM-dd). The API returns ISO 8601 but the report renderer must convert.",
  project: "acme-web"
)
```

**When to use:** Whenever you discover something worth preserving. If you think "someone should know this," record it.

---

### `block`

Register a blocker. Auto-deduplicates and auto-links.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `description` | string | yes | What is blocked and why |
| `project` | string | no | Falls back to default project |
| `urgency` | string | no | `low`, `medium`, `high`, `critical` |
| `relatedTo` | string[] | no | Node IDs this relates to |

```
block(
  description: "Cannot deploy to staging — Azure AD B2C tenant configuration is missing",
  urgency: "high",
  project: "acme-web"
)
```

**When to use:** When you hit something that prevents progress. Include `urgency` to help triage.

---

### `resolve`

Resolve a blocker. **Automatically creates a linked fact node** titled "Resolved: {blockerTitle}" containing the resolution, connected via `led_to` edge.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `nodeId` | string | yes | Node ID of blocker to resolve |
| `resolution` | string | yes | What resolved the blocker |

```
resolve(
  nodeId: "blocker-xyz-789",
  resolution: "B2C tenant configured by DevOps. Connection string added to Key Vault."
)
```

**When to use:** When a blocker has been cleared. Always document the resolution — it becomes a searchable fact.

---

### `forget`

Mark knowledge as stale. Does NOT delete — sets status to `stale`. If forgetting a decision, also marks linked contradiction nodes as stale.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `nodeId` | string | yes | Node ID to mark as stale |
| `reason` | string | no | Why this is being forgotten |

```
forget(nodeId: "fact-old-123", reason: "API migrated to v2 — v1 docs no longer accurate")
```

**When to use:** When information is outdated. Prefer `forget` over leaving stale data in search results.

---

### `link`

Create a typed edge between two nodes. Only 5 edge types are available via this tool — the remaining types (`affects`, `contributed_to`, `implements`, `part_of`, `owns`, `expert_in`, `reviews`) are managed by the auto-linker.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fromNodeId` | string | yes | Source node ID |
| `toNodeId` | string | yes | Target node ID |
| `type` | string | yes | `led_to`, `contradicts`, `depends_on`, `supersedes`, `related_to` |

```
link(fromNodeId: "decision-abc", toNodeId: "decision-xyz", type: "supersedes")
link(fromNodeId: "plan-123", toNodeId: "decision-abc", type: "depends_on")
```

**When to use:** When you identify a relationship between two nodes that was not automatically detected.

---

### `send`

Send async message to another agent.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `to` | string | yes | Recipient agent name, or `"*"` for broadcast |
| `subject` | string | yes | Thread subject |
| `content` | string | yes | Message content (markdown) |
| `relatedTo` | string | no | Node ID to link the message to |

```
send(
  to: "security-reviewer",
  subject: "Review needed: auth token rotation",
  content: "Added refresh token rotation in PR #142. Needs security review.",
  relatedTo: "decision-token-rotation"
)

send(to: "*", subject: "Breaking change: API v1 deprecated", content: "All v1 endpoints return 410 after next deploy.")
```

**When to use:** When you need input from another agent or need to broadcast information.

---

### `reply`

Reply in an existing message thread.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `threadId` | string | yes | Root message ID |
| `content` | string | yes | Reply content (markdown) |
| `relatedTo` | string | no | Node ID to link this reply to |

```
reply(threadId: "thread-abc-123", content: "Reviewed. Token rotation looks correct.")
```

---

### `mark_read`

Mark a thread as read without reading full content.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `threadId` | string | yes | Thread ID |

```
mark_read(threadId: "thread-abc-123")
```

---

### `propose`

Submit an observation without classifying it. The observation is sent to the Fae Gateway where **Sage** (the orchestrator agent) decides if and how to add it to the knowledge graph. This is the entry point for agents with Propose permission (lower than Write).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `content` | string | yes | What you observed — plain text |
| `project` | string | no | Falls back to default project |
| `task` | string | no | Work item or task reference |
| `repo` | string | no | Repository name |
| `branch` | string | no | Branch name |
| `relatedTo` | string | no | Node ID this observation relates to (hint for Sage) |

```
propose(
  content: "The caching strategy may cause stale data under high concurrency. Consider cache invalidation on write.",
  project: "acme-web",
  task: "WI-1234",
  repo: "acme-webshop"
)
```

**When to use:** When you observe something noteworthy but aren't sure how to classify it. When you only have Propose permission. When you want Sage to evaluate your observation before it enters the knowledge graph.

---

## Project Management Tools

### `project`

Create or get a project. If the project doesn't exist, creates it with scaffolded base entity nodes.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | yes | Project name |

```
project(name: "acme-web")
```

---

### `audit`

Full audit log — who created/updated each node, when, type, and status. Supports filtering, pagination, and CSV export.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project` | string | no | Omit to see all projects |
| `agent` | string | no | Filter by createdBy |
| `type` | string | no | Filter by node type |
| `status` | string | no | Filter by status |
| `from` | string | no | Date filter start (e.g., `2026-03-11`) |
| `to` | string | no | Date filter end |
| `format` | string | no | `table` (default) or `csv` |
| `limit` | int | no | Max entries per page (default: 50, 0 = all) |
| `offset` | int | no | Skip first N entries for pagination |

```
audit(project: "acme-web")
audit(agent: "implementer", type: "decision", from: "2026-04-01")
```

**When to use:** For traceability and compliance. When you need to trace what happened — who created what, when.

---

## Automatic Intelligence

These features run automatically — you don't trigger them, but you should know they exist:

| Feature | What it does | Triggered by |
|---------|-------------|-------------|
| **Deduplication** | Rejects new nodes that are near-duplicates of existing ones (embedding similarity > 0.84) | `remember`, `decide`, `block` |
| **Contradiction detection** | Creates `contradiction` nodes when new decisions conflict with existing ones (similarity 0.78-0.84) | `decide` |
| **Auto-linking** | Links new nodes to semantically related existing nodes via typed edges | All write tools |
| **Confidence decay** | Old, unreferenced nodes gradually lose confidence score over time | Background service |
| **Periodic contradiction scan** | Compares all decision pairs every 6 hours | Background service |
