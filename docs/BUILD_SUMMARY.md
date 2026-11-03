# Phase 1 CLI build summary

This file summarizes the completed Python CLI implementation on `main`. It is
not a summary of the complete TallyDan web application. For the current
full-stack project, see the [repository overview](../README.md),
[Django backend](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend),
and [frontend](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend).

## Implemented

- Complete interactive CLI expense, budget, report, settings, CSV import, and
  CSV export workflows.
- Exact two-decimal `Decimal` money, stable expense UUIDs, business dates, and
  UTC creation timestamps.
- One persisted application currency, initially `USD`, with safe change rules.
- Version 2 JSON documents with legacy reading and controlled upgrade on save.
- Atomic replacement, previous-valid backups, validated recovery, and explicit
  persistence errors.
- Persistence-neutral repository protocols and service injection.
- Central composition, environment configuration, and deterministic clock and
  UUID providers.
- Automated lint, format, syntax, coverage, integration, subprocess, storage,
  and multi-version CI gates.

## Phase 1 milestone status

All seven Phase 1 milestones are complete. The `v1.0.0` release passed
clean-install, dependency, quality, acceptance, repository-hygiene, and final
pull-request checks.

The authoritative state is
[PHASE_1_UPGRADE_ROADMAP.md](PHASE_1_UPGRADE_ROADMAP.md).

## Explicitly not implemented

- Django or Django REST Framework
- an HTTP API
- PostgreSQL or another database adapter
- authentication, authorization, or multiple users
- AI/ML features

Those capabilities were outside the scope of the Phase 1 CLI release. They
were subsequently implemented as a separate Django backend and React frontend
in the branches linked above. The migration-readiness document records the
original planning context rather than the current implementation status.

Release evidence and known limitations are recorded in
[PHASE_1_RELEASE_REPORT.md](PHASE_1_RELEASE_REPORT.md). The completed gate is
listed in [COMPLETION_CHECKLIST.md](COMPLETION_CHECKLIST.md).
