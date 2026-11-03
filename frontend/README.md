# TallyDan — Frontend

The React + TypeScript single-page application for **TallyDan**, an expense tracking and personal financial management app.

This frontend communicates with the Django REST Framework API in the [django-backend branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend).

- **Live application:** https://tallydan-expense-tracker.vercel.app
- **Backend API:** https://expense-tracker-api-n5s7.onrender.com
- **Repository:** https://github.com/danielabdouroihamane9-gif/TallyDan
- **Backend documentation:** [Django REST API README](https://github.com/danielabdouroihamane9-gif/TallyDan/blob/django-backend/README.md)
- **Original Python CLI:** [main branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main)

This README describes the frontend source in the frontend/ directory. The backend API and original CLI have their own documentation.

---

## Contents

- [Features](#features)
- [Technology stack](#technology-stack)
- [Application structure](#application-structure)
- [Frontend and API integration](#frontend-and-api-integration)
- [Authentication and session handling](#authentication-and-session-handling)
- [Environment variables](#environment-variables)
- [Run locally](#run-locally)
- [Available scripts](#available-scripts)
- [Testing and code quality](#testing-and-code-quality)
- [Deployment](#deployment)
- [Known behavior and limitations](#known-behavior-and-limitations)
- [Project evolution](#project-evolution)

## Features

### Authentication and account
- Account registration and sign-in using the Django API.
- Authenticated routes for the dashboard and financial-management pages.
- JWT access-token refresh after eligible unauthorized API responses.
- Session persistence for the current browser tab using sessionStorage.
- Account profile and currency preference settings.

### Dashboard
- Select a month and view total spending and transaction count.
- View all-time average expense statistics.
- View remaining budget across categories.
- Visualize recent expenses and category spending.
- View a six-month spending trend derived from monthly summary API responses.

### Expense management
- Create, view, edit, and delete expenses.
- Search and filter by category, date range, and amount range.
- Change list ordering and navigate paginated results.
- Duplicate an expense with a selected date.
- Import expenses from CSV and export matching expenses to CSV.
- Client-side validation for required fields, amounts, and future expense dates.

### Budgets and settings
- Create, view, update, and delete category budgets.
- Display category spending against its monthly budget.
- View account profile information and update currency, subject to backend validation.

## Technology stack

| Area | Technology |
|---|---|
| UI framework | React 19 |
| Language | TypeScript |
| Build tool and development server | Vite |
| Styling | Tailwind CSS 4 and application CSS |
| API communication | Axios |
| Server state and caching | TanStack Query |
| Routing | React Router |
| Forms | React Hook Form |
| Validation | Zod and Hook Form resolvers |
| Charts | Recharts |
| UI primitives | Radix UI |
| Icons | Lucide React |
| Notifications | Sonner |
| Tests | Vitest, Testing Library, jsdom |
| Linting | Oxlint |
| Hosting | Vercel |

Dependency versions are defined in package.json; exact resolved packages are recorded in package-lock.json.

## Application structure

The frontend is organized by feature. Each feature owns its page and API functions, while shared components and utilities are kept in dedicated directories.

```text
frontend/
├── public/
├── src/
│   ├── components/
│   │   ├── layout/       # Application shell and navigation
│   │   ├── shared/       # Reusable page states and elements
│   │   └── ui/           # Shared UI primitives
│   ├── features/
│   │   ├── auth/         # Login, registration, auth context/provider
│   │   ├── dashboard/    # Financial overview and charts
│   │   ├── expenses/     # Expense CRUD, filtering, CSV workflows
│   │   ├── budgets/      # Category budget management
│   │   └── settings/     # Profile and currency preferences
│   ├── lib/              # API client, auth storage, currency, errors
│   ├── types/            # API and domain TypeScript types
│   ├── App.tsx           # Routes and access guards
│   ├── index.css         # Global and component styles
│   └── main.tsx          # Application entry point
├── .env.example
├── package.json
├── package-lock.json
├── vite.config.ts
├── vitest.config.ts
└── vercel.json
```

### Main routes

| Route | Access | Page |
|---|---|---|
| /login | Public | Sign in |
| /register | Public | Create an account |
| /dashboard | Authenticated | Financial overview |
| /expenses | Authenticated | Expense management |
| /budgets | Authenticated | Budget management |
| /settings | Authenticated | Profile and currency settings |

Protected pages are rendered through an authentication gate. Page components are lazy-loaded, and dashboard charts are loaded separately.

## Frontend and API integration

The frontend uses Axios to communicate with the Django REST API. The API prefix is /api/v1/; all application requests follow the [documented API contract](../docs/API_CONTRACT.md).

| Frontend feature | API resources |
|---|---|
| Authentication | /auth/register/, /auth/token/, /auth/token/refresh/, /auth/me/ |
| Account settings | /auth/settings/ |
| Dashboard | /expenses/summary/, /expenses/statistics/, /expenses/ |
| Expenses | /expenses/, /expenses/{id}/duplicate/, /expenses/import/, /expenses/export/ |
| Budgets | /budgets/ |

TanStack Query manages server-state fetching, caching, and invalidation after data mutations. Axios request and response interceptors attach access tokens and handle eligible token refreshes.

### Local development proxy

During local development, the browser sends requests to the Vite origin using relative /api/v1/... paths. Vite proxies /api requests to the Django development server, by default http://127.0.0.1:8000.

This keeps local browser requests same-origin and avoids requiring production CORS configuration for local development.

### Production API origin

In production, the Axios client uses VITE_API_BASE_URL when provided. If it is empty in a production build, the frontend falls back to:

https://expense-tracker-api-n5s7.onrender.com

Set the variable to the API origin only; do not append /api/v1.

## Authentication and session handling

1. A user signs in through POST /api/v1/auth/token/.
2. The frontend stores the access and refresh tokens in browser sessionStorage.
3. The Axios request interceptor attaches the access token as Authorization: Bearer <access_token>.
4. If an eligible API request returns HTTP 401, the response interceptor attempts one refresh through POST /api/v1/auth/token/refresh/ and retries the original request once.
5. If the refresh token is rejected with HTTP 400 or 401, the frontend clears the stored tokens and dispatches the logout event.
6. On application load, the frontend checks the session through GET /api/v1/auth/me/.

Tokens are scoped to the browser tab's session and are cleared when the tab session ends or the user signs out. sessionStorage is accessible to JavaScript; it is not equivalent to an HttpOnly cookie. This implementation should not be described as cookie-based authentication.

## Environment variables

Copy .env.example to .env.local for local development.

| Variable | Used by | Purpose |
|---|---|---|
| VITE_API_BASE_URL | Browser build | API origin for production requests. Leave empty locally to use the Vite proxy. |
| API_PROXY_TARGET | Vite dev server | Optional local proxy target; defaults to http://127.0.0.1:8000. |

Example local .env.local:

```dotenv
VITE_API_BASE_URL=
API_PROXY_TARGET=http://127.0.0.1:8000
```

Example production setting:

```dotenv
VITE_API_BASE_URL=https://expense-tracker-api-n5s7.onrender.com
```

Variables prefixed with VITE_ are included in the browser bundle. Do not put passwords, private keys, Django secrets, database URLs, or other confidential values in frontend environment variables.

## Run locally

### Prerequisites
- Node.js compatible with the Vite version in the lockfile.
- npm.
- Python 3.13 and backend dependencies if you also want to run Django locally.

### 1. Clone the repository

```bash
git clone --branch frontend https://github.com/danielabdouroihamane9-gif/TallyDan.git
cd TallyDan/frontend
```

### 2. Install dependencies

```bash
npm ci
```

### 3. Configure the API target

Windows PowerShell:

```powershell
Copy-Item .env.example .env.local
```

macOS / Linux:

```bash
cp .env.example .env.local
```

For local full-stack development, leave VITE_API_BASE_URL empty. The Vite proxy forwards API requests to http://127.0.0.1:8000 by default.

### 4. Start the Django backend

In a separate terminal, start the backend from the repository's backend/ directory. Follow the [backend README](https://github.com/danielabdouroihamane9-gif/TallyDan/blob/django-backend/README.md) for dependency installation, environment configuration, and database migrations.

The backend should be available at http://127.0.0.1:8000.

### 5. Start the frontend

From the frontend/ directory:

```bash
npm run dev
```

Open the local URL printed by Vite, normally http://localhost:5173.

Register a local account or sign in with an existing backend account. The frontend does not use hard-coded demo financial records.

## Available scripts

Run these commands from frontend/:

| Command | Description |
|---|---|
| npm run dev | Start the Vite development server |
| npm run build | Run TypeScript project builds and create the production bundle |
| npm run preview | Preview the production build locally |
| npm run lint | Run Oxlint |
| npm test | Run the Vitest suite once |
| npm run test:watch | Run Vitest in watch mode |

The production build is generated in dist/.

## Testing and code quality

The frontend test suite uses Vitest, jsdom, and Testing Library. The checked-in tests cover selected application behavior, including API client and API contract behavior, API error handling, currency utilities, expense schema and CSV validation, Vite configuration, dialog behavior, and branding.

Run all checks before submitting frontend changes:

```bash
npm run lint
npm test
npm run build
```

These checks validate the frontend code. They do not replace integration testing against a running Django backend or end-to-end testing of the deployed application.

## Deployment

The frontend is deployed on **Vercel**:

https://tallydan-expense-tracker.vercel.app

The checked-in vercel.json configures:
- Build command: npm run build
- Output directory: dist
- SPA rewrite: application routes resolve to /index.html

For a Vercel project connected to this repository:
1. Set the **Root Directory** to frontend.
2. Use npm run build as the build command.
3. Set the output directory to dist.
4. Set VITE_API_BASE_URL to https://expense-tracker-api-n5s7.onrender.com.
5. Redeploy after changing environment variables.

The backend is hosted separately on Render. The frontend and backend must agree on the API origin and browser CORS configuration.

### Cross-origin API requests

The frontend is hosted on a different origin from the Render API. Browsers therefore enforce CORS for production API requests. The Django settings currently checked into this branch do not include django-cors-headers, CORS middleware, or an allowed-origin setting. The repository contains a proposed backend change in [docs/BACKEND_CORS_PATCH.md](../docs/BACKEND_CORS_PATCH.md).

Do not assume that local proxy success proves production CORS is configured. After any backend CORS change and redeployment, verify from the deployed Vercel origin that preflight OPTIONS requests and authenticated API requests succeed. Configure only the exact frontend origins you control; do not enable unrestricted origins.

## Known behavior and limitations

- The backend accepts a three-letter currency code based on format; the frontend does not provide a comprehensive currency catalogue or exchange-rate conversion.
- The backend rejects currency changes while the account has expenses or budgets. Existing amounts are not converted.
- The dashboard's six-month trend is assembled from six monthly summary requests; the backend does not expose a dedicated month-range trend endpoint.
- Expense forms and CSV selection apply client-side future-date checks. Server-side validation remains authoritative.
- CSV file selection in the frontend is limited to 10 MB. A client-side limit does not replace a server-side upload-size limit.
- JWTs are stored in sessionStorage, which is accessible to JavaScript running on the page.
- Successful operation depends on the availability and correct configuration of the separately deployed backend API.

## Project evolution

TallyDan evolved through three stages:

1. **Python CLI:** a local Python expense tracker with JSON persistence and a service-oriented structure.
2. **Django REST API:** an authenticated backend with relational persistence, expense and budget resources, and financial summary endpoints.
3. **Full-stack application:** this React frontend integrated with the Django API and deployed through Vercel and Render.

The original CLI remains available on the [main branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/main). The backend source and API documentation are in the [django-backend branch](https://github.com/danielabdouroihamane9-gif/TallyDan/tree/django-backend).

This frontend demonstrates practical work with component-based UI development, typed API integration, client-side state and validation, authentication flows, data visualization, and full-stack application integration.

---

## License

See the repository [LICENSE](../LICENSE).
