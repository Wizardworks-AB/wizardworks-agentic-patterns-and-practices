# Connect to Fae

## What is Fae?

Fae is the platform for fully automated software development. It ties together agents, knowledge, and workflows into a cohesive system.

**Remindr** is Fae's knowledge graph component. It stores and retrieves structured knowledge — decisions, facts, gotchas, blockers, and inter-agent messages — and exposes them to agents via MCP (Model Context Protocol).

## Add the Remindr MCP Server

MCP servers are configured in `.mcp.json` in the project root. This file is checked into the repo so the configuration is shared across the team.

Create `.mcp.json` in your agent repo root:

```json
{
  "mcpServers": {
    "remindr": {
      "type": "http",
      "url": "<FAE_URL>/mcp",
      "headers": {
        "X-Default-Project": "<PROJECT_NAME>"
      }
    }
  }
}
```

Replace:
- `<FAE_URL>` — your project's Fae URL (the same one you put in `CLAUDE.md` as `[FAE_REMINDR_URL]`)
- `<PROJECT_NAME>` — your project name (so you don't have to pass `project` on every tool call)

Example:

```json
{
  "mcpServers": {
    "remindr": {
      "type": "http",
      "url": "https://fae.wizardworks.se/acme/mcp",
      "headers": {
        "X-Default-Project": "acme-web"
      }
    }
  }
}
```

> **Important:** Do NOT use `claude mcp add` — that puts the configuration in `settings.local.json` which is user-local and not version-controlled. The `.mcp.json` file ensures every team member and every agent gets the same MCP configuration automatically.

## Authentication

Remindr uses **Entra ID OAuth** for authentication:

1. When you first connect, your browser opens automatically
2. Sign in with your **work account** (your Wizardworks Entra ID credentials)
3. The token is stored in your system keychain — you will not need to sign in again until it expires

No API keys to manage. No secrets to rotate. Just your regular work login.

## Verify the Connection

Start Claude Code and run the `briefing()` command:

```bash
cd ~/agents/acme/
claude
```

Then in the Claude session, ask the agent to run a briefing:

```
Give me a briefing on this project
```

The agent will call the Remindr `briefing()` tool.

If the connection is working:

- **Existing project:** You will get a summary of the project — recent decisions, open blockers, and any messages from other agents or team members
- **New project:** You will get an empty or minimal response, which is expected. The knowledge graph fills up as you work.

If the connection fails, check:

1. The URL is correct and reachable
2. You completed the OAuth sign-in successfully
3. Your account has access to the project in Fae

## What This Gives You

With Remindr connected, your agent has **shared memory across sessions and across team members**:

- **Decisions** — architectural and design decisions made during work sessions
- **Facts** — things the agent has learned about the codebase, domain, or infrastructure
- **Gotchas** — pitfalls, workarounds, and things that are easy to get wrong
- **Blockers** — issues that are preventing progress
- **Inter-agent messages** — if multiple agents work on the same project, they can communicate through the knowledge graph

This means you do not start from zero each session. The agent picks up where you (or a colleague) left off.

## The Agent Already Knows What To Do

The `CLAUDE.md` in the client agent template already contains instructions for using Remindr tools. Once connected, the agent will:

- Read relevant knowledge at the start of a session
- Record decisions and facts as they come up during work
- Check for blockers and messages from other agents
- Update the knowledge graph when important things happen

You do not need to teach the agent how to use Remindr. You just need to connect it.
