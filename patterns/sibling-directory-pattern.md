# The Sibling Directory Pattern

## The Problem

A client agent repository contains Wizardworks intellectual property: development processes, engineering standards, agent configurations, workflow definitions, and potentially pricing context. The customer's code repository contains the customer's intellectual property: their application code, business logic, and data.

These two bodies of IP **must not be mixed**. Wizardworks configuration must never leak into a customer's repository. Customer code must never be committed to a Wizardworks agent repo.

## The Solution

Place the two repositories side by side as siblings in the filesystem. They never overlap.

```
~/agents/acme/              ← Client agent repo (Wizardworks IP)
~/code/acme-project/        ← Customer code repo (Customer IP)
```

Each repo is independently version-controlled. Each has its own remote. They are completely separate git histories.

## How It Works

1. **You run `claude` from the agent directory:**

   ```bash
   cd ~/agents/acme/
   claude
   ```

2. **CLAUDE.md tells the agent where the code lives:**

   Inside `~/agents/acme/CLAUDE.md`, a section specifies the path to the customer's code:

   ```markdown
   ## Code Repository
   The code repository is located at ~/code/acme-project/
   ```

3. **The agent navigates to the code repo to work:**

   When the agent needs to read, modify, or test code, it navigates to `~/code/acme-project/`. All file operations happen there.

4. **Nothing crosses the boundary:**

   - No `.claude/` directory is created in the customer's code repo.
   - No agent configuration files are committed to customer code.
   - No customer source code is committed to the agent repo.

## Directory Structure in Practice

```
~/agents/
├── acme/                       # Client agent for Acme Corp
│   ├── CLAUDE.md               # Points to ~/code/acme-webapp/
│   ├── CONSTITUTION.md
│   ├── DOC.md
│   └── .claude/
│       ├── agents/
│       ├── commands/
│       ├── hooks/
│       └── ...
├── globex/                     # Client agent for Globex Inc
│   ├── CLAUDE.md               # Points to ~/code/globex-api/
│   └── .claude/
│       └── ...
└── initech/                    # Client agent for Initech
    ├── CLAUDE.md               # Points to ~/code/initech-platform/
    └── .claude/
        └── ...

~/code/
├── acme-webapp/                # Acme's application code
│   ├── src/
│   ├── tests/
│   └── .git/                   # Customer's git history
├── globex-api/                 # Globex's API code
│   └── ...
└── initech-platform/           # Initech's platform code
    └── ...
```

## Benefits

**Clean IP separation.** Wizardworks processes and customer code never share a repository. There is zero risk of accidentally pushing agent config to a customer remote or customer code to a Wizardworks remote.

**Independent version control.** Agent configuration evolves on its own timeline. You can update workflows, add new hooks, or change agent models without touching the customer's code repo. Similarly, customer code changes don't affect the agent setup.

**Team consistency.** Multiple team members clone the same agent repo and get identical configuration. Everyone uses the same workflows, the same review standards, the same hooks. The only thing that varies is the local path to the code repo, which each developer sets in their CLAUDE.md.

**Customer-safe deployments.** When the engagement ends, the customer keeps their code repo. It contains nothing Wizardworks-specific. No cleanup needed.

## Setup

For a new engagement with customer "Acme" and their project "acme-webapp":

```bash
# Clone the agent repo (created from the template)
git clone git@github.com:wizardworks/acme-agent.git ~/agents/acme/

# Clone the customer's code repo
git clone git@github.com:acme-corp/acme-webapp.git ~/code/acme-webapp/

# Edit CLAUDE.md to point to the code repo
# Set: "The code repository is located at ~/code/acme-webapp/"

# Start working
cd ~/agents/acme/
claude
```

## Key Rule

The agent repo is where you **launch** Claude Code. The code repo is where Claude Code **works**. These are always two different directories.
