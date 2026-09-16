# Business Rules

This document defines the business rules and domain constraints that govern the behavior of the queue management system.

---

## 1. Branch and Service Rules

**BR-01** — A branch may provide one or more services.

**BR-02** — A counter belongs to exactly one branch.

**BR-03** — Each counter is dedicated to exactly one service.

**BR-04** — A service must belong to a specific branch.

**BR-05** — A customer can request a service only from an active branch.

**BR-06** — A customer can request only an active service provided by the selected branch.

---

## 2. Ticket and Queue Rules

**BR-07** — A ticket belongs to exactly one branch and one service.

**BR-08** — A customer must select a branch and a service before obtaining a ticket.

**BR-09** — A ticket is placed in the queue corresponding to its branch and service.

**BR-10** — Ticket numbers must be unique within their defined queue scope.

**BR-11** — A ticket follows a defined lifecycle and cannot move directly to an invalid state.

**BR-12** — A customer can only receive service for a valid ticket associated with the selected branch and service.

---

## 3. Operator Rules

**BR-13** — An operator must be assigned to a specific branch.

**BR-14** — An operator is assigned to a counter within their branch.

**BR-15** — An operator can serve customers only through their assigned counter.

**BR-16** — An operator can call customers only from the queue associated with the counter's designated service.

**BR-17** — A deactivated operator cannot perform queue operations.

---

## 4. Service Completion Rules

**BR-18** — A service interaction must be associated with a valid ticket and operator.

**BR-19** — A completed service must produce a corresponding Service Log.

**BR-20** — Service duration is calculated from the service start and completion times.

**BR-21** — A counter becomes available for another customer after the current service is completed.

---

## 5. Resource Status Rules

**BR-22** — A deactivated branch cannot issue new tickets.

**BR-23** — A deactivated service cannot receive new tickets.

**BR-24** — A deactivated counter cannot be used to serve customers.

**BR-25** — Existing historical records associated with deactivated resources must remain available for reporting and auditing purposes.

---

## 6. Administrative Rules

**BR-26** — A Branch Administrator is assigned to a specific branch.

**BR-27** — A Branch Administrator can manage only resources belonging to their assigned branch.

**BR-28** — A Global Administrator can manage branches across the organization.

**BR-29** — A Global Administrator can create and manage Branch Administrators.

**BR-30** — A Branch Administrator can create and manage operators within their assigned branch.

**BR-31** — A Branch Administrator can create and manage counters within their assigned branch.

**BR-32** — A Branch Administrator can manage services provided by their assigned branch.

---

## 7. Access Control Rules

**BR-33** — Users must be authenticated before accessing protected system operations.

**BR-34** — Access to protected operations is determined by the user's role.

**BR-35** — Users must not access resources outside their authorized scope.

---

## 8. Waiting Time Rules

**BR-36** — Estimated waiting time is calculated based on the current queue and recent service performance.

**BR-37** — The average service time used for waiting-time estimation is based on service interactions recorded during the previous 30 minutes.

**BR-38** — Waiting-time estimation takes the number of active counters into account.

---

## 9. Real-Time Queue Rules

**BR-39** — Relevant queue state changes must be propagated to the affected users in real time.

**BR-40** — When a customer's ticket is called, the customer must receive a real-time notification.

**BR-41** — Real-time queue events must be limited to the relevant branch and authorized users.

---

## 10. Data Consistency Rules

**BR-42** — Concurrent ticket requests must not result in duplicate ticket numbers.

**BR-43** — Queue operations must preserve the consistency of ticket state and queue order.

**BR-44** — A ticket must not be processed simultaneously by multiple operators.
::
