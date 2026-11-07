# AI agent operations: useful autonomy without silent production changes

**Status:** Self-hosted agent deployment and supporting tooling implemented; upstream contributions are **open PRs**, not merged fixes.  
**Role:** System design, integration, guardrails, operational tooling, and open-source contributions.  
**Stack:** Python, Docker, GitHub, Sentry, model routing, SQLite, messaging integrations.

## Problem

A generic chatbot is not enough for a working software team. Long-running tasks require ownership, context, incident intake, reproducible handoffs, and a clear line between proposing a change and changing production.

## System and decisions

I built and operated a workflow on top of the open-source **Hermes Agent** project. My configured setup uses **13 specialist agents**, isolated memory/procedural context, a dispatcher, and model-provider failover. Hermes is upstream software; this case study describes my orchestration and operational integrations, **not authorship of Hermes itself**.

- **Delegation:** route work to specialists instead of giving every agent unrestricted access to every task.
- **Incident intake:** turn monitoring signals into a diagnosis and a proposed code change, with a human gate before deployment.
- **Auditability:** use a read-only ticket board to inspect stage, owner, handoffs, PR status, and elapsed time without giving the dashboard write access to the incident system.
- **Boundary:** investigations can be automated; deployments remain approval-controlled.

## Recent open-source work (November 2026)

- [Hermes #148628 — protect updater state from false-corruption restores](https://github.com/NousResearch/hermes-agent/pull/148628). Addresses stale snapshot selection, locked-vs-corrupt SQLite classification, and truthful reporting after an applied restore. **Open / unmerged.** The PR notes that native Windows reproduction and the integrated new-head checks are still outstanding.
- [Hermes #147446 — repair advice for the correct systemd unit](https://github.com/NousResearch/hermes-agent/pull/147446). Fixes a misleading update warning that told operators to reinstall a gateway when a different named unit needed a drop-in override. **Open / unmerged.** The PR reports 29 passing targeted tests and two Linux-only skips on macOS.

These PRs are specific contributions, not evidence that the fixes have landed upstream.

## Tradeoffs and limits

Specialization and memory improve continuity but introduce routing complexity. Automated diagnosis does not prove a suggested code change is safe. Human approval, regression tests, and an explicit review trail are still required. A self-hosted environment also creates a maintenance and recovery burden.

## Evidence

- [Upstream change proposals #148628](https://github.com/NousResearch/hermes-agent/pull/148628) and [#147446](https://github.com/NousResearch/hermes-agent/pull/147446).
- The ticket-board implementation and deployed agent configuration are maintained separately; this public write-up intentionally excludes access tokens, ticket details, and private infrastructure settings.

**Lesson:** The interesting part of agent engineering is the **authority model and recovery path**, not the agent count.
