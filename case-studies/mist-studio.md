# MIST Studio: 3D jewelry preparation in a browser

**Status:** Public implementation repository with local Docker workflow and test infrastructure; not a claim that every optional service or paid feature is live.  
**Role:** Product and rendering-system engineering.  
**Stack:** Next.js, React Three Fiber, Three.js/WebGPU, FastAPI, PostgreSQL, Docker, R2/S3.

## Problem

Jewelry rendering normally spans CAD tools, material setup, studio lighting, image exports, and storefront-ready visuals. The goal was to reduce that handoff into a browser workflow without flattening the product into a static image.

## System

[MIST Studio](https://github.com/sd2389/studio.mist.com) imports CAD/mesh formats, converts units, finds material slots, lets the operator dress metals and stones, and saves scene configuration for both media exports and an embeddable viewer.

The public README documents:
- **10 supported input formats**, including Rhino 3DM, STEP, IGES, GLB, STL, and other meshes.
- **22 stone cuts**, **74 gem materials**, and **21 metal options**, with a WebGPU rendering path and WebGL 2 fallback.
- Stills, turntables, and combined media packs; a configurable storefront viewer that reads the saved scene.
- A parametric ring-design workflow exporting STL, OBJ, and GLB.

## Decisions

- **Browser-first preview:** accelerate iteration without requiring a local desktop render workstation for every edit.
- **Separate scene data from presentation:** a product viewer should reproduce the saved materials, lights, backdrop, and camera intent rather than hard-code them in each storefront.
- **Graceful graphics fallback:** WebGPU for advanced effects when supported; WebGL 2 for wider browser reach.
- **Split responsibilities:** browser handles interactive rendering; the FastAPI service owns accounts, persistence, and stored assets.
- **Reproducible local environment:** Docker Compose, migrations, and dedicated testing for rendering and API behavior.

## Honest limitations

Rendering quality still depends on supported GPUs, geometry quality, lighting/material calibration, and output settings. AI backgrounds and on-model imagery are identified as beta; optional billing and third-party integrations require configuration. This write-up does not report production traffic or rendering benchmarks that have not been measured.

## Evidence

- [Public source, screenshots, and documentation](https://github.com/sd2389/studio.mist.com)

**Lesson:** A visual tool is an engineering system when scene state, output reproducibility, and downstream embeds are part of the contract.
