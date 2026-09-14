# ENABLER Example: OAuth 2.0 Authentication Service Integration

## Story Type: ENABLER | Feature: 577031 | Status: Ready for Sprint

---

## Description

### Story Card
**As a** Development Team  
**I want** a centralized OAuth 2.0 authentication service  
**So that** all microservices can delegate user authentication/authorization without building it individually

### Context
Currently, authentication logic is duplicated across multiple services (Web API, Mobile API, Admin Portal), making it hard to maintain and update security policies centrally. An OAuth 2.0 authorization server will:
- Centralize token management
- Simplify client onboarding
- Enable single sign-on (SSO)
- Support role-based access control (RBAC)
- Allow dependent stories (user-facing features) to be built faster

**Which stories depend on this**: 
- User login/registration flows
- Multi-tenant access
- API authentication
- Admin role management

### Objective
Deliver a fully functional OAuth 2.0 authorization server supporting Bearer Token grant flows, with integration guides for consuming services.

### Scope

**In Scope:**
- OAuth 2.0 server deployment (Authorization Server, Resource Server patterns)
- Bearer Token generation and validation
- Role-based access control (Pharmacist, Clinician, Admin roles)
- Token refresh mechanism
- User session management
- Integration with existing user database
- Monitoring and audit logging

**Out of Scope:**
- Social login (Google, Facebook, etc.)
- Multi-factor authentication (separate story)
- User interface for token management
- LDAP/Active Directory integration (potential future phase)

---

## Key Considerations

- **Dependencies**: 
  - PostgreSQL database (for token store, user roles)
  - Existing user database (must be integrated/migrated)
  - Redis (for session cache)
  - Networking: OAuth server must be accessible to all consuming services

- **Risks**: 
  - Security: Tokens must be securely generated; HTTPS enforcement mandatory
  - Performance: High volume of token validation requests; caching critical
  - Integration: Each consuming service must update to use OAuth (may cause temporary outages)

- **Assumptions**: 
  - We're using industry-standard OAuth 2.0 flows (Authorization Code, Refresh Token)
  - All clients are server-side services (not browser-based SPAs initially)
  - Token lifetime: 1 hour; refresh tokens valid 7 days

- **Performance Targets**: 
  - Token validation: < 50ms (95th percentile)
  - Token generation: < 100ms
  - Support 10,000 simultaneous tokens

- **Security Considerations**: 
  - HTTPS only (no HTTP)
  - Tokens must use RS256 (RSA signature)
  - Rate limiting: max 100 token requests/minute per client
  - Secure token storage (no plaintext in logs)

---

## Acceptance Criteria

### Scenario 1: OAuth Server Deployed & Accessible
```gherkin
Given the OAuth 2.0 server code is ready
When the infrastructure team deploys to staging
Then the server is accessible at: https://oauth.[domain]/
And health check endpoint responds: GET /health → 200 OK
And server logs show no startup errors
And database connection is established and tested
```

### Scenario 2: Bearer Token Generation Works
```gherkin
Given a client sends valid credentials
When they POST /oauth/token with grant_type=client_credentials
Then the server responds with status 200
And the response includes:
  - access_token: "[JWT token]"
  - token_type: "Bearer"
  - expires_in: 3600
  - refresh_token: "[refresh token]"
And the token is valid for 1 hour
```

### Scenario 3: Token Validation & Authorization
```gherkin
Given a service has a valid Bearer token
When they send: Authorization: Bearer [token]
Then the OAuth server validates the token
And returns role/permission information
And validation completes in < 50ms (95th percentile)
And expired/revoked tokens are rejected
```

### Scenario 4: RBAC Role Assignment
```gherkin
Given users are loaded into the system
When the admin assigns roles via API: POST /users/{id}/roles
Then the user is granted role: [Pharmacist, Clinician, Admin]
And the role is reflected in future token claims
And tokens include: "roles": ["Pharmacist"]
And services can validate user permissions based on role
```

### Scenario 5: Security & Logging
```gherkin
Given tokens are being generated and validated
When requests are logged
Then:
  - All authentication events are logged (user, timestamp, success/failure)
  - No plaintext tokens appear in logs
  - Rate limiting prevents brute force: after 100 requests/minute → 429 Too Many Requests
  - HTTPS is enforced (HTTP requests rejected)
  - Token rotation works: refresh token generates new access token
```

### Scenario 6: Documentation & Team Training
```gherkin
Given the OAuth server is deployed
When the documentation is reviewed
Then it includes:
  - Architecture diagram
  - API specification (Swagger)
  - Client integration guide: [step-by-step for each service]
  - Troubleshooting guide
  - Security best practices
And the team has attended training
And each service team can integrate independently
```

---

## Technical Specification

**Architecture:**
```
[Client Services] --POST /oauth/token--> [OAuth Server] --query--> [Token Store (Redis/DB)]
                 <--Bearer Token--
                 
[Client Services] --GET /resource, Header: Authorization: Bearer [token]--> [Resource Server]
                 --validate token--> [OAuth Server] 
                 <--role/permissions--
```

**Dependencies:**
- Spring Security (OAuth2 server) or equivalent: v6.1+
- PostgreSQL: v14+
- Redis: v7+
- Java/Kotlin: v17 LTS
- Docker & Kubernetes for deployment

**Configuration:**
- Token signing algorithm: RS256 (RSA 2048)
- Token lifetime: 3600 seconds (1 hour)
- Refresh token lifetime: 604800 seconds (7 days)
- Rate limit: 100 requests/minute per client

---

## Definition of Done

- [ ] OAuth 2.0 server implemented per specification
- [ ] Bearer token generation working (tested with curl/Postman)
- [ ] Token validation logic implemented and tested
- [ ] Role-based access control (RBAC) working
- [ ] Database migrations created and tested
- [ ] Code reviewed by security team
- [ ] Unit tests passing (80%+ coverage)
- [ ] Integration tests passing (with mock consuming services)
- [ ] Security audit completed:
  - [ ] HTTPS enforced
  - [ ] Rate limiting working
  - [ ] Token signing algorithm verified (RS256)
  - [ ] Secrets management reviewed
- [ ] Performance benchmarks met (< 50ms token validation)
- [ ] Documentation complete (architecture, API, integration guides)
- [ ] Team training completed
- [ ] Deployed to staging environment
- [ ] Smoke tests passing in staging
- [ ] Dependent stories can proceed

---

## Downstream Stories (Ready to Start After DoD)
- US 585590: User login flow (Web)
- US 585591: User login flow (Mobile API)
- US 585592: Role-based access control for Admin Portal
- US 585593: Token refresh mechanism
- US 585594: Logout and session cleanup

---

## Success Metrics
- All dependent stories can successfully integrate (0 blockers)
- Token validation performance < 50ms (95th percentile)
- Security audit passes
- Team productivity: feature development 30% faster post-OAuth (no custom auth per service)
- Compliance: meets NHS/healthcare security standards

---

## Notes
- **Timeline**: 2 sprints (Spike + Implementation)
- **Team**: 2 Backend engineers + 1 Security engineer (part-time)
- **Deployment**: Blue-green deployment recommended to ensure zero downtime
- **Rollback Plan**: Keep existing auth for 2 weeks; if OAuth has issues, can revert quickly
