# Actors

An **Actor** is an external entity that interacts with the system to achieve a specific goal. Actors may represent human users or external systems that communicate with the queue management system.

The following actors are identified for the system:

---

## 1. Customer

The **Customer** is the person who requests a service and uses the queue management system to obtain and track a ticket.

The Customer interacts with the system to:

- Select a required service from the service catalog.
- Select a city from the cities that have active branches.
- Optionally provide their geographical location.
- Compare eligible branches based on estimated waiting times and, when their location is provided, distance.
- Select an eligible branch.
- Obtain a ticket and join the corresponding service queue. A customer may hold only one active ticket (`WAITING` or `CALLED`) at a time.
- View the status of their own ticket and the latest relevant queue information.
- Cancel their own ticket while its status is `WAITING`.
- Receive real-time notifications when the ticket is called. If not connected at that moment, the customer sees the current ticket status after reconnecting.

---

## 2. Operator

The **Operator** is a staff member responsible for serving customers through an assigned service counter. At any moment, an operator is assigned to exactly one counter.

The Operator interacts with the system to:

- View the queue associated with their assigned counter and service.
- Call the next eligible customer (the oldest waiting ticket, first in, first out).
- Manage the current service interaction.
- Complete the service and proceed to the next customer.
- Record a ticket as `NO_SHOW`, after explicitly confirming that the customer has not appeared, if at least 60 seconds have elapsed since the ticket was called and the ticket remains in the `CALLED` status.

---

## 3. Branch Administrator

The **Branch Administrator** is responsible for managing the operations and resources of a specific branch.

The Branch Administrator interacts with the system to:

- Enable catalog services for the branch and activate or deactivate them (Branch Services).
- Create and manage counters within the branch.
- Register operators (with a temporary password) and manage operators assigned to the branch.
- Assign operators to counters.
- Monitor queue activity and branch operations.
- Access branch-level statistics and operational information.

The Branch Administrator is restricted to the resources and operations of the assigned branch. A Branch Administrator can reactivate a deactivated Branch Service, counter, or operator, except one that a Global Administrator has deactivated as an emergency action. A Branch Administrator cannot create or modify catalog services.

---

## 4. Global Administrator

The **Global Administrator** is responsible for managing and monitoring the organization across all branches.

The Global Administrator interacts with the system to:

- Create and manage branches.
- Activate or deactivate branches.
- Create and manage the service catalog (create, update, activate, and deactivate services).
- Register Branch Administrators (with a temporary password) and manage them.
- View the Branch Services, counters, and operators of all branches.
- Deactivate a Branch Service, counter, or operator in any branch as an emergency action, and reactivate resources deactivated this way.
- Manage organization-wide resources and configurations.
- Monitor queues and operations across branches.
- Access system-wide statistics and performance information.

The Global Administrator has access to the entire organization rather than being restricted to a single branch. Enabling services for a branch, creating counters, and registering operators remain the responsibility of Branch Administrators. The first Global Administrator account is provided when the system is first set up.

---

## Actor Overview

| Actor                    | Scope               | Primary Interaction                                                                                         |
| ------------------------ | ------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Customer**             | Personal            | Compare branches, obtain and track a single active ticket, and cancel waiting tickets                       |
| **Operator**             | Counter / Service   | Manage queues, serve customers, and record No-Shows                                                         |
| **Branch Administrator** | Single Branch       | Manage branch services, counters, and operators                                                             |
| **Global Administrator** | Entire Organization | Manage branches, the service catalog, and Branch Administrators; view all resources; emergency deactivation |
