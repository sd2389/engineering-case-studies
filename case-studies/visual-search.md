# Jewelry visual search: trustworthy matching before ambitious model claims

**Status:** Feature-gated product slice and tested change proposal; **not a production-grade vector-search system**.  
**Role:** Search workflow, correctness boundaries, and evaluation-oriented engineering.  
**Stack:** Python/FastAPI, image preprocessing, perceptual matching, query filters, multi-tenant API controls.

## Problem

A jewelry search experience must translate a customer's picture into relevant, explainable product matches. Ambiguous objects, look-alike materials, wrong categories, malformed uploads, and tenant boundaries can make a visually attractive demo misleading.

## Pipeline and design choices

`Image upload → decode / crop → candidate match → category + metal filtering → rank → explain results`

- Use a region crop when available so background noise does not dominate a query.
- Treat matching and category filtering as separate responsibilities.
- Make filters strict: requesting **gold rings AND silver bracelets** should not silently turn into an OR query.
- Guard category parsing so `ring` does not match the substring inside `earrings`.
- Reject undecodable images safely rather than returning invented matches.
- Ensure tenant-scoped catalog access and explicit unauthorized responses.

## Proof and current limits

The [visual-search PR](https://github.com/sd2389/Mist-AI/pull/1) documents **59 passing targeted tests** on its stated head, including invalid-image handling, query filtering, authentication, and tenant isolation.

Importantly, that PR is **open and not merged**. Its matching implementation is crop/perceptual-and-token based, with an O(n) candidate scan and a full-frame fallback. It does **not** implement FashionCLIP embeddings, pgvector ANN search, or an RF-DETR detector. Those are future architecture options, not shipped features.

## Tradeoffs

A simpler deterministic pipeline is easier to test and debug than claiming an opaque semantic model is universally accurate. However, it needs proper retrieval infrastructure and image-model evaluation before it can scale to larger catalogs or reliably distinguish multiple overlapping objects.

## Evidence

- Code review and test record: [Mist AI visual-search PR #1](https://github.com/sd2389/Mist-AI/pull/1) (implementation repository may not be publicly accessible)

**Lesson:** Make the result honest, safe, and testable before calling it intelligent.
