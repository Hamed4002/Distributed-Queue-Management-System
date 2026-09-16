# Actors

An **Actor** is an external entity that interacts with the system to achieve a specific goal. Actors may represent human users or external systems that communicate with the queue management system.

The following actors are identified for the system:

## 1. Customer

The **Customer** is the person who requests a service and uses the queue management system to obtain and track a ticket.

The Customer interacts with the system to:

- Select a branch.
- Select a required service.
- Obtain a ticket.
- Monitor queue and ticket status.
- Receive real-time notifications when the ticket is called.

---

## 2. Operator

The **Operator** is a staff member responsible for serving customers through an assigned service counter.

The Operator interacts with the system to:

- View the relevant service queue.
- Call the next customer.
- Manage the current service request.
- Complete the service and move to the next customer.

---

## 3. Branch Administrator

The **Branch Administrator** is responsible for managing the operations and resources of a specific branch.

The Branch Administrator interacts with the system to:

- Manage services and counters within the branch.
- Register and manage operators assigned to the branch.
- Monitor queue activity and branch operations.
- Access branch-level statistics and operational information.

The Branch Administrator is restricted to the resources and operations of the assigned branch.

---

## 4. Global Administrator

The **Global Administrator** is responsible for managing and monitoring the organization across all branches.

The Global Administrator interacts with the system to:

- Create and manage branches.
- Activate or deactivate branches.
- Manage organization-wide resources and configurations.
- Monitor queues and operations across branches.
- Access system-wide statistics and performance information.

The Global Administrator has access to the entire organization rather than being restricted to a single branch.

---

## Actor Overview

| Actor                    | Scope               | Primary Interaction                    |
| ------------------------ | ------------------- | -------------------------------------- |
| **Customer**             | Personal            | Request and track a service            |
| **Operator**             | Counter / Service   | Serve customers and manage the queue   |
| **Branch Administrator** | Single Branch       | Manage branch resources and operations |
| **Global Administrator** | Entire Organization | Manage and monitor all branches        |
