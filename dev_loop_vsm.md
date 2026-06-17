# AI-Native Software Delivery Value Stream

This document describes the end-to-end engineering value stream, split into:
- Executive view (high-level flow)
- Engineering execution view (detailed operational lifecycle)

---

# 1. Executive View (High-Level)

This view is intended for product, leadership, and cross-functional alignment.

```mermaid
flowchart TD

A[Business Need] --> B[PRD + Success Metrics]
B --> C[Architecture & ADRs]
C --> D[Build & Implement]
D --> E[Testing & Validation]
E --> F[Release to Production]
F --> G[Measure Business Outcomes]
G --> H[Learn & Iterate]

H --> B
```

---

# 2. Engineering Execution View (Detailed System)

This is the operational model used during development and delivery.

```mermaid
flowchart TD

%% =========================
%% DISCOVERY
%% =========================
A[Business Need]
B[PRD + Success Metrics]

A --> B

%% =========================
%% ARCHITECTURE
%% =========================
C[Architecture Design]
D[ADR Creation]
E[ADR Review]

B --> C --> D --> E

%% =========================
%% DELIVERY PLANNING
%% =========================
F[PR Decomposition]
G[Simplification Pass]
H[Speckit Specification]

E --> F --> G --> H

%% =========================
%% IMPLEMENTATION LOOP
%% =========================
I[Implementation PRs]

H --> I

J[Automated Review]
K[Domain Expert Review]

I --> J --> K

K --> L{Changes Required?}
L -- Yes --> I
L -- No --> M[Merge to Staging]

%% =========================
%% VALIDATION
%% =========================
N[Integration Testing]
O[Manual QA / E2E Testing]
P[Production Deployment]
Q[Observability Active]
R[Business Outcome Validation]
S[Retrospective & Learnings]

M --> N --> O --> P --> Q --> R --> S

%% =========================
%% FEEDBACK LOOPS
%% =========================
S --> B
S --> C
S --> H
```

---

# 3. Key Principles

## 3.1 Every phase produces an artifact
- PRD
- ADRs
- Speckit
- PRs
- Tests
- Metrics
- Observability dashboards

---

## 3.2 Every phase has a quality gate
Work cannot proceed without:
- Clear inputs
- Defined outputs
- Explicit review criteria

---

## 3.3 Feedback is mandatory
Production outcomes feed directly back into:
- Product discovery
- Architecture decisions
- Implementation specifications

---

## 3.4 Separation of concerns
- PRD = what + why
- ADR = how (system-level)
- Speckit = how (implementation-level)
- PR = execution

---

# 4. Optional Extension (Operational Layering)

If running locally, the implementation phase assumes:

- Frontend server
- API server
- Database
- Worker processes
- Queue/pubsub
- Observability stack

(Recommended to map these to fixed ports in local dev environments)
