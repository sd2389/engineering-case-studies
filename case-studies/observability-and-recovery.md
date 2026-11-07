# Self-hosted observability: prove recovery, don't just collect dashboards

**Status:** Self-hosted monitoring and recovery workflows described in the existing portfolio; counts below represent the documented setup, not a fresh independent production audit.  
**Role:** Operations architecture, incident visibility, backup verification.  
**Stack:** Prometheus, Grafana, Alertmanager, Loki, Alloy, Docker, AWS CloudWatch, Tailscale, Cloudflare Access, restic.

## Problem

An office server, an agent VPS, and cloud applications create many disconnected sources of health and failure. Publishing monitoring dashboards directly on the internet would solve visibility by creating a new attack surface.

## Architecture

A containerized collector stack exposes host, application, log, and synthetic-probe signals. Prometheus records metrics, Loki retains selected server logs, Grafana displays dashboards, Alertmanager routes notifications, and CloudWatch data is collected through restricted AWS access. Private links carry host-to-host data, and Grafana access is gated.

The previously documented configuration included **15 dashboards, 76 alert rules, and 64 recording rules**, along with bounded log and metric retention. These are configuration counts, **not availability or incident-response performance claims**.

## Decisions that mattered

- **Private-by-default:** core exporters and monitoring APIs stay bound locally; use controlled remote access instead of opening monitoring endpoints.
- **Least privilege:** read-only AWS access and a read-only Docker socket proxy limit the collector's authority.
- **Alert quality over count:** idle blue/green backends should not create false production-outage alarms.
- **Backups should be exercised:** periodic restore tests target disposable containers and check database content, rather than simply seeing that a backup file exists.
- **Observable schedules:** systemd timers make backup, restore, security, and load-check jobs restartable and inspectable.

## Failures the system helped surface

- A monitoring-adjacent admin account missing MFA.
- An alert-rule mismatch around intentionally idle infrastructure.
- Certificate-renewal connectivity trouble surfaced by a dry run.

## Outstanding limitations

At the time of the earlier write-up, tracing infrastructure was deployed but services were not emitting traces, some cloud logs had not been moved into Loki, and a full offsite restore from object storage had not been verified.

**Lesson:** Monitoring is useful when it shortens detection and makes restoration testable—not when it merely increases the number of graphs.
