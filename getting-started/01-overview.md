# What is Agentic Development at Wizardworks

## AI-First, Human Responsibility

At Wizardworks, we use AI agents as our primary development tool. But "AI-first" does not mean "AI-only." The philosophy is simple:

**Agents execute. Humans decide and are responsible.**

You are responsible for the code that ships, the architecture decisions that are made, and the quality of the end result. The agent is a powerful collaborator that does the heavy lifting under your direction.

## The 70-20-10 Rule

A typical work session with an agent follows this distribution:

| Share | Who | What |
|-------|-----|------|
| **70%** | Agent produces | Writing code, running tests, searching codebases, generating boilerplate, refactoring |
| **20%** | You steer | Reviewing output, course-correcting, making architectural decisions, asking the right questions |
| **10%** | You fix/polish | Edge cases the agent missed, final quality checks, domain-specific nuances |

This ratio shifts depending on task complexity. Greenfield CRUD work might be 90-5-5. A tricky concurrency bug might be 40-30-30. The point is: the agent does most of the typing, but you do most of the thinking.

## Key Components

Agentic development at Wizardworks involves four key components working together:

### Client Agent (Claude Code config)
A repository containing the agent's configuration: `CLAUDE.md` instructions, hooks, rules, commands, and skills. This is what makes the agent understand your project, your conventions, and your workflow.

### Code Repo (customer code)
The actual codebase the agent works in. This is the customer's project — a .NET API, a React frontend, a microservices system, or whatever the engagement involves.

### Fae (knowledge platform)
Fae is the platform for fully automated software development. It provides the infrastructure that ties agents, knowledge, and workflows together.

### Remindr (knowledge graph MCP)
Remindr is Fae's knowledge graph component, exposed as an MCP server. It gives agents shared memory — decisions, facts, gotchas, blockers, and inter-agent messages — that persists across sessions and across team members.

## What This Means Practically

The day-to-day workflow looks like this:

1. **Configure an agent** — create a client agent from the template, fill in project details
2. **Point it at a codebase** — the agent reads and works in the customer's code repo
3. **Connect it to shared knowledge** — via Remindr, the agent has access to everything the team has learned
4. **Work together** — you give direction, the agent executes, knowledge accumulates

## The Competitive Advantage

What makes this reproducible across projects and team members is not the AI model itself — everyone has access to the same models. The advantage comes from:

- **Documented processes** — every step of the workflow is written down
- **Templates** — new projects start from a proven baseline, not from scratch
- **Hooks** — automated quality checks run on every agent action
- **Patterns** — architectural patterns are encoded in rules the agent follows
- **Shared knowledge** — what one agent learns, all agents can access

This means a new consultant can join a project and be productive on day one. The agent already knows the conventions, the architecture, and the decisions that have been made. That is the real advantage.
