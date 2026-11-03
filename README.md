# TallyDan — Django REST API

The Django backend for **TallyDan**, a full-stack expense tracking and financial management application.

This branch contains the Django REST Framework API used by the React frontend. It provides account registration and authentication, user-scoped expense and budget management, financial summaries, statistics, and CSV import/export.

- **Live frontend:** https://tallydan-expense-tracker.vercel.app
- **Live backend:** https://expense-tracker-api-n5s7.onrender.com
- **Repository:** https://github.com/danielabdouroihamane9-gif/TallyDan
- **Frontend source:** [`frontend` branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend)
- **Original Python CLI:** [`main` branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main)

The frontend is deployed on Vercel and the backend is hosted on Render. The API base path is `/api/v1/`.

---

## Contents

- [Technology stack](#technology-stack)
- [Backend capabilities](#backend-capabilities)
- [Architecture](#architecture)
- [API reference](#api-reference)
- [Data model](#data-model)
- [Run locally](#run-locally)
- [Environment configuration](#environment-configuration)
- [Tests and CI](#tests-and-ci)
- [Deployment notes](#deployment-notes)
- [Repository structure](#repository-structure)
- [Project evolution](#project-evolution)

## Technology stack

| Area | Technology |
|---|---|
| Language | Python 3.13 |
| Web framework | Django 5.2 |
| API framework | Django REST Framework |
| Authentication | djangorestframework-simplejwt |
| Filtering | django-filter |
| Database | SQLite by default; PostgreSQL when `DATABASE_URL` is configured |
| Configuration | python-decouple |
| Database URL parsing | dj-database-url |
| PostgreSQL driver | psycopg |
| WSGI server | Gunicorn |
| Static-file serving | WhiteNoise |
| CORS | django-cors-headers |
| Containerization | Docker / Docker Compose |
| Backend testing | Django test framework and GitHub Actions |

Exact dependency versions are recorded in [`backend/Pipfile.lock`](backend/Pipfile.lock).

## Backend capabilities

### Accounts and authentication

- User registration with username, optional email, password confirmation, and optional three-letter currency code.
- Django password hashing and configured password validation.
- JWT access and refresh tokens.
- Authenticated current-user profile endpoint.
- Per-user currency settings.
- Currency changes are blocked while the account has expenses or budgets. No currency conversion is performed.

JWT lifetimes are configured as 15 minutes for access tokens and 7 days for refresh tokens.

### Expense management

- Create, list, retrieve, update, and delete expenses.
- UUID primary keys for expenses.
- Expenses are assigned to the authenticated user; clients cannot select the owner or currency.
- User-scoped querysets prevent users from retrieving another user's expenses.
- Positive decimal amounts stored to two decimal places.
- Category and date filtering, description search, and ordering.
- Page-number pagination with a configured page size of 20.
- Future expense dates are rejected.
- Duplicate an existing expense with a supplied date.
- Monthly category summary and expense statistics.
- CSV export and validated CSV import.

### Budget management

- Create, list, retrieve, update, and delete category budgets.
- Each budget belongs to the authenticated user.
- One budget per category per user.
- Positive decimal budget amounts.
- Category filtering and ordering.
- User-specific budget data.

### Financial summaries

The monthly summary combines the authenticated user's expenses and category budgets. It returns the selected year and month, account currency, total spending, and category-level values including spent amount, budget, remaining amount, and percentage used.

The statistics endpoint returns the number of matching expenses, total and average spending, highest and lowest expenses, and spending totals/counts by category. Existing expense filters are applied to the statistics response.

---

## Architecture

The backend is organized as a Django project with separate apps for account concerns, expenses, and budgets.

```text
backend/
├── accounts/
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   └── urls.py
├── budgets/
│   ├── models.py
│   ├── serializers.py
│   └── views.py
├── expenses/
│   ├── choices.py
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   └── urls.py
├── config/
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
├── manage.py
├── Dockerfile
├── docker-compose.yml
├── Pipfile
└── Pipfile.lock
```

**Request flow:**

```text
React frontend
     |
     | HTTPS / JSON / Bearer access token
     v
Django URL routing
     |
     v
DRF views and viewsets
     |
     +---- serializers (input validation / representation)
     |
     +---- authentication and permissions
     |
     v
Django ORM
     |
     v
SQLite (local default) / PostgreSQL (when configured)
```

The accounts app manages registration, authentication-related endpoints, and user settings. The expenses and budgets apps own their respective models and API operations. The expense summary also reads the authenticated user's budgets to calculate category budget status.

---

## API reference

All endpoints are relative to:

```text
/api/v1/
```

The live API base is:

```text
https://expense-tracker-api-n5s7.onrender.com/api/v1/
```

### Authentication and account endpoints

Base path: `/api/v1/auth/`

| Method | Endpoint | Authentication | Purpose |
|---|---|---|---|
| POST | `register/` | Public | Create an account and its currency settings |
| POST | `token/` | Public | Obtain JWT access and refresh tokens |
| POST | `token/refresh/` | Public | Obtain a new access token from a refresh token |
| GET | `me/` | Required | Retrieve the authenticated user's profile |
| GET | `settings/` | Required | Retrieve the user's currency settings |
| PUT / PATCH | `settings/` | Required | Update the user's currency setting, subject to validation |

Registration accepts `username`, `password`, and `password_confirm`; `email` is optional and `currency` defaults to `USD`.

Example registration request:

```json
{
  "username": "sampleuser",
  "email": "sample@example.com",
  "password": "Use-A-Strong-Password-123!",
  "password_confirm": "Use-A-Strong-Password-123!",
  "currency": "USD"
}
```

Token requests use the configured Django authentication user fields (username and password by default). A successful token response contains `access` and `refresh`.

### Expense endpoints

Base path: `/api/v1/expenses/`

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `expenses/` | List the authenticated user's expenses |
| POST | `expenses/` | Create an expense |
| GET | `expenses/{uuid}/` | Retrieve one of the user's expenses |
| PUT / PATCH | `expenses/{uuid}/` | Update one of the user's expenses |
| DELETE | `expenses/{uuid}/` | Delete one of the user's expenses |
| POST | `expenses/{uuid}/duplicate/` | Duplicate an expense using a supplied `occurred_on` date |
| GET | `expenses/summary/` | Retrieve a monthly category and budget summary |
| GET | `expenses/statistics/` | Retrieve aggregate expense statistics |
| GET | `expenses/export/` | Export matching expenses as CSV |
| POST | `expenses/import/` | Import expenses from an uploaded CSV file |

Supported list filters include:

- `category`
- `occurred_on`, `occurred_on__gte`, `occurred_on__lte`
- `amount__gte`, `amount__lte`
- `search` for description search
- `ordering` for supported ordering fields: `occurred_on`, `amount`, `created_at`, and `category`

The summary accepts optional `year` and `month` query parameters. Supply both together or omit both to use the current month.

Example:

```text
GET /api/v1/expenses/summary/?year=2026&month=10
```

CSV import accepts a multipart file in the form-data field named `file`. It accepts the original CLI columns (`Date,Amount,Category,Description`) and the backend export format. Invalid rows reject the import without creating any expenses. Duplicate rows are skipped.

### Budget endpoints

Base path: `/api/v1/budgets/`

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `budgets/` | List the authenticated user's budgets |
| POST | `budgets/` | Create a category budget |
| GET | `budgets/{id}/` | Retrieve one of the user's budgets |
| PUT / PATCH | `budgets/{id}/` | Update a budget |
| DELETE | `budgets/{id}/` | Delete a budget |

Budget list filtering supports `category`; ordering supports `category` and `amount`.

### Authentication header

For protected endpoints, send the access token as a Bearer token:

```http
Authorization: Bearer <access_token>
```

The API uses page-number pagination for list endpoints. The configured page size is 20; paginated responses contain a `results` collection and pagination metadata.

---

## Data model

The backend uses Django ORM models and relational database constraints.

- **User:** Django's configured authentication user model.
- **UserSettings:** one-to-one relationship with a user; stores the account currency.
- **Expense:** belongs to a user; stores a UUID, date, decimal amount, currency, category, description, and creation timestamp.
- **Budget:** belongs to a user; stores a category and positive decimal amount. A unique constraint prevents more than one budget for the same user/category pair.

Important data rules:

- Expense amounts and budget amounts use `DecimalField` with two decimal places.
- Expense currency is taken from the user's settings, not trusted from client input.
- Expense records are scoped to the authenticated user.
- Currency changes are blocked when expenses or budgets exist; no exchange-rate conversion is performed.
- Expense category, positive amount, and non-empty description constraints are enforced in the model/database layer as configured.

---

## Run locally

The Django project is inside the `backend/` directory on this branch.

### Prerequisites

- Python 3.13
- Pipenv
- Git
- PostgreSQL only if you want to run with a PostgreSQL database; SQLite is used when `DATABASE_URL` is not configured.

### 1. Clone the repository and select the backend branch

```bash
git clone --branch django-backend https://github.com/danielabdouroihamane9-gif/TallyDan.git
cd TallyDan/backend
```

### 2. Create and activate a virtual environment

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

macOS / Linux:

```bash
source .venv/bin/activate
```

### 3. Install the locked dependencies

```bash
python -m pip install --upgrade pip pipenv
pipenv install --dev --deploy
```

### 4. Configure environment variables

The settings module requires `SECRET_KEY`. For local development, set the variables in your shell or in a local environment file that is not committed to Git.

Windows PowerShell example:

```powershell
$env:SECRET_KEY = "replace-with-a-local-development-secret"
$env:DEBUG = "True"
$env:ALLOWED_HOSTS = "localhost,127.0.0.1"
$env:CORS_ALLOWED_ORIGINS = "http://localhost:5173"
```

If `DATABASE_URL` is omitted, the project uses the local SQLite database at `backend/db.sqlite3`.

For PostgreSQL, set `DATABASE_URL` to a valid PostgreSQL connection URL. The repository includes a Docker Compose configuration for a local PostgreSQL service; configure a local `.env` file for the Compose setup and do not commit credentials.

### 5. Apply migrations and start Django

```bash
python manage.py migrate
python manage.py runserver
```

The local API is available at:

```text
http://127.0.0.1:8000/api/v1/
```

Optional: create an administrator account:

```bash
python manage.py createsuperuser
```

The Django admin is available at `/admin/`.

---

## Environment configuration

The settings module reads the following environment variables:

| Variable | Required | Purpose |
|---|---|---|
| `SECRET_KEY` | Yes | Django secret key |
| `DEBUG` | No | Debug mode; defaults to `False` |
| `ALLOWED_HOSTS` | Deployment-specific | Comma-separated allowed hostnames |
| `CORS_ALLOWED_ORIGINS` | Frontend integration | Comma-separated trusted frontend origins |
| `DATABASE_URL` | No | Database connection URL; SQLite is used when omitted |

For deployment, configure these values in the hosting provider's environment settings. Use a strong unique secret key, keep `DEBUG=False`, and restrict allowed hosts and CORS origins to the required domains. Never commit secrets, production database credentials, or real user data.

---

## Tests and CI

The backend has Django automated tests covering account registration, authentication, user settings, expense operations, budget operations, API validation, and CORS configuration.

Run the test suite from the `backend/` directory:

```bash
python manage.py test accounts expenses budgets
```

Run Django's system check:

```bash
python manage.py check
```

Check that model changes have corresponding migrations:

```bash
python manage.py makemigrations --check --dry-run
```

The GitHub Actions workflow in `.github/workflows/django-ci.yml` provisions PostgreSQL 16, installs the locked backend dependencies, runs Django checks and migration checks, collects static files, and runs the automated test suite on Python 3.13.

These are automated backend tests. They do not, by themselves, establish a complete end-to-end test of the deployed React application.

---

## Deployment notes

The current application deployment uses:

- **Backend:** Render — https://expense-tracker-api-n5s7.onrender.com
- **Frontend:** Vercel — https://tallydan-expense-tracker.vercel.app
- **Production database:** PostgreSQL, configured through `DATABASE_URL`

The backend Dockerfile installs the locked Pipenv dependencies, runs database migrations and static-file collection at container startup, and starts Gunicorn. WhiteNoise is configured for static-file serving.

For a new deployment, configure the environment variables in the hosting provider, connect the intended repository branch, and ensure the production database URL and allowed hosts match the deployment. The frontend's API base URL must point to the deployed backend API.

Deployment configuration and hosting-provider settings may change independently of this source code. Refer to the live Render and Vercel project settings for the currently active build and start commands.

---

## Repository structure

```text
backend/
├── accounts/       # Registration, user profile, currency settings
├── budgets/        # Budget model, serializer, and viewset
├── config/         # Django project settings and root URL configuration
├── expenses/       # Expense model, API, summaries, CSV workflows
├── manage.py
├── Dockerfile
├── docker-compose.yml
├── Pipfile
└── Pipfile.lock
```

The repository also retains the original CLI source and documentation on this branch as part of the project's shared history. The current Django implementation is contained in `backend/`.

## Project evolution

TallyDan developed through three connected stages:

1. **Python CLI:** a local expense tracker built around Python application logic, JSON persistence, validation, and testing.
2. **Django REST API:** a relational, authenticated backend with user-scoped expense and budget operations.
3. **Full-stack application:** a React frontend connected to the Django API and deployed as TallyDan.

The original CLI remains documented on the [`main` branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main). The complete React application is documented on the [`frontend` branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/frontend).

This project represents practical work in backend engineering, API design, relational data modeling, authentication, and full-stack integration. These software engineering foundations also support later work on data-driven and AI-enabled applications.

---

## License

See the repository [LICENSE](LICENSE).
