# Dealflow360

Welcome to **Dealflow360**, a modern, full-stack web application. This repository is structured as an npm workspace monorepo, containing the frontend, backend, and shared utilities, providing a seamless development experience and robust architecture.

## 🏗️ Architecture & Tech Stack

This project is built using a modern TypeScript stack.

- **Frontend:** React 18, Vite, Tailwind CSS, TypeScript, Socket.io-client, React Router
- **Backend:** Node.js, Express, Prisma ORM, PostgreSQL, Socket.io, Zod (validation), TypeScript
- **Shared:** Common TypeScript interfaces, types, and utility functions shared across both frontend and backend
- **Infrastructure:** Docker & Docker Compose for local database management

## 📁 Repository Structure

```text
.
├── backend/          # Express API server, Prisma schema, and WebSocket handlers
├── frontend/         # React SPA built with Vite and styled with Tailwind CSS
├── shared/           # Shared TypeScript types, schemas, and utilities
├── docker-compose.yml# Docker configuration for PostgreSQL database
└── package.json      # Root package.json defining the npm workspaces
```

## 🚀 Getting Started

Follow these instructions to set up the project locally.

### Prerequisites

- [Node.js](https://nodejs.org/) (v20 or higher recommended)
- [npm](https://www.npmjs.com/)
- [Docker](https://www.docker.com/) (for the PostgreSQL database)

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/amarnotcool/Krazzzy
   cd Krazzzy
   ```

2. **Install dependencies:**
   This will install dependencies for the root workspace, as well as the frontend, backend, and shared packages.
   ```bash
   npm install
   ```

3. **Set up Environment Variables:**
   You will need to configure the `.env` files for both the frontend and backend.
   - Copy `backend/.env.example` to `backend/.env`
   - Copy `frontend/.env.example` to `frontend/.env`
   - Update the values in the `.env` files as needed. 

4. **Start the Database:**
   Use Docker Compose to spin up the PostgreSQL database.
   ```bash
   docker compose up -d
   ```
   *Note: The local Docker database is configured to run on port `5433` (mapped from `5432` internally) to avoid conflicts with default local Postgres installations.*

5. **Run Database Migrations & Seeding:**
   Initialize the Prisma client, apply migrations, and seed the database with initial data.
   ```bash
   npm run prisma:generate -w backend
   npm run prisma:migrate -w backend
   npm run seed
   ```

### Development

To start the development servers for both the frontend and backend concurrently, run the following command from the root directory:

```bash
npm run dev
```

- **Frontend:** Typically accessible at `http://localhost:5173`
- **Backend:** Typically accessible at `http://localhost:3000` (check `.env` or server logs)

## 🛠️ Available Scripts (Root)

From the root directory, you can run the following workspace scripts:

- `npm run dev`: Starts both frontend and backend development servers concurrently.
- `npm run build`: Builds the shared package, backend, and frontend for production.
- `npm run typecheck`: Runs TypeScript type checking across all workspaces without emitting files.
- `npm run seed`: Executes the database seed script located in the backend.

## 🧪 Testing

The backend is configured with [Vitest](https://vitest.dev/) for testing.

```bash
# Run backend tests
npm run test -w backend
```

## 📄 License

This project is licensed under the [MIT License](LICENSE) - see the LICENSE file for details.
