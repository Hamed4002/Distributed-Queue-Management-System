# System Overview and Processes

## System Overview

The system is a **Distributed Real-Time Queue Management System** designed for multi-branch service organizations.

Services are defined once in an organization-wide **service catalog** managed by Global Administrators. Each branch provides one or more of these services (as Branch Services) through dedicated counters. Customers first select the service they require and the city in which they want to receive the service. They may also optionally provide their location. The system then evaluates eligible branches (active branches in the city that provide the service through an active Branch Service and have at least one active counter for it) based on the estimated waiting time for the selected service and, when a location is available, the distance to each branch. The customer chooses a branch and then receives a ticket and joins the corresponding queue. A customer may hold only one active ticket at a time.

Operators manage customers through their assigned counters. They call customers in first-in, first-out order, provide services, complete service interactions, and handle cases where a customer does not appear when called. An operator may record a ticket as `NO_SHOW` only after at least 60 seconds have elapsed since the customer was called, provided that the operator confirms the customer has not appeared and the ticket remains in the `CALLED` status.

Administrators manage system resources and monitor operations according to their access scope. Global Administrators can additionally view (without changing) the Branch Services, counters, and operators of any branch.

The system also provides user registration, authentication, and authorization to ensure secure access to system resources and operations.

---

## Ticket Lifecycle

```text
                 ┌──────────── Cancel (Customer) ────────────► CANCELLED
                 │
(Issue) ──► WAITING ──── Call (Operator) ────► CALLED ──── Complete (Operator) ────► COMPLETED
                 ▲                               │
                 │                               ├──── No-Show, ≥ 60 s (Operator) ──► NO_SHOW
                 │                               │
                 └─── Counter or operator ───────┘
                      deactivated (System)
```

`COMPLETED`, `CANCELLED` and `NO_SHOW` are final statuses. A service interrupted by the deactivation of its counter or operator is not considered completed and produces no Service Log.

---

## Overall System Workflow

```text
User
   ↓
Register / Login
   ↓
Authenticate User
   ↓
Authorize Access
   ↓
Use System According to Role
```

### Customer Queue Workflow

```text
Customer
   ↓
Select Service
   ↓
Select City
   ↓
Provide Location (Optional)
   ↓
Compare Eligible Branches
   ├── Estimated Waiting Time
   └── Distance (if Location is provided)
   ↓
Select Branch
   ↓
Obtain Ticket (rejected if the customer already has an active ticket)
   ↓
Join Queue
   ↓
Wait (status changes arrive in real time; the current status is
   loaded when the customer opens the app or reconnects)
   ├── Cancel Ticket
   │      └── Allowed only for own ticket while status is WAITING
   │
   └── Operator Calls Next Customer (oldest WAITING ticket)
              ↓
       Ticket Status → CALLED
              ↓
       Customer is Notified
       (if not connected, status is shown on reconnection)
              ↓
       Counter or Operator Deactivated?
          ├── Yes → Ticket Returns to WAITING
          │          (customer notified, another counter takes over)
          │
          └── No
                 ↓
       Customer Arrives?
          ├── Yes → Service is Provided
          │          ↓
          │     Service is Completed
          │          ↓
          │     Service Log is Recorded
          │          ↓
          │     Counter Becomes Available
          │
          └── No → Wait Until 60 Seconds Have Elapsed
                         ↓
                  Customer Still Absent?
                         ↓
                  Is Ticket Still CALLED?
                    ├── Yes → Operator Confirms and Records NO_SHOW
                    │              ↓
                    │       Ticket Status is Updated
                    │              ↓
                    │       Counter Becomes Available
                    │              ↓
                    │       Queue Continues
                    │
                    └── No → Reject No-Show Operation
```

Administrative operations run alongside the queue process:

```text
Global Administrator
   ├── Manage Branches
   ├── Manage Service Catalog
   ├── Manage Branch Administrators
   └── View Branch Resources (read-only)
              ↓
       Branch Administrator
          ├── Manage Branch Services (enable / activate / deactivate)
          ├── Manage Counters
          └── Manage Operators
```

---

# System Processes

## 1. Register Customer

### Objective

Allow a new customer to create an account in the queue management system.

### Workflow

```text
Customer
   ↓
Enter Registration Information
   ↓
System Validates Information
   ↓
System Creates User Account (Customer role only)
   ↓
Registration Completed
```

### Involved Actors

- Customer

---

## 2. Authenticate User

### Objective

Verify the identity of a registered user before granting access to protected system functionality.

### Workflow

```text
User
   ↓
Enter Login Credentials
   ↓
System Validates Credentials
   ↓
Authentication Successful
   ↓
Temporary Password?
   ├── Yes → Require Password Change
   │            ↓
   │       User Changes Password
   │
   └── No  → (continue)
   ↓
User Gains Access
```

### Involved Actors

- Customer
- Operator
- Branch Administrator
- Global Administrator

---

## 3. Authorize User Access

### Objective

Ensure that an authenticated user can only access resources and perform operations permitted for their role and scope.

### Workflow

```text
Authenticated User
   ↓
Request Protected Resource / Operation
   ↓
System Checks User Role and Access Scope
   ↓
Access Granted or Denied
```

### Involved Actors

- Customer
- Operator
- Branch Administrator
- Global Administrator

---

## 4. Compare Eligible Branches

### Objective

Allow a customer to compare eligible branches that provide the selected service in the selected city before obtaining a ticket.

### Workflow

```text
Customer
   ↓
Select Service (from the active service catalog)
   ↓
Select City (from the list of cities with active branches)
   ↓
Provide Location (Optional)
   ↓
System Identifies Eligible Branches
   (active, in the city, provides the service through an active
    Branch Service, has at least one active counter for it)
   ↓
System Calculates Estimated Waiting Time
   (may be "unknown" if the queue has no service history)
   ↓
Location Provided?
   ├── Yes → Calculate Distance (straight line, km)
   └── No  → Skip Distance Calculation
                  ↓
        Display Branch Comparison
```

An active counter is a counter that is itself active and has an active operator assigned to it.

### Involved Actors

- Customer

---

## 5. Obtain Ticket

### Objective

Allow a customer to select a branch and join the appropriate service queue by receiving a ticket.

### Workflow

```text
Customer
   ↓
Select Branch
   ↓
Request Ticket
   ↓
System Validates Branch and Service Eligibility
   (active branch, active catalog Service, active Branch Service,
    at least one active counter)
   ↓
System Checks the Customer Has No Active Ticket
   ↓
System Creates Ticket
   ↓
System Assigns Ticket Number
   (unique per branch + service + day, restarting daily)
   ↓
Customer Joins Queue
```

### Involved Actors

- Customer

---

## 6. Cancel Ticket

### Objective

Allow a customer to cancel their own ticket only while its status is `WAITING`.

### Workflow

```text
Customer
   ↓
Request Ticket Cancellation
   ↓
System Verifies the Ticket Belongs to the Customer
   ├── No  → Reject Cancellation
   │
   └── Yes
          ↓
   System Checks Ticket Status
          ↓
   Is Status WAITING?
      ├── Yes → Cancel Ticket
      │             ↓
      │        Update Queue
      │
      └── No  → Reject Cancellation
```

### Involved Actors

- Customer

---

## 7. Call Next Customer

### Objective

Allow an operator to call the next eligible customer from the queue for the assigned service.

### Workflow

```text
Operator
   ↓
Request Next Customer
   ↓
System Selects the Next Eligible Ticket
   (the WAITING ticket with the earliest issue time)
   ↓
System Updates Ticket Status to CALLED
   ↓
Customer is Notified in Real Time
   (if not connected, current status is shown on reconnection)
```

### Involved Actors

- Operator

---

## 8. Handle No-Show Customer

### Objective

Allow an operator to record a ticket as `NO_SHOW` if the customer has not appeared at the assigned counter after at least 60 seconds have elapsed since the ticket was called.

### Workflow

```text
Operator Calls Customer
   ↓
Ticket Status is CALLED
   ↓
Wait at Least 60 Seconds
   ↓
Has the Customer Appeared?
   ├── Yes → Continue Service Process
   │
   └── No → System Checks Ticket Status
                  ↓
           Is Status Still CALLED?
              ├── Yes → Operator Confirms Absence and Records NO_SHOW
              │              ↓
              │       System Updates Ticket Status
              │              ↓
              │       Counter Becomes Available
              │              ↓
              │       Queue Continues
              │
              └── No → Reject No-Show Operation
```

A `NO_SHOW` ticket is final; the customer must obtain a new ticket to receive service. No Service Log is created for it.

### Involved Actors

- Operator

---

## 9. Complete Service

### Objective

Allow an operator to complete the current customer service and record the service interaction.

### Workflow

```text
Operator
   ↓
Complete Customer Service (ticket in CALLED status)
   ↓
System Updates Ticket Status to COMPLETED
   ↓
Service Duration is Recorded
   (from call time to completion time)
   ↓
Service Log is Created
   ↓
Counter Becomes Available
```

### Involved Actors

- Operator

---

## 10. Manage Branch Services

### Objective

Allow a Branch Administrator to manage the services provided by their branch by enabling catalog services and activating or deactivating them for the branch.

### Workflow

```text
Branch Administrator
   ↓
Select a Service from the Catalog
   ↓
Enable / Activate / Deactivate Branch Service
   ↓
System Updates Branch Service Information
```

Deactivating a Branch Service blocks new tickets for it in that branch only; existing tickets continue through their normal lifecycle.

### Involved Actors

- Branch Administrator

---

## 11. Manage Counters

### Objective

Allow a Branch Administrator to manage the specialized counters within their branch.

### Workflow

```text
Branch Administrator
   ↓
Create / Activate / Deactivate Counter
   ↓
Deactivating a Counter Handling a CALLED Ticket?
   ├── Yes → Ticket Returns to WAITING
   │          Customer is Notified
   └── No  → (continue)
   ↓
System Updates Counter Information
```

### Involved Actors

- Branch Administrator

---

## 12. Manage Operators

### Objective

Allow a Branch Administrator to manage operators assigned to their branch.

### Workflow

```text
Branch Administrator
   ↓
Register (with Temporary Password) / Update / Activate / Deactivate Operator
   ↓
Deactivating an Operator Handling a CALLED Ticket?
   ├── Yes → Ticket Returns to WAITING
   │          Customer is Notified
   └── No  → (continue)
   ↓
Assign Operator to Counter
   (ends the previous assignment; history is retained)
   ↓
System Updates Operator Information
```

### Involved Actors

- Branch Administrator

---

## 13. Manage Branches

### Objective

Allow a Global Administrator to manage branches across the organization.

### Workflow

```text
Global Administrator
   ↓
Create / Update Branch
   (address, city, coordinates, time zone)
   ↓
Activate / Deactivate Branch
   ↓
System Updates Branch Information
```

Deactivating a branch blocks new tickets only; existing tickets continue through their normal lifecycle.

### Involved Actors

- Global Administrator

---

## 14. Manage Branch Administrators

### Objective

Allow a Global Administrator to create and manage Branch Administrators for individual branches.

### Workflow

```text
Global Administrator
   ↓
Register (with Temporary Password) / Update / Activate / Deactivate Branch Administrator
   ↓
Assign Branch Administrator to a Branch
   ↓
System Updates Administrator Information
```

### Involved Actors

- Global Administrator

---

## 15. View Operational Statistics

### Objective

Provide administrators with information about queue activity and service performance within their authorized scope.

### Workflow

```text
Administrator
   ↓
Request Statistics
   ↓
System Retrieves and Processes Relevant Data
   ↓
Operational Statistics are Displayed
```

### Involved Actors

- Branch Administrator
- Global Administrator

---

## 16. View Branch Resources

### Objective

Allow a Global Administrator to view the Branch Services, counters, and operators of any branch without changing them.

### Workflow

```text
Global Administrator
   ↓
Select a Branch (or All Branches)
   ↓
System Retrieves Branch Services / Counters / Operators
   ↓
Resources and Their Status are Displayed (read-only)
```

Creating, activating, and deactivating these resources is the responsibility of the branch's Branch Administrator.

### Involved Actors

- Global Administrator

---

## 17. Manage Service Catalog

### Objective

Allow a Global Administrator to define and manage the services available across the organization.

### Workflow

```text
Global Administrator
   ↓
Create / Update Service
   ↓
Activate / Deactivate Service
   ↓
System Updates the Service Catalog
```

Deactivating a catalog Service blocks new tickets for it in all branches; tickets already issued continue through their normal lifecycle.

### Involved Actors

- Global Administrator
