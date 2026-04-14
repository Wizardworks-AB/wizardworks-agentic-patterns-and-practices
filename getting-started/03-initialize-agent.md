# Initialize Your Client Agent

Once you have created and cloned your client agent, you need to configure it for your specific project.

## Step 1: Fill in CLAUDE.md Placeholders

Open `CLAUDE.md` in the agent repo and replace the placeholder values:

```bash
cd ~/agents/acme/
# Open CLAUDE.md in your editor
```

Find and replace the following placeholders:

| Placeholder | Replace with | Example |
|-------------|-------------|---------|
| `[CUSTOMER_NAME]` | Customer's company name | `Acme Corp` |
| `[DOMAIN_DESCRIPTION]` | Brief description of the business domain | `E-commerce platform for industrial supplies` |
| `[PROJECT_NAME]` | Name of the project or system | `Acme Webshop` |
| `[TECH_STACK]` | Primary technologies in use | `.NET 8, React 18, PostgreSQL, Azure` |
| `[FAE_REMINDR_URL]` | URL for the Fae/Remindr instance | `https://fae.wizardworks.se/acme` |

## Step 2: Add Project-Specific Notes

Below the placeholders, add information the agent needs to know:

- **Domain terminology** — words and concepts specific to the customer's business (e.g., "In Acme's domain, a 'requisition' is different from an 'order'")
- **Tech deviations** — anything that differs from Wizardworks defaults (e.g., "This project uses Dapper instead of EF Core")
- **Key contacts** — who to ask about what (e.g., "Domain questions: Anna Svensson, Infrastructure: DevOps team in #acme-infra")

## Step 3: Choose a Variant (If Needed)

The template defaults to a **greenfield** setup. If your project is different, copy the appropriate variant's `CLAUDE.md` over the root one:

| Variant | When to use |
|---------|-------------|
| **Greenfield** (default) | New project, building from scratch |
| **Existing codebase** | Joining an established project with existing conventions |
| **Maintenance** | Bug fixes and minor features on a stable system |

To switch variant:

```bash
# For an existing codebase project:
cp variants/existing-codebase/CLAUDE.md ./CLAUDE.md

# For a maintenance project:
cp variants/maintenance/CLAUDE.md ./CLAUDE.md
```

Then repeat Step 1 to fill in the placeholders in the new `CLAUDE.md`.

## Step 4: Review Hooks

All 14 hooks are active by default. They run automatically and check the agent's output for common issues.

**For greenfield projects:** Keep all hooks active. They enforce Wizardworks conventions from the start.

**For existing codebases:** Some hooks may conflict with the customer's existing patterns. Review and disable the ones that do not apply:

| Hook | What it checks | When to disable |
|------|---------------|-----------------|
| `check-dto-usage` | DTOs follow naming conventions | Customer uses different DTO patterns |
| `check-layer-separation` | Clean layer boundaries | Customer has different architectural layering |
| `check-public-ids` | Public IDs instead of database IDs in APIs | Customer exposes database IDs |
| `check-immutability` | Immutable data structures where appropriate | Customer prefers mutable patterns |
| `check-tanstack-query` | TanStack Query usage in frontend | Customer uses a different data fetching library |

**Always keep active:**
- `check-secrets` — prevents accidental commits of secrets and credentials. Never disable this.

To disable a hook, comment it out or remove it from the hooks configuration in `.claude/settings.json`.

## Step 5: Review Rules

Check the rules in the `rules/` directory, particularly `coding-style.md`. If the customer uses different conventions (naming, formatting, file organization), adjust accordingly.

The goal is for the agent to produce code that looks like it belongs in the customer's codebase, not code that looks like it came from a template.

## Step 6: Customize Models (Optional)

The default model configuration works for most projects. Only change models if:

- Budget constraints require using cheaper models for certain tasks
- Specific tasks benefit from a different model's strengths

Prefer the defaults unless you have a concrete reason to change.

## Verification

To verify your setup, start Claude Code in the agent directory:

```bash
cd ~/agents/acme/
claude
```

Ask the agent: "Summarize this project's configuration." It should be able to describe the customer, domain, tech stack, and active hooks based on what you configured.
