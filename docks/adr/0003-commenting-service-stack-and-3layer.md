# 0003 - Commenting BC uses ASP.NET + EF Core with a 3-layer architecture

Date: 2026-02-21

## Status
Accepted

## Context
The commenting domain is not highly domain-rich at this stage.
We want a clean, standard structure without overengineering.

## Decision
Commenting BC will be built using:
- ASP.NET (Web API)
- Entity Framework Core (data access)
- 3-layer architecture:
  - Presentation/API (Controllers)
  - Application/Service (use-cases, validation, orchestration)
  - Infrastructure/Data (EF Core, repositories)

We may apply DDD ideas (entities/value objects) inside the service layer where useful, without forcing a full 4-layer DDD structure.

## Alternatives considered
- Full DDD (4 layers) for commenting
- Minimal “routes only” approach

## Consequences
+ Simple, familiar structure
+ Testable business logic in service layer
- If domain grows significantly, we may refactor into richer domain model / additional layers
