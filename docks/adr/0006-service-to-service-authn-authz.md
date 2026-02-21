# 0006 - Secure service-to-service communication between Auth and Commenting

Date: 2026-02-21

## Status
Proposed (or Accepted)

## Context
Commenting service must validate user identity and permissions.
Auth is a separate service, so Commenting needs a secure way to trust the identity/claims.

## Decision
We will use token-based authentication:
- Users authenticate with Auth service and receive an access token (JWT).
- Commenting service validates the JWT (signature + expiry) and enforces authorization rules.
- For machine-to-machine calls (if Commenting calls Auth), we will use:
  - m2m credentials (client id/secret) OR
  - a service token (JWT) issued for service identity

Authorization rules for comments:
- Create: authenticated users
- Update/Delete: comment owner OR admin/mod role

## Alternatives considered
- Session cookies shared across services
- Commenting calls Auth on every request (introspection) instead of validating JWT locally

## Consequences
+ Commenting can be stateless and fast (local JWT validation)
+ Clear ownership + role-based authorization
- Need key rotation strategy (JWKS) if using asymmetric signing
- Need to handle token revocation strategy (short TTL + refresh token, etc.)
