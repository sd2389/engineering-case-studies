# Independent SaaS products: CRM, Sales, omnichannel inbox, and commerce adapters

**Status:** Multiple product slices implemented and tested; **some integrations are demo-only, stubbed, or behind cutover gates**.  
**Role:** Product contracts, service boundaries, authorization, and operational workflows.  
**Stack:** Django/DRF, FastAPI, Next.js/React, PostgreSQL, Redis, OAuth, queues.

## Problem

It is tempting to call everything a single "ERP platform." That hides different owners for customer relationships, inventory, sales intelligence, and conversations—and creates unsafe dependencies when one product's access rules are implicitly reused by another.

## Four separate boundaries

### CRM: relationship workflow

A standalone CRM owns leads, companies, contacts, opportunities, audit history, saved views, and custom fields. It has CSV import, permissions, API keys, webhook foundations, and a staged Keycloak integration. Communications and AI provider adapters are present, but **fake/no-op providers** are the safe default until real services and permissions are proven.

**Decision:** Authorization must combine the platform's tenant/app decision with the CRM's page and record-level rights. A coded adapter is not an enabled production rollout.

### Sales: explainable stock and reorder decisions

The Sales Dashboard separates sales intelligence from CRM, using tenant-scoped data and backend-defined reorder rules. Recommendation logic includes explainable scores and hold conditions, while operators can override quantities.

**Decision:** The application explicitly marks example-seed data. Example sales rows are **not** presented as live revenue or production purchasing guidance. Authentication and ingestion/cutover require separate verification.

### Unify: multi-tenant conversation inbox

Unify has organization workspaces, admin/staff assignment boundaries, a FastAPI API, Postgres-backed records, JWT cookie auth, and realtime UI transport. Its first messaging ingest slice is based on **webhook-token stubs**; there is no implication that all advertised channels are connected to production accounts.

**Decision:** Assignment filtering belongs on the server, not just in the inbox UI.

### Channels: storefront sync without taking over the merchant's catalog

The commerce-channel adapter uses an interface between core sync and Shopify-specific logic. A connected merchant retains ownership of their titles, media, and merchandising. Live stock is reconciled separately from optionally managed product content.

**Decision:** Product-content changes are opt-in. Sync is constrained to mapped inventory units and channel-owned data; it must not overwrite unrelated merchant-managed variants.

## Integration strategy and limits

These products should share identity and application access contracts through MIST while preserving distinct data ownership. A roadmap diagram is not evidence of production integration. The case studies intentionally separate tested local behavior, demo seeds, adapter stubs, and rollout gates.

## Evidence

- [Public Channels implementation](https://github.com/sd2389/devjewels-channels) (other product implementations are maintained privately)

**Lesson:** Modularity is real when teams can state *which product owns a record, which credential authorizes it, and what happens when an upstream system is unavailable*.
