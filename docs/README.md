# Phase 1 CLI technical documentation

This directory primarily documents the original Python CLI implementation
preserved on `main`. It is not the complete technical documentation set for
the deployed TallyDan application. For the current full-stack project, see the
[repository overview](../README.md), the
[Django backend branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend),
and the
[frontend branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend).

## Phase 1 CLI system

- [Architecture](ARCHITECTURE.md)
- [Architecture decisions](ARCHITECTURE_DECISIONS.md)
- [Application composition and configuration](APPLICATION_COMPOSITION.md)
- [Repository and service boundaries](REPOSITORY_CONTRACTS.md)
- [Data schema](DATA_SCHEMA.md)
- [Persistence reliability](PERSISTENCE_RELIABILITY.md)
- [Quality automation](QUALITY_AUTOMATION.md)

## Phase 1 status

- [Upgrade roadmap](PHASE_1_UPGRADE_ROADMAP.md)
- [Data-foundation acceptance](PHASE_1_ACCEPTANCE.md)
- [Current build summary](BUILD_SUMMARY.md)
- [Release completion checklist](COMPLETION_CHECKLIST.md)
- [Release-candidate verification report](PHASE_1_RELEASE_REPORT.md)

## Django handoff record

- [Django migration readiness](DJANGO_MIGRATION_READINESS.md)

This document records the design and data-migration considerations prepared
before the Django implementation. It remains a historical design reference;
the backend is now implemented in the separate `django-backend` branch and
used by the React frontend. The `main` branch still contains the standalone
single-user CLI from Phase 1.

The root [documentation index](../DOCUMENTATION_INDEX.md) also maps setup,
navigation, and repository-structure documents.
