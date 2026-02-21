# System Diagrams

## High Level Architecture

```mermaid
flowchart LR
  U[Client] --> A[Auth Service (Go)]
  U --> C[Commenting Service (.NET)]

  A --> ADB[(Auth DB - PostgreSQL)]
  C --> CDB[(Commenting DB - PostgreSQL)]

  C <--> A
  
  
  sequenceDiagram
  participant U as User
  participant A as Auth Service
  participant DB as Auth DB

  U->>A: POST /login
  A->>DB: Verify password
  DB-->>A: OK
  A-->>U: Return JWT
