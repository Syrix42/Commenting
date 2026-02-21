# 0002 - Use microservices with two bounded contexts

Date: 2026-02-21

## Status
Accepted

## Context
The system has two clear concerns:
1) Authentication/Authorization (generic and reusable)
2) Commenting domain (core business capability)

Auth already exists as a separate service and should remain independent.

## Decision
We will implement two services (microservice-style):
- Auth BC (Generic) as an independent service
- Commenting BC (Core) as an independent service

They communicate via machine-to-machine calls.

## Alternatives considered
- Monolith containing auth + commenting
- Modular monolith with internal modules

## Consequences
+ Independent deployment and scaling
+ Clear separation of responsibilities
- Requires service-to-service authentication and versioned contracts
- Adds operational complexity (network, retries, observability)
