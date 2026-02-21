# 0004 - Auth BC uses Go + Fiber + sqlx with layered (4-layer) architecture

Date: 2026-02-21

## Status
Accepted

## Context
An auth service implementation already exists in Go.
It uses a layered architecture that we want to preserve with only minor changes.

## Decision
Auth BC will use:
- Go (Golang)
- Fiber (HTTP framework)
- sqlx (data access)
- Layered architecture (4 layers), e.g.:
  - Transport/HTTP
  - Application/Use-cases
  - Domain (auth rules, token rules)
  - Infrastructure (DB via sqlx, external integrations)

## Alternatives considered
- Rewriting auth in .NET to match the commenting stack
- Combining auth into the commenting service

## Consequences
+ Reuse existing implementation
+ Keeps auth generic and reusable
- Two stacks increases team/tooling complexity
- Must standardize contracts between services
