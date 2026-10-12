# Non-Functional Requirements

This document defines the key quality attributes and operational constraints of the Distributed Real-Time Queue Management System.

---

## 1. Concurrency and Data Consistency

**NFR-01** — The system shall support concurrent queue operations without compromising data consistency.

**NFR-02** — The system shall prevent race conditions during concurrent ticket issuance, ticket cancellation, customer calling, service completion, and No-Show recording. This includes preventing a customer from obtaining more than one active ticket through concurrent requests.

**NFR-03** — The system shall support at least **100 concurrent ticket requests within one second** without generating duplicate ticket numbers and without losing any valid ticket. Response latency, error rate and throughput under this load shall be measured with k6 and reported; no mandatory numeric latency threshold is defined.

**NFR-04** — Failed transactional operations shall not leave the system in an inconsistent state.

---

## 2. Real-Time Communication

**NFR-05** — Relevant queue and ticket state changes shall be delivered to connected clients in real time.

**NFR-06** — Connected customers shall receive a real-time notification when their ticket is called, without requiring a page refresh. A customer who is not connected at that moment shall be shown the current ticket status after reconnecting.

**NFR-07** — Real-time events shall be delivered only to the relevant branch and authorized users.

---

## 3. Performance and High Load

**NFR-08** — The system shall remain responsive under normal operating conditions. Responsiveness shall be assessed through k6 measurements, which are recorded and reported.

**NFR-09** — The system shall maintain stable behavior as the number of concurrent requests increases.

**NFR-10** — System performance and stability shall be evaluated under high-load conditions.

---

## 4. Reliability

**NFR-11** — Application and database errors shall be handled without causing unexpected server termination.

**NFR-12** — The system shall preserve required historical service and queue data for reporting and analysis.

**NFR-13** — The system shall provide sufficient logging to support debugging and operational analysis.

---

## 5. Security and Access Control

**NFR-14** — Protected system operations shall require user authentication and authorization.

**NFR-15** — Users shall not be able to access resources outside their authorized branch or organizational scope.

---

## 6. Scalability

**NFR-16** — The system architecture shall support multiple branches.

**NFR-17** — The system shall support the addition of application instances without requiring major changes to the core queue management logic.

---

## 7. Maintainability

**NFR-18** — The system shall follow a modular backend structure with clear separation of responsibilities.
