# User Service - Implementation Tasks

This document outlines the implementation tasks required to rebuild the User Service from scratch, organized by capability and referencing scenarios and facts from orig-behavior.md and orig-design-facts-and-rationale.md.

---

## 1. Foundation & Infrastructure

### 1.1 Project Setup

- [ ] 1.1.1 Initialize Go module with Go 1.20 [FA01.001]
- [ ] 1.1.2 Set up project structure (cmd/, internal/, configs/)
- [ ] 1.1.3 Configure golangci-lint and gofmt
- [ ] 1.1.4 Add core dependencies (gorilla/mux, pgx, bcrypt) [FA01.001]
- [ ] 1.1.5 Set up Makefile for build, test, lint targets

### 1.2 Configuration Management

- [ ] 1.2.1 Implement YAML config loading [FA07.001]
- [ ] 1.2.2 Add environment variable override support [FA07.001]
- [ ] 1.2.3 Implement secret loading from volume mounts
- [ ] 1.2.4 Add feature flag configuration
- [ ] 1.2.5 Implement config validation

### 1.3 Logging & Tracing

- [ ] 1.3.1 Set up structured logging with zap library [FA08.001]
- [ ] 1.3.2 Implement log levels (ERROR, WARN, INFO, DEBUG)
- [ ] 1.3.3 Add optional distributed tracing integration
- [ ] 1.3.4 Implement context-based trace propagation
- [ ] 1.3.5 Add sensitive data redaction rules

### 1.4 Database Setup

- [ ] 1.4.1 Configure PostgreSQL connection with pgx [FA02.001, FH00.001]
- [ ] 1.4.2 Implement connection pooling (min:2, max:20) [FH00.001]
- [ ] 1.4.3 Add connection health checks and retry logic [FF04.003]
- [ ] 1.4.4 Set up migration framework
- [ ] 1.4.5 Add SQLite support for testing [FJ04.001]

### 1.5 Message Queue Setup

- [ ] 1.5.1 Configure RabbitMQ connection with AMQP [FA02.004]
- [ ] 1.5.2 Implement persistent connection with auto-reconnect
- [ ] 1.5.3 Set up topic exchange for event publishing [FA02.004]
- [ ] 1.5.4 Implement message retry buffer (messages table) [FG04.003]
- [ ] 1.5.5 Add dead letter queue handling [FG04.003]

---

## 2. Database Schema & Migrations

### 2.1 Core Tables

- [ ] **2.1.1** Create tenants reference (external table)
  - Acceptance: Reference table exists for foreign key constraints
  - References: [FB00.004]

- [ ] **2.1.2** Create users table with all fields [FH03.001, FB00.001]
  - Acceptance: Table exists with columns (id, tenant_id, email, username, password_hash, full_name, status, created_at, updated_at, deleted_at, created_by, updated_by)
  - References: [FB00.001], [FH03.001]

- [ ] **2.1.3** Create audit_logs table
  - Acceptance: Table exists with columns for tracking user actions
  - References: [FG03.002], [FG03.003]

- [ ] **2.1.4** Create user_sessions table
  - Acceptance: Table exists for session tracking
  - References: External Auth Provider integration

- [ ] **2.1.5** Create messages table for retry buffer [FG04.003]
  - Acceptance: Table exists with columns for failed event storage
  - References: [FG04.003]

### 2.2 Indexes & Constraints

- [ ] **2.2.1** Add UNIQUE constraint on (tenant_id, email) for users [FB01.002, FH03.001]
  - Acceptance: Constraint exists, duplicate email within tenant returns error
  - References: [FB01.002], [FH03.001]

- [ ] **2.2.2** Add INDEX on (tenant_id, status) [FH03.001]
  - Acceptance: Index exists, query performance verified for list operations
  - References: [FH03.001]

- [ ] **2.2.3** Add INDEX on (deleted_at) [FH03.001]
  - Acceptance: Index exists, soft-delete filtering optimized
  - References: [FH03.001], [FH04.002]

- [ ] **2.2.4** Add foreign key constraints
  - Acceptance: FK constraints exist for tenant_id references
  - References: [FB00.004]

---

## 3. Domain Model

### 3.1 Entities

- [ ] **3.1.1** Define User entity struct [FB00.001]
  - Acceptance: Struct matches database schema, includes JSON tags for API serialization
  - References: [FB00.001], user.go

- [ ] **3.1.2** Define Tenant entity struct (reference)
  - Acceptance: Struct includes id, uuid, status fields
  - References: [FB00.004], tenant.go

- [ ] **3.1.3** Define AuditLog entity struct
  - Acceptance: Struct includes actor, action, timestamp, metadata fields
  - References: [FG03.002], [FG03.003]

- [ ] **3.1.4** Define UserSession entity struct
  - Acceptance: Struct includes user_id, session_id, expires_at fields
  - References: Session management logic

### 3.2 Value Objects

- [ ] 3.2.1 Implement UserStatus enumeration [FB03.001]
- [ ] 3.2.2 Implement Email value object with validation
- [ ] 3.2.3 Implement Username value object with validation [FB02.002]
- [ ] 3.2.4 Implement Password value object with complexity validation [FE01.002]

### 3.3 Repositories

- [ ] 3.3.1 Implement UserRepository (CRUD operations) [FH02.001-FH02.006]
- [ ] 3.3.2 Implement AuditLogRepository
- [ ] 3.3.3 Implement UserSessionRepository
- [ ] 3.3.4 Implement MessageRepository
- [ ] 3.3.5 Add error handling (NotFoundError, DuplicateError)

### 3.4 Domain Services

- [ ] 3.4.1 Implement UserManager [Scenario 1-7]
- [ ] 3.4.2 Implement TenantAccessManager [FB00.004]
- [ ] 3.4.3 Implement ValidationService [FB02.001]
- [ ] 3.4.4 Implement PasswordHashingService [FE01.002]

### 3.5 Business Rules

- [ ] 3.5.1 Implement soft delete cascade logic [FH04.002]
- [ ] 3.5.2 Implement email uniqueness validation [FB01.002]
- [ ] 3.5.3 Implement status transition validation [FB03.001]
- [ ] 3.5.4 Implement password complexity validation [FE01.002]
- [ ] 3.5.5 Implement username format validation [FB02.002]

---

## 4. REST API

### 4.1 HTTP Server Setup

- [ ] 4.1.1 Set up gorilla/mux router [FA01.001]
- [ ] 4.1.2 Implement RootHandler with error handling
- [ ] 4.1.3 Add request logging middleware [FC09.001]
- [ ] 4.1.4 Add panic recovery middleware [FC09.007]
- [ ] 4.1.5 Configure HTTP server timeouts

### 4.2 Authentication & Authorization

- [ ] 4.2.1 Implement JWT authentication middleware [FC09.002, FE01.001]
- [ ] 4.2.2 Implement RS256 signature verification [FE01.001]
- [ ] 4.2.3 Extract claims (uid, subject, tenant_id, roles) [FE01.001]
- [ ] 4.2.4 Implement authorization middleware
- [ ] 4.2.5 Implement role-based access checks [FE02.001]
- [ ] 4.2.6 Implement self-service access checks

### 4.3 User Endpoints

- [ ] 4.3.1 POST /api/users/v1/users [FC00.001, Scenario 1]
- [ ] 4.3.2 GET /api/users/v1/users/{id} [FC00.002, Scenario 2]
- [ ] 4.3.3 PUT /api/users/v1/users/{id} [FC00.003, Scenario 3]
- [ ] 4.3.4 DELETE /api/users/v1/users/{id} [FC00.004, Scenario 4]
- [ ] 4.3.5 GET /api/users/v1/users [FC00.005, Scenario 5]
- [ ] 4.3.6 GET /api/users/v1/users/search [FC00.006, Scenario 6]
- [ ] 4.3.7 PATCH /api/users/v1/users/{id}/status [FC00.007, Scenario 7]

### 4.4 Request Validation

- [ ] 4.4.1 Implement request body validation [FE05.003]
- [ ] 4.4.2 Implement UUID format validation [FE05.003]
- [ ] 4.4.3 Implement enum validation [FE05.003]
- [ ] 4.4.4 Implement field length constraints
- [ ] 4.4.5 Implement pagination parameter validation [FC01.004]

### 4.5 Response Handling

- [ ] 4.5.1 Implement standard response schemas [FC05.001-FC05.003]
- [ ] 4.5.2 Implement error response formatting [FC05.002]
- [ ] 4.5.3 Implement pagination cursor generation [FC05.003]
- [ ] 4.5.4 Add X-Request-Id header to responses

---

## 5. External Integrations

### 5.1 Auth Provider Integration

- [ ] **5.1.1** Implement Auth Provider HTTP client [FG01.001, FG01.002, FA05.001]
  - Acceptance: Client configured with base URL from config, connection pooling enabled
  - References: [FA05.001], [FG01.001], [FG01.002]

- [ ] **5.1.2** Add POST /api/auth/v1/verify call [FG01.001]
  - Acceptance: Validates JWT token, returns claims on success, 401 on failure
  - References: [FG01.001], `internal/clients/auth_client.go`

- [ ] **5.1.3** Add POST /api/auth/v1/revoke call [FG01.002]
  - Acceptance: Revokes user sessions, logs error on failure (non-critical)
  - References: [FG01.002], `internal/clients/auth_client.go`

- [ ] **5.1.4** Implement retry logic (3 retries, exponential backoff) [FF04.003]
  - Acceptance: Retries with 1s, 2s, 4s backoff intervals
  - References: [FF04.003], [FA05.001]

- [ ] **5.1.5** Add circuit breaker (50% threshold)
  - Acceptance: Circuit opens after 50% failures over 10 requests, sleep window 5s
  - References: Integration Points section

- [ ] **5.1.6** Set timeout to 10 seconds [FG01.001, FA05.001]
  - Acceptance: Connection timeout and request timeout both 10s
  - References: [FG01.001], [FA05.001]

### 5.2 Tenant Service Integration

- [ ] **5.2.1** Implement Tenant Service HTTP client [FA05.003]
  - Acceptance: Client configured with base URL from config, caching enabled (5 min TTL)
  - References: [FA05.003], `internal/clients/tenant_client.go`

- [ ] **5.2.2** Add GET /api/tenants/v1/tenants/{id} call [FB00.004, FG01.003]
  - Acceptance: Retrieves tenant details, validates status, returns 404 if not found
  - References: [FB00.004], [FG01.003]

- [ ] **5.2.3** Implement retry logic (3 retries) [FF04.003]
  - Acceptance: Retries with exponential backoff on failure
  - References: [FF04.003], [FA05.003]

- [ ] **5.2.4** Add circuit breaker
  - Acceptance: Circuit breaker configured with appropriate thresholds
  - References: Integration Points section

- [ ] **5.2.5** Set timeout to 30 seconds [FA05.003]
  - Acceptance: Request timeout 30s, connection timeout 10s
  - References: [FA05.003]

---

## 6. Event Publishing

### 6.1 Event Infrastructure

- [ ] 6.1.1 Implement event publisher with RabbitMQ
- [ ] 6.1.2 Implement JSON serialization for events
- [ ] 6.1.3 Add routing key generation [FA02.004]
- [ ] 6.1.4 Implement event target logic

### 6.2 Notification Events

- [ ] 6.2.1 Implement UserCreated event [FG02.001]
- [ ] 6.2.2 Implement UserUpdated event [FG02.002]
- [ ] 6.2.3 Implement UserDeleted event [FG02.003]
- [ ] 6.2.4 Implement UserStatusChanged event [FG02.004]
- [ ] 6.2.5 Implement UserPruned event [FG02.005]

### 6.3 Audit Events

- [ ] 6.3.1 Implement audit event routing keys [FG03.002, FG03.003]
- [ ] 6.3.2 Add actor and action metadata to audit events
- [ ] 6.3.3 Implement audit event target logic

### 6.4 Event Retry Mechanism

- [ ] 6.4.1 Store failed events in messages table [FG04.003]
- [ ] 6.4.2 Implement background job for retry
- [ ] 6.4.3 Add dead letter queue for permanently failed messages [FG04.003]
- [ ] 6.4.4 Implement 24-hour expiration for failed messages [FG04.003]

---

## 7. Background Jobs

### 7.1 Prune Inactive Users Job

- [ ] **7.1.1** Implement CLI flag or cron trigger [FI02.001]
  - Acceptance: Job can be triggered via CLI or scheduled cron (daily at 00:00 UTC)
  - References: [FI02.001], job scheduler configuration

- [ ] **7.1.2** Query users with status INACTIVE and updated_at > 30 days [FH02.002, FB03.002]
  - Acceptance: Query returns correct users based on retention period (configurable)
  - References: [FH02.002], [FB03.002]

- [ ] **7.1.3** Implement batch processing (100 users per batch) [FI02.001]
  - Acceptance: Job processes users in batches to avoid memory issues
  - References: [FI02.001]

- [ ] **7.1.4** Perform hard delete [FH02.002]
  - Acceptance: DELETE FROM users WHERE id IN (...) executed successfully
  - References: [FH02.002]

- [ ] **7.1.5** Publish UserPruned events [FG02.005]
  - Acceptance: Event published for each deleted user with correct payload
  - References: [FG02.005]

- [ ] **7.1.6** Log job outcome [FA08.001]
  - Acceptance: Structured log with count of pruned users, duration, errors
  - References: [FA08.001]

- [ ] **7.1.7** Set timeout to 1 hour [FI02.001]
  - Acceptance: Job terminates after 1 hour if not complete
  - References: [FI02.001]

- [ ] **7.1.8** Implement retry logic on database errors [FF04.003]
  - Acceptance: 3 retries with exponential backoff on transient DB errors
  - References: [FF04.003]

### 7.2 Message Retry Job

- [ ] 7.2.1 Implement periodic job (every 5 minutes)
- [ ] 7.2.2 Query messages table for failed messages
- [ ] 7.2.3 Retry message publishing
- [ ] 7.2.4 Delete successful messages
- [ ] 7.2.5 Mark messages >24h as permanently failed [FG04.003]

---

## 8. Observability

### 8.1 Metrics

- [ ] 8.1.1 Set up Prometheus metrics endpoint
- [ ] 8.1.2 Add http_requests_total counter
- [ ] 8.1.3 Add http_request_duration_seconds histogram
- [ ] 8.1.4 Add db_query_duration_seconds histogram
- [ ] 8.1.5 Add external_api_duration_seconds histogram
- [ ] 8.1.6 Add events_published_total counter
- [ ] 8.1.7 Add errors_total counter

### 8.2 Health Checks

- [ ] 8.2.1 Implement GET /health endpoint
- [ ] 8.2.2 Add database connectivity check
- [ ] 8.2.3 Add message queue connectivity check

---

## 9. Security

### 9.1 Input Validation

- [ ] 9.1.1 Implement SQL injection prevention (parameterized queries)
- [ ] 9.1.2 Add path traversal prevention
- [ ] 9.1.3 Implement command injection prevention

### 9.2 Secret Management

- [ ] 9.2.1 Load secrets from volume mounts
- [ ] 9.2.2 Override config with secrets
- [ ] 9.2.3 Implement secret rotation support

### 9.3 Password Security

- [ ] 9.3.1 Implement bcrypt hashing with cost factor 12 [FE01.002]
- [ ] 9.3.2 Validate password complexity [FE01.002]
- [ ] 9.3.3 Never log or store plain text passwords

---

## 10. Testing

### 10.1 Unit Tests

- [ ] 10.1.1 Write tests for domain entities [FJ00.001]
- [ ] 10.1.2 Write tests for repositories [FJ00.001]
- [ ] 10.1.3 Write tests for domain services [FJ00.001]
- [ ] 10.1.4 Write tests for business rules [FJ00.002-FJ00.007]
- [ ] 10.1.5 Set up test fixtures (root tenant, test users) [FH07.001, FH07.002]
- [ ] 10.1.6 Configure test coverage reporting [FJ00.001]

### 10.2 Integration Tests

- [ ] 10.2.1 Set up test database (PostgreSQL or SQLite) [FJ04.001]
- [ ] 10.2.2 Write API endpoint tests [FJ01.001]
- [ ] 10.2.3 Write database integration tests [FJ04.001]
- [ ] 10.2.4 Write message queue integration tests [FJ04.001]
- [ ] 10.2.5 Write external API integration tests (with mocks)

### 10.3 Scenario Tests

- [ ] 10.3.1 Test: Create user [FJ00.002]
- [ ] 10.3.2 Test: Email uniqueness [FJ00.003]
- [ ] 10.3.3 Test: Soft delete [FJ00.004]
- [ ] 10.3.4 Test: Status transition [FJ00.005]
- [ ] 10.3.5 Test: Self-manage [FJ00.006]
- [ ] 10.3.6 Test: Pruning job [FJ00.007]

---

## 11. Deployment

### 11.1 Containerization

- [ ] 11.1.1 Create Dockerfile with multi-stage build
- [ ] 11.1.2 Build static binary with CGO_ENABLED=0
- [ ] 11.1.3 Use minimal runtime image
- [ ] 11.1.4 Add version injection via ldflags

### 11.2 Kubernetes Resources

- [ ] 11.2.1 Create Deployment manifest
- [ ] 11.2.2 Create Service manifest (ClusterIP)
- [ ] 11.2.3 Create ConfigMap for configuration
- [ ] 11.2.4 Create Secrets for credentials
- [ ] 11.2.5 Configure health check probes
- [ ] 11.2.6 Set resource limits

### 11.3 Helm Chart

- [ ] 11.3.1 Create Helm chart structure
- [ ] 11.3.2 Add values.yaml with environment-specific configs
- [ ] 11.3.3 Template Kubernetes resources

### 11.4 CI/CD Pipeline

- [ ] 11.4.1 Set up CI pipeline
- [ ] 11.4.2 Add lint stage
- [ ] 11.4.3 Add unit test stage
- [ ] 11.4.4 Add API test stage
- [ ] 11.4.5 Add coverage stage
- [ ] 11.4.6 Add build stage
- [ ] 11.4.7 Add deploy stage

---

## 12. Documentation

### 12.1 API Documentation

- [ ] 12.1.1 Document all REST endpoints with examples
- [ ] 12.1.2 Document request/response schemas
- [ ] 12.1.3 Document error codes and messages
- [ ] 12.1.4 Document authentication and authorization

### 12.2 Operational Documentation

- [ ] 12.2.1 Document deployment procedures
- [ ] 12.2.2 Document configuration options
- [ ] 12.2.3 Document monitoring and alerting
- [ ] 12.2.4 Document troubleshooting guides

### 12.3 Developer Documentation

- [ ] 12.3.1 Document project structure
- [ ] 12.3.2 Document coding standards
- [ ] 12.3.3 Document testing guidelines
- [ ] 12.3.4 Document contribution guidelines

---

## 13. Optional/Future Enhancements

### 13.1 Dead Code Cleanup

- [ ] 13.1.1 Evaluate email verification - complete or remove [F00.010]
- [ ] 13.1.2 Evaluate two-factor authentication - complete or remove [F00.011]
- [ ] 13.1.3 Define user preferences schema or remove column [F00.012]
- [ ] 13.1.4 Implement password reset token cleanup job or remove [F00.013]
- [ ] 13.1.5 Remove deprecated user_roles table [F00.014]

### 13.2 Performance Optimizations

- [ ] 13.2.1 Add database query optimization
- [ ] 13.2.2 Implement connection pooling tuning
- [ ] 13.2.3 Add caching for frequently accessed data
- [ ] 13.2.4 Optimize event publishing for large batches

### 13.3 Feature Enhancements

- [ ] 13.3.1 Add user data versioning/history
- [ ] 13.3.2 Add rate limiting per tenant
- [ ] 13.3.3 Add advanced search capabilities
- [ ] 13.3.4 Add bulk user import/export

---

## Task Completion Checklist

After completing all tasks:

- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] All API tests passing
- [ ] Code coverage >80%
- [ ] All linting checks passing
- [ ] Documentation complete
- [ ] Deployment successful in dev environment
- [ ] Deployment successful in staging environment
- [ ] Performance testing completed
- [ ] Security review completed
- [ ] Production deployment approved
