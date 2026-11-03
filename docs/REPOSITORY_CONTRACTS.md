# Phase 1 CLI repository and service boundaries

Milestone 3 of the original CLI roadmap separated application behavior from
JSON persistence and terminal presentation. Services receive repository
dependencies that satisfy typed `Protocol` contracts; they do not import or
construct `JSONStorage`. This document describes the CLI architecture, not the
separate Django backend's ORM and API boundaries.

## Dependency direction

```text
CLI presentation
      |
      v
Application services --> repository protocols <-- JSONStorage
      |
      v
Domain model and validation
```

The CLI receives fully constructed services. The configuration-aware
composition root in `src/application.py` constructs the concrete repository,
services, commands, and menu without changing service behavior.

## Repository contracts

`src/repositories/contracts.py` defines three independent protocols:

- `ExpenseRepository`: `load_expenses()` and `save_expenses(expenses)`.
- `BudgetRepository`: `load_budgets()` and `save_budgets(budgets)`.
- `SettingsRepository`: `load_settings()` and `save_settings(settings)`.

`JSONStorage` implements all three protocols structurally. Services depend only
on the protocols and `RepositoryError`. This created a useful persistence
abstraction in the CLI, but it should not be read as a claim that the current
Django backend directly injects this same adapter or reuses every CLI service.

The reusable assertions in `tests/repository_contracts.py` define the behavior
expected from adapters to these CLI protocols. `tests/test_json_repository_contract.py`
applies those assertions to `JSONStorage`. The current Django backend is a
separate implementation; its own model, serializer, view, and API tests are
the authority for its behavior.

## Service constructor contract

- `ExpenseTrackerService(expense_repository, settings_repository)`
- `BudgetService(budget_repository, settings_repository)`
- `SettingsService(settings_repository, expense_repository, budget_repository)`

Services never accept a data-directory path. Path selection and implementation
choice belong to application composition, not business logic.

## Presentation-neutral results

Mutation and export services return domain objects or immutable result values:

- expense creation and duplication return `Expense`;
- budget updates return `BudgetUpdateResult`;
- currency updates return `CurrencyUpdateResult`;
- imports return `ExpenseImportResult` with typed row errors;
- exports return `ExportResult`.

These values contain no checkmarks, crosses, or terminal-ready success
sentences. The CLI owns those messages and the formatter owns import-summary
layout.

## Exception boundaries

The shared hierarchy in `src/exceptions.py` distinguishes:

- domain validation errors;
- application rule failures such as blocked currency changes;
- export and CSV import failures.

Repository failures derive from `RepositoryError`. JSON-specific failures add
the more precise persistence exceptions documented in
`PERSISTENCE_RELIABILITY.md`. The CLI translates expected exceptions into user
messages; unexpected programming errors remain visible to the entry point.
