# Create a Client Agent

## Prerequisites

Before you begin, make sure you have:

- **GitHub access** to the [Wizardworks-AB](https://github.com/Wizardworks-AB) organization
- **Claude Code** installed and working (`claude --version` to verify)

## How It Works

A client agent is a **local clone** of the shared template repository. Each developer clones the template, configures it for their client, and uses it locally. Agent instances are never pushed back to GitHub.

This means:

- **One template**, many local instances
- **Multiple developers** can have their own agent for the same client
- Each agent is personal configuration, not shared code
- The template repo evolves independently and can be pulled for updates

## Step 1: Clone the Template

Clone the client agent template into your local agents directory:

```bash
git clone https://github.com/Wizardworks-AB/wizardworks-client-agent-template ~/agents/acme/
```

Replace `acme` with the client name (lowercase, hyphenated).

> **Important:** Do not create a new GitHub repository for the agent. Do not push your local agent instance. It lives only on your machine.

## Step 2: Verify the Customer Code Repo

Make sure the customer's code repository is cloned into your `~/code/` directory:

```bash
# If not already cloned:
git clone https://github.com/customer-org/customer-project.git ~/code/customer-project/
```

Verify both directories exist:

```bash
ls ~/agents/acme/
ls ~/code/customer-project/
```

## The Sibling Directory Pattern

Wizardworks uses a consistent directory layout across all machines and all consultants:

```
~/
├── agents/          # Agent configurations (local only, never pushed)
│   ├── acme/        # Client agent for Acme project
│   └── beta/        # Client agent for Beta project
│
└── code/            # Customer code repositories
    ├── acme-api/    # Acme's backend
    └── beta-web/    # Beta's frontend
```

This separation is deliberate:

- **Agent config** is a local clone of the template, customized per client and per developer
- **Customer code** stays untouched by agent configuration files
- The agent knows where to find the code repo via paths configured in `CLAUDE.md`

## Multiple Developers, Same Client

When two developers work on the same client, they each have their own local agent:

```
Developer A:                     Developer B:
~/agents/acme/  (their config)   ~/agents/acme/  (their config)
~/code/acme-api/ (shared repo)   ~/code/acme-api/ (shared repo)
```

The code repo is shared (same GitHub repo, normal git workflow). The agent config is personal. If Developer A customizes their agent's hooks or adds context, it does not affect Developer B.

## Updating the Template

When the template improves, pull updates into your local agent:

```bash
cd ~/agents/acme/
git pull origin main
```

Resolve any conflicts with your local customizations. The template provides the structure; your `CLAUDE.md` and local tweaks are yours to maintain.

## What You Get

The template gives you a fully configured agent out of the box:

| Component | Count | Purpose |
|-----------|-------|---------|
| `CLAUDE.md` | 1 | Main agent instructions with placeholders to fill in |
| Agents | 10 | Specialized agent configurations for different tasks |
| Commands | 10 | Slash commands for common workflows |
| Hooks | 14 | Automated quality checks that run on agent actions |
| Rules | Multiple | Coding style, architecture, and convention rules |
| Skills | Multiple | Reusable capabilities the agent can invoke |
| Contexts | Multiple | Domain and project context files |

You do not need to build any of this from scratch. The next step is to initialize the agent by filling in project-specific details.
