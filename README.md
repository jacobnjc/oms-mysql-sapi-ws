# OMS — Order Management System (SQLite System API)

SQLite System API for Order Management System — Centimark Training

This repository implements a lightweight System API (SAPI) for an Order Management System (OMS). It stores order and payment data in SQLite and exposes a small HTTP web-service API to create, read, update and delete orders and related payments. The repository’s source artifacts are DataWeave artifacts (MuleSoft / integration-friendly transformations).

This README provides an actionable guide for developers to set up and run the service locally, inspect the database, exercise the API, and extend the project.


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
- README (this file)

Requirements
------------
- SQLite 3.x (for inspecting/managing the DB file)
- An HTTP runtime that executes the DataWeave flows (e.g., Mule runtime or other integration runtime) — verify runtime and start commands in the repository
  
-------------
The service expects a running SQLite database file and a small set of environment variables or configuration properties. Typical variables :


Database schema (suggested)
---------------------------
 SQLite schema to create the required tables:

Running the service
-------------------

<TDAL.> 


API endpoints & examples
------------------------
The exact URL paths and payloads depend on the project implementation. Below are conventional endpoints used by this type of SAPI. Replace base URL and field names as appropriate.


Testing
-------
- TO be done with MUnits <TBAddedLater>

License
-------
<TBAddedLater>
