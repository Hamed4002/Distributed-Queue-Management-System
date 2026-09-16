# System Overview and Processes

## System Overview

The system is a **Distributed Real-Time Queue Management System** designed for multi-branch service organizations.

Each branch provides one or more specialized services through dedicated counters. Customers select a branch and the service they require, then receive a ticket and join the corresponding queue.

Operators manage customers through their assigned counters. When a customer is called and served, the ticket state is updated and the queue continues.

Administrators manage system resources and monitor operations according to their access scope.

The system also provides user registration, authentication, and authorization to ensure secure access to system resources and operations.

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

Customer queue workflow:

```text
Customer
   ↓
Select Branch
   ↓
Select Service
   ↓
Obtain Ticket
   ↓
Join Queue
   ↓
Wait
   ↓
Operator Calls Next Customer
   ↓
Customer Receives Notification
   ↓
Service is Provided
   ↓
Service is Completed
   ↓
Service Log is Recorded
```

Administrative operations run alongside the queue process:

```text
Global Administrator
   ├── Manage Branches
   └── Manage Branch Administrators
              ↓
       Branch Administrator
          ├── Manage Services
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
System Creates User Account
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

## 4. Obtain Ticket

### Objective

Allow a customer to join the appropriate queue and receive a ticket for a selected branch and service.

### Workflow

```text
Customer
   ↓
Select Branch
   ↓
Select Service
   ↓
Request Ticket
   ↓
System Creates Ticket
   ↓
Customer Joins Queue
```

### Involved Actors

- Customer

---

## 5. Call Next Customer

### Objective

Allow an operator to call the next eligible customer from the queue for the assigned service.

### Workflow

```text
Operator
   ↓
Request Next Customer
   ↓
System Selects the Next Eligible Ticket
   ↓
Ticket Status is Updated
   ↓
Customer is Notified
```

### Involved Actors

- Operator
- Customer

---

## 6. Complete Service

### Objective

Allow an operator to complete the current customer service and record the service interaction.

### Workflow

```text
Operator
   ↓
Complete Customer Service
   ↓
Ticket Status is Updated
   ↓
Service Duration is Recorded
   ↓
Service Log is Created
   ↓
Counter Becomes Available
```

### Involved Actors

- Operator

---

## 7. Manage Services

### Objective

Allow a Branch Administrator to manage the services provided by their branch.

### Workflow

```text
Branch Administrator
   ↓
Select Service
   ↓
Create / Update / Deactivate Service
   ↓
System Updates Service Information
```

### Involved Actors

- Branch Administrator

---

## 8. Manage Counters

### Objective

Allow a Branch Administrator to manage the specialized counters within their branch.

### Workflow

```text
Branch Administrator
   ↓
Create / Update / Deactivate Counter
   ↓
System Updates Counter Information
```

### Involved Actors

- Branch Administrator

---

## 9. Manage Operators

### Objective

Allow a Branch Administrator to manage operators assigned to their branch.

### Workflow

```text
Branch Administrator
   ↓
Register / Update / Deactivate Operator
   ↓
Assign Operator to Counter
   ↓
System Updates Operator Information
```

### Involved Actors

- Branch Administrator

---

## 10. Manage Branches

### Objective

Allow a Global Administrator to manage branches across the organization.

### Workflow

```text
Global Administrator
   ↓
Create / Update Branch
   ↓
Activate / Deactivate Branch
   ↓
System Updates Branch Information
```

### Involved Actors

- Global Administrator

---

## 11. Manage Branch Administrators

### Objective

Allow a Global Administrator to create and manage Branch Administrators for individual branches.

### Workflow

```text
Global Administrator
   ↓
Register / Update / Deactivate Branch Administrator
   ↓
Assign Branch Administrator to a Branch
   ↓
System Updates Administrator Information
```

### Involved Actors

- Global Administrator

---

## 12. Estimate Waiting Time

### Objective

Provide an estimated waiting time for customers based on the current queue and recent service performance.

### Workflow

```text
Customer
   ↓
Request Waiting Time
   ↓
System Retrieves Queue Information
   ↓
System Retrieves Recent Service Data
   ↓
Waiting Time is Calculated
   ↓
Estimated Wait Time is Displayed
```

### Involved Actors

- Customer

---

## 13. View Operational Statistics

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
