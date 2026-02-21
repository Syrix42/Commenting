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

## 2. Create Comment  (Comment Service)
```mermaid
sequenceDiagram
    actor User
    participant System
    participant Moderator

    User->>System: Write Comment
    User->>System: Submit Comment

    System->>System: Validate Comment

    alt Validation Failed
        System-->>User: Show Validation Error
    else Validation Passed
        System->>System: Create Comment (state=Pending)
        System->>Moderator: Send for Moderation

        Moderator->>System: Review Comment

        alt Approved
            System->>System: Set State = Approved
            System-->>User: Notify Approved
        else Rejected
            System->>System: Set State = Rejected (Store Reason)
            System-->>User: Notify Rejected
        end
    end
```    

---

## 3. Delete  Comment Flow

```mermaid
sequenceDiagram
    actor User
    participant System

    User->>System: Request Comment Deletion

    System->>System: Validate Deletion Request (Authorization + State)

    alt Deletion Not Allowed
        System-->>User: Reject Deletion
    else Deletion Allowed
        System->>System: Mark Comment as Deleted
        System->>System: Set DeletedAt, DeletedBy, Reason?
        System-->>User: Notify Deletion Success
    end
```

---

## 4. Edit Comment Flow 

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Moderator

    User->>System: Request Comment Edit (Proposed Content)

    System->>System: Validate Permission + Content

    alt Invalid Request
        System-->>User: Reject Edit Request
    else Valid
        System->>System: Create EditRequest (Pending)
        System->>Moderator: Send EditRequest for Review

        Moderator->>System: Review EditRequest

        alt Approved
            System->>System: Apply Edit Atomically
            System->>System: Update Comment Content
            System->>System: Mark EditRequest Approved
            System-->>User: Notify Approved
        else Rejected
            System->>System: Mark EditRequest Rejected (Store Reason)
            System-->>User: Notify Rejected
        end
    end
```

---

## 5. Replay flow

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Moderator

    User->>System: Write Reply
    User->>System: Submit Reply

    System->>System: Validate Reply (Auth + Integrity + Content)

    alt Validation Failed
        System-->>User: Reject Reply
    else Validation Passed
        System->>System: Create Reply (state=Pending)
        System->>Moderator: Send Reply for Moderation

        Moderator->>System: Review Reply

        alt Approved
            System->>System: Set State = Approved
            System-->>User: Notify Approved
        else Rejected
            System->>System: Set State = Rejected (Store Reason)
            System-->>User: Notify Rejected
        end
    end
```

## 6. Upvote Flow (Toggle Behavior)

```mermaid
sequenceDiagram
    actor User
    participant System

    User->>System: Commit Upvote

    System->>System: Validate Auth + Comment Visibility

    System->>System: Check if Vote Exists

    alt Vote Exists
        System->>System: Remove Vote
        System->>System: Decrement UpvoteCount
        System-->>User: State = Not Voted
    else Vote Not Exists
        System->>System: Create Vote
        System->>System: Increment UpvoteCount
        System-->>User: State = Voted
    end
```    

