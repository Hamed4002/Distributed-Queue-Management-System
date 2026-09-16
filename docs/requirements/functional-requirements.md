# Functional Requirements

This document defines the functional requirements of the Distributed Real-Time Queue Management System. These requirements describe the operations and capabilities that the system must provide to its users.

---

## 1. User Registration and Authentication

**FR-01** — The system shall allow customers to create an account.

**FR-02** — The system shall validate the information provided during customer registration.

**FR-03** — The system shall allow registered users to authenticate using their credentials.

**FR-04** — The system shall identify the authenticated user's role.

**FR-05** — The system shall restrict access to protected operations based on the user's role and authorized scope.

---

## 2. Ticket Management

**FR-06** — The system shall allow an authenticated customer to select an active branch.

**FR-07** — The system shall allow a customer to select an active service within the selected branch.

**FR-08** — The system shall issue a ticket for the selected branch and service.

**FR-09** — The system shall assign a valid ticket number to each issued ticket.

**FR-10** — The system shall place the issued ticket in the appropriate service queue.

**FR-11** — The system shall provide the customer with ticket information after successful ticket issuance.

**FR-12** — The system shall maintain the current state of each ticket throughout its lifecycle.

---

## 3. Queue Management

**FR-13** — The system shall allow an operator to view the queue associated with their assigned counter and service.

**FR-14** — The system shall allow an operator to call the next eligible customer from the queue.

**FR-15** — The system shall update the status of the called ticket.

**FR-16** — The system shall notify the corresponding customer when their ticket is called.

**FR-17** — The system shall allow an operator to complete the current service.

**FR-18** — The system shall update the ticket status after service completion.

**FR-19** — The system shall make the counter available for the next customer after the current service is completed.

---

## 4. Service Logging and Waiting Time

**FR-20** — The system shall record completed service interactions in the Service Log.

**FR-21** — The system shall record the duration of completed service interactions.

**FR-22** — The system shall calculate an estimated waiting time for customers.

**FR-23** — The system shall use recent service performance and current queue conditions when calculating the estimated waiting time.

---

## 5. Service Management

**FR-24** — The system shall allow a Branch Administrator to create a service within their branch.

**FR-25** — The system shall allow a Branch Administrator to activate a service within their branch.

**FR-26** — The system shall allow a Branch Administrator to deactivate a service within their branch.

**FR-27** — The system shall allow customers to select only active services available within the selected branch.

---

## 6. Counter Management

**FR-28** — The system shall allow a Branch Administrator to create a counter within their branch.

**FR-29** — The system shall allow a Branch Administrator to activate a counter within their branch.

**FR-30** — The system shall allow a Branch Administrator to deactivate a counter within their branch.

**FR-31** — The system shall associate each counter with its designated service.

---

## 7. Operator Management

**FR-32** — The system shall allow a Branch Administrator to register an operator for their branch.

**FR-33** — The system shall allow a Branch Administrator to update an operator's information.

**FR-34** — The system shall allow a Branch Administrator to activate an operator.

**FR-35** — The system shall allow a Branch Administrator to deactivate an operator.

**FR-36** — The system shall allow a Branch Administrator to assign an operator to a counter.

**FR-37** — The system shall ensure that operator management is restricted to the administrator's assigned branch.

---

## 8. Branch Management

**FR-38** — The system shall allow a Global Administrator to create a branch.

**FR-39** — The system shall allow a Global Administrator to update branch information.

**FR-40** — The system shall allow a Global Administrator to activate a branch.

**FR-41** — The system shall allow a Global Administrator to deactivate a branch.

**FR-42** — The system shall prevent new queue operations for a deactivated branch.

---

## 9. Branch Administrator Management

**FR-43** — The system shall allow a Global Administrator to register a Branch Administrator.

**FR-44** — The system shall allow a Global Administrator to update a Branch Administrator's information.

**FR-45** — The system shall allow a Global Administrator to activate a Branch Administrator.

**FR-46** — The system shall allow a Global Administrator to deactivate a Branch Administrator.

**FR-47** — The system shall allow a Global Administrator to assign a Branch Administrator to a branch.

---

## 10. Real-Time Communication

**FR-48** — The system shall provide real-time updates for relevant queue events.

**FR-49** — The system shall notify customers in real time when their ticket is called.

**FR-50** — The system shall provide real-time queue updates to relevant operators.

**FR-51** — The system shall ensure that real-time events are delivered only to the relevant branch and authorized users.

---

## 11. Operational Statistics

**FR-52** — The system shall provide administrators with queue and operational statistics within their authorized scope.

**FR-53** — The system shall provide hourly traffic statistics.

**FR-54** — The system shall provide average waiting-time statistics.

**FR-55** — The system shall provide statistical data in a format suitable for frontend visualization.
