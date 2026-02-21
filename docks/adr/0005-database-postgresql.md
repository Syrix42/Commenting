# 0005 - Use PostgreSQL as the database

Date: 2026-02-21

## Status
Accepted

## Context
We need a reliable relational database for users, comments, and related constraints (FKs, indexes, transactions).

## Decision
We will use PostgreSQL for persistent storage in both services (logical separation per service).

## Alternatives considered
- MySQL
- MongoDB / document stores

## Consequences
+ Strong relational guarantees, indexing, constraints
+ Common ecosystem support (.NET + Go)
- Need migrations strategy per service
- Must decide cross-service data boundaries (no shared tables)
