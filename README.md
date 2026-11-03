# TallyDan — Expense Tracking & Financial Management

TallyDan is a full-stack expense-tracking application that evolved from a local Python command-line project into a deployed web application.

This repository documents the project's development across three stages:

1. **Expense Tracker CLI** — a local Python application for expense and budget management.
2. **Django REST API** — a backend that provides authentication, protected expense operations, and financial summaries.
3. **TallyDan Full Stack** — a React frontend integrated with the Django REST API and deployed as a web application.

The branches preserve these stages and their development histories. Start with the full-stack branch if you want to explore the deployed product.

## Live application

- **TallyDan web application:** https://tallydan-expense-tracker.vercel.app
- **Backend service:** https://expense-tracker-api-n5s7.onrender.com

The frontend is deployed on Vercel and the Django backend is hosted on Render. Availability may depend on the hosting providers and deployment configuration.

## Project evolution and source code

| Stage | Branch | What it contains |
|---|---|---|
| Expense Tracker CLI | [`main`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main) | Original Python CLI, expense and budget workflows, reporting, CSV import/export, JSON persistence, validation, and automated quality checks. |
| Django backend API | [`django-backend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend) | Django and Django REST Framework API, JWT authentication, protected expense operations, ownership permissions, and monthly summaries. |
| TallyDan frontend / full-stack presentation | [`frontend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend) | React and Vite frontend, API integration, application screenshots, architecture overview, and deployment information. |

**Note:** These are separate branches in one repository, not three folders on the default branch. Switch to the relevant branch to inspect its files. The `main` branch remains the CLI project's default branch.

## TallyDan — full-stack application

TallyDan brings together a browser-based frontend and a Django REST backend to provide expense-management functionality.

### Frontend

- React
- Vite
- Responsive web interface
- Frontend deployment on Vercel

### Backend

- Python 3.13
- Django 5.2
- Django REST Framework (DRF)
- SimpleJWT for access and refresh token authentication
- PostgreSQL for production database configuration
- SQLite for local development
- `python-decouple` for environment-based configuration

### Application capabilities

- User registration and authentication
- JWT access and refresh token workflow
- Expense creation, retrieval, updating, and deletion
- Authenticated, user-specific expense access
- Ownership-based permission checks for protected expense operations
- Monthly expense summaries grouped by category
- Frontend-to-backend communication through REST API requests

## Architecture

The full-stack application follows a decoupled client-server architecture:

```text
       User
        |
        v
+---------------------+
| React + Vite        |
| Frontend            |
| Deployed on Vercel  |
+---------------------+
        |
        | HTTPS / REST API
        | JSON + JWT
        v
+---------------------+
| Django REST         |
| Framework API       |
| Hosted on Render    |
+---------------------+
        |
        v
+---------------------+
| PostgreSQL          |
| Production database |
+---------------------+
```

The frontend is responsible for the user interface and consuming the API. The backend handles authentication, authorization, application logic, and database operations.

## Backend API overview

The backend includes endpoints for:

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/auth/register/` | Register a user |
| POST | `/api/auth/login/` | Authenticate and obtain JWT tokens |
| POST | `/api/auth/token/refresh/` | Refresh an access token |
| GET | `/api/expenses/` | Retrieve the authenticated user's expenses |
| POST | `/api/expenses/` | Create an expense for the authenticated user |
| PUT | `/api/expenses/{id}/` | Update an authorized expense |
| DELETE | `/api/expenses/{id}/` | Delete an authorized expense |
| GET | `/api/expenses/summary/` | Retrieve monthly category totals |

The API was manually exercised with Postman during development. Refer to the backend branch for its implementation details.

## Original CLI application

The first version was a local command-line expense tracker written in Python. Its capabilities include:

- Add, edit, duplicate, search, filter, sort, inspect, and delete expenses.
- Set category budgets and review monthly budget status.
- Generate monthly, statistical, category, and top-category reports.
- Import and export currency-aware CSV data.
- Persist data in versioned JSON documents.
- Validate data and use backup/recovery safeguards.
- Separate application services, repository contracts, and terminal presentation.

See the [CLI source on `main`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main) for its documentation, tests, and implementation.

## Screenshots

The full-stack branch contains the current application preview images and additional project documentation:

[View TallyDan screenshots and documentation](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend)

## Running locally

The CLI, backend, and frontend have different dependencies and setup procedures. Follow the instructions in the corresponding branch rather than assuming that the root of `main` contains the full-stack application.

- [CLI setup and development checks](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main)
- [Backend implementation](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend)
- [Frontend and full-stack setup](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend)

For deployment, configure secrets and environment-specific settings in the hosting providers. Do not commit `.env` files, credentials, secret keys, or real user data.

## What this project represents

TallyDan records a practical progression through software development:

- Building application logic and persistence in Python.
- Structuring a backend with Django and REST APIs.
- Implementing authentication and user-level access control.
- Connecting a frontend to a backend service.
- Configuring and deploying a full-stack application.

The project is part of my broader development in software engineering, data science, and AI/ML. It provides a software-engineering foundation for future data-driven and intelligent application work.

## Repository navigation

- [CLI — `main`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main)
- [Django backend — `django-backend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend)
- [TallyDan frontend — `frontend`](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend)
- [Live application](https://tallydan-expense-tracker.vercel.app)

---
