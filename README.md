# Project Hub

Project Hub is a web application for managing a project portfolio, committee stages, documents, progress update history, and a monitoring dashboard. The backend provides a REST API built with Express, Prisma, and PostgreSQL. This repository also includes a simple test frontend in `public/index.html`; the team's React/Vite frontend can run separately and use the same API.

## Technology Stack

- Node.js with JavaScript ESM
- Express 5 and Zod
- Prisma 7 with PostgreSQL
- JWT and bcryptjs for authentication
- Multer for document uploads
- A DeepSeek-compatible API for AI Summary and chatbot intent processing

## Prerequisites

- Node.js and npm
- A PostgreSQL server reachable from the application machine
- A PostgreSQL connection URL
- An AI model API key, only if you want to use AI Summary or the chatbot

## Local Setup

1. Install dependencies:

   ```sh
   npm install
   ```

2. Create a `.env` file in the repository root. Example variables:

   ```env
   PORT=3000
   DATABASE_URL=postgresql://USER:PASSWORD@localhost:5432/project_hub
   JWT_SECRET=replace-with-a-random-secret-at-least-32-characters-long

   # Optional; required only for AI features
   LLM_BASE_URL=https://model-api-host
   LLM_API_KEY=server-side-api-key
   LLM_MODEL=model-name
   ```

   Update the database name, username, password, and API URL for your environment. Do not commit or share `.env`, and never put `LLM_API_KEY` in the frontend.

3. Apply database migrations and generate the Prisma Client:

   ```sh
   npm run db:migrate
   npm run db:generate
   ```

4. Start the backend:

   ```sh
   npm run dev
   ```

   The server is available at `http://localhost:3000` by default. Check it at `http://localhost:3000/health`; a successful response is `{ "status": "ok" }`. The test frontend is available at `http://localhost:3000/`.

## Running a Separate Frontend

For a React/Vite frontend running on the same computer, set the API base URL in the frontend's `.env.local` file:

```env
VITE_API_BASE_URL=http://localhost:3000
```

Restart Vite after changing the configuration. If the frontend and backend run on different computers, use a backend host/IP address reachable by the frontend; `localhost` points to the computer running the browser. The backend currently allows all origins through CORS for local development convenience; restrict allowed origins before deployment.

All API endpoints except `/health`, `POST /auth/login`, and `POST /auth/activate` require a JWT in the request header:

```http
Authorization: Bearer <token>
```

## npm Commands

| Command | Description |
| --- | --- |
| `npm run dev` | Start the server in watch mode. |
| `npm start` | Start the server. |
| `npm run db:migrate` | Create/apply Prisma migrations to the configured database. |
| `npm run db:generate` | Generate the Prisma Client from the schema. |
| `npm run db:studio` | Open Prisma Studio to inspect database records. |
| `npm run db:create-admin` | Create the initial admin using `BOOTSTRAP_ADMIN_*` variables. |
| `npm run db:seed` | Delete application data and repopulate the initial sample data. Destructive. |
| `npm run db:seed:demo` | Refresh the demo fixture. Use only with a local, non-production database; fixture data will be changed. |

To create the initial admin, temporarily set `BOOTSTRAP_ADMIN_NAME`, `BOOTSTRAP_ADMIN_EMAIL`, and `BOOTSTRAP_ADMIN_PASSWORD` in `.env`, run `npm run db:create-admin`, then remove those values from `.env`. The password must be at least 8 characters and no more than 72 bytes.

Run seeds only against a database intended for sample data. `npm run db:seed` deletes projects, documents, updates, master data, and users before recreating the initial sample data.

## Documentation

- [Architecture documentation](DOKUMENTASI.md) — module responsibilities, dependencies, diagrams, execution flows, configuration, and maintenance notes.
- [API documentation](docs/API.md) — endpoints, authentication, request examples, error formats, and enums.
- [OpenAPI specification](docs/openapi.yaml) — API contract for OpenAPI tooling.

## Security Notes

- Do not share `.env`, passwords, JWTs, `JWT_SECRET`, or `LLM_API_KEY`.
- Restrict CORS to the approved frontend origins before exposing the server to a public network.
- Uploaded files are stored in the local `uploads/` directory; plan storage and backups for the deployment environment.
- The backend enforces roles and project assignments on write endpoints. Hiding buttons in the frontend is not a substitute for server-side authorization.
