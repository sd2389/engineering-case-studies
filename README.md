# Engineering Case Studies

**Smit Desai** · Software engineering, platform systems, AI-integrated products  
[GitHub](https://github.com/sd2389) · [LinkedIn](https://www.linkedin.com/in/smssmit/) · [Selected open-source project: MacUp](https://github.com/sd2389/macup)

> I build systems where product decisions have engineering consequences: who can act, which data is authoritative, what happens when a dependency fails, and how to recover without hiding the failure.

This is a curated, **decision-first** collection of work across agent systems, SaaS infrastructure, computer vision, native developer tools, and jewelry commerce. The goal is to explain **what I built, why I made a choice, what evidence supports it, and what remains unfinished**—not to present every prototype as a shipped product.

**Last reviewed:** November 7, 2026.

## Start here

| Case study | Engineering problem | Evidence / maturity |
| --- | --- | --- |
| **[AI agent operations + Hermes contributions](case-studies/ai-agents-and-hermes.md)** | Delegate work safely; preserve human control during incident-to-fix automation | Self-hosted workflows; [two upstream PRs under review](https://github.com/NousResearch/hermes-agent/pulls?q=is%3Apr+author%3Asd2389) |
| **[MacUp — native macOS maintenance tool](case-studies/macup.md)** | Make software changes explainable, least-privileged, and reversible | [Open source](https://github.com/sd2389/macup); [v0.4.0 prerelease](https://github.com/sd2389/macup/releases/tag/v0.4.0) |
| **[MIST control plane](case-studies/mist-control-plane.md)** | Prove tenant, role, app, and service authority across products | Documented Keycloak / outbox E2E; integration gates remain |
| **[Jewelry visual search](case-studies/visual-search.md)** | Match visual queries correctly without inventing semantic-search capabilities | [59 targeted tests reported on open PR](https://github.com/sd2389/Mist-AI/pull/1); not production vector retrieval |

## Product and infrastructure studies

| Case study | What it demonstrates | Evidence / maturity |
| --- | --- | --- |
| [MIST Studio — browser-based CAD and rendering](case-studies/mist-studio.md) | Interactive 3D, reusable scene state, WebGPU fallback, storefront embeds | [Public implementation](https://github.com/sd2389/studio.mist.com) |
| [DevJewels commerce, scanning, and inventory](case-studies/commerce-and-scanning.md) | Server-authoritative pricing, unique-item identity, reliable scanning, direct media uploads | Implemented workflows; [merged barcode change](https://github.com/sd2389/devjewels-frontend/pull/15); RFID is in progress |
| [CRM, Sales, Unify, and Channels](case-studies/business-product-boundaries.md) | Service/data ownership, tenant permissions, clear demo-vs-live contracts | Product slices and adapters; [public Channels code](https://github.com/sd2389/devjewels-channels) |
| [Self-hosted observability and recovery](case-studies/observability-and-recovery.md) | Protected monitoring, alert reliability, restore verification | Documented deployed setup and uncompleted DR checks |
| [Federated enterprise systems](case-studies/federated-enterprise.md) | Shared identity without a shared business-data free-for-all | Earlier architecture; retained as historical work |

## How to read these studies

Every study uses a consistent structure:

1. **Problem** — why the engineering work mattered.
2. **Design / decisions** — the important alternatives and boundaries.
3. **Implementation evidence** — repository, pull request, test, or documented workflow where available.
4. **Tradeoffs and limits** — what is still experimental, gated, or unverified.

Status language is intentionally strict:

- **Published / merged:** a release or merged change exists. This does not automatically prove real-world adoption.
- **Implemented / tested:** code and a local or documented verification path exist; not necessarily a production cutover.
- **Open PR / in progress:** a proposal or partial integration, **not** a shipped feature.
- **Historical:** useful earlier engineering work, not claimed as today's active product.

## Engineering principles

- **Authority before automation.** A system should prove who is allowed to change state before offering one-click actions or agent workflows.
- **Server-side truth for transactions.** Pricing, unique inventory units, access decisions, and irreversible operations must not depend on browser assumptions.
- **Clear data ownership.** Integrations are easier to reason about when product boundaries and failure behavior are explicit.
- **Observable recovery.** Backups, retries, and monitoring are only valuable when failures can be detected and the recovery path can be tested.
- **Earn the AI label.** Distinguish actual search/retrieval methods and measured test coverage from future embedding/vector-search plans.
- **Product quality includes restraint.** Showing the exact command, explaining a rejected action, or preserving a merchant's data is a feature.

## What this portfolio does not claim

Hermes is an upstream open-source project I contribute to, **not a codebase I authored**. The two referenced upstream fixes are **unmerged** as of this review. The jewelry visual-search PR does **not** implement FashionCLIP/pgvector/RF-DETR. MIST's multi-product rollout is **not complete**, CRM communications/AI use safe stub adapters in the reviewed code, and Sales example data is **not real revenue**. RFID designs are **not** evidence of complete physical-tag detection under occlusion.

Some commercial implementations are private. These notes intentionally exclude credentials, employer/customer data, confidential configuration, and proprietary source code. Public links and clearly marked test reports are supplied when available.

## Other projects

- [TownHall](https://github.com/sd2389/TownHall)
- [Product Intelligence — simulated-data prototype](https://github.com/sd2389/product-intelligence)
- [GitHub profile](https://github.com/sd2389)

For technical discussion or collaboration, reach me on [LinkedIn](https://www.linkedin.com/in/smssmit/).
