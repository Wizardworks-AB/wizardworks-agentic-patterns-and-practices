# Client Agent Structure

## What Is a Client Agent?

A client agent is a **local clone** of the shared client agent template, configured for a specific customer project. It holds everything the AI agent needs to work effectively within a particular codebase: project context, workflow definitions, coding standards, specialist agent configurations, and automated guardrails.

Think of it as the agent's "workstation setup" — personal to you, tailored to one customer engagement.

## Creating a New Client Agent

Client agents are created by cloning the `wizardworks-client-agent-template` repository locally. Each developer maintains their own local instance. Agent instances are never pushed back to GitHub.

```bash
git clone https://github.com/Wizardworks-AB/wizardworks-client-agent-template ~/agents/acme/
```

See [Create a Client Agent](../getting-started/02-create-client-agent.md) for the full setup guide.

## The Key File: CLAUDE.md

`CLAUDE.md` sits at the repo root and is the **first thing Claude Code reads** when it starts. This is your most important configuration surface.

It typically contains:

- **Project information** — customer name, tech stack, repo locations
- **Workflow instructions** — how to plan, implement, test, and commit
- **Standards** — naming conventions, architectural patterns, domain terminology
- **Fae integration** — instructions for the memory/recall system (briefing, decisions, session logging)

Everything else in the repo supports what CLAUDE.md sets in motion.

## The .claude/ Directory Structure

```
.claude/
├── agents/        # 10 specialist agent definitions
├── commands/      # 10 slash commands for workflows
├── contexts/      # 3 execution modes
├── hooks/         # 14 automated guardrails
├── rules/         # 4 always-loaded standards
├── skills/        # 3 pattern libraries (on-demand)
├── docs/          # Reference documents
└── plans/         # Architecture decision records and plans
```

### agents/ — Specialist Agents

Contains **10 agent definitions**, each a markdown file with YAML frontmatter specifying:

| Field         | Purpose                                      |
|---------------|----------------------------------------------|
| `name`        | Agent identifier                             |
| `description` | What the agent does                          |
| `tools`       | Which tools the agent can use                |
| `model`       | Which Claude model to use (haiku/sonnet/opus)|

Each agent is a specialist: test writer, implementer, code reviewer, security reviewer, documentation updater, and so on. The model assignment is deliberate — lightweight tasks get haiku, complex reasoning gets opus.

### commands/ — Slash Commands

Contains **10 slash commands** that map to standard workflows:

| Command          | Purpose                                |
|------------------|----------------------------------------|
| `/plan`          | Analyze codebase and propose approach  |
| `/tdd`           | Write failing tests (RED phase)        |
| `/tdd-implement` | Write code to pass tests (GREEN phase) |
| `/code-review`   | Run automated code review              |
| `/security-review` | Security-focused review              |
| `/commit`        | Validate and commit changes            |
| `/e2e`           | Run end-to-end tests                   |
| `/update-docs`   | Update documentation                   |
| And more...      |                                        |

Slash commands are the primary interface for day-to-day work. They encode best practices so you don't have to remember them.

### contexts/ — Execution Modes

Contains **3 execution modes** that set behavioral expectations for the agent:

- **dev** — Full development mode. Agent can read, write, test, and modify code.
- **research** — Read-only analysis. Agent explores and reports but does not modify files.
- **review** — Review mode. Agent reads code and provides feedback but does not change anything.

Contexts prevent accidental modifications during analysis sessions.

### hooks/ — Automated Guardrails

Contains **14 hooks** defined in `hooks.json` with corresponding Node.js scripts. Hooks run automatically on events like file write and file edit.

They enforce standards without human intervention — catching issues like incorrect file naming, missing test files, security anti-patterns, and formatting violations before they reach a commit.

### rules/ — Always-Loaded Standards

Contains **4 standard documents** that are loaded into every agent session:

1. **Coding style** — formatting, naming, structure conventions
2. **Testing** — test requirements, coverage thresholds, framework choices
3. **Security** — security baselines, secret handling, input validation
4. **Git workflow** — branch naming, commit message format, PR conventions

Rules are non-negotiable. They apply to every task regardless of context.

### skills/ — Pattern Libraries

Contains **3 pattern libraries** loaded on demand when the agent needs domain-specific guidance:

- **.NET patterns** — C#/ASP.NET conventions, project structure, common implementations
- **React patterns** — component structure, state management, testing approaches
- **Infrastructure as Code** — Bicep patterns, resource naming, deployment practices

Skills are loaded only when relevant, keeping the agent's context focused.

### docs/ — Reference Documents

Contains reference material such as the **TDD playbook** — a detailed guide with routing rules for which test framework to use based on file type and technology stack.

### plans/ — Architecture Decision Records

Stores architecture decisions and implementation plans created during `/plan` sessions.

## Other Key Files

- **CONSTITUTION.md** — A versioned document containing Wizardworks engineering principles. This is the foundational "why" behind the rules and workflows. Found in the client agent repo at `.claude/CONSTITUTION.md`.
- **DOC.md** — Complete usage guide for the client agent. Found in the client agent repo at `.claude/DOC.md`.

## How to Customize

1. **Edit CLAUDE.md** — Update project name, tech stack, repo paths, domain-specific terminology, and any workflow deviations.
2. **Disable/enable hooks** — Comment out or remove hooks in `hooks.json` if they don't apply to the customer's stack.
3. **Adjust rules** — Modify rule documents to match customer conventions (e.g., different naming standards, different branching strategy).
4. **Add or modify skills** — Add customer-specific pattern libraries if the project uses a stack not covered by the defaults.
5. **Review agent models** — Each agent has an optimal model assignment. Do not override unless there is documented justification (e.g., budget constraints requiring all-haiku).

The template gives you a working baseline. Customization makes it fit the specific engagement.
