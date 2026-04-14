# Hook System

## What Hooks Are

Hooks are automated scripts that run when Claude Code writes or edits files. They are defined in `.claude/hooks/hooks.json` in the client agent repo.

Hooks act as guardrails — they catch common mistakes at write time, before the code is committed. Think of them as a real-time linter that understands your project's architectural rules, not just syntax.

## How They Work

Each hook has two parts:

1. **Matcher** — determines which files and operations trigger the hook
2. **Action** — a Node.js script or prompt injection that runs when triggered

### Matcher Example

```json
{
  "tool": "Write",
  "matcher": "tool_input.file_path matches 'Controller'"
}
```

This triggers when Claude Code writes a file with "Controller" in its path. Matchers can check the tool being used (`Write`, `Edit`) and the file path being targeted.

### Action Types

- **Node.js scripts** — Run a script that analyzes the file content and returns findings with severity levels
- **Prompt injections** — Inject a reminder into the agent's context (no script execution)

## The 14 Hooks

### Blocking Hooks

These will stop the operation if a critical finding is detected.

#### check-secrets.js

**Detects:** Hardcoded API keys, passwords, connection strings, tokens, certificates.

CRITICAL findings block the write. This is the only hook that can prevent a file from being written. **Always keep this hook enabled** — there is never a valid reason to commit secrets.

```
Examples caught:
  "Password": "hunter2"
  Authorization: "Bearer sk-..."
  connectionString: "Server=...;Password=..."
```

### Warning Hooks

These warn but allow the operation to continue. The agent sees the warning and should address it.

#### check-public-ids.js

**Detects:** Database ID exposure in Controllers and DTOs. Internal IDs (auto-increment integers, GUIDs used as PKs) should not leak to the public API.

#### check-dto-usage.js

**Detects:** Entity/model classes used directly in Controllers instead of DTOs. Controllers should never expose database entities — use Data Transfer Objects.

#### check-layer-separation.js

**Detects:** Architecture violations:
- Repository dependencies injected directly into Controllers (should go through a service layer)
- Business logic in the wrong layer (Controller doing what a Service should do)

#### check-console-log.js

**Detects:** `console.log`, `console.warn`, `console.error` in TypeScript and JavaScript files. Use proper logging infrastructure instead.

#### check-async-await.js

**Detects:** Async/await anti-patterns:
- `async` methods that never `await` (probably missing an await)
- `.Result` or `.Wait()` calls that block on async code (deadlock risk)
- `async void` methods (fire-and-forget, exception-swallowing)

#### check-immutability.js

**Detects:** State mutation patterns:
- `array.push()` instead of spread
- Direct object mutation instead of creating new objects
- `array.sort()` without spreading first (mutates in place)

#### check-tanstack-query.js

**Detects:** Direct `fetch()` or `axios` calls in React components, and `useEffect` for data fetching. Use TanStack Query instead — it handles caching, deduplication, loading states, and error handling.

### Reminder Hooks

These inject reminders into the agent's context at relevant moments. No script runs — the agent just gets a nudge.

| Hook | Reminder |
|---|---|
| **Check Test Coverage** | Reminds the agent to verify test coverage meets the 80% threshold |
| **Verify TDD Workflow** | Reminds the agent to follow RED-GREEN-BLUE, not skip phases |
| **Infrastructure as Code** | Reminds the agent to use Bicep for any infrastructure changes |
| **Security Review** | Reminds the agent to consider security implications |
| **Docker Config** | Reminds the agent to follow Docker best practices (multi-stage builds, non-root user) |
| **Code Review** | Reminds the agent to self-review before marking complete |

## Customizing Hooks

### For New Projects

Use the full set of hooks as-is. They encode Wizardworks best practices and catch the most common agent mistakes.

### For Existing Codebases

Disable hooks that conflict with the customer's established patterns:

- Customer uses `console.log` intentionally? Disable `check-console-log.js`.
- Customer uses Entity Framework entities in Controllers? Disable `check-dto-usage.js`.
- Customer has a different layering convention? Disable `check-layer-separation.js`.
- Customer does not use TanStack Query? Disable `check-tanstack-query.js`.

**Always keep `check-secrets.js`.** There is no codebase where committing secrets is acceptable.

### How to Disable a Hook

In `.claude/hooks/hooks.json`, remove or comment out the hook entry. Do not delete the hook script file — you may want to re-enable it later.

## Hook Output

Hooks produce findings with severity levels:

| Severity | Behavior | Example |
|---|---|---|
| `CRITICAL` | Blocks the operation | Hardcoded secret detected |
| `WARNING` | Warns, allows to continue | Database ID exposed in DTO |
| `INFO` | Informational note | Reminder to check test coverage |
