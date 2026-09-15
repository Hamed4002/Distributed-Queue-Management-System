# Introduction

Modern service organizations such as banks, government offices, customer service centers, and similar institutions often need to serve a large number of customers through multiple branches and specialized service counters.

As customer traffic increases, managing queues efficiently becomes more challenging. Traditional queue management approaches may rely on manual coordination or simple ticketing mechanisms that are not designed to handle a large number of concurrent requests or provide real-time visibility into queue status.

A reliable queue management system must therefore address several important challenges, including maintaining ticket consistency under concurrent requests, efficiently managing waiting customers, coordinating specialized service counters, and providing real-time updates when the state of a queue changes.

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

5. **Efficiently manage service queues**
   Customers requesting the same service should be organized into the appropriate queue, while available counters for that service process customers according to the defined queue rules.

6. **Provide waiting-time estimation and operational statistics**
   The system should collect service data and use it to estimate waiting times and provide useful information about queue activity and service performance.

7. **Remain stable under high concurrent load**
   The system must maintain correctness and availability when a large number of users interact with the queue simultaneously.

---

# System Domain

The system belongs to the domain of **multi-branch service organizations that serve customers through specialized service counters**.

The domain is intentionally defined at a general level so that the system can be applied to different types of service organizations without being tightly coupled to a specific industry.

Examples of suitable environments include:

* **Banking institutions**, where different counters provide services such as account-related operations, financial transactions, or customer support.
* **Government service offices**, where customers are served based on the type of administrative service they require.
* **Customer service centers**, where customers are assigned to specialized service queues based on their request.
* **Other multi-branch service organizations** that operate through physical service counters and customer queues.

The core domain consists of the following concepts:

| Concept         | Description                                                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Branch**      | A physical location where the organization provides services to customers.                                                      |
| **Service**     | A specific type of service offered by a branch.                                                                                 |
| **Counter**     | A physical service desk within a branch dedicated to a specific service.                                                        |
| **Queue**       | An ordered collection of customers waiting to receive a specific service.                                                       |
| **Ticket**      | A queue entry assigned to a customer for a specific service within a branch.                                                    |
| **Customer**    | A person requesting and receiving a service through the queue management system.                                                |
| **Service Log** | A record of a completed service interaction, including information required to measure service duration and system performance. |

The detailed relationships, business rules, actors, roles, and system behaviors will be defined in the subsequent **Domain Modeling and Requirements Analysis phases**.

---

# Domain Principle

> **The customer chooses the service they need; the system manages the queue and determines when and where the service is provided.**

This principle separates the customer's request from the physical execution of the service. Customers do not need to select a specific counter; instead, they enter the queue associated with the requested service, while the system coordinates the available specialized counters and operators.

# System Users

The system supports three main user roles: **Customers, Operators, and Administrators**. Administrators are divided into **Branch Administrators** and **Global Administrators**, based on their scope of responsibility and access.

## 1. Customer

A customer is a person who requests a service through the queue management system.

The customer's workflow is:

1. Select the desired **Branch**.
2. Select the required **Service**.
3. Receive a **Ticket** and join the corresponding queue.
4. Monitor the ticket status and estimated waiting time.
5. Receive a **real-time notification** when the ticket is called.
6. Proceed to the assigned counter and receive the requested service.

Customers do not select a specific counter. They only specify the service they need, while the system manages the queue and determines when and where the service will be provided.

---

## 2. Operator

An operator is a staff member responsible for serving customers at a specific **Counter**.

The operator is responsible for:

* Viewing customers waiting for the service handled by the counter.
* Calling the next eligible customer.
* Starting and completing a service.
* Updating the state of the current ticket.
* Moving to the next customer after completing the current service.

When an operator calls the next customer, the customer's ticket status must be updated and the customer must be notified **in real time** without requiring a page refresh.

---

## 3. Administrator

Administrators are responsible for managing and monitoring the queue management system. The system defines two administrative roles based on their access scope.

### 3.1 Branch Administrator

A Branch Administrator manages the operations and resources of a **single branch**.

The Branch Administrator is responsible for:

* Managing the services provided by the branch.
* Creating, deactivating, and reactivating **Counters** within the branch.
* Registering and deactivating **Operators** assigned to the branch.
* Assigning operators to counters according to the branch's operational needs.
* Monitoring the current queue status.
* Monitoring branch-level operational statistics.
* Managing branch-specific configuration and operational data.

A Branch Administrator must only have access to resources and operations belonging to their assigned branch.

---

### 3.2 Global Administrator

A Global Administrator has system-wide access and is responsible for managing **all branches** within the organization.

The Global Administrator is responsible for:

* Creating new **Branches**.
* Activating, deactivating, and reactivating branches.
* Managing branches across the organization.
* Managing branch-level services, counters, and operators when necessary.
* Monitoring queues across all branches.
* Accessing system-wide statistics and performance information.
* Managing organization-wide configurations and operational data.

A Global Administrator has a broader access scope than a Branch Administrator and can operate across the entire system.

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

The exact permissions, access rules, resource ownership, and authorization policies for each role will be defined during the **Requirements Analysis and Authorization Design** phases.
# Key System Features

* **Multi-Branch Queue Management** — Manage queues across multiple branches and service counters.
* **Service-Based Queuing** — Customers select the required service and receive a ticket for the corresponding queue.
* **Concurrent Ticket Processing** — Safely handle multiple simultaneous ticket requests while maintaining data consistency.
* **Race Condition Prevention** — Prevent duplicate or invalid ticket numbers during concurrent operations.
* **Real-Time Queue Updates** — Keep customers and operators synchronized through real-time communication.
* **Real-Time Notifications** — Notify customers immediately when their ticket is called.
* **Socket Room Management** — Isolate real-time communication between branches.
* **Service Logging** — Record completed services and service durations.
* **Waiting Time Estimation** — Estimate customer waiting time based on queue and recent service performance.
* **Administrative Management** — Support branch-level and global administration.
* **Statistics & Reporting** — Provide queue, traffic, and waiting-time statistics.
* **Load & Stress Testing** — Evaluate system stability and performance under high traffic.
* **API Documentation** — Document the system APIs using Swagger/OpenAPI.
* **Containerized Infrastructure** — Run required services such as PostgreSQL and Redis using Docker.
* **Code Quality** — Maintain consistent and clean code using TypeScript, ESLint, and Prettier.
