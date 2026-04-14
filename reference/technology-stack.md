# Technology Stack

## The Wizardworks Default Stack

This is the default stack for new projects built by Wizardworks. It represents the team's best practices and what the agent skills are optimized for.

**Important:** For existing codebases, **follow the customer's stack**. Do not impose Wizardworks defaults on a project that already has established technology choices. The agent should adapt to the project, not the other way around.

---

## Backend

| Technology | Purpose |
|---|---|
| **.NET 10+** | Primary backend platform |
| **ASP.NET Core** | Web API framework |
| **Entity Framework Core** | ORM for new projects |
| **Dapper** | Lightweight data access for existing projects or read-heavy queries |
| **xUnit** | Test framework |
| **FluentAssertions** | Assertion library (readable, expressive assertions) |
| **Moq** | Mocking framework |
| **Docker** | Containerization |

### Key Backend Conventions

- Use DTOs at the API boundary — never expose EF Core entities
- Service layer between Controllers and Repositories
- Async/await throughout — no blocking calls
- Structured logging (Serilog or built-in ILogger)
- Options pattern for configuration

---

## Frontend

| Technology | Purpose |
|---|---|
| **React 19+** | UI framework |
| **TypeScript** | Language (strict mode enabled) |
| **TanStack Query** | Server state management (data fetching, caching) |
| **TanStack Form** | Form handling and validation |
| **TanStack Table** | Data tables with sorting, filtering, pagination |
| **TanStack Router** | Type-safe routing |
| **Vitest** | Unit test framework |
| **Playwright** | End-to-end testing |
| **Tailwind CSS** | Utility-first styling |

### Key Frontend Conventions

- No direct `fetch()` or `axios` in components — use TanStack Query
- No `useEffect` for data fetching — use TanStack Query
- No `useState` for server data — use TanStack Query
- Strict TypeScript — no `any`, no type assertions without justification
- Component composition over inheritance
- Immutable state updates (no `.push()`, no direct mutation)

---

## Infrastructure

| Technology | Purpose |
|---|---|
| **Azure** | Cloud platform |
| **Azure App Service** | Web app hosting |
| **Azure SQL** | Relational database |
| **Azure Key Vault** | Secrets management |
| **Azure Container Apps** | Container hosting (Fae agents) |
| **Bicep** | Infrastructure as Code |
| **GitHub Actions** or **Azure DevOps Pipelines** | CI/CD |

### Key Infrastructure Conventions

- All infrastructure defined in Bicep — no manual portal configuration
- Secrets in Key Vault — never in config files or environment variables in code
- CI/CD for all deployments — never deploy from a local machine
- Separate environments: dev, staging, production
- Managed identities where possible — minimize connection strings

---

## AI / Agent

| Technology | Purpose |
|---|---|
| **Claude Code** | Primary development tool (interactive mode) |
| **Fae platform** | Autonomous agent operations |
| **Remindr** | Knowledge graph MCP server |

---

## Quick Reference: Package Choices

| Need | Use | Not |
|---|---|---|
| Data fetching | TanStack Query | fetch, axios, useEffect |
| Forms | TanStack Form | Formik, react-hook-form |
| Tables | TanStack Table | ag-grid, custom tables |
| Routing | TanStack Router | react-router |
| Styling | Tailwind CSS | CSS modules, styled-components |
| Backend tests | xUnit + FluentAssertions | NUnit, MSTest |
| Frontend tests | Vitest | Jest |
| E2E tests | Playwright | Cypress, Selenium |
| ORM (new project) | Entity Framework Core | Dapper (unless read-heavy) |
| ORM (existing project) | Whatever they use | — |
| IaC | Bicep | Terraform, ARM templates |
| Mocking | Moq | NSubstitute, FakeItEasy |

---

## Skill Libraries

The `skills/` directory in the client agent template contains detailed pattern libraries for each part of the stack:

- **.NET patterns** — Controller structure, service layer, repository pattern, error handling, validation
- **React patterns** — Component structure, TanStack Query usage, form handling, state management
- **Infrastructure as Code** — Bicep module patterns, naming conventions, environment configuration

These pattern libraries are loaded by the relevant agent skills and ensure consistent code generation across projects.
