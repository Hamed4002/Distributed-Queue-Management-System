# Introduction

Modern service organizations such as banks, government offices, customer service centers, and similar institutions often need to serve a large number of customers through multiple branches and specialized service counters.

As customer traffic increases, managing queues efficiently becomes more challenging. Traditional queue management approaches may rely on manual coordination or simple ticketing mechanisms that are not designed to handle a large number of concurrent requests or provide real-time visibility into queue status.

A reliable queue management system must therefore address several important challenges, including maintaining ticket consistency under concurrent requests, efficiently managing waiting customers, coordinating specialized service counters, comparing queue conditions across branches, and providing real-time updates when the queue state changes.

This project aims to design and implement a **Distributed Real-Time Queue Management System** for **multi-branch service organizations**, with a strong focus on **concurrency control, data consistency, real-time communication, system reliability, and scalability under high load**.

---

# System Objective

The primary objective of the system is to provide a reliable and scalable platform for managing customer queues across multiple branches and specialized service counters.

The system is designed to achieve the following objectives:

1. **Ensure ticket consistency and correctness**
   The system must safely process concurrent ticket requests and prevent duplicate or invalid ticket numbers.

2. **Provide real-time queue management**
   Queue state changes, ticket calls, and other relevant events should be delivered to customers and service operators in real time without requiring manual page refreshes.

3. **Support multiple branches**
   The system must allow an organization to manage multiple independent branches, each with its own services, counters, queues, and operational data.

4. **Support specialized service counters**
   Each counter is dedicated to a specific service, and customers select the service they require rather than selecting a physical counter directly.

5. **Compare queue conditions across branches**
   Customers should be able to specify their desired **Service** and **City** and compare the conditions of eligible branches before obtaining a ticket.

6. **Provide optional location-based information**
   Customers may provide their **Location** or choose not to. When a location is available, the system calculates the straight-line distance between the customer and eligible branches using geographical coordinates.

7. **Efficiently manage service queues**
   Customers requesting the same service should be organized into the appropriate queue, while active counters for that service process customers in first-in, first-out order.

8. **Provide waiting-time estimation and operational statistics**
   The system should collect service data and use it to estimate waiting times and provide useful information about queue activity and service performance.

9. **Support ticket cancellation**
   The system should allow customers to cancel their tickets while the ticket is still waiting, before it has been called.

10. **Remain stable under high concurrent load**
    The system must maintain correctness and availability when a large number of users interact with the queue simultaneously.

---

# System Domain

The system belongs to the domain of **multi-branch service organizations that serve customers through specialized service counters**.

The domain is intentionally defined at a general level so that the system can be applied to different types of service organizations without being tightly coupled to a specific industry.

Examples of suitable environments include:

- **Banking institutions**, where different counters provide services such as account-related operations, financial transactions, or customer support.
- **Government service offices**, where customers are served based on the type of administrative service they require.
- **Customer service centers**, where customers are assigned to specialized service queues based on their request.
- **Other multi-branch service organizations** that operate through physical service counters and customer queues.

The core domain consists of the following concepts:

| Concept            | Description                                                                                                                                                               |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Branch**         | A physical service location belonging to the organization, including its address, city, geographical location, and time zone.                                             |
| **Service**        | A branch-independent definition of a type of service in the organization-wide service catalog (for example, "Bank Account Operations"), managed by Global Administrators. |
| **Branch Service** | The provision of one catalog Service at one specific branch, with its own active or inactive status. Customers' queues are organized per Branch Service.                  |
| **Counter**        | A physical service desk within a branch dedicated to a specific Branch Service.                                                                                           |
| **Queue**          | An ordered collection of customers waiting to receive a specific service at a specific branch.                                                                            |
| **Ticket**         | A queue entry assigned to a customer for a specific service within a selected branch. A customer may hold only one active ticket at a time.                               |
| **Customer**       | A person requesting and receiving a service through the queue management system.                                                                                          |
| **Service Log**    | A record of a completed service interaction, including information required to measure service duration and system performance.                                           |

The detailed relationships, business rules, actors, roles, and system behaviors are defined in the accompanying documents (`business-rules.md`, `functional-requirements.md`, `actors.md`, and others).

---

# Domain Principle

> **The customer selects the Service and City they need and may optionally provide their Location; the system presents eligible branches and their queue conditions, allowing the customer to choose a Branch before obtaining a Ticket.**

This principle separates the customer's initial selection from the queue and service process. The customer first specifies the required **Service** (from the service catalog) and **City** and may optionally provide their **Location**. The system then presents eligible branches (active branches in the city that provide the service through an active Branch Service and have at least one active counter for it), their estimated waiting times, and, when a location is available, the distance to each branch. The customer can then select a **Branch** and obtain a **Ticket**.

# System Users

The system supports three main user roles: **Customer, Operator, and Administrator**. Administrators are divided into **Branch Administrators** and **Global Administrators**, based on their scope of responsibility and access.

## 1. Customer

A Customer is a person who requests a service through the queue management system.

The customer's workflow is:

1. Select the required **Service** from the service catalog.
2. Select the desired **City** from the cities that have active branches.
3. Optionally provide their **Location**.
4. View eligible branches along with estimated waiting times.
5. If a location is provided, view the distance to each eligible branch.
6. Select a **Branch**.
7. Receive a **Ticket** and join the corresponding queue. A customer may hold only one active ticket at a time.
8. View the status of their own ticket and, optionally, cancel it while its status is `WAITING`.
9. Receive a **real-time notification** when the ticket is called. If the customer is not connected at that moment, the current ticket status is shown after reconnecting.
10. Proceed to the assigned counter and receive the requested service.

Customers do not select a specific counter. They specify the Service and City they need and may optionally provide their Location. They then choose a Branch based on the information provided and may cancel their ticket while it is still waiting.

---

## 2. Operator

An Operator is a staff member responsible for providing services at a specific **Counter**. At any moment, an operator is assigned to exactly one counter.

The Operator is responsible for:

- Viewing customers waiting for the service handled by the counter.
- Calling the next eligible customer (the oldest waiting ticket, first in, first out).
- Completing the service for the called customer.
- Recording a ticket as `NO_SHOW` when the customer has not appeared at least 60 seconds after being called.
- Moving to the next customer after completing the current service or recording a No-Show.

When an Operator calls the next customer, the customer's ticket status must be updated and the customer must be notified **in real time** without requiring a page refresh.

---

## 3. Administrator

Administrators are responsible for managing and monitoring the queue management system. This role is divided into two types based on access scope.

### 3.1 Branch Administrator

A Branch Administrator is responsible for managing the operations and resources of a specific **Branch**.

The Branch Administrator is responsible for:

- Enabling catalog services for the branch and activating or deactivating them (Branch Services).
- Creating, deactivating, and reactivating **Counters** within the branch.
- Registering (with a temporary password) and deactivating **Operators** assigned to the branch.
- Assigning operators to counters according to the branch's operational needs.
- Monitoring the current queue status.
- Monitoring branch-level operational statistics.
- Managing branch-specific configuration and operational data.

A Branch Administrator must only have access to resources and operations belonging to their assigned branch. Only the Branch Administrator can activate or deactivate the branch's Branch Services, counters, and operators. A Branch Administrator cannot create or modify catalog services.

---

### 3.2 Global Administrator

A Global Administrator manages branches, the organization-wide service catalog, and Branch Administrator accounts. The Global Administrator has read-only access to Branch Services, counters, and operators across all branches.

The Global Administrator is responsible for:

- Creating new **Branches**.
- Activating, deactivating, and reactivating branches.
- Managing branches across the organization.
- Creating and managing the **service catalog** (creating, updating, activating, and deactivating Services).
- Registering and managing **Branch Administrators** (with temporary passwords).
- Viewing (without changing) the Branch Services, counters, and operators of any branch.
- Monitoring queues across all branches.
- Accessing system-wide statistics and performance information.

A Global Administrator has a broader access scope than a Branch Administrator and can operate across the entire system. Enabling services for a branch, creating counters, registering operators, and activating or deactivating these resources are the sole responsibility of Branch Administrators. The first Global Administrator account is provided when the system is first set up.

---

## User Role Hierarchy

```text
User
├── Customer
├── Operator
└── Administrator
    ├── Branch Administrator
    └── Global Administrator
```

The exact permissions, access rules, resource ownership, and authorization policies for each role are defined in the business rules and functional requirements.

# Key System Features

- **Multi-Branch Queue Management** — Manage queues across multiple branches and service counters.
- **Service Catalog** — Define services once at the organization level and provide them per branch.
- **Service-Based Queuing** — Customers select the required service and receive a ticket for the corresponding queue.
- **Branch Comparison** — Compare eligible branches based on estimated waiting time before obtaining a ticket.
- **Optional Location-Based Distance** — Calculate the straight-line distance between the customer and eligible branches when a location is provided.
- **Ticket Tracking and Cancellation** — Let customers view their own ticket and cancel it while it is still waiting.
- **Single Active Ticket per Customer** — Prevent a customer from holding more than one active ticket at a time.
- **FIFO Queue Order** — Call tickets in order of issue time within each queue.
- **Concurrent Ticket Processing** — Safely handle multiple simultaneous ticket requests while maintaining data consistency.
- **Race Condition Prevention** — Prevent duplicate or invalid ticket numbers during concurrent operations.
- **Real-Time Queue Updates** — Keep customers and operators synchronized through real-time communication.
- **Real-Time Notifications** — Notify connected customers immediately when their ticket is called.
- **Branch-Scoped Real-Time Communication** — Isolate real-time communication between branches.
- **Service Logging** — Record completed services and service durations.
- **Waiting Time Estimation** — Estimate customer waiting time based on queue and recent service performance.
- **Administrative Management** — Support branch-level and global administration with clearly separated responsibilities.
- **Account Provisioning** — Create staff accounts with temporary passwords that must be changed at first login.
- **Statistics & Reporting** — Provide queue, traffic, and waiting-time statistics.

# Technical Highlights

- **Load & Stress Testing** — Evaluate system stability and performance under high traffic using k6.
- **API Documentation** — Document the system APIs using Swagger/OpenAPI.
- **Containerized Infrastructure** — Run services such as PostgreSQL and Redis using Docker.
- **Code Quality** — Maintain clean and consistent code using C#, .NET analyzers, and `dotnet format`.
