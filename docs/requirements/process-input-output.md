# Process Inputs and Outputs

This document identifies the primary inputs and outputs of each system process from the perspective of interaction between actors and the system.

---

## 1. Register Customer

### Inputs

- Customer name
- Email or username
- Password
- Required registration information

### Outputs

- Customer account
- Registration status
- Validation or error information

**Note:** Public registration creates only Customer accounts.

---

## 2. Authenticate User

### Inputs

- Email or username
- Password

### Outputs

- Authentication result
- Authentication token or session
- User identity and role information
- Indicator that a password change is required (for accounts created with a temporary password)
- Authentication error information, if applicable

**Note:** While a password change is required, the only permitted operation is changing the password.

---

## 3. Authorize User Access

### Inputs

- Authenticated user identity
- Requested resource or operation

### Outputs

- Access decision: granted or denied
- Authorization result
- Error or denial information, if applicable

---

## 4. Compare Eligible Branches

### Inputs

- Customer identity
- Selected service (from the active service catalog)
- Selected city (chosen from the list of cities that have active branches)
- Customer geographical location (optional)

### Outputs

- List of eligible branches: active branches in the city that provide the selected service through an active Branch Service and have at least one active counter for it (an active counter is itself active and has an active operator assigned)
- Estimated waiting time for the selected service at each eligible branch (or "unknown" if the queue has no service history)
- Straight-line distance in kilometers to each eligible branch, if the customer's location is provided
- Branch comparison results
- Information indicating that no eligible branches were found, if applicable
- Validation error, if the provided coordinates are invalid
- Rejection reason, if the customer already has an active ticket (WAITING or CALLED)

---

## 5. Obtain Ticket

### Inputs

- Customer identity
- Selected branch
- Selected service
- Ticket issuance request

### Outputs

- Issued ticket
- Ticket number
- Queue position
- Initial ticket status (`WAITING`)
- Estimated waiting time
- Ticket issuance result or error information, if applicable

**Validation conditions:** The request is rejected, with a clear reason, if the branch, the catalog Service, or the Branch Service is inactive, if the branch has no active counter for the service, or if the customer already has an active ticket (`WAITING` or `CALLED`).

---

## 6. Cancel Ticket

### Inputs

- Customer identity
- Ticket identity
- Ticket cancellation request

### Outputs

- Cancellation result
- Updated ticket status (`CANCELLED`), if cancellation is successful
- Updated queue information, if cancellation is successful
- Rejection reason, if cancellation is not permitted

**Cancellation conditions:** The ticket must belong to the requesting customer. The system shall permit cancellation only while the ticket status is `WAITING`. A cancellation request for a ticket that has already been called, or for a ticket belonging to another customer, shall be rejected.

---

## 7. Call Next Customer

### Inputs

- Operator identity
- Counter identity
- Request to call the next customer

### Outputs

- Selected ticket (the `WAITING` ticket with the earliest issue time in the queue)
- Updated ticket status (`CALLED`) and call time
- Customer notification (real time if connected; otherwise the current status is shown after reconnection)
- Current counter information
- Operation result or error information, if applicable

---

## 8. Handle No-Show Customer

### Inputs

- Operator identity
- Ticket identity
- Request to record a No-Show
- Operator confirmation that the customer has not appeared

### Outputs

- No-Show operation result
- Updated ticket status (`NO_SHOW`), if the operation is successful
- Updated queue information, if the operation is successful
- Counter availability information, if the operation is successful (the counter becomes available for the next customer)

**Validation conditions:** The system shall accept the No-Show request only if at least 60 seconds have elapsed since the ticket was called and the ticket remains in the `CALLED` status. The operator must confirm that the customer has not appeared. A `NO_SHOW` ticket is final; the customer must obtain a new ticket to receive service. No Service Log is created for a No-Show ticket.

---

## 9. Complete Service

### Inputs

- Operator identity
- Current ticket identity (the ticket must be in `CALLED` status)
- Service completion information

### Outputs

- Updated ticket status (`COMPLETED`)
- Service completion time
- Service duration (from the call time to the completion time)
- Service Log record
- Counter availability information
- Operation result or error information, if applicable

---

## 10. Manage Branch Services

### Inputs

- Branch Administrator identity
- Branch identity
- Catalog Service to enable for the branch
- Requested operation (enable, activate, or deactivate a Branch Service)

### Outputs

- Updated Branch Service information
- Branch Service activation status, where applicable
- Operation result or error information

---

## 11. Manage Counters

### Inputs

- Branch Administrator identity
- Branch identity
- Counter information (including its designated Branch Service)
- Requested operation

### Outputs

- Updated counter information
- Counter activation status, where applicable
- Ticket returned to `WAITING` and customer notified, if a counter handling a `CALLED` ticket is deactivated (no Service Log is created for the interrupted service)
- Operation result or error information

---

## 12. Manage Operators

### Inputs

- Branch Administrator identity
- Branch identity
- Operator information
- Temporary password (when registering an operator)
- Counter assignment, where applicable
- Requested operation

### Outputs

- Updated operator information
- Operator assignment information, where applicable (a new assignment ends the previous assignment, and the assignment history is retained)
- Operator activation status, where applicable
- Temporary password, shown once to the creator
- Ticket returned to `WAITING` and customer notified, if an operator handling a `CALLED` ticket is deactivated (no Service Log is created for the interrupted service)
- Operation result or error information, if applicable

---

## 13. Manage Branches

### Inputs

- Global Administrator identity
- Branch information (address, city, geographical coordinates, time zone)
- Requested operation

### Outputs

- Updated branch information
- Branch activation or deactivation status, where applicable
- Operation result or error information

---

## 14. Manage Branch Administrators

### Inputs

- Global Administrator identity
- Branch Administrator information
- Temporary password (when registering a Branch Administrator)
- Branch assignment
- Requested operation

### Outputs

- Updated Branch Administrator information
- Branch assignment information
- Administrator activation status, where applicable
- Temporary password, shown once to the creator
- Operation result or error information

---

## 15. View Operational Statistics

### Inputs

- Administrator identity
- Requested statistics
- Branch or organization scope
- Time range

### Outputs

- Traffic statistics
- Average waiting time
- Queue statistics
- Service performance statistics
- Statistical results within the administrator's authorized scope

---

## 16. View Branch Resources

### Inputs

- Global Administrator identity
- Branch identity (or all branches)
- Resource type (Branch Services, counters, or operators)

### Outputs

- Branch Services, counters, and operators of the requested branch or all branches, with their status
- Operation result or error information

**Note:** This process is read-only. Global Administrators cannot create, activate, or deactivate these resources.

---

## 17. Manage Service Catalog

### Inputs

- Global Administrator identity
- Service information (name and description)
- Requested operation (create, update, activate, or deactivate a catalog Service)

### Outputs

- Updated catalog Service information
- Service activation status, where applicable
- Operation result or error information

**Note:** Deactivating a catalog Service blocks new tickets for it in all branches; tickets already issued continue through their normal lifecycle.
