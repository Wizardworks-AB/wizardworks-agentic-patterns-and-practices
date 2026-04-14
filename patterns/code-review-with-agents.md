# Code Review with Agents

## Two-Stage Review Process

Every change goes through two review stages:

1. **Agent review** — Automated first pass using specialist review agents
2. **Human review** — Final review by a developer focusing on what agents cannot reliably judge

Neither stage replaces the other. Agent reviews are fast, thorough, and consistent. Human reviews catch business logic issues, architectural misjudgments, and subtle domain errors that agents miss.

## The Self-Validation Loop

After implementation, run all review agents. If issues are found, fix them and re-run. Repeat until clean.

```
/tdd-implement
      │
      ▼
┌─────────────────────────────────────────────┐
│  Run in parallel:                           │
│  /code-review  /security-review  /e2e       │
│  /update-docs                               │
└─────────────────────────────────────────────┘
      │
      ▼
  Issues found?
      │
  ┌───┴───┐
  YES     NO
  │       │
  ▼       ▼
 FIX    DONE → Ready for human review
  │
  └──→ Re-run reviews
```

This loop continues until all automated checks pass. Only then does the change go to a human reviewer.

## Why Parallel Reviews

The review agents are **independent** — code review does not depend on security review, and neither depends on e2e tests. Running them in parallel provides two benefits:

1. **Speed.** Four reviews complete in the time of one.
2. **Token efficiency.** Each agent runs in its own context window, keeping the context focused and reducing token usage compared to one agent doing everything sequentially.

## What Each Reviewer Checks

### code-reviewer (sonnet)

The general-purpose code quality reviewer.

| Area                | What it checks                                              |
|---------------------|-------------------------------------------------------------|
| Patterns compliance | Does the code follow project patterns defined in skills?    |
| Naming              | Are variables, methods, and classes named per conventions?   |
| Architecture        | Does the code respect layer boundaries and dependencies?     |
| Code quality        | Duplication, complexity, readability, single responsibility  |

Uses **sonnet** — fast enough to review large diffs, capable enough to catch structural issues.

### security-reviewer (opus)

The security-focused reviewer. Deliberately uses a more powerful model because security flaws are high-impact and subtle.

| Area              | What it checks                                             |
|-------------------|------------------------------------------------------------|
| Vulnerabilities   | SQL injection, XSS, CSRF, path traversal, deserialization |
| Secrets           | Hardcoded credentials, API keys, connection strings        |
| Input validation  | Missing or insufficient validation on user input           |
| Auth issues       | Broken authentication, missing authorization checks        |

Uses **opus** — security review requires deeper reasoning about attack vectors.

### e2e-runner (haiku)

Runs end-to-end tests covering critical user flows.

| Area              | What it checks                                             |
|-------------------|------------------------------------------------------------|
| Critical paths    | Login, core workflows, data creation/retrieval             |
| Integration       | Do components work together correctly?                      |
| Regressions       | Did the change break existing user flows?                  |

Uses **haiku** — the task is execution and reporting, not deep analysis.

### doc-updater (haiku)

Checks that documentation stays current with code changes.

| Area              | What it checks                                             |
|-------------------|------------------------------------------------------------|
| API docs          | Do endpoint descriptions match implementation?             |
| README updates    | Are setup instructions still accurate?                     |
| Inline docs       | Are public method summaries present and correct?           |

Uses **haiku** — documentation comparison is straightforward.

## The /commit Review Gate

The `/commit` command includes a built-in validation step. Before creating a commit, it checks the changes against Patterns & Practices standards.

```
/commit
  │
  ▼
Validate against P&P
  │
  ├── Pass → Create commit with conventional message
  │
  └── Fail → Report issues, do not commit
```

This means even if you skip the explicit `/code-review` step, the commit itself enforces a baseline quality check. However, `/commit` is a lighter check than the full review loop. For production code, always run the full review suite.

## Human Review Focus Areas

After agent reviews pass, the human reviewer focuses on:

- **Business logic correctness** — Does the code do what the business actually needs?
- **Architectural fit** — Does this change belong here, or should it be structured differently?
- **Edge cases** — Are there scenarios the agents didn't consider?
- **Domain accuracy** — Are business rules implemented correctly?
- **Long-term maintainability** — Will this be understandable in six months?

The human review is faster and more focused because the agents have already caught the mechanical issues.
