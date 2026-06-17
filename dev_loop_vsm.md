flowchart TD

%% =========================
%% DISCOVERY
%% =========================
A[Business Need / Idea] --> B[PRD + Success Metrics]

B --> C[Architecture Design]
C --> D[ADR Creation]
D --> E[ADR Review]

%% =========================
%% PLANNING
%% =========================
E --> F[PR Decomposition]
F --> G[Simplification Pass]
G --> H[Speckit Specification]

%% =========================
%% IMPLEMENTATION LOOP
%% =========================
H --> I[Implementation PR 1..N]

I --> J[Automated Code Review]
J --> K[Domain Expert Review]

K --> L{Fix Required?}
L -- Yes --> I
L -- No --> M[Merge to Staging]

%% =========================
%% VALIDATION
%% =========================
M --> N[Integration Testing]
N --> O[Manual QA / E2E Tests]

O --> P[Production Deployment]
P --> Q[Observability Active]

Q --> R[Business Outcome Validation]
R --> S[Retrospective & Learnings]

%% =========================
%% FEEDBACK LOOPS
%% =========================
S --> B
S --> C
S --> H

%% =========================
%% LOCAL DEV STACK STATES
%% =========================

%% Planning stage
B -.-> B1{{No local stack required}}
C -.-> C1{{No local stack required}}
D -.-> D1{{No local stack required}}

%% Decomposition stage
F -.-> F1{{Light local stack (optional mocks)}}
G -.-> G1{{Light local stack (optional mocks)}}

%% Speckit stage
H -.-> H1{{Partial stack (DB schema + API stubs)}}

%% Implementation stage
I -.-> I1{{FULL LOCAL STACK REQUIRED}}
I1 --> I1a[App server]
I1 --> I1b[Database (Postgres etc.)]
I1 --> I1c[Background workers]
I1 --> I1d[Queue / pubsub]
I1 --> I1e[Frontend dev server]
I1 --> I1f[Observability stack]

%% Review stage
J -.-> J1{{Partial stack (tests + lint)}}
K -.-> K1{{Partial or full stack depending on domain}}

%% Staging
M -.-> M1{{Full staging stack required}}
N -.-> N1{{Full staging stack required}}
O -.-> O1{{Full staging stack required}}

%% Production
P -.-> P1{{Production stack}}
Q -.-> Q1{{Production + monitoring stack}}
