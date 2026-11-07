# MIST: identity and event boundaries for a multi-product SaaS platform

**Status:** Phase-1 control-plane foundations implemented and covered by a documented local end-to-end flow; product convergence is **in progress**.  
**Role:** Platform architecture, tenant/security contracts, testable integration boundaries.  
**Stack:** Django/DRF, PostgreSQL, Keycloak OIDC, Redis, NATS JetStream, Docker.

## Problem

Several products—CRM, Sales, Studio, messaging, and commerce integrations—need shared sign-in and organization controls without sharing unsafe privileges or forcing every product into one database.

## Architectural decisions

- **Control plane vs. product data:** centralize identity, organizations, membership, app enablement, roles, and audit while keeping each product responsible for its own business records.
- **Tenant scope is a requirement, not a UI selection:** product requests must prove token identity, audience, organization membership, enabled app, and relevant permissions.
- **Humans and services are different identities:** Keycloak OIDC for users and service-principal credentials for machine-to-machine work.
- **Revocation matters:** disabling a membership or account should affect access decisions, not just hide navigation.
- **Events cross a durable boundary:** use an outbox worker and NATS JetStream so a database write is not silently coupled to a best-effort publish call.
- **Stable contracts:** a consistent `{data, meta, error}` response shape and request correlation facilitate product adapters and debugging.

## What is demonstrably implemented

The platform's documented Compose E2E flow exercises a real Keycloak login, owner bootstrapping, organization context, app-disabled rejection, tenant-scoped membership disable, a service-principal call, outbox publishing, readiness endpoints, and an authorized vs. denied global disable.

Other merged work includes a plan/entitlement model and snapshot safety checks intended to keep development copies from pointing at production resources.

## What is *not* complete

This is not a claim that the entire MIST ecosystem is production-integrated. The CRM Keycloak adapter has an explicitly controlled rollout; Sales and Studio have separate auth/integration work; cross-product event bridges and final production cutovers remain gated. Longer-horizon agent mesh and autopilot work belong to MIST 2.0, not the current 1.0 stabilization track.

## Evidence and confidentiality

Implementation and E2E documentation are in the product workspace. This public summary deliberately omits tenant identifiers, identity-provider configuration, credentials, endpoints requiring authentication, and internal schemas.

**Lesson:** A shared login screen is easy; proving a request's authority across independently deployed products is the platform problem.
