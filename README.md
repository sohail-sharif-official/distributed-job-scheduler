# Distributed Lease-Based Job Scheduler (Java 21)

![CI Status](https://github.com/sohail-sharif-official/distributed-job-scheduler/actions/workflows/verify.yml/badge.svg)
![Compliance](https://img.shields.io/badge/Enterprise_NDA-Clean--Room_Reference-10B981?style=flat-square)
**Stack:** `Java 21 · Lease Coordination · Bounded Retries · Dead-Letter Queue`

> [!IMPORTANT]
> **🔒 Enterprise Confidentiality & Clean-Room Reference Notice (For HR & Technical Reviewers)**
> Due to strict **Non-Disclosure Agreements (NDAs)**, SOC2/PCI-DSS security compliance, and proprietary Intellectual Property (IP) governance across enterprise production systems, the full commercial source code, internal infrastructure manifests (Terraform state, Helm charts, internal VPC/IAM topologies), and proprietary third-party adapters cannot be published to public GitHub.
>
> **What is included in this repository:**
> This repository provides a **clean-room, zero-dependency architectural reference core** written from scratch to demonstrate the underlying **domain invariants, state machines, concurrency models, and automated verification suites** without exposing proprietary business logic or client infrastructure. Full architectural walkthroughs and production trade-off discussions are available during technical interviews.

---

## Overview
A distributed task coordination engine implementing time-bounded worker leases, exclusive job claiming, expired-lease recovery, and dead-letter isolation after bounded retry exhaustion.

## Architecture & Execution Flow
```mermaid
flowchart LR
  Queue["Pending Job Queue"] --> Claim["Worker Lease Claim (TTL)"]
  Claim --> Exec["Job Execution"]
  Exec -- "Success" --> Done["Completed"]
  Exec -- "Failure (< Max)" --> Queue
  Exec -- "Exhausted" --> DLQ["Dead-Letter Queue"]
```

## Key Engineering Capabilities
- **Time-Bounded Exclusive Leases:** Prevents concurrent workers from executing the same scheduled job simultaneously.
- **Stale Lease Recovery:** Reclaims jobs from stalled workers once lease TTL expires.
- **Bounded Retry & Dead-Letter Routing:** Transitions persistently failing jobs to a dead-letter state after configured retry limits.
- **Continuous CI Verification:** Executed via `java Main.java` on GitHub Actions.

## Why a Clean-Room Domain Core?
In enterprise microservices, 80% of production incidents stem from broken domain invariants (race conditions, non-idempotent retries, cross-tenant data leakage, or unbalanced state transitions) rather than HTTP/ORM boilerplate. By isolating the **pure domain engine** from proprietary cloud adapters:
1. **Confidentiality & IP Safety:** Zero proprietary client schemas, credentials, or internal endpoints are exposed.
2. **Deterministic Verification:** Reviewers can clone and execute the full invariant test suite in under 2 seconds (`java Main.java`) with zero external database or cloud setup required.
3. **Clear Architectural Boundaries:** Demonstrates strict separation between domain rules and infrastructure delivery.

## Local Verification
Run the self-contained verification suite locally:

```bash
java Main.java
```

## Documentation
- [Architectural Decisions & Trade-offs](docs/DECISIONS.md)
- [Security, NDA & Isolation Model](docs/SECURITY.md)
