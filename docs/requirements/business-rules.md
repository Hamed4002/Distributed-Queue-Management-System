# Business Rules

This document defines the business rules and domain constraints that govern the behavior of the queue management system.

---

## 1. Branch and Service Rules

**BR-01** — A branch may provide one or more services. A branch provides a service through a **Branch Service** (see BR-04).

**BR-02** — A counter belongs to exactly one branch.

**BR-03** — Each counter is dedicated to exactly one Branch Service, that is, to one service provided by the counter's own branch.

**BR-04** — A **Service** is a branch-independent definition in the organization-wide service catalog (for example, "Bank Account Operations"). A **Branch Service** represents the provision of one catalog Service at exactly one branch and has its own active or inactive status. A Branch Service is created from a catalog Service; a branch cannot provide a service that does not exist in the catalog.

**BR-05** — A customer can request a service only from an active branch.

**BR-06** — A customer can request only a Service that is active in the catalog and is provided by the selected branch through an active Branch Service.

**BR-07** — A customer can compare only active branches within the selected city that provide the selected Service through an active Branch Service and have at least one active counter for it. An **active counter** is a counter that is itself active and has an active operator assigned to it. A branch without an active counter for the selected service is not eligible: it is not displayed in the comparison and no ticket can be issued in it.

---

## 2. Location and Branch Comparison Rules

**BR-08** — A customer must select a service and a city before comparing eligible branches.

**BR-09** — Providing the customer's location is optional.

**BR-10** — If a customer provides a location, the system shall calculate the straight-line distance over the Earth's surface (Haversine formula) between the customer and each eligible branch using their geographical coordinates. The distance is expressed in kilometers with one decimal place. The value is approximate and does not represent the actual road distance.

**BR-11** — If a customer does not provide a location, branch distance shall not be calculated.

**BR-12** — The customer must select an eligible branch before obtaining a ticket.

**BR-61** — City matching is case-insensitive and ignores leading and trailing whitespace.

---

## 3. Ticket and Queue Rules

**BR-13** — A ticket belongs to exactly one branch and one service (one Branch Service).

**BR-14** — A ticket is placed in the queue corresponding to its branch and service.

**BR-15** — Ticket numbers are unique within the scope of **branch + service + calendar day**. Numbers start at 1, increase sequentially, and restart from 1 each day for every queue. The day boundary is determined by the branch's time zone.

**BR-16** — Every ticket is in exactly one of the following statuses: `WAITING`, `CALLED`, `COMPLETED`, `CANCELLED`, `NO_SHOW`. A ticket may only change status through the transitions below.

| From       | To          | Triggered by       | Condition                                                                                      |
| ---------- | ----------- | ------------------ | ---------------------------------------------------------------------------------------------- |
| (creation) | `WAITING`   | Customer           | Ticket issuance                                                                                |
| `WAITING`  | `CALLED`    | Operator           | Call the next customer                                                                         |
| `WAITING`  | `CANCELLED` | Customer           | Allowed only while status is `WAITING`                                                         |
| `CALLED`   | `COMPLETED` | Operator           | Service completion                                                                             |
| `CALLED`   | `NO_SHOW`   | Operator           | At least 60 seconds have elapsed since the ticket was called and the customer has not appeared |
| `CALLED`   | `WAITING`   | System (automatic) | The counter or operator that called the ticket is deactivated (see BR-57)                      |

`COMPLETED`, `CANCELLED` and `NO_SHOW` are final statuses; no further transition is allowed from them.

**BR-17** — A customer can only receive service for a valid ticket associated with the selected branch and service.

**BR-18** — A customer may cancel their ticket only while its status is `WAITING`. Once the ticket has been called, it cannot be cancelled by the customer.

**BR-19** — A cancelled ticket cannot be called or served.

**BR-56** — A customer may hold at most one active ticket (status `WAITING` or `CALLED`) in the entire system at any time. A ticket request made while the customer already has an active ticket is rejected, including when such requests arrive concurrently. After the ticket reaches `COMPLETED`, `CANCELLED` or `NO_SHOW`, the customer may obtain a new ticket. Comparing branches is not restricted by this rule.

**BR-58** — Tickets in each queue (branch + service) are called in FIFO order: the `WAITING` ticket with the earliest issue time is called first. If issue times are equal, the smaller ticket number is called first.

**BR-63** — A customer can view and cancel only their own tickets. A request that refers to a ticket belonging to another customer is rejected.

---

## 4. Operator Rules

**BR-20** — An operator must be assigned to a specific branch.

**BR-21** — At any moment, an operator is assigned to exactly one counter within their branch. Over time, a counter may be assigned to different operators (for example, in different shifts), but it has at most one active assigned operator at any moment. An operator without a counter assignment cannot perform queue operations.

**BR-22** — An operator can serve customers only through their assigned counter.

**BR-23** — An operator can call customers only from the queue associated with the counter's designated service.

**BR-24** — A deactivated operator cannot perform queue operations.

**BR-25** — A ticket must not be processed simultaneously by multiple operators.

**BR-57** — If a counter or operator is deactivated while handling a ticket in `CALLED` status, the ticket automatically returns to `WAITING` and becomes available to another active counter of the same service. The customer is notified of the status change in real time, and the 60-second No-Show timer restarts at the next call. Because ordering is by issue time (BR-58), the ticket keeps its original position in the queue. A service interrupted in this way is not considered completed and produces no Service Log.

---

## 5. Service Completion and No-Show Rules

**BR-26** — A service interaction must be associated with a valid ticket and operator.

**BR-27** — A completed service must produce a corresponding Service Log.

**BR-28** — Service duration is calculated from the time the ticket was called (`CALLED`) to the time the service was completed (`COMPLETED`). Consequently, the duration includes the time the customer takes to reach the counter.

**BR-29** — A counter becomes available for another customer after the current service is completed or after a valid `NO_SHOW` is recorded for its ticket.

**BR-30** — An operator may mark a ticket as `NO_SHOW` only after at least 60 seconds have elapsed since the ticket was called, provided that the operator confirms the customer has not appeared and the ticket remains in the `CALLED` status.

**BR-31** — A `NO_SHOW` ticket is final and cannot be served again. The customer must obtain a new ticket to receive service.

**BR-32** — A No-Show ticket does not produce a completed Service Log because the service was not provided.

---

## 6. Resource Status Rules

**BR-33** — A deactivated branch cannot issue new tickets. Deactivating a branch does not affect tickets already issued: they continue through the normal lifecycle, and the branch's active counters and operators continue to process them.

**BR-34** — A deactivated service cannot receive new tickets. Deactivating a catalog Service blocks new tickets for it in every branch; deactivating a Branch Service blocks new tickets for it in that branch only. Tickets already issued continue through their normal lifecycle.

**BR-35** — A deactivated counter cannot call new customers or serve customers. Any ticket in `CALLED` status at that counter is handled according to BR-57.

**BR-36** — Existing historical records associated with deactivated resources must remain available for reporting and auditing purposes.

---

## 7. Administrative Rules

**BR-37** — A Branch Administrator is assigned to a specific branch.

**BR-38** — A Branch Administrator can manage only resources belonging to their assigned branch.

**BR-39** — A Global Administrator can manage branches and the service catalog across the organization. For the Branch Services, counters and operators of any branch, a Global Administrator can only view them and deactivate them in emergency situations; enabling services for a branch, creating counters and registering operators remain the responsibility of the Branch Administrator.

**BR-40** — A Global Administrator can create and manage Branch Administrators.

**BR-41** — A Branch Administrator can create and manage operators within their assigned branch.

**BR-42** — A Branch Administrator can create and manage counters within their assigned branch.

**BR-43** — A Branch Administrator can manage the services provided by their assigned branch by enabling catalog Services for the branch and activating or deactivating the corresponding Branch Services. A Branch Administrator cannot create or modify catalog Services.

**BR-62** — A Branch Administrator may activate a deactivated Branch Service, counter or operator unless it was deactivated as an emergency action by a Global Administrator. A resource deactivated as an emergency action may be reactivated only by a Global Administrator.

**BR-64** — Catalog Services are created, updated, activated and deactivated only by Global Administrators.

---

## 8. Access Control Rules

**BR-44** — Users must be authenticated before accessing protected system operations.

**BR-45** — Access to protected operations is determined by the user's role.

**BR-46** — Users must not access resources outside their authorized scope.

**BR-59** — Public registration creates only accounts with the Customer role. Operator, Branch Administrator and Global Administrator accounts cannot be created through public registration.

**BR-60** — An account created by an administrator with a temporary password must change its password at first login. Until the password is changed, the only permitted operation is changing the password.

---

## 9. Waiting Time Rules

**BR-47** — Estimated waiting time is calculated based on the current queue and recent service performance.

**BR-48** — The average service time is calculated from the services completed in the previous 30 minutes for the same queue (branch + service). If no service was completed in that period, the average of the most recently recorded services for the same queue (at any time) is used. The number of recent services considered is configurable, with a default of 10. If the queue has no recorded service history at all, the waiting time is shown as "unknown", and ticket issuance remains allowed.

**BR-49** — Waiting-time estimation takes into account the number of active counters assigned to the selected service, as defined in BR-07.

---

## 10. Real-Time Queue Rules

**BR-50** — Relevant queue state changes must be propagated to the affected users in real time.

**BR-51** — When a customer's ticket is called, the customer must receive a real-time notification. If the customer is not connected at that moment, the ticket status is still persisted, and the current status is shown to the customer after reconnection. No alternative notification channel is provided.

**BR-52** — Real-time queue events must be limited to the relevant branch and authorized users.

---

## 11. Data Consistency Rules

**BR-53** — Concurrent ticket requests must not result in duplicate ticket numbers.

**BR-54** — Queue operations must preserve the consistency of ticket state and queue order.

**BR-55** — Ticket state changes must follow exactly the transitions defined in BR-16 and must not bypass valid intermediate states.
