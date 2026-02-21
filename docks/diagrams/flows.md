# System Diagrams

This document contains the main architecture and flow diagrams for the Commenting System.

---

## 1. High Level Architecture (Microservices)

```mermaid
flowchart LR
    U[Client] --> A[Auth Service - Go]
    U --> C[Commenting Service - .NET]

    A --> ADB[(Auth DB - PostgreSQL)]
    C --> CDB[(Commenting DB - PostgreSQL)]

    C <--> A
```

---

## 2. Login Flow (Auth Service)

```mermaid
sequenceDiagram
    participant U as User
    participant A as Auth Service
    participant DB as Auth DB

    U->>A: POST /login (username, password)
    A->>DB: Verify password hash
    DB-->>A: OK
    A-->>U: Return JWT (access token)
```

---

## 3. Create Comment Flow

```mermaid
sequenceDiagram
    participant U as User
    participant C as Commenting Service
    participant DB as Commenting DB

    U->>C: POST /comments (Bearer JWT, content)
    C->>C: Validate JWT (signature + expiry)
    C->>C: Validate input
    C->>DB: Insert comment(userId, content)
    DB-->>C: OK
    C-->>U: 201 Created
```

---

## 4. Delete Comment Flow (Authorization Check)

```mermaid
sequenceDiagram
    participant U as User
    participant C as Commenting Service
    participant DB as Commenting DB

    U->>C: DELETE /comments/{id} (Bearer JWT)
    C->>C: Validate JWT
    C->>DB: SELECT ownerId FROM comments
    DB-->>C: ownerId

    alt User is owner OR role is admin/mod
        C->>DB: DELETE comment
        DB-->>C: OK
        C-->>U: 204 No Content
    else Not authorized
        C-->>U: 403 Forbidden
    end
```

---

## 5. Service-to-Service (JWT Public Key Fetch)

```mermaid
sequenceDiagram
    participant C as Commenting Service
    participant A as Auth Service

    C->>A: GET /.well-known/jwks.json
    A-->>C: Return public keys (JWKS)
    C->>C: Cache keys for token validation
```
