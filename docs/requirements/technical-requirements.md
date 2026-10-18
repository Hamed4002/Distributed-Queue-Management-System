# Technical Requirements

This document defines the technical requirements and technologies used to implement the Distributed Real-Time Queue Management System.

---

## 1. Backend

**TR-01** — The backend shall be implemented using **C#**.

**TR-02** — The backend shall use **ASP.NET Core** to implement HTTP APIs and server-side application logic.

---

## 2. Database and Data Access

**TR-03** — The system shall use **PostgreSQL** as its primary relational database.

**TR-04** — The system shall use **Entity Framework Core (EF Core)** for database access and object-relational mapping.

**TR-05** — The system shall use PostgreSQL transactions and appropriate row-level locking, including `SELECT ... FOR UPDATE` where necessary, to protect ticket issuance and other critical queue operations against concurrent modifications. Database constraints shall also be used where appropriate to enforce data integrity. In particular:

- Ticket numbers shall be generated from a **ticket-number sequence row** per (branch, service, date). This row is a database record used only for numbering and is unrelated to the physical service counters.
- The sequence row shall be created atomically on first use, for example with `INSERT ... ON CONFLICT ... DO UPDATE` that increments and returns the new value, so that concurrent first requests at the start of an operational day cannot create duplicate rows or bypass the lock. Subsequent increments shall be serialized by the same atomic statement or by `SELECT ... FOR UPDATE` on the row.
- A unique constraint on `(branch_id, service_id, date, number)` shall act as a final safeguard against duplicate ticket numbers.
- The rule that a customer may have at most one active ticket (`WAITING` or `CALLED`) shall be enforced by a partial unique index on the customer identifier restricted to those statuses, in addition to the application-level check.

---

## 3. Distributed and Real-Time Infrastructure

**TR-06** — The system architecture shall support multiple backend application instances running concurrently.

**TR-07** — The system shall use **Redis** as a shared backplane for synchronizing SignalR messages across multiple backend instances.

**TR-08** — The system shall use **ASP.NET Core SignalR** for real-time communication between the backend and connected clients.

**TR-09** — The system shall use SignalR Groups to organize real-time connections by branch and other required authorization scopes. Group membership and event delivery shall be controlled by the server. After every reconnection, the server shall re-establish the client's group membership based on its authenticated identity.

**TR-10** — The deployment architecture shall support a load balancer or reverse proxy to distribute incoming requests across backend instances. The specific technology shall be selected during architecture design.

---

## 4. Authentication and Authorization

**TR-11** — The system shall use **JWT-based authentication** with ASP.NET Core authentication middleware to authenticate users.

**TR-12** — The system shall implement role-based authorization for Customer, Operator, Branch Administrator, and Global Administrator, while enforcing each user's authorized resource scope.

---

## 5. Containerization and Infrastructure

**TR-13** — The project shall provide a **Docker Compose** environment for running the required infrastructure services.

**TR-14** — The Docker Compose environment shall include **PostgreSQL and Redis**.

The deployment configuration shall support running multiple backend instances and integrating the selected load balancer or reverse proxy.

---

## 6. API Documentation

**TR-15** — The system APIs shall be documented using **OpenAPI**, with Swagger tooling where appropriate.

---

## 7. Testing

**TR-16** — The project shall use **xUnit** for automated unit testing.

**TR-17** — The project shall use **ASP.NET Core integration testing tools**, including `WebApplicationFactory`, to test HTTP APIs and application behavior.

**TR-18** — The project may use **Postman** as a supporting tool for manual API testing and development validation. A Postman collection is provided for this purpose; it is not an automated verification requirement.

**TR-19** — The project shall use **k6** for load and performance testing, including concurrent ticket issuance scenarios.

---

## 8. Code Quality and Version Control

**TR-20** — The project shall use **.NET code analyzers** to identify potential code issues and enforce applicable coding standards.

**TR-21** — The project shall use **`dotnet format`** to maintain consistent code formatting.

**TR-22** — The project shall use **Git** for version control and maintain the source code in a **GitHub** repository.

---

## 9. Account Provisioning

**TR-23** — The initial Global Administrator account (FR-06) shall be created through an EF Core migration seed or environment-based configuration. Its credentials shall be read from environment variables or configuration, never from source code. Passwords, including temporary passwords, shall be stored only in hashed form.
