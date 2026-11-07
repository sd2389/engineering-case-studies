# Federated enterprise tools: one identity, separate domain ownership

**Status:** Earlier system implementation described in the original case-study repository; retained for continuity.  
**Stack:** Frappe/ERPNext, CRM, HRMS, Helpdesk, Keycloak, PostgreSQL, Redis, Docker.

## Problem

Employees need a coherent experience across sales, operations, HR, and support, but giving every application direct write access to one shared domain database creates coupling and makes permissions difficult to audit.

## Design

A federation layer connects separately owned applications through navigation, sign-on, role mapping, and provisioning. Keycloak provides OIDC identity; each application retains its own domain responsibilities.

The underlying system used a Frappe Bench with ERPNext, CRM, HRMS, and Helpdesk sites, supporting services, and repeatable container startup/migration automation.

## Tradeoff

Federation requires extra integration work, error handling, and identity mapping. The payoff is reducing accidental cross-product data coupling. This case study is a historical architectural example, **not a statement that these apps are all part of the current MIST 1.0 production cutover**.

**Lesson:** Share identity and navigation when useful; do not confuse them with shared ownership of every business table.
