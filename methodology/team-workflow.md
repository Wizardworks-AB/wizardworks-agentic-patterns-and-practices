# Team Workflow

How multiple people coordinate when everyone is working with agents. This covers task ownership, knowledge sharing, and handoffs.

## The Core Problem

When multiple developers are running agent sessions on the same project, you need:
- **No collisions** — two agents don't work on the same thing
- **Shared knowledge** — insights from one session are available to others
- **Clean handoffs** — if you stop mid-task, someone else can pick it up
- **Single source of truth** — everyone agrees on what's done and what's next

## Previous Approach

Earlier, the Wizardworks agents repo used a `humans/` directory with per-person `status.md` files. Each developer maintained their own status file describing what they were working on, what was blocked, and what they'd learned.

This worked but didn't scale. Status files went stale, knowledge was siloed per person, and there was no real-time coordination.

## Current Approach: Fae + Remindr + DevOps Board

Three systems handle coordination:

```
┌──────────────────────────────────────────────────────────┐
│  Azure DevOps Board                                      │
│  Single source of truth for task ownership                │
│  Who is doing what, what's done, what's next              │
├──────────────────────────────────────────────────────────┤
│  Fae / Remindr (Knowledge Graph)                         │
│  Shared memory across all agent sessions                  │
│  Decisions, facts, context, state                         │
├──────────────────────────────────────────────────────────┤
│  Agent Sessions                                           │
│  Individual developer + agent working on a task           │
│  Reads from and writes to Remindr                         │
└──────────────────────────────────────────────────────────┘
```

### Azure DevOps Board

The board is the **single source of truth** for task ownership and status. All work items live here. This is where the team goes to see what needs doing and who's doing it.

- Tasks are assigned to specific people
- Status is updated as work progresses
- The board prevents double-assignment — if a task is assigned to you, no one else picks it up

### Fae / Remindr

Remindr is the knowledge graph that provides shared memory across agent sessions. When an agent session discovers something important — a decision, a technical fact, a gotcha — it writes that to Remindr. Other sessions can then read it.

Key Remindr operations:
- **`briefing()`** — Get up to speed on the current project state
- **`context()`** — Pull relevant context for a specific task
- **`decide()`** — Record a decision with rationale
- **`remember()`** — Store a fact, insight, or state for future sessions
- **`recall()`** — Retrieve stored knowledge

This replaces the old `humans/` and `shared/` directory approach with a structured, queryable knowledge graph.

## The Workflow

### Step by step

```
1. Pick a task from the DevOps board
   └→ Assign it to yourself, move to "In Progress"

2. Start an agent session
   └→ cd ~/agents/<customer>/ && claude

3. Agent checks briefing and context from Remindr
   └→ briefing() for project state
   └→ context() for task-specific information

4. Work the task
   └→ Follow the implementation lifecycle (plan → TDD → review)
   └→ Record decisions and insights as you go

5. Record decisions and insights to Remindr
   └→ decide() for architectural/design decisions
   └→ remember() for facts and learnings

6. Commit and push
   └→ Create PR, agent runs reviews

7. Update the DevOps board
   └→ Move task to "Review" or "Done"
```

### Parallel Work

A team can have multiple agents running simultaneously on different tasks. This is one of the key advantages of the AI-First approach — agent sessions are cheap to spin up.

```
Developer A ──→ Agent Session ──→ Task: User auth endpoints
Developer B ──→ Agent Session ──→ Task: Payment integration
Developer C ──→ Agent Session ──→ Task: Email notification service
                    │
                    ▼
              All three read/write
              to shared Remindr
              knowledge graph
```

Each session is independent, but they share knowledge through Remindr. If Developer A makes an architectural decision that affects Developer C's work, it's immediately available.

## Conflict Avoidance

### Rules

1. **One task per agent session.** Don't try to work multiple board items in a single session. It creates confusion and makes handoffs harder.

2. **The board prevents double-assignment.** Check the board before picking up work. If it's assigned to someone, it's theirs.

3. **Communicate via Remindr, not just chat.** Verbal "I'm working on X" gets forgotten. The knowledge graph persists.

4. **Respect file boundaries.** If two tasks touch the same files, sequence them rather than parallelize. Merge conflicts waste more time than waiting.

## Knowledge Sharing

Knowledge flows through two channels:

### Automatic (via Remindr)
- Decisions recorded with `decide()` are available to all sessions
- Facts stored with `remember()` are queryable by all sessions
- New sessions start with `briefing()` which aggregates current state

### Deliberate (via team practices)
- PR reviews surface knowledge from one person's work to others
- Stand-ups reference Remindr state rather than relying on memory
- Architectural decisions are captured as ADRs (agent drafts, team reviews)

## Handoffs

If you stop working mid-task (end of day, switching to something urgent, whatever), make the handoff clean:

```
# Before ending your session
remember("state", "Task X: implemented the API endpoints and unit tests.
  Remaining: integration tests and Playwright E2E for the happy path.
  Note: the PaymentService mock needs to handle the timeout case — 
  see the TODO in PaymentServiceTests.cs line 42.
  Branch: feature/payment-integration")
```

The next person (or your future self) starts their session, calls `briefing()`, and gets this context immediately. No Slack messages to parse, no "what were you working on?" conversations.

### What to record in a handoff

- What's done
- What's remaining
- Any blockers or gotchas discovered
- Branch name and relevant file locations
- Decisions made and their rationale
