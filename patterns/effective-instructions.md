# Writing Effective Agent Instructions

## The Core Truth

The quality of agent output is **directly proportional** to the quality of your instructions. Vague input produces vague output. Specific input produces specific output. This is not a limitation of the tool — it is how language models work.

## Principles

### 1. Be Specific

Tell the agent exactly what you want. Include constraints, expected behavior, and technical details.

```
BAD:
"Add login"

GOOD:
"Add a login endpoint that accepts email/password via POST /api/auth/login,
validates against the Users table, returns a JWT with 1h expiry, and returns
401 with a generic error message for invalid credentials."
```

The second instruction eliminates ambiguity about the endpoint path, HTTP method, data source, token format, expiry, and error handling. The agent doesn't have to guess.

### 2. Provide Context

The agent can only work with what it knows. Tell it about:

- **Existing patterns** — "We use the repository pattern with Unit of Work"
- **Related code** — "The UserService already handles password hashing"
- **Domain constraints** — "Email addresses must be unique per tenant, not globally"
- **Non-obvious requirements** — "This endpoint will be called by the mobile app, which expects camelCase JSON"

Context prevents the agent from making reasonable-but-wrong assumptions.

### 3. Break Down Large Tasks

One feature per instruction. Large tasks cause the agent to lose focus, make inconsistent decisions across files, and produce harder-to-review output.

```
BAD:
"Add user authentication"

GOOD (as separate tasks):
1. "Add POST /api/auth/login endpoint"
2. "Add POST /api/auth/register endpoint with email verification"
3. "Add POST /api/auth/forgot-password endpoint"
4. "Add JWT middleware that validates tokens on protected routes"
5. "Add refresh token rotation"
```

Each task is small enough to implement, test, review, and commit independently.

### 4. Use /plan First

Even for tasks that seem simple, start with `/plan`. The agent will:

- Analyze the existing codebase for relevant patterns
- Identify files that need to change
- Propose an implementation approach
- Flag potential issues or conflicts

Review the plan before proceeding. It's far cheaper to correct direction at the planning stage than after implementation.

```
/plan "Add email verification to user registration"
→ Review the plan
→ Adjust if needed
→ /tdd to write tests
→ /tdd-implement to build it
```

### 5. Reference Existing Code

When the project already has a pattern you want followed, point to it explicitly.

```
"Add a ProductController following the same pattern as OrderController —
same base class, same error handling, same response envelope format."
```

This gives the agent a concrete example to match rather than relying on its general knowledge of what a controller "should" look like.

## CLAUDE.md: Your Most Important Instruction

`CLAUDE.md` is loaded at the start of every session. It's the persistent context that informs every task the agent performs.

Put this in CLAUDE.md:

| Content                  | Example                                              |
|--------------------------|------------------------------------------------------|
| Project-specific notes   | "This is a multi-tenant SaaS application"            |
| Domain terminology       | "A 'workspace' is a tenant. A 'member' is a user within a workspace." |
| Tech stack deviations    | "We use Dapper instead of Entity Framework for data access" |
| Code locations           | "Business logic lives in src/Domain/, API endpoints in src/Api/Controllers/" |
| Known constraints        | "The database does not support cascading deletes"    |

Every piece of context you add to CLAUDE.md saves you from repeating it in individual instructions.

## Slash Commands as Workflow Anchors

For standard workflows, **use slash commands instead of freeform instructions**. Slash commands encode best practices and ensure consistency.

| Instead of...                     | Use...                |
|-----------------------------------|-----------------------|
| "Write some tests for this"       | `/tdd`                |
| "Review this code"                | `/code-review`        |
| "Make a plan for this feature"    | `/plan`               |
| "Commit these changes"            | `/commit`             |
| "Check for security issues"       | `/security-review`    |

Slash commands spawn the right agent, with the right model, following the right process. Freeform instructions leave all of those decisions to chance.

## The Anti-Pattern: Vague Instructions + Frustration

The most common workflow failure:

1. Give a vague instruction: "Make it better"
2. Get output that doesn't match unspoken expectations
3. Get frustrated at the agent
4. Give another vague instruction: "No, not like that"
5. Repeat

"Make it better" is not an instruction. Better how? Faster performance? Cleaner code? More readable? More testable? Different architecture? Each of those is a different task with a different approach.

If you find yourself thinking "the agent should know what I mean," that's a signal to be more explicit. The agent has no memory of your internal expectations — only what you write down.
