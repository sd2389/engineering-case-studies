# Engineering Case Studies

Sanitized notes on systems I have built or helped build. The focus is on engineering decisions: boundaries, data flow, reliability, security, and the tradeoffs behind the implementation.

This repository intentionally contains no employer source code, credentials, customer data, internal schemas, or deployment secrets.

## 1. B2B Jewelry Commerce Platform

**Problem**

A legacy ERP and storefront needed to support live wholesale inventory, customer-specific pricing, supplier data, and ordering without allowing the browser to become the source of truth for price or availability.

**System**

- Hybrid Django + FastAPI backend on AWS EC2, with nginx routing admin and storefront traffic
- Next.js 16 and TypeScript storefront spanning 127+ routes
- PostgreSQL as the transactional source of truth
- Redis for live supplier inventory caching and fast visibility controls
- Shared service and data layers across 24 Django apps so Django and FastAPI do not duplicate business rules

**Decisions that mattered**

- Kept checkout server-authoritative and stored pricing snapshots with each order.
- Modeled a four-tier pricing hierarchy instead of scattering price calculations across UI components.
- Normalized supplier payloads into a canonical diamond model before exposing inventory.
- Used hashed API keys, scoped permissions, and IP allowlists for B2B integrations.
- Put feature flags around navigation and releases so incomplete workflows stayed isolated.

**Scale represented in the system**

4,000+ jewelry designs, live stock and memo inventory, multi-supplier diamond feeds, and external customer API access.

## 2. AI Agent Orchestration Platform

**Problem**

A collection of specialist agents needed clear ownership, durable context, model failover, and human control over consequential actions.

**System**

- 13 specialist agents with isolated memory and procedural learning
- Dispatcher that routes incoming work to the best-fit agent
- LLM routing across 20+ providers with automatic failover
- Messaging gateway spanning 15+ platforms, including Telegram, Discord, Slack, and WhatsApp
- Docker deployment with Sentry-based incident intake

**Safety boundary**

Sentry events can trigger diagnosis and produce a proposed GitHub fix, but a human approval step is required before code ships. The system is designed to automate investigation and preparation without silently turning suggestions into production changes.

## 3. Federated ERP / CRM Platform

**Problem**

ERP, CRM, HR, and support workflows needed a unified employee experience without merging every application into one database or duplicating business data.

**System**

- Frappe Bench deployment with ERPNext, CRM, HRMS, and Helpdesk on separate sites
- Shared PostgreSQL infrastructure and Redis queues
- Keycloak OIDC single sign-on
- Federation Hub for unified navigation, role mapping, provisioning, and cross-site metrics
- Docker Compose and automation scripts for repeatable startup, migrations, and routing

**Tradeoff**

The platform centralizes identity and navigation while letting each application retain ownership of its domain data. This adds federation work, but it avoids a tightly coupled database shared by unrelated products.

## 4. Self-Hosted Observability Platform

**Problem**

A small production footprint spread across an office-hosted server, a separate VPS for the agent runtime, and AWS EC2 for the commerce backend needed one place to see health, logs, and alerts, without exposing monitoring endpoints to the public internet.

**System**

- Docker Compose stack on an office-hosted Ubuntu VM: Prometheus, Alertmanager, Grafana, Loki, Alloy, blackbox exporter, node_exporter, cAdvisor, YACE, a custom read-only AWS inventory exporter, cloudflared, and a read-only Docker socket proxy (13 containers once Tempo was added)
- Host and container metrics from node_exporter and cAdvisor; blackbox probes for websites, APIs, reachability, response time, and certificate expiry
- AWS CloudWatch metrics through YACE with read-only access; Django, FastAPI, and Celery metrics reach Prometheus over Tailscale
- Loki and Alloy collect server-side logs (Mattermost, nginx, selected systemd journal units, fail2ban) with 30-day retention and bounded storage; Prometheus keeps 15 days or 12 GB
- Alertmanager routes alerts to a Mattermost monitoring channel, incident tickets, and ntfy phone notifications
- Self-hosted Mattermost (customized build from a GitHub Container Registry image, PostgreSQL, nginx with TLS), with nightly restic snapshots and offsite copies to Cloudflare R2

**Decisions that mattered**

- Published Grafana only through Cloudflare Tunnel and Cloudflare Access with an identity-restricted one-time-PIN policy; core monitoring endpoints stay bound locally, and Tailscale carries private host-to-host traffic.
- Gave the log collector Docker access only through a read-only socket proxy, and used read-only AWS access for CloudWatch and inventory metrics.
- Ran backups, security checks, restore tests, and scripted k6 checks as systemd timers, so scheduled work is visible and restartable.
- Tested backups by restoring into disposable containers and checking database row counts, rather than trusting that snapshots exist.

**Scale represented in the system**

15 provisioned Grafana dashboards, 76 alert rules, 64 recording rules, and checks covering backups, disk capacity, security services, administrator MFA, database storage, and domain and certificate expiry.

**What it caught**

- A Mattermost administrator account without MFA.
- False alarms from an intentionally idle blue/green backend, which led to correcting the alert rules.
- A certificate-renewal connectivity problem, found during a dry run.

**Not done yet**

- Tempo is deployed, but no service emits traces yet.
- AWS backend logs still live in CloudWatch rather than Loki.
- An offsite disaster-recovery restore from R2 has not been tested.

## Engineering principles

- Put business rules behind stable service boundaries.
- Treat the server and database as authoritative for transactional state.
- Design access control before exposing an integration.
- Prefer observable, recoverable background work over hidden automation.
- Keep operational shortcuts from becoming permanent architecture.

## Related repositories

- [GitHub profile](https://github.com/sd2389)
- [TownHall](https://github.com/sd2389/TownHall)
- [Product Intelligence](https://github.com/sd2389/product-intelligence)
