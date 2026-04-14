# Agent Roster

## All Agents

| # | Agent Name | Model | Command | Role |
|---|---|---|---|---|
| 1 | **Planner** | Sonnet | `/plan` | Break down features into tasks |
| 2 | **Architect** | Opus | Direct invoke | Design decisions, architecture validation |
| 3 | **TDD Test Writer** | Sonnet | `/tdd` | Write failing tests (RED phase) |
| 4 | **TDD Implementer** | Opus | `/tdd-implement` | Make tests pass (GREEN), then refactor (BLUE) |
| 5 | **Code Reviewer** | Sonnet | `/code-review` | Quality, patterns, standards review |
| 6 | **Security Reviewer** | Opus | `/security-review` | Vulnerability detection, security audit |
| 7 | **Build Error Resolver** | Haiku | `/build-fix` | Fix compilation and CI failures |
| 8 | **E2E Runner** | Haiku | `/e2e` | Write and run Playwright E2E tests |
| 9 | **Refactor Cleaner** | Sonnet | `/refactor-clean` | Tech debt reduction, code smell removal |
| 10 | **Doc Updater** | Haiku | `/update-docs` | Sync documentation with code changes |

## Agent Details

### 1. Planner (Sonnet)

**Command:** `/plan`
**When to use:** For any non-trivial feature. Before writing any code.
**What it does:** Reads the requirements and existing codebase, then produces a structured implementation plan: approach, affected files, risks, open questions, and task breakdown.
**What it produces:** Implementation plan with task list, approach description, and risk assessment.

### 2. Architect (Opus)

**Command:** Direct invoke (no slash command — called by the developer or other agents when design decisions are needed)
**When to use:** Before implementation, when there are structural decisions to make. When you need to validate an approach against existing architecture.
**What it does:** Analyzes architecture, evaluates options, makes design decisions, validates proposed approaches against project patterns.
**What it produces:** Design decisions with rationale, architecture validation, ADR drafts.

### 3. TDD Test Writer (Sonnet)

**Command:** `/tdd`
**When to use:** At the start of every implementation task. This is the RED phase.
**What it does:** Reads requirements and acceptance criteria, examines existing test patterns, writes tests that define expected behavior. All tests must fail (nothing is implemented yet).
**What it produces:** Failing test suite covering happy path, edge cases, and error conditions.

### 4. TDD Implementer (Opus)

**Command:** `/tdd-implement`
**When to use:** After tests are written and failing. This is the GREEN and BLUE phase.
**What it does:** Reads the failing tests, writes the minimal implementation to make them pass, then refactors for quality while keeping tests green.
**What it produces:** Working implementation that passes all tests, refactored for clarity and patterns.

### 5. Code Reviewer (Sonnet)

**Command:** `/code-review`
**When to use:** After every implementation, before merge.
**What it checks:** Code quality, adherence to project patterns, naming conventions, error handling, test coverage, separation of concerns, SOLID principles.
**What it produces:** Review feedback with specific, actionable findings.

### 6. Security Reviewer (Opus)

**Command:** `/security-review`
**When to use:** For any code touching authentication, authorization, data handling, external APIs, user input, or sensitive operations.
**What it checks:** OWASP Top 10, injection vulnerabilities, authentication flaws, authorization bypasses, data exposure, insecure configurations, dependency vulnerabilities.
**What it produces:** Security audit report with severity-rated findings and remediation guidance.

### 7. Build Error Resolver (Haiku)

**Command:** `/build-fix`
**When to use:** When the build breaks — compilation errors, CI pipeline failures, dependency conflicts.
**What it does:** Reads build output, diagnoses the root cause, applies the fix.
**What it produces:** Fixed build with explanation of what was wrong.

### 8. E2E Runner (Haiku)

**Command:** `/e2e`
**When to use:** For critical user flows — login, checkout, data submission, anything the user touches directly.
**What it does:** Writes and runs Playwright end-to-end tests that simulate real user interaction.
**What it produces:** E2E test suite with pass/fail results.

### 9. Refactor Cleaner (Sonnet)

**Command:** `/refactor-clean`
**When to use:** When explicitly asked to improve code quality. Not part of the standard feature flow — invoke when tech debt needs attention.
**What it does:** Identifies code smells, duplication, complexity, and pattern violations. Refactors while keeping all tests green.
**What it produces:** Cleaner code with explanation of what was improved and why.

### 10. Doc Updater (Haiku)

**Command:** `/update-docs`
**When to use:** After every feature implementation. Documentation must stay in sync with code.
**What it does:** Scans code changes, identifies affected documentation, updates or creates docs to match the current state.
**What it produces:** Updated documentation (README, API docs, inline comments, architecture docs).

## Model Tier Rationale

The model assigned to each agent is deliberate. Do not override these assignments.

| Tier | Model | Used For | Why |
|---|---|---|---|
| **Critical reasoning** | Opus | Architecture, security, implementation | Mistakes are expensive. Wrong architecture decisions, missed vulnerabilities, or broken implementations cost far more to fix later than the extra cost of a stronger model. |
| **Judgment calls** | Sonnet | Planning, code review, test writing, refactoring | Requires good judgment but errors are caught by downstream agents. Fast enough for iteration, smart enough for nuanced decisions. |
| **Structured tasks** | Haiku | Build fixes, documentation, E2E tests | Well-defined, pattern-following work. The task is clear, the output format is known, and the results are immediately verifiable. Speed and cost matter more than depth. |

**NEVER override agent models.** Each agent is optimized for its task. Using a cheaper model for security review or a more expensive model for documentation updates wastes money or creates risk — or both.
