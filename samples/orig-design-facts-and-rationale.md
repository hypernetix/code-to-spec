# User Service - Original Facts & Design

This document contains reusable facts extracted from the User Service and design decisions, categorized according to FACTS_TAXONOMY.md. Each fact is assigned a unique ID in the format [FXX.YYY].

---

## FACTS

### FA — Context & System Definition

### FA00: Service Identity

**[FA00.001] – Service Name**

- **Fact**: The service is named "User Service" (internal: user-service)
- **Rationale**: Descriptive name indicating purpose as user identity and profile management
- **Code Reference**: `cmd/user-service/main.go`, service configuration files

**[FA00.002] – Service Purpose**

- **Fact**: User Service provides multi-tenant user identity and profile management with CRUD operations via REST API
- **Rationale**: Centralized user management enables consistent identity across platform services
- **Code Reference**: API specifications, service documentation

**[FA00.003] – Bounded Context**

- **Fact**: User management for multi-tenant environments, user lifecycle, profile management, authentication integration, event publishing
- **Rationale**: Clear service boundaries prevent overlap with Auth Provider (authentication) and Tenant Service (tenant management)
- **Code Reference**: Domain model definitions in `internal/domain/`

### FA01: Technology Stack

**[FA01.001] – Primary Language**

- **Fact**: Service implemented in Go 1.20
- **Rationale**: Performance, concurrency support, strong typing for enterprise service
- **Code Reference**: `go.mod`, build configurations

**[FA01.002] – HTTP Router**

- **Fact**: gorilla/mux for HTTP routing
- **Rationale**: Mature, feature-rich router with good middleware support
- **Code Reference**: `internal/api/router.go`

**[FA01.003] – Password Hashing**

- **Fact**: bcrypt library for password hashing with cost factor 12
- **Rationale**: Industry standard, resistant to brute force attacks
- **Code Reference**: `internal/security/password.go`

### FA02: Data & Messaging Infrastructure

**[FA02.001] – Database**

- **Fact**: PostgreSQL 14+ as primary data store, pgx driver
- **Rationale**: ACID compliance, JSONB support, mature ecosystem
- **Code Reference**: Database migration files, `internal/repository/`

**[FA02.002] – Database Driver**

- **Fact**: pgx driver for PostgreSQL connectivity
- **Rationale**: Native Go driver, better performance than database/sql
- **Code Reference**: `go.mod` dependencies, connection pool configuration

**[FA02.003] – Message Queue**

- **Fact**: RabbitMQ (AMQP 0.9.1) for event publishing
- **Rationale**: Reliable message delivery, topic-based routing, industry standard
- **Code Reference**: `internal/mq/` directory, AMQP configuration

**[FA02.004] – API Protocol**

- **Fact**: RESTful HTTP/HTTPS with JSON payloads
- **Rationale**: Standard protocol for microservices, wide client support
- **Code Reference**: API handler implementations in `internal/api/`

### FA05: External Dependencies

**[FA05.001] – Auth Provider Dependency**

- **Fact**: Service depends on Auth Provider for JWT token validation and session management
- **Rationale**: Centralized authentication and authorization management
- **Code Reference**: `internal/clients/auth_client.go`, `configs/config.yml` auth_provider section
- **Configuration**: Base URL, connection timeout (10s), request timeout (10s)

**[FA05.002] – Message Queue Dependency**

- **Fact**: Service depends on RabbitMQ for event publishing
- **Rationale**: Asynchronous event delivery, decoupling from consumers
- **Code Reference**: `internal/mq/publisher.go`, `configs/config.yml` rabbitmq section
- **Configuration**: Host/port, connection timeout (30s), heartbeat interval (60s)

**[FA05.003] – Tenant Service Dependency**

- **Fact**: Service depends on Tenant Service for tenant validation and hierarchy information
- **Rationale**: Validate tenant existence and retrieve tenant metadata
- **Code Reference**: `internal/clients/tenant_client.go`, `configs/config.yml` tenant_service section
- **Configuration**: Base URL, connection timeout (10s), request timeout (30s), cache TTL (5 minutes)

### FA07: Configuration

**[FA07.001] – Configuration Sources**

- **Fact**: Configuration loaded from YAML file, environment variables, and volume-mounted secrets
- **Rationale**: Flexible configuration for different deployment environments
- **Code Reference**: `configs/config.yml`, `internal/config/loader.go`

### FA08: Observability

**[FA08.001] – Log Format**

- **Fact**: Structured JSON logging via zap library
- **Rationale**: Machine-parsable logs for aggregation and analysis
- **Code Reference**: `internal/logging/logger.go`

---

### FB — Domain Model

**[FB00.001] – User entity**
Identity entity within tenant. Fields: UUID, tenant_id, email, username, password_hash, full_name, status, created_at, updated_at, deleted_at, created_by, updated_by.

**[FB00.004] – Tenant validation**
All operations validate tenant exists and is enabled before proceeding.

**[FB01.002] – Email uniqueness**
Email must be unique within tenant scope. Enforced via UNIQUE INDEX on (tenant_id, email).

**[FB02.001] – User data validation**
Composite validation: email format, username format (alphanumeric 3-20 chars), password complexity.

**[FB02.002] – Username format**
Username must be alphanumeric and between 3-20 characters. Regex: `^[a-zA-Z0-9]{3,20}$`.

**[FB03.001] – User status state machine**
Status transitions: PENDING → ACTIVE (verification), ACTIVE ↔ INACTIVE (activation/deactivation), Any → DELETED (soft delete).

**[FB03.002] – Pruning lifecycle**
Users with status INACTIVE for >30 days are permanently deleted by background job.

---

### FC — REST API

**[FC00.001] – POST /api/users/v1/users**
Create user. Auth: JWT. Request: email, username, password (optional). Response: 201. Errors: 400, 401, 403, 404, 409, 500.

**[FC00.002] – GET /api/users/v1/users/{id}**
Get user by ID. Auth: JWT. Response: 200 with user profile. Errors: 400, 401, 403, 404, 500.

**[FC00.003] – PUT /api/users/v1/users/{id}**
Update user. Auth: JWT. Request: email, username, full_name (optional). Response: 204. Errors: 400, 401, 403, 404, 409, 500.

**[FC00.004] – DELETE /api/users/v1/users/{id}**
Soft delete user. Auth: JWT. Response: 204. Errors: 401, 403, 404, 500.

**[FC00.005] – GET /api/users/v1/users**
List users. Query: tenant_id, status, allow_deleted, email, username, limit, after. Response: 200 with pagination.

**[FC00.006] – GET /api/users/v1/users/search**
Search users. Query: q (required), tenant_id, fields, limit, after. Response: 200 with search results.

**[FC00.007] – PATCH /api/users/v1/users/{id}/status**
Update user status. Auth: JWT. Request: status. Response: 204. Errors: 400, 401, 403, 404, 500.

**[FC01.001] – tenant_id query parameter**
UUID format, filters results by tenant.

**[FC01.002] – status query parameter**
Enum: PENDING, ACTIVE, INACTIVE, DELETED. Default: ALL.

**[FC01.003] – allow_deleted query parameter**
Boolean. If true, includes soft-deleted records. Default: false.

**[FC01.004] – limit query parameter**
Pagination limit. Default: 100, max: 1000.

**[FC01.005] – after query parameter**
Cursor for pagination (base64 encoded).

**[FC03.001] – CreateUserRequest schema**

```json
{
  "email": "string (required)",
  "username": "string (required)",
  "password": "string (optional)",
  "full_name": "string (optional)",
  "tenant_id": "uuid (required)"
}
```

**[FC03.002] – UpdateUserRequest schema**

```json
{
  "email": "string (optional)",
  "username": "string (optional)",
  "full_name": "string (optional)"
}
```

**[FC03.003] – UpdateStatusRequest schema**

```json
{
  "status": "enum (PENDING|ACTIVE|INACTIVE)"
}
```

**[FC05.001] – UserResponse schema**

```json
{
  "id": "uuid",
  "tenant_id": "uuid",
  "email": "string",
  "username": "string",
  "full_name": "string",
  "status": "enum",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

**[FC05.002] – ErrorResponse schema**

```json
{
  "domain": "user-service",
  "code": "string",
  "reason": "string",
  "message": "string"
}
```

**[FC05.003] – PaginatedResponse schema**

```json
{
  "items": ["array of objects"],
  "pagination": {
    "limit": "integer",
    "after": "string (cursor)",
    "has_more": "boolean"
  }
}
```

**[FC07.001] – 401 Unauthorized**
Missing or invalid JWT token.

**[FC07.002] – 403 Forbidden**
Insufficient permissions, namespace not in scope, tenant not accessible.

**[FC07.003] – 409 Conflict**
Email already exists, resource conflict.

**[FC07.004] – 200 OK**
Successful retrieval.

**[FC07.005] – 201 Created**
Successful creation.

**[FC07.006] – 204 No Content**
Successful update or delete.

**[FC07.007] – 400 Bad Request**
Invalid request format, validation failure.

**[FC07.008] – 404 Not Found**
Resource not found, soft-deleted.

**[FC07.011] – 500 Internal Server Error**
Database error, event publishing failure.

**[FC09.001] – Request logging middleware**
Logs all incoming requests with method, path, status, duration.

**[FC09.002] – Authentication middleware**
Validates JWT token on all protected endpoints.

**[FC09.007] – Recovery/panic middleware**
Catches panics and returns 500 error.

---

### FE — Security Model

**[FE01.001] – JWT authentication**
RS256 algorithm. Claims: uid, subject, tenant_id, roles. Public key from Auth Provider.

**[FE01.002] – Password complexity**
Minimum 12 characters, mixed case, numbers, symbols. Hashed with bcrypt cost factor 12.

**[FE02.001] – Role model**
Roles in JWT claims. Format: user:create, user:read, user:update, user:delete, user:update:status. Special: self_manage.

**[FE05.003] – Validation**
JSON schema validation for request bodies. UUID format for IDs. Enum for status values.

---

### FF — Service Layer

**[FF04.003] – Retry logic**
Database operations: 3 retries with exponential backoff. External APIs: 3 retries.

---

### FG — Outbound Gateways

**[FG01.001] – Auth Provider verify**

- **Fact**: POST /api/auth/v1/verify validates JWT token
- **Rationale**: Centralized token validation ensures consistent security across services
- **Code Reference**: `internal/clients/auth_client.go` VerifyToken method
- **Configuration**: Timeout 10s, retry 3x with exponential backoff (1s, 2s, 4s)

**[FG01.002] – Auth Provider revoke**

- **Fact**: POST /api/auth/v1/revoke revokes user sessions
- **Rationale**: Enables immediate session termination when user is deleted or deactivated
- **Code Reference**: `internal/clients/auth_client.go` RevokeSession method
- **Configuration**: Timeout 10s, retry 3x, non-critical (log error and continue)

**[FG01.003] – Tenant Service get tenant**

- **Fact**: GET /api/tenants/v1/tenants/{id} retrieves tenant details
- **Rationale**: Validate tenant existence and status before user operations
- **Code Reference**: `internal/clients/tenant_client.go` GetTenant method
- **Configuration**: Timeout 30s, retry 3x, cache TTL 5 minutes

**[FG02.001] – UserCreated event**
Routing: users.created. Payload: user_id, tenant_id, email, username, status.

**[FG02.002] – UserUpdated event**
Routing: users.updated. Payload: user_id, tenant_id, old_values, new_values.

**[FG02.003] – UserDeleted event**
Routing: users.deleted. Payload: user_id, tenant_id, deleted_at.

**[FG02.004] – UserStatusChanged event**
Routing: users.status_changed. Payload: user_id, tenant_id, old_status, new_status.

**[FG02.005] – UserPruned event**
Routing: users.pruned. Payload: user_id, tenant_id, pruned_at.

**[FG03.002] – UserCreated audit event**
Routing: users.audit.created. Payload: actor, action, timestamp, user_id.

**[FG03.003] – UserUpdated/Deleted audit event**
Routing: users.audit.{action}. Payload: actor, action, timestamp, user_id, changes.

**[FG04.003] – Dead letter queue**
Failed messages stored in messages table. Background job retries. Max 24 hours.

---

### FH — Persistence

**[FH00.001] – Connection pool**
Min: 2, Max: 20, Max idle: 30 min, Max lifetime: 1 hour.

**[FH02.001] – Insert user**
INSERT into users table. Returns: UUID. Constraint violations → DuplicateError.

**[FH02.002] – Hard delete user**
DELETE FROM users WHERE id = ?. Used by pruning job.

**[FH02.003] – Update user**
UPDATE users SET ... WHERE id = ? AND deleted_at IS NULL.

**[FH02.004] – Get user by ID**
SELECT * FROM users WHERE id = ? AND deleted_at IS NULL.

**[FH02.005] – List users**
SELECT * FROM users with filters, pagination. Cursor-based pagination.

**[FH02.006] – Search users**
Full-text search using LIKE or search engine. Fields: email, username, full_name.

**[FH03.001] – users table**
Columns: id (UUID PK), tenant_id (UUID FK), email, username, password_hash, full_name, status, created_at, updated_at, deleted_at, created_by, updated_by. UNIQUE: (tenant_id, email). INDEX: (tenant_id, status), (deleted_at).

**[FH04.002] – Soft delete filter**
Standard queries exclude deleted_at IS NOT NULL. Application-level filter.

**[FH08.001] – Transaction isolation**
Read Committed (PostgreSQL default). Transactions for multi-step operations.

---

### FI — Triggers & Background Execution

**[FI02.001] – PruneInactiveUsers job**
Trigger: Cron (daily at 00:00 UTC). Deletes users with status INACTIVE for >30 days. Timeout: 1 hour. Batch size: 100.

---

### FJ — Tests

**[FJ00.001] – Unit tests**
Command: `go test ./...`. Coverage: Tracked via `go test -cover`.

**[FJ01.001] – API tests**
Framework: Go testing. Location: tests/api.

**[FJ04.001] – Integration tests**
Database: PostgreSQL or SQLite. Message queue: Mock or real RabbitMQ.

---

### FK — Test Fixtures & Scenarios

#### Common Fixtures

**Root tenant**
UUID: 00000000-0000-0000-0000-000000000000, enabled: true.

**Test users**
Pending user, active user, inactive user for testing state transitions.

#### Test Scenario Guidance

**[FK00.001] – Test: Create user**
Function: TestCreateUser. Steps: 1) POST user with valid data. 2) Verify DB insert. 3) Verify event published. Expected: 201 Created, user in DB, UserCreated event.

**[FK00.002] – Test: Email uniqueness**
Function: TestEmailUniqueness. Steps: 1) Create user with email. 2) Attempt to create another user with same email in same tenant. Expected: 409 Conflict.

**[FK00.003] – Test: Soft delete**
Function: TestSoftDelete. Steps: 1) Create user. 2) DELETE user. 3) GET user. Expected: 404 Not Found, deleted_at set in DB.

**[FK00.004] – Test: Status transition**
Function: TestStatusTransition. Steps: 1) Create user (PENDING). 2) PATCH status to ACTIVE. 3) Verify status changed. Expected: 204 No Content, status = ACTIVE.

**[FK00.005] – Test: Self-manage**
Function: TestSelfManage. Steps: 1) User updates own profile. 2) User attempts to change own status. Expected: Profile update succeeds, status update fails with 403.

**[FK00.006] – Test: Pruning job**
Function: TestPruningJob. Steps: 1) Create inactive user with old updated_at. 2) Run pruning job. 3) Verify user hard deleted. Expected: User removed from DB, UserPruned event.

---

### FL — Dead Code Facts

**[FL00.001] – Email verification**
Status: Partially implemented, not enforced. Location: email_verification table. Recommendation: Complete or remove.

**[FL00.002] – Two-factor authentication**
Status: Table exists, no API/enforcement. Location: user_2fa table. Recommendation: Complete or remove.

**[FL00.003] – User preferences**
Status: Column exists, no validation. Location: users.preferences JSONB. Recommendation: Define schema or remove.

**[FL00.004] – Password reset tokens**
Status: Implemented but no expiration cleanup. Location: password_reset_tokens table. Recommendation: Implement cleanup job.

**[FL00.005] – User roles**
Status: Deprecated, moved to Auth Provider. Location: user_roles table. Recommendation: Remove table after migration complete.

---

## Design Decisions

This section captures the architectural decisions and design patterns used in the User Service implementation.

---

### 1. Architecture Overview

#### 1.1 Architectural Style

**Layered Architecture with Domain-Driven Design:**

The service follows a clean layered architecture:

```text
┌─────────────────────────────────────────┐
│         REST API Layer (HTTP)           │
│  - Request validation                   │
│  - Authentication/Authorization         │
│  - Response formatting                  │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│         Domain Service Layer            │
│  - Business logic                       │
│  - Domain services                      │
│  - Business rule enforcement            │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│         Repository Layer                │
│  - Data access abstraction              │
│  - Query building                       │
│  - Transaction management               │
└─────────────────────────────────────────┘
                   ↓
┌─────────────────────────────────────────┐
│         Infrastructure Layer            │
│  - PostgreSQL database                  │
│  - RabbitMQ message queue               │
│  - External HTTP clients                │
└─────────────────────────────────────────┘
```

**Rationale:**

- Clear separation of concerns
- Testability (each layer can be tested independently)
- Maintainability (changes in one layer don't affect others)
- Domain logic isolated from infrastructure details

---

#### 1.2 Service Boundaries

**Bounded Context: User Identity Management**:

The User Service owns:

- User entity definitions and lifecycle
- Profile data and metadata
- User status management
- User events

The service does NOT own:

- Tenant master data (owned by Tenant Service)
- Authentication tokens (owned by Auth Provider)
- User permissions and roles (owned by Auth Provider)
- Session management (owned by Auth Provider)

**Integration Pattern:** The service integrates with external systems via HTTP APIs and publishes events for downstream consumers.

---

### 2. Data Model Design

#### 2.1 Entity Relationship Model

```text
┌──────────────┐
│   tenants    │ (external, managed by Tenant Service)
└──────────────┘
       ↑
       │
┌──────────────┐       ┌──────────────────┐
│    users     │───────│   audit_logs     │
└──────────────┘       └──────────────────┘
       │
       │
┌──────────────────┐
│  user_sessions   │
└──────────────────┘
```

**Key Design Decisions:**

1. **Tenant Scoped Email Uniqueness**
   - UNIQUE INDEX on (tenant_id, email)
   - **Rationale:** Allows same email across different tenants (multi-tenancy isolation)
   - **Trade-off:** Cannot enforce global email uniqueness, but provides tenant isolation

2. **Soft Delete Pattern**
   - All entities have `deleted_at` timestamp column
   - **Rationale:** Enables recovery, audit trail, and gradual cleanup
   - **Trade-off:** Queries must filter deleted items, but provides safety net

3. **Status Enum**
   - Status stored as VARCHAR with application-level enum validation
   - **Rationale:** Flexibility to add new statuses without schema changes
   - **Trade-off:** No database-level constraint, but easier to extend

4. **Password Hashing**
   - Bcrypt with cost factor 12
   - **Rationale:** Industry standard, resistant to brute force attacks
   - **Trade-off:** Slower than other algorithms, but provides better security

---

#### 2.2 Multi-tenancy Design

**Decision:** Shared database with tenant_id column (shared schema approach)

**Rationale:**

- Simpler infrastructure management
- Cost-effective for large number of tenants
- Easier to implement cross-tenant queries for administrators

**Trade-offs:**

- Must enforce tenant isolation at application level
- Risk of data leakage if tenant checks are missed
- Cannot easily scale individual tenants independently

**Alternatives Considered:**

- Database per tenant: Rejected due to operational complexity
- Schema per tenant: Rejected due to connection pool limitations

---

### 3. API Design

#### 3.1 RESTful Design Principles

**URL Structure:**

```text
/api/users/v1/users
/api/users/v1/users/{id}
/api/users/v1/users/{id}/status
/api/users/v1/users/search
```

**Design Decisions:**

1. **Version in URL**
   - **Rationale:** Makes API version explicit, enables gradual migration
   - **Trade-off:** Longer URLs, but clearer versioning

2. **Resource-based URLs**
   - **Rationale:** RESTful convention, self-documenting
   - **Trade-off:** May require multiple calls for complex operations

3. **Sub-resource for Status**
   - PATCH /users/{id}/status instead of PUT /users/{id}
   - **Rationale:** Status changes are significant operations that warrant separate endpoint
   - **Trade-off:** More endpoints, but clearer intent

---

#### 3.2 Query Parameter Design

**Pagination:**

- Cursor-based pagination (not offset-based)
- Cursor encoded as base64 JSON
- **Rationale:** Consistent results even with concurrent updates
- **Trade-off:** Cannot jump to arbitrary page, but more reliable

**Filtering:**

- `tenant_id`: Filter by tenant
- `status`: Filter by status
- `allow_deleted`: Include soft-deleted items
- **Rationale:** Common filtering needs without complex query language
- **Trade-off:** Limited flexibility, but simple to use

---

#### 3.3 Error Handling Design

**Error Response Format:**

```json
{
  "domain": "user-service",
  "code": "404",
  "reason": "NotFound",
  "message": "User not found"
}
```

**Design Decisions:**

1. **Structured Error Responses**
   - **Rationale:** Machine-readable errors for client error handling
   - **Trade-off:** More verbose, but enables better client UX

2. **Error Type Hierarchy**
   - Custom error types (NotFoundError, ValidationError, etc.)
   - **Rationale:** Type-safe error handling in code
   - **Trade-off:** More boilerplate, but clearer intent

---

### 4. Security Design

#### 4.1 Authentication & Authorization

**JWT-based Authentication:**

- RS256 signature algorithm
- Public key from Auth Provider
- Claims: uid, subject, tenant_id, roles

**Design Decisions:**

1. **Stateless Authentication**
   - No session storage
   - **Rationale:** Horizontal scalability, no session management
   - **Trade-off:** Cannot revoke tokens (must wait for expiration)

2. **Role-based Authorization**
   - Roles embedded in JWT claims
   - Format: user:create, user:read, user:update, user:delete
   - **Rationale:** Fine-grained permissions per operation
   - **Trade-off:** Large tokens with many roles, but flexible access control

3. **Self-Service Access**
   - Users can read/update their own profile
   - **Rationale:** Reduces admin burden, empowers users
   - **Trade-off:** Must carefully validate self-access rules

---

#### 4.2 Input Validation

**Validation Layers:**

1. Request parameter validation (types, formats)
2. Business rule validation (uniqueness, state transitions)
3. Database constraint validation (foreign keys, unique indexes)

**Design Decisions:**

1. **Multi-layer Validation**
   - **Rationale:** Defense in depth, catches errors early
   - **Trade-off:** Some duplication, but more robust

2. **Parameterized Queries**
   - pgx with parameterized queries for all database operations
   - **Rationale:** SQL injection prevention
   - **Trade-off:** Slightly more verbose, but secure

---

### 5. Event-Driven Architecture

#### 5.1 Event Publishing Design

**Event Types:**

- Notification events: For downstream consumers
- Audit events: For compliance and logging

**Design Decisions:**

1. **Separate Event Types**
   - **Rationale:** Different consumers have different needs
   - **Trade-off:** More events to manage, but clearer separation

2. **Asynchronous Publishing**
   - Events published after database commit
   - **Rationale:** Ensures data consistency before notification
   - **Trade-off:** Brief window where DB updated but event not published

3. **Event Retry Mechanism**
   - Failed events stored in messages table
   - Background job retries every 5 minutes
   - **Rationale:** Ensures eventual delivery
   - **Trade-off:** Delayed event delivery, but more reliable

---

### 6. Performance Optimizations

#### 6.1 Database Optimizations

**Indexes:**

- UNIQUE INDEX on (tenant_id, email)
- INDEX on (tenant_id, status)
- INDEX on (deleted_at)

**Connection Pooling:**

- Min: 2, Max: 20 connections
- Max idle time: 30 minutes
- Max lifetime: 1 hour

**Query Optimizations:**

- Cursor-based pagination for consistent performance
- Prepared statements for frequently executed queries

---

#### 6.2 Caching Strategy

**Decision:** No caching layer for user data

**Rationale:**

- User data changes frequently
- Consistency more important than performance
- Database performance sufficient for current load

**Future Consideration:**

- Add Redis cache if read load increases significantly
- Cache user profiles with short TTL (1-5 minutes)

---

### 7. Observability Design

#### 7.1 Logging Strategy

**Structured Logging:**

- JSON format in production
- Human-readable in development
- Log levels: ERROR, WARN, INFO, DEBUG

**Context Propagation:**

- Request ID in all log entries
- Tenant ID and user ID where applicable

**Rationale:**

- Machine-parsable logs for aggregation
- Correlation across service boundaries
- Easy filtering and searching

---

### 7.2 Metrics Design

**Key Metrics:**

- Request counters (by method, path, status)
- Latency histograms (by method, path)
- Database query histograms (by operation)
- Event publishing counters (by event type)

**Rationale:**

- Standard metrics for monitoring and alerting
- Enables SLO/SLA tracking
- Facilitates performance analysis

---

### 8. Testing Strategy

#### 8.1 Test Pyramid

```text
        ┌─────────┐
        │   E2E   │  (Few, critical paths)
        └─────────┘
      ┌─────────────┐
      │ Integration │  (Moderate, API + DB)
      └─────────────┘
    ┌─────────────────┐
    │   Unit Tests    │  (Many, domain logic)
    └─────────────────┘
```

**Unit Tests:**

- Domain entities, value objects
- Business rules
- Repositories (with mock DB)

**Integration Tests:**

- API endpoints (with test DB)
- Database queries
- External API calls (with mocks)

**E2E Tests:**

- Critical user scenarios
- End-to-end flows

---

### 9. Trade-offs & Technical Debt

#### 9.1 Known Limitations

1. **No Rate Limiting**
   - Relies on API gateway for rate limiting
   - **Impact:** Service vulnerable to abuse if gateway bypassed
   - **Mitigation:** Deploy behind API gateway, monitor metrics

2. **No User Data Versioning**
   - Only current state maintained
   - **Impact:** Cannot easily revert to previous profile data
   - **Mitigation:** Event history can be used for manual reconstruction

3. **Email Verification Not Enforced**
   - Table exists but feature incomplete
   - **Impact:** Users can use unverified emails
   - **Mitigation:** Complete implementation or remove table

---

### 9.2 Technical Debt

1. **Email Verification**
   - Partially implemented, not enforced
   - **Recommendation:** Complete implementation or remove

2. **Two-Factor Authentication**
   - Table exists, no API/enforcement
   - **Recommendation:** Complete implementation or remove table

3. **User Preferences**
   - Column exists, no validation
   - **Recommendation:** Define schema or remove column

4. **Password Reset Tokens**
   - Implemented but no expiration cleanup
   - **Recommendation:** Implement cleanup job

5. **User Roles**
   - Deprecated, moved to Auth Provider
   - **Recommendation:** Remove table after migration complete

---

### 10. Summary

The User Service is a well-architected multi-tenant user management system that balances simplicity, security, and maintainability. Key strengths include:

- **Clean layered architecture** with clear separation of concerns
- **Robust security** with JWT authentication and role-based authorization
- **Multi-tenancy support** with tenant isolation
- **Event-driven integration** for loose coupling with consumers
- **Comprehensive observability** with metrics and logging

Areas for improvement include:

- **Completing or removing** partially implemented features (email verification, 2FA)
- **Implementing rate limiting** at service level
- **Adding caching layer** if read load increases
- **Cleaning up technical debt** (deprecated tables, unused columns)

Overall, the service provides a solid foundation for user management and can be extended to support additional use cases with minimal changes.
