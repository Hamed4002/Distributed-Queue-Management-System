# Functional Requirements

This document defines the functional requirements of the Distributed Real-Time Queue Management System. These requirements describe the operations and capabilities that the system must provide to its users.

---

## 1. User Registration and Authentication

**FR-01** — The system shall allow customers to create an account. Public registration shall create only Customer accounts.

**FR-02** — The system shall validate the information provided during customer registration.

**FR-03** — The system shall allow registered users to authenticate using their credentials.

**FR-04** — The system shall identify the authenticated user's role.

**FR-05** — The system shall restrict access to protected operations based on the user's role and authorized scope.

**FR-06** — The system shall provide an initial Global Administrator account when it is first set up. (The provisioning mechanism is defined in `technical-requirements.md`.)

---

## 2. Branch Comparison and Selection

**FR-07** — The system shall allow customers to select a service and a city before selecting a branch. The service shall be chosen from the active catalog Services, and the city from the list provided by FR-14.If the customer already has an active ticket (WAITING or CALLED), the system shall inform the customer and shall not allow the selection of a city and the comparison of branches to proceed.

**FR-08** — The system shall allow customers to optionally provide their geographical location.

**FR-09** — The system shall identify active branches within the selected city that provide the selected service through an active Branch Service and have at least one active counter for it. An active counter is a counter that is itself active and has an active operator assigned to it.

**FR-10** — The system shall calculate the estimated waiting time for the selected service at each eligible branch.

**FR-11** — If a customer provides their location, the system shall calculate the straight-line distance (Haversine) in kilometers, with one decimal place, between the customer and each eligible branch using geographical coordinates.

**FR-12** — The system shall display eligible branches and their estimated waiting times, including distance information when the customer's location is available.

**FR-13** — The system shall allow customers to select an eligible branch before obtaining a ticket.

**FR-14** — The system shall provide the list of cities that have at least one active branch, so that customers select a city from it. City matching shall be case-insensitive and ignore leading and trailing whitespace.

**FR-15** — The system shall validate the geographical coordinates provided by customers (latitude between −90 and 90, longitude between −180 and 180) and reject invalid values. The customer's location shall be used only for the calculation and shall not be stored.

---

## 3. Ticket Management

**FR-16** — The system shall issue a ticket for the selected branch and service. Before issuing, the system shall verify that the branch, the catalog Service and the Branch Service are active, that the branch has at least one active counter for the service, and that the customer has no other active ticket (`WAITING` or `CALLED`). A request that fails any of these checks shall be rejected with a clear reason.

**FR-17** — The system shall assign a valid ticket number to each issued ticket, unique within branch + service + calendar day, starting from 1 each day (based on the branch's time zone).

**FR-18** — The system shall place the issued ticket in the appropriate service queue.

**FR-19** — The system shall provide the customer with ticket information after successful ticket issuance.

**FR-20** — The system shall maintain the current state of each ticket throughout its lifecycle (`WAITING`, `CALLED`, `COMPLETED`, `CANCELLED`, `NO_SHOW`) and shall allow only the transitions defined in BR-17.

**FR-21** — The system shall allow customers to cancel their own tickets only while their status is `WAITING` and shall reject cancellation requests after a ticket has been called or when the ticket belongs to another customer.

**FR-22** — The system shall update the relevant queue when a ticket is cancelled.

**FR-23** — In addition to real-time updates, the system shall provide customers with the current status of their own tickets (queue position and estimated waiting time while the ticket is `WAITING`, counter information while it is `CALLED`) when they open the application and after they reconnect, and shall reject requests for tickets belonging to other customers.

---

## 4. Queue Management

**FR-24** — The system shall allow an operator to view the queue associated with their assigned counter and service.

**FR-25** — The system shall allow an operator to call the next eligible customer from the queue. The next eligible customer is the one holding the `WAITING` ticket with the earliest issue time in the queue (FIFO).

**FR-26** — The system shall update the status of the called ticket to `CALLED` and record the time of the call.

**FR-27** — The system shall notify the corresponding customer when their ticket is called.

**FR-28** — The system shall allow an operator to complete the current service for a ticket in `CALLED` status.

**FR-29** — The system shall update the ticket status to `COMPLETED` after service completion.

**FR-30** — The system shall make the counter available for the next customer after the current service is completed or after a valid No-Show is recorded.

**FR-31** — The system shall, when a counter or operator is deactivated while handling a `CALLED` ticket, return that ticket to `WAITING`, make it available to another active counter of the same service, and notify the customer in real time. No Service Log shall be created for the interrupted service.

---

## 5. No-Show Management

**FR-32** — The system shall allow an operator to mark a ticket as `NO_SHOW` only after at least 60 seconds have elapsed since the ticket was called, provided that the operator explicitly confirms the customer has not appeared and the ticket remains in the `CALLED` status.

**FR-33** — The system shall update the ticket status when a No-Show is recorded.

**FR-34** — The system shall make the counter available and allow the queue to continue after a No-Show ticket has been handled. No Service Log shall be created for a No-Show ticket.

---

## 6. Service Logging and Waiting Time

**FR-35** — The system shall record completed service interactions in the Service Log.

**FR-36** — The system shall record the duration of completed service interactions, measured from the time the ticket was called to the time the service was completed.

**FR-37** — The system shall calculate estimated waiting times using the current queue size, recent average service duration, and the number of active counters assigned to the selected service, according to: `estimated waiting time = (number of WAITING tickets × average service duration) ÷ number of active counters`. When the queue is empty, the estimated waiting time is zero.

**FR-38** — The system shall calculate the average service duration using service interactions recorded during the previous 30 minutes for the same queue. If there are none, the system shall use the average of the most recently recorded services for that queue (the number of services considered is configurable; default 10). If the queue has no service history, the system shall report the waiting time as unknown.

---

## 7. Service Management

**FR-39** — The system shall allow a Branch Administrator to enable a catalog Service for their branch, creating the corresponding Branch Service.

**FR-40** — The system shall allow a Branch Administrator to activate a Branch Service within their branch.

**FR-41** — The system shall allow a Branch Administrator to deactivate a Branch Service within their branch.

**FR-42** — The system shall allow customers to select only Services that are active in the catalog and provided through at least one active Branch Service.

**FR-43** — The system shall allow a Global Administrator to create and update Services in the service catalog.

**FR-44** — The system shall allow a Global Administrator to activate and deactivate Services in the service catalog. Deactivating a catalog Service shall block new tickets for it in all branches, while tickets already issued continue through their normal lifecycle.

---

## 8. Counter Management

**FR-45** — The system shall allow a Branch Administrator to create a counter within their branch.

**FR-46** — The system shall allow a Branch Administrator to activate a counter within their branch.

**FR-47** — The system shall allow a Branch Administrator to deactivate a counter within their branch.

**FR-48** — The system shall associate each counter with its designated Branch Service.

---

## 9. Operator Management

**FR-49** — The system shall allow a Branch Administrator to register an operator for their branch with a temporary password. The temporary password shall be shown to the creator only once and stored only in hashed form.

**FR-50** — The system shall allow a Branch Administrator to update an operator's information.

**FR-51** — The system shall allow a Branch Administrator to activate an operator.

**FR-52** — The system shall allow a Branch Administrator to deactivate an operator.

**FR-53** — The system shall allow a Branch Administrator to assign an operator to a counter. A new assignment ends the operator's previous assignment and the counter's previous operator assignment, and the history of assignments shall be retained for reporting.

**FR-54** — The system shall ensure that operator management is restricted to the administrator's assigned branch.

---

## 10. Branch Management

**FR-55** — The system shall allow a Global Administrator to create a branch, including its address, city, geographical coordinates and time zone.

**FR-56** — The system shall allow a Global Administrator to update branch information.

**FR-57** — The system shall allow a Global Administrator to activate a branch.

**FR-58** — The system shall allow a Global Administrator to deactivate a branch.

**FR-59** — The system shall prevent new ticket issuance for a deactivated branch. Tickets already issued shall continue through their normal lifecycle.

---

## 11. Branch Administrator Management

**FR-60** — The system shall allow a Global Administrator to register a Branch Administrator with a temporary password. The temporary password shall be shown to the creator only once and stored only in hashed form.

**FR-61** — The system shall allow a Global Administrator to update a Branch Administrator's information.

**FR-62** — The system shall allow a Global Administrator to activate a Branch Administrator.

**FR-63** — The system shall allow a Global Administrator to deactivate a Branch Administrator.

**FR-64** — The system shall allow a Global Administrator to assign a Branch Administrator to a branch.

**FR-65** — The system shall require every account created with a temporary password to change it at first login, and shall permit no operation other than changing the password until it has been changed.

---

## 12. Real-Time Communication

**FR-66** — The system shall provide real-time updates for relevant queue events.

**FR-67** — The system shall notify connected customers in real time when their ticket is called, without requiring a page refresh. If the customer is not connected at that moment, the ticket status shall remain persisted and the customer shall receive the current ticket status after reconnecting.

**FR-68** — The system shall provide real-time queue updates to relevant operators.

**FR-69** — The system shall ensure that real-time events are delivered only to the relevant branch and authorized users.

---

## 13. Operational Statistics

**FR-70** — The system shall provide administrators with queue and operational statistics within their authorized scope.

**FR-71** — The system shall provide hourly traffic statistics.

**FR-72** — The system shall provide average waiting-time statistics.

**FR-73** — The system shall provide statistical data in a format suitable for frontend visualization.

---

## 14. Global Administrator Access to Branch Resources

**FR-74** — The system shall allow a Global Administrator to view (without changing) the Branch Services, counters and operators of all branches.

**FR-75** — The system shall restrict the creation, activation and deactivation of Branch Services, counters and operators to the Branch Administrator of the owning branch, and shall reject such operations when attempted by a Global Administrator.
