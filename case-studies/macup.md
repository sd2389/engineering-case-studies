# MacUp: trustworthy macOS developer-environment maintenance

**Status:** Public open-source app and CLI; [v0.4.0 published as a prerelease](https://github.com/sd2389/macup/releases/tag/v0.4.0).  
**Role:** Product and implementation.  
**Stack:** Swift 6, native macOS UI, shared core library, launchd, Homebrew, npm, mise.

## Problem

A developer's Mac can have updates scattered across Homebrew, global npm tools, runtime managers, and macOS itself. A broad `upgrade` command hides what will change, and uninstallers often leave local state behind.

## Design

[MacUp](https://github.com/sd2389/macup) brings several update sources into a **single review-and-approve workflow**. A native app and CLI use the same core engine.

- **Plan before execution:** show the executable, arguments, affected versions, policy decision, and risk rationale before a command runs.
- **Respect user intent at execution time:** check rules again immediately before a change, rather than trusting a potentially stale UI decision.
- **Least privilege:** never run MacUp as root; present separately reviewable instructions for privileged cleanup rather than executing them behind the user's back.
- **Local-first:** no account, cloud dependency, telemetry, or persistent privileged daemon.
- **Reversible cleanup:** move leftover files to Trash by default, and keep user data unselected unless explicitly chosen.
- **Diagnosis instead of mystery repairs:** explain conflicting Homebrew installs, environment-path mismatches, and runtime-manager drift with deterministic checks.

## Operational details

MacUp includes update history, policy controls, scheduled checks using a user launchd agent, menu-bar status, and redacted diagnostics. The UI and `macup` CLI share the same `MacUpCore` rules so policy decisions do not diverge between two frontends.

## Tradeoffs and limits

A cautious workflow involves more confirmation than a one-shot upgrade, deliberately. Scheduling is opt-in and bounded by policy. At the time of the reviewed README, the app is **not Apple-notarized**; a published prerelease is not the same as a broadly validated stable release.

## Evidence

- [Source code and screenshots](https://github.com/sd2389/macup)
- [v0.4.0 prerelease](https://github.com/sd2389/macup/releases/tag/v0.4.0)

**Lesson:** A developer tool earns trust by making changes legible, scoped, and recoverable.
