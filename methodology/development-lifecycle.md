# Development Lifecycle

The Wizardworks development lifecycle has six phases. AI agents are involved in every phase, but the degree of human involvement varies. The constant: humans make the decisions, agents do the execution.

## Workflow Overview

Every implementation task follows this inner loop:

```
PLAN → DESIGN → IMPLEMENT → REVIEW → SECURE → VERIFY → DOCUMENT
 │                                                          │
 └──────────────────── feedback ────────────────────────────┘
```

This loop sits inside the broader six-phase lifecycle described below.

---

## Phase 1: Requirements

**Lead:** PM / Product Owner
**Agent role:** Structure and organize

| Activity | Who |
|---|---|
| Customer meetings | Human leads |
| Transcribe meetings | AI (speech-to-text) |
| Structure into user stories | Agent, human reviews |
| Write acceptance criteria | Agent drafts, PO approves |
| Prioritize backlog | Human decides |

The agent takes raw meeting transcripts and customer input, then structures them into well-formed user stories with acceptance criteria. The PM/PO reviews, adjusts priorities, and ensures the stories reflect actual customer needs — not just what the agent inferred.

**Output:** Prioritized backlog with user stories and acceptance criteria.

---

## Phase 2: Technical Design

**Lead:** Architect
**Agent role:** Analyze, propose, document

| Activity | Who |
|---|---|
| Analyze existing codebase | Agent |
| Propose architecture options | Agent proposes, architect decides |
| Generate diagrams | Agent |
| Write ADRs (Architecture Decision Records) | Agent drafts, architect approves |
| Define data models | Agent proposes, architect validates |
| Define API specs | Agent proposes, architect validates |

The architect makes the structural decisions. The agent does the analysis and documentation work — scanning codebases, identifying patterns, proposing approaches, and writing up the decisions once made.

**Output:** Design documents, data models, API specifications, ADRs.

---

## Phase 3: Implementation

**Lead:** Developer (as orchestrator)
**Agent role:** Primary executor

This is where the agent does the heaviest lifting. The workflow follows TDD and looks like this:

```
/plan
  └→ Architect validates plan
       └→ /tdd (RED — write failing tests)
            └→ /tdd-implement (GREEN → REFACTOR)
                 └→ Parallel:
                      ├─ /code-review
                      ├─ /security-review
                      ├─ /e2e
                      └─ /update-docs
```

### Step by step

1. **Plan** — Agent creates an implementation plan. Architect reviews and adjusts.
2. **TDD: Red** — Agent writes failing tests based on the acceptance criteria.
3. **TDD: Green** — Agent writes the minimum code to make tests pass.
4. **TDD: Refactor** — Agent refactors while keeping tests green.
5. **Review (parallel)** — Code review, security review, E2E tests, and documentation updates run in parallel.

### Key principle: smaller tasks produce better results

Break work into small, focused tasks. An agent working on a well-scoped task with clear context will outperform one working on a vague, sprawling task every time. If a task takes more than a couple of hours, it should probably be split.

**Output:** Working, tested, reviewed, documented code.

---

## Phase 4: Test & QA

**Lead:** QA / Developer
**Agent role:** Test execution, gap analysis

### Testing Pyramid

```
         ╱  Manual QA  ╲          ← Exploratory, edge cases
        ╱───────────────╲
       ╱    E2E Tests    ╲        ← Playwright
      ╱───────────────────╲
     ╱  Integration Tests  ╲      ← API, database
    ╱───────────────────────╲
   ╱      Unit Tests         ╲    ← xUnit, Vitest
  ╱───────────────────────────╲
```

| Test level | Tooling | Agent involvement |
|---|---|---|
| Unit tests | xUnit (.NET), Vitest (frontend) | Agent writes and runs |
| Integration tests | In-memory DB, test containers | Agent writes and runs |
| E2E tests | Playwright | Agent writes, human reviews scenarios |
| Manual QA | Human | Human performs exploratory testing |

### Test Quality: Mutation Testing

Unit and integration tests are validated with **mutation testing** (Stryker). The agent runs Stryker, analyzes surviving mutants, and writes additional tests to kill them. This ensures tests actually verify behavior, not just execute code paths.

**Output:** Full test suite passing, mutation score above threshold, QA sign-off.

---

## Phase 5: Deployment

**Lead:** DevOps / Developer
**Agent role:** Pipeline setup and configuration

**Hard rule: deployments always go through CI/CD pipelines. Never deploy from a terminal.**

| Activity | Who |
|---|---|
| CI/CD pipeline setup | Agent configures, human reviews |
| Pipeline maintenance | Agent handles, human reviews changes |
| Go/no-go decision | Human decides |
| Trigger deployment | Human triggers |
| Monitor rollout | Human monitors |

The agent can set up and configure pipelines, but the human makes the deployment decision and triggers it. This is a deliberate safety boundary.

**Output:** Successful deployment to target environment.

---

## Phase 6: Production

**Lead:** Team
**Agent role:** Triage, analysis, hotfixes

| Activity | Who |
|---|---|
| Monitor alerts | Human + automated |
| Bug triage | Agent analyzes, human prioritizes |
| Hotfix implementation | Agent implements (same TDD flow) |
| Root cause analysis | Agent investigates, human validates |
| Feed learnings back | Human ensures loop closes |

Production issues feed back into Phase 1 as new requirements or bug reports. The cycle continues.

**Output:** Stable production system, learnings captured for future work.

---

## The Full Picture

```
Phase 1          Phase 2           Phase 3            Phase 4
Requirements  →  Technical      →  Implementation  →  Test & QA
(PM/PO leads)    Design            (TDD loop)         (Pyramid)
                 (Architect                            
                  leads)                               

     ↑                                                    │
     │           Phase 6           Phase 5                │
     └────────── Production    ←   Deployment    ←────────┘
                 (Feedback)        (CI/CD only)
```

Each phase has clear ownership and clear boundaries for agent involvement. The agent is deeply involved throughout, but human judgment gates every transition between phases.
