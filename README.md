# OMS — Order Management System (SQLite System API)

SQLite System API for Order Management System — Centimark Training

This repository implements a lightweight System API (SAPI) for an Order Management System (OMS). It stores order and payment data in SQLite and exposes a small HTTP web-service API to create, read, update and delete orders and related payments. The repository’s source artifacts are DataWeave artifacts (MuleSoft / integration-friendly transformations).

This README provides an actionable guide for developers to set up and run the service locally, inspect the database, exercise the API, and extend the project.

Table of contents
- Overview
- What’s in this repo
- Requirements
- Configuration
- Database schema (SQLite)
- Running the service (recommended approaches)
- API endpoints & examples (curl)
- Testing
- Development notes
- Contributing
- License

Overview
--------
This SAPI is intended to be a compact backend for training and small demos. It provides:
- Orders: create, retrieve, update status, list, delete
- Payments: create payment records tied to orders, list payments by order, retrieve single payment
- Persistence: SQLite (single-file DB) for easy local development / training

What’s in this repo
-------------------
- DataWeave artifacts (transformations / flow definitions)
- Configuration templates (example .env or properties — if present)
- SQL schema or migration helpers (if included)
- README (this file)

If you don't see these exact files, they may be named differently. Use the repo browser to view top-level files.

Requirements
------------
- SQLite 3.x (for inspecting/managing the DB file)
- An HTTP runtime that executes the DataWeave flows (e.g., Mule runtime or other integration runtime) — verify runtime and start commands in the repository
- curl (or similar) for API testing
- Docker & docker-compose (optional — recommended for reproducible local environments)

Configuration
-------------
The service expects a running SQLite database file and a small set of environment variables or configuration properties. Typical variables (adjust to actual names in the project):

- APP_PORT — port to bind the HTTP service (default: 8000)
- DB_PATH — path to SQLite file (default: ./data/oms.db)
- LOG_LEVEL — log verbosity (info|debug)

If the repo contains a `.env.example`, copy to `.env` and edit values:
cp .env.example .env
# then edit .env

Database schema (suggested)
---------------------------
If the repository contains schema migration files, run those. Otherwise you can use the following sample SQLite schema to create the required tables:

-- create-orders-payments.sql (example)
CREATE TABLE IF NOT EXISTS orders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  customer_name TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'pending',
  total_amount REAL NOT NULL DEFAULT 0.0,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS payments (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  order_id INTEGER NOT NULL,
  amount REAL NOT NULL,
  method TEXT,
  status TEXT NOT NULL DEFAULT 'pending',
  processed_at DATETIME,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
);

To create the DB and run the SQL locally:
mkdir -p data
sqlite3 data/oms.db < create-orders-payments.sql

Running the service
-------------------
Because the repository contains DataWeave artifacts, confirm how the service is intended to be run (Mule application, standalone script, etc.). Choose one of these approaches:

A) Run via the repository's documented start script or runtime
- Inspect repo for a run script, Dockerfile, or Mule application XML
- Follow the contained run instructions (for example `mvn mule:deploy` or starting Mule runtime)

B) Docker (recommended for reproducible environments)
If the repo contains a Dockerfile or docker-compose.yml, prefer that. If not present, here's an example docker-compose to run a generic app that expects DB at /data/oms.db:

version: "3.8"
services:
  oms:
    image: <replace-with-app-image-or-build-context>
    build: .
    environment:
      - APP_PORT=8000
      - DB_PATH=/data/oms.db
    ports:
      - "8000:8000"
    volumes:
      - ./data:/data

Notes:
- Ensure the app waits for DB file availability if it connects at startup.
- If your runtime requires Mule runtime, either install Mule locally or use a Mule-enabled Docker image.

API endpoints & examples
------------------------
The exact URL paths and payloads depend on the project implementation. Below are conventional endpoints used by this type of SAPI. Replace base URL and field names as appropriate.

Base URL (example):
http://localhost:8000

Health
- GET /health
  - Returns service health/status

Orders
- GET /orders
  - Returns a list of orders (optional pagination/filtering)
- GET /orders/{id}
  - Get a single order by id
- POST /orders
  - Create a new order
  - Example body:
    {
      "customer_name": "Acme Corp",
      "items": [
        {"sku": "SKU1", "qty": 2, "unit_price": 12.50}
      ],
      "total_amount": 25.00
    }
- PUT /orders/{id}
  - Update order fields (e.g., status)
  - Example body:
    { "status": "shipped" }
- DELETE /orders/{id}
  - Delete order (soft or hard depending on implementation)

Payments
- GET /orders/{order_id}/payments
  - List payments for an order
- GET /payments/{id}
  - Get payment by id
- POST /orders/{order_id}/payments
  - Create a payment for an order
  - Example body:
    { "amount": 25.00, "method": "card", "status": "completed" }

Example curl (adjust paths to match the real service)
Create order:
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"customer_name":"Alice","items":[{"sku":"SKU1","qty":1,"unit_price":10.00}],"total_amount":10.00}'

Create payment:
curl -X POST http://localhost:8000/orders/1/payments \
  -H "Content-Type: application/json" \
  -d '{"amount":10.00,"method":"card","status":"completed"}'

Testing
-------
- If the repo includes tests, run them using the project's test runner. For DataWeave / Mule projects, tests may be executed through Maven or Anypoint Studio test runners.
- Add automated checks for migrations and API contract (e.g., Postman/Newman or integration tests).

Development notes
-----------------
- Keep SQL migrations in source control; prefer versioned migrations (Flyway / Liquibase) when the project grows.
- Add a seed script to populate sample orders/payments for local dev.
- Add OpenAPI/Swagger to auto-generate API docs if you plan clients to integrate.
- Add graceful DB connection retry/backoff on startup, especially when using containers.

Contributing
------------
Contributions are welcome. Typical workflow:
1. Fork the repository
2. Create a feature branch
3. Run tests and linters locally
4. Open a pull request describing the change and tests added

License
-------
If a LICENSE file is not present, add one (MIT or Apache-2.0 are common choices). Include license name here once added.

Notes & next steps
------------------
- This README is written to match the repository description: "SQLite System API for Order Management System - Centimark Training" and the observed DataWeave composition. I have not modified repository files — I prepared this README content for the repo root.
- Next, I can:
  - Update the README to include exact start commands, runtime-specific instructions, and real endpoints after you confirm or share the main run file (Dockerfile, Mule app descriptor, or start script).
  - Push this README.md directly to the repository if you want (I can create a PR or commit it to a branch).