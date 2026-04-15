# Wizardworks Agentic Patterns and Practices

How Wizardworks builds software with AI agents.

This repository is the single source of truth for our agentic development methodology — how we set up client agents, how we work with them, and how they connect to the Fae platform for shared knowledge and coordination.

## Quick Start

### Starting a new client/project agent

Open Claude Code and tell it:

```
Set up a new client project called <project-name> based on
https://github.com/Wizardworks-AB/wizardworks-agentic-patterns-and-practices
```

Replace `<project-name>` with the name of the customer or project. Claude will clone this repo, create the agent from the template, and walk you through configuration.

### Want to understand the process in detail?

Follow the [Getting Started](getting-started/01-overview.md) guide:

1. [Overview](getting-started/01-overview.md) — What is agentic development at Wizardworks
2. [Create Client Agent](getting-started/02-create-client-agent.md) — Set up your agent from the template
3. [Initialize Agent](getting-started/03-initialize-agent.md) — Customize for your project
4. [Connect to Fae](getting-started/04-connect-to-fae.md) — Link to the shared knowledge graph
5. [Living Documentation](getting-started/05-living-documentation.md) — Set up persistent project knowledge

### Already set up?

Jump to the section you need:

- [Methodology](methodology/) — The AI-first development philosophy and lifecycle
- [Patterns](patterns/) — Practical patterns for working with agents
- [Fae](fae/) — Architecture, Remindr integration, and MCP tools
- [Reference](reference/) — Agent roster, commands, hooks, tech stack

## Repo Map

```
wizardworks-agentic-patterns-and-practices/
├── getting-started/          Onboarding flow (do this first)
│   ├── 01-overview.md
│   ├── 02-create-client-agent.md
│   ├── 03-initialize-agent.md
│   ├── 04-connect-to-fae.md
│   └── 05-living-documentation.md
│
├── methodology/              The Wizardworks Way
│   ├── ai-first-human-responsibility.md
│   ├── development-lifecycle.md
│   ├── developer-agent-dynamics.md
│   ├── team-workflow.md
│   └── when-agents-when-manual.md
│
├── patterns/                 Practical patterns
│   ├── client-agent-structure.md
│   ├── sibling-directory-pattern.md
│   ├── tdd-with-agents.md
│   ├── code-review-with-agents.md
│   ├── effective-instructions.md
│   └── common-pitfalls.md
│
├── fae/                      Fae platform integration
│   ├── architecture-overview.md
│   ├── remindr-integration.md
│   ├── mcp-tools-reference.md
│   ├── knowledge-repo-pattern.md
│   └── autonomous-vs-interactive.md
│
└── reference/                Quick reference
    ├── agent-roster.md
    ├── command-reference.md
    ├── hook-system.md
    └── technology-stack.md
```

## Related Repositories

| Repository | Purpose |
|-----------|---------|
| [wizardworks-client-agent-template](https://github.com/Wizardworks-AB/wizardworks-client-agent-template) | Template for creating new client agents |
| [fae-architecture](https://github.com/Wizardworks-AB/fae-architecture) | Fae platform architecture documentation |
| [remindr](https://github.com/Wizardworks-AB/remindr) | Knowledge graph MCP server |

## For Agents

This repo includes a `CLAUDE.md` that instructs AI agents how to read and use this documentation. If you're an agent reading this repo, start with `CLAUDE.md` for your instructions.
