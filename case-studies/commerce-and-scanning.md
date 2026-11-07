# DevJewels: transactional correctness across commerce, media, and physical inventory

**Status:** Implemented commerce and admin workflows with subsequent merged fixes; **RFID fleet ingestion remains an active design track**, not a proven production rollout.  
**Role:** System design, product engineering, operational workflows.  
**Stack:** Django, FastAPI, Next.js, TypeScript, PostgreSQL, Redis, Cloudflare R2.

## Problem

Wholesale jewelry changes hands through stock, memo/consignment, sales, and customer orders. A visually convincing storefront is dangerous if it can show a stale piece as available, accept a browser-calculated price, or mark the same unique job twice.

## Foundational decisions

- Keep the **server authoritative** for price, availability, and checkout; preserve price snapshots with orders.
- Route admin operations and high-traffic APIs through Django and FastAPI while sharing the same business rules rather than implementing different rule sets in each layer.
- Normalize supplier feeds before the product catalog sees them.
- Use typed identifiers for physical sellable units, and reject duplicate or already-sold tag scans.
- Limit B2B API access using permission-scoped, hashed credentials and other access controls.

The original system documentation describes **4,000+ designs** and a **127+ route** storefront; those are historical implementation counts, not independently measured active customers or revenue.

## More recent engineering work

### Cross-browser tag scanning

A merged camera-scanning change (private frontend PR #15) chooses native barcode decoding only when the browser supports the relevant formats; otherwise it uses ZXing. It also handles empty native reads, camera constraint failures, and user-facing permission errors without breaking manual or hardware scan paths.

**Tradeoff:** Capability detection is more reliable than a browser-name switch, but true device/browser field validation remains necessary.

### Large mobile photos

Merged direct-to-object-storage upload work (private frontend PR #40) avoids routing large mobile files through a small reverse-proxy body limit: request an upload URL, send the bytes directly to object storage, then save only the object reference and related metadata. The server still validates the resulting record.

**Tradeoff:** Direct uploads reduce application-server transfer costs, but require careful short-lived upload authorization and cleanup of orphaned assets.

### RFID architecture (in progress)

The ongoing goal is to support Android handhelds, scanner guns, tray readers, and doorway/portal readers without assuming every manufacturer exposes the same device interface. Browser keyboard-emulation/HID scanning is attractive for the first supported devices. Burst handling, per-item deduplication, reliable counts, and tough physical conditions (occluded or overlapping tags) require verification before making all-stock coverage guarantees.

This is a **design and integration initiative**, not a claim that every tag in a tightly packed bundle can already be read.

## What the work demonstrates

The central challenge is preserving correctness while connecting browsers, scanners, back-office workflows, and inventory state. Fast scanning is only useful if the resulting inventory transactions are safe.

**Lesson:** The real inventory API contract is not "scan a code"; it is "prove which unique physical unit may change state."
