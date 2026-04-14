# Command Reference

## All Commands

| Command | What It Does | Agent | Model | Workflow Position |
|---|---|---|---|---|
| `/plan` | Break down requirements, analyze codebase, propose approach | Planner | Sonnet | Start of feature work |
| `/tdd` | Write failing tests (RED phase) | TDD Test Writer | Sonnet | After plan approval |
| `/tdd-implement` | Make tests pass, refactor (GREEN then BLUE) | TDD Implementer | Opus | After tests are written |
| `/code-review` | Quality and standards review | Code Reviewer | Sonnet | After implementation |
| `/security-review` | Security vulnerability audit | Security Reviewer | Opus | After implementation |
| `/build-fix` | Diagnose and fix build/CI failures | Build Error Resolver | Haiku | When build breaks |
| `/e2e` | Write and run Playwright E2E tests | E2E Runner | Haiku | After implementation |
| `/refactor-clean` | Improve code quality, reduce tech debt | Refactor Cleaner | Sonnet | On demand |
| `/update-docs` | Sync documentation with code | Doc Updater | Haiku | After implementation |
| `/commit` | Validate against P&P, generate docs, commit + push | Built-in | — | End of task |
| `/retrospective` | Session learnings, save insights to memory | Built-in | — | End of session |

## Command Details

### /plan

Analyzes the requirements and existing codebase, then produces a structured implementation plan. The plan includes the proposed approach, affected files, risks, open questions, and a task breakdown.

Run this before any non-trivial implementation. The plan should be reviewed by the architect or developer before proceeding.

### /tdd

Writes failing tests that define the expected behavior. This is the RED phase of TDD. The tests must fail because no implementation exists yet.

The test writer examines existing test patterns and conventions in the codebase to ensure consistency.

### /tdd-implement

Takes the failing tests from `/tdd` and writes the minimal implementation to make them pass (GREEN). Then refactors the implementation for quality while keeping all tests green (BLUE).

Uses Opus because correct implementation requires deep understanding of test intent and production code patterns.

### /code-review

Reviews the implementation for quality, patterns, standards, and best practices. Checks naming, error handling, separation of concerns, test coverage, and adherence to project conventions.

### /security-review

Audits the implementation for security vulnerabilities. Checks for OWASP Top 10 issues, injection flaws, authentication/authorization problems, data exposure, and insecure configurations.

Use for any code touching auth, data handling, external APIs, or user input.

### /build-fix

Diagnoses and fixes build failures — compilation errors, CI pipeline failures, dependency conflicts, configuration issues. Uses Haiku because the task is well-defined: read the error, find the cause, apply the fix.

### /e2e

Writes and runs Playwright end-to-end tests for critical user flows. Simulates real user interaction with the application.

### /refactor-clean

Identifies and fixes code smells, duplication, unnecessary complexity, and pattern violations. Not part of the standard feature flow — use when explicitly asked to improve code quality.

### /update-docs

Scans code changes and updates affected documentation. Keeps READMEs, API docs, inline comments, and architecture docs in sync with the current state of the code.

### /commit

The built-in commit command with a review gate. Before committing, it validates the changes against Wizardworks Patterns and Practices, generates or updates documentation, and then commits and pushes.

This is not just `git commit`. It is a quality gate that ensures every commit meets the project's standards.

### /retrospective

Captures session learnings and saves insights to memory (Remindr). Run at the end of a session to record what went well, what was difficult, and what was discovered.

These learnings are available to future sessions through `briefing()` and `context()`.

## Typical Feature Workflow

```
/plan
  │
  ▼
Architect validates plan
  │
  ▼
/tdd                          ← RED: write failing tests
  │
  ▼
/tdd-implement                ← GREEN + BLUE: implement and refactor
  │
  ▼
┌─────────────────────────────────────┐
│  Run in parallel:                   │
│  /code-review                       │
│  /security-review                   │
│  /e2e                               │
│  /update-docs                       │
└─────────────────────────────────────┘
  │
  ▼
/commit                       ← Validate, commit, push
```

### When Things Go Wrong

| Situation | Command |
|---|---|
| Build breaks after implementation | `/build-fix` |
| Code review finds issues | Fix issues, then `/code-review` again |
| Security review finds vulnerabilities | Fix issues, then `/security-review` again |
| Tech debt accumulates | `/refactor-clean` (on demand, not every task) |
| End of session | `/retrospective` |
