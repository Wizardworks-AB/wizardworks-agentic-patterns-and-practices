# Fae Architecture Overview

## What Is Fae?

Fae is Wizardworks' product for fully automated software development. It automates the entire agile chain — from requirements through implementation, review, and documentation — using AI agents running 24/7 in the cloud.

The key philosophy behind Fae:

> **The model is interchangeable. The harness is the product.**

Fae is an agent harness. The value is not in which LLM you call, but in the infrastructure surrounding the model: how events are routed, how context is assembled, how knowledge persists, how skills are composed, and how work is verified. Models improve and change. The harness stays.

## Components

| Component | What It Does |
|---|---|
| **fae-gateway** | Thin proxy layer. Teams bot, webhook receiver, cron scheduler, portal backend. Routes events into the system. |
| **fae-agent** | The runtime. Skills, tools, context system, knowledge repo access. This is where the agent does its work. |
| **fae-portal** | Multi-tenant Next.js dashboard. Customers and Wizardworks staff monitor agent activity, review output, manage configuration. |
| **fae-common** | Shared contracts, Bicep templates, pipeline templates. Everything reused across components. |
| **remindr** | Knowledge graph MCP server. Separate repo. See [remindr-integration.md](remindr-integration.md). |

## Event Flow

```
Event Sources                    fae-gateway              Processing
─────────────                    ───────────              ──────────
Teams messages    ─┐
DevOps webhooks   ─┼──→  fae-gateway  ──→  Azure Storage Queues  ──→  Workers/Executors  ──→  Agent
Cron schedules    ─┤
Portal actions    ─┘
```

Every interaction enters through fae-gateway and lands in a queue. Workers pick up queued items and spin up agent instances to process them. This decouples event ingestion from execution and provides natural backpressure.

## No Orchestrator

A critical architectural decision: Fae uses **one agent with multiple skills**, not multiple specialized agents coordinated by an orchestrator.

Traditional multi-agent systems use an orchestrator to route work between specialized agents (a planner agent, an implementer agent, a reviewer agent, etc.). This creates coordination complexity, state synchronization problems, and failure modes at every handoff.

Fae's approach: a single agent runtime that loads different skill configurations depending on the task. The skills are: plan, refine, implement, review, and security-scan. The agent switches behavior by switching skills, not by passing work to a different process.

This means:
- No inter-agent communication overhead for a single task
- No state synchronization between agents
- No orchestrator as a single point of failure
- Simpler debugging — one agent, one log stream per task

## Multi-Tenancy

Fae runs as shared infrastructure with per-tenant isolation where it matters:

| Shared | Per-Tenant |
|---|---|
| fae-gateway | Agent instances |
| Queue infrastructure | Knowledge repos |
| fae-portal | Credentials and secrets |
| Deployment pipelines | Customer code access |

Each customer gets their own agent instances, knowledge graph data, and credential store. The gateway, queue infrastructure, and portal are shared across tenants.

## Customer Experience

The customer's PM/PO interacts through **Teams** and **Azure DevOps** — they never leave their normal workflow. There is no new tool to learn, no new portal to check (though the portal is available for deeper visibility). The agent shows up as a Teams bot and updates work items in their existing DevOps board.

This is deliberate. Adoption friction kills products. By meeting customers where they already work, Fae becomes invisible infrastructure rather than another tool to manage.
