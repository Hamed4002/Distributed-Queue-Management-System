# Technical Requirements

This document defines the technical requirements and technologies used to implement the Distributed Real-Time Queue Management System.

---

## 1. Backend

**TR-01** — The backend shall be implemented using **Node.js**.

**TR-02** — The project shall use **TypeScript** with a standard `tsconfig.json` configuration.

**TR-03** — The backend shall use **Express.js** as the web application framework.

---

## 2. Database

**TR-04** — The system shall use **PostgreSQL** as its primary relational database.

**TR-05** — The system shall use **Prisma ORM** for database access and schema management.

---

## 3. Distributed and Real-Time Infrastructure

**TR-06** — The system shall support multiple backend application instances.

**TR-07** — The system shall use **Redis** as a shared Pub/Sub layer for synchronizing real-time events between backend instances.

**TR-08** — The system shall use **Socket.io** to provide real-time communication between the backend and connected clients.

**TR-09** — The system shall use Socket.io Rooms to isolate real-time events between branches.

---

## 4. Authentication and Authorization

**TR-10** — The system shall use **JWT-based authentication** for identifying authenticated users.

**TR-11** — The system shall implement role-based authorization for Customer, Operator, Branch Administrator, and Global Administrator.

---

## 5. Containerization and Infrastructure

**TR-12** — The project shall provide a **Docker Compose** environment for running the required infrastructure services.

**TR-13** — The Docker Compose environment shall include **PostgreSQL and Redis**.

---

## 6. API Documentation

**TR-14** — The system APIs shall be documented using **Swagger / OpenAPI**.

---

## 7. Testing

**TR-15** — The project shall use **Jest** for unit and integration testing.

**TR-16** — The project shall use **Supertest** for automated HTTP API testing.

**TR-17** — The project shall use **Postman** for manual API testing and development validation.

**TR-18** — The project shall use **k6** for load and performance testing.

---

## 8. Code Quality and Version Control

**TR-19** — The project shall use **ESLint** for static code analysis and maintaining coding standards.

**TR-20** — The project shall use **Prettier** for consistent code formatting.

**TR-21** — The project shall use **Git** for version control and maintain the project in a GitHub repository.
