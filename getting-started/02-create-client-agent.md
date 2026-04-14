# Create a Client Agent

## Prerequisites

Before you begin, make sure you have:

- **GitHub access** to the [Wizardworks-AB](https://github.com/Wizardworks-AB) organization
- **Claude Code** installed and working (`claude --version` to verify)

## Step 1: Open the Template

Go to the client agent template repository:

```
https://github.com/Wizardworks-AB/wizardworks-client-agent-template
```

## Step 2: Create Your Agent Repo

Click **"Use this template"** and then **"Create a new repository"**.

- **Owner:** Wizardworks-AB
- **Name:** Follow the naming convention `customer-agent`, for example `acme-agent`
- **Visibility:** Private

## Step 3: Clone to Your Agents Directory

Clone the new repo into your local `~/agents/` directory:

```bash
git clone https://github.com/Wizardworks-AB/acme-agent.git ~/agents/acme/
```

## Step 4: Verify the Customer Code Repo

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
├── agents/          # Agent configurations
│   ├── acme/        # Client agent for Acme project
│   └── beta/        # Client agent for Beta project
│
└── code/            # Customer code repositories
    ├── acme-api/    # Acme's backend
    └── beta-web/    # Beta's frontend
```

This separation is deliberate:

- **Agent config** lives in its own repo, versioned independently from the customer's code
- **Customer code** stays untouched by agent configuration files
- The agent knows where to find the code repo via paths configured in `CLAUDE.md`

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
