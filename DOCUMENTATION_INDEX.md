# Documentation index

This index maps the documentation for the original Phase 1 Python CLI. These
files remain useful as historical implementation records and as the design
background for TallyDan, but they do not describe the complete current
full-stack application. The repository-level [README](README.md) is the entry
point for the overall project and links to the current backend and frontend
branches. If two Phase 1 documents conflict, the narrower technical contract
governs CLI behavior and the Phase 1 roadmap governs that milestone's status.

## Start here

| Document | Purpose |
| --- | --- |
| [README.md](README.md) | Project purpose, capabilities, setup, and status |
| [QUICK_START.md](QUICK_START.md) | Safe manual start and automated verification |
| [PROJECT_INDEX.md](PROJECT_INDEX.md) | Code areas, menu map, test map, and runtime artifacts |
| [STRUCTURE.md](STRUCTURE.md) | Source tree, ownership, and dependency direction |
| [SETUP_CHECKLIST.md](SETUP_CHECKLIST.md) | Environment and contributor readiness checklist |

## Architecture and contracts

| Document | Authority |
| --- | --- |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Current component boundaries and runtime flows |
| [docs/ARCHITECTURE_DECISIONS.md](docs/ARCHITECTURE_DECISIONS.md) | Recorded Phase 1 design decisions and consequences |
| [docs/DATA_SCHEMA.md](docs/DATA_SCHEMA.md) | Version 2 JSON and legacy migration contract |
| [docs/PERSISTENCE_RELIABILITY.md](docs/PERSISTENCE_RELIABILITY.md) | Atomic save, backup, recovery, and failure behavior |
| [docs/REPOSITORY_CONTRACTS.md](docs/REPOSITORY_CONTRACTS.md) | Repository protocols and service boundary |
| [docs/APPLICATION_COMPOSITION.md](docs/APPLICATION_COMPOSITION.md) | Composition root, configuration, clock, and UUID providers |

## Quality and release progress

| Document | Purpose |
| --- | --- |
| [docs/QUALITY_AUTOMATION.md](docs/QUALITY_AUTOMATION.md) | Local checks and GitHub Actions gates |
| [docs/PHASE_1_UPGRADE_ROADMAP.md](docs/PHASE_1_UPGRADE_ROADMAP.md) | Authoritative milestone map |
| [docs/PHASE_1_ACCEPTANCE.md](docs/PHASE_1_ACCEPTANCE.md) | Data-foundation acceptance evidence |
| [docs/COMPLETION_CHECKLIST.md](docs/COMPLETION_CHECKLIST.md) | Remaining Phase 1 release-gate checklist |
| [docs/BUILD_SUMMARY.md](docs/BUILD_SUMMARY.md) | Concise current implementation summary |
| [docs/PHASE_1_RELEASE_REPORT.md](docs/PHASE_1_RELEASE_REPORT.md) | Verified release-candidate evidence, limitations, and remaining publication work |

## Django handoff and current full-stack implementation

[docs/DJANGO_MIGRATION_READINESS.md](docs/DJANGO_MIGRATION_READINESS.md)
records the design considerations prepared during the CLI phase. The planned
Django direction has since progressed into a separate implementation branch:
[`django-backend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend).
The React application and its integration documentation are maintained in
[`frontend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend).
The Phase 1 handoff document remains valuable for understanding the original
data model and migration risks, but it is no longer a statement that Django
work is still pending.

## Documentation maintenance rule

Every behavior change must update its narrow contract document, any affected
setup or menu guidance, and the roadmap status in the same pull request. Avoid
fixed test-count claims because the suite grows; the CI result is the source of
truth. Historical reports belong in Git history rather than active files that
claim an unfinished phase is complete.
