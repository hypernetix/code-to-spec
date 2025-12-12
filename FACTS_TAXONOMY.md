# Facts Taxonomy for Microservice Migration

## Purpose

This document contains a taxonomy of knowledge about a microservice that is useful for service-to-service comparison or conversion from one programming language to another (e.g., Go to Rust).

The facts taxonomy can be also used to explain LLMs what to improve/change during translation

---

## 1. Core Principles

### 1.1 What is a Fact?

A **Fact** is:

> Something that can change independently and must be referenced by more than one consumer OR verified independently.

**If it doesn't meet this bar → it's an attribute, not a Fact.**

### 1.2 Promotion Rule (Shared vs Local)

| Item | Representation |
|------|----------------|
| Used by ≥2 handlers | Own Fact ID |
| Used by 1 handler only | Attribute of handler fact |
| Semantically reusable | Fact |
| Purely structural | Attribute |

### 1.3 Fact Reference Rule

**Facts may only reference other facts by ID, never inline duplication.**

### 1.4 ID Format

```
F X Y . zzz
│ │ │   └── sequential atomic fact ID
│ │ └────── subcategory
│ └──────── category
└────────── Fact
```

**IDs are append-only. Never renumber.**

---

## 2. Category Map

### F0 — Context & System Definition

#### F00 — Overview

- [F00.001] – Service mission statement
- [F00.002] – Bounded context definition
- [F00.003] – Non-goals / explicitly unsupported behavior
- [F00.004] – Service dependencies map
- [F00.005] – Data ownership boundaries

#### F01 — Project Facts

- [F01.001] – Programming language & runtime
- [F01.002] – Project layout conventions
- [F01.003] – Coding standards
- [F01.004] – Versioning strategy
- [F01.005] – Dependency management approach
- [F01.006] – Code generation tools

#### F02 — External Dependencies

- [F02.001] – PostgreSQL dependency
- [F02.002] – Redis dependency
- [F02.003] – External auth provider
- [F02.004] – Message queue (Kafka/RabbitMQ/etc.)
- [F02.005] – Object storage (S3/MinIO/etc.)
- [F02.006] – External monitoring services

#### F03 — Deployment

- [F03.001] – Deployment topology
- [F03.002] – Kubernetes resources
- [F03.003] – Scaling rules
- [F03.004] – Health check endpoints
- [F03.005] – Readiness probe configuration
- [F03.006] – Liveness probe configuration
- [F03.007] – Resource limits (CPU/Memory)
- [F03.008] – Environment-specific configurations

#### F04 — Build

- [F04.001] – Build toolchain
- [F04.002] – CI pipeline
- [F04.003] – Artifact format
- [F04.004] – Build optimization flags
- [F04.005] – Multi-stage build process
- [F04.006] – Dependency caching strategy

#### F05 — Secrets Handling

- [F05.001] – Secret storage mechanism
- [F05.002] – Rotation rules
- [F05.003] – Secret injection method
- [F05.004] – Encryption at rest for secrets

#### F06 — Crypto Primitives

- [F06.001] – Hashing algorithm
- [F06.002] – Signing algorithm
- [F06.003] – Encryption at rest
- [F06.004] – Key derivation function
- [F06.005] – Random number generation

#### F07 — Configuration Management

- [F07.001] – Configuration sources (env vars, files, remote)
- [F07.002] – Configuration validation rules
- [F07.003] – Hot reload support
- [F07.004] – Default values strategy
- [F07.005] – Feature flags mechanism

#### F08 — Logging & Tracing

- [F08.001] – Log format (JSON/structured/plain)
- [F08.002] – Log levels and their usage
- [F08.003] – Distributed tracing implementation
- [F08.004] – Trace sampling strategy
- [F08.005] – Log aggregation destination
- [F08.006] – Sensitive data redaction rules

---

### F2 — Domain Model

#### F20 — Domain Entities

- [F20.001] – User entity
- [F20.002] – Session entity
- [F20.003] – AuditLog entity
- [F20.004] – Entity lifecycle rules
- [F20.005] – Entity validation rules
- [F20.006] - Aggregates

#### F21 — Value Objects

- [F21.001] – Email value object
- [F21.002] – PhoneNumber value object
- [F21.003] – Address value object
- [F21.004] – Money value object

#### F22 — End-to-End Scenarios

*(These are orchestrations, not APIs)*

- [F22.001] – Create user scenario
- [F22.002] – Update user scenario
- [F22.003] – Delete user scenario
- [F22.004] – Bulk import scenario
- [F22.005] – Data export scenario

Each scenario references:
- REST / gRPC handlers
- Service methods
- DB operations
- Events

#### F23 - Formal product-management like feature set

- [F23.001] - hierarchical multoi-tenancy support
- [F23.002] - per tenant and per user LLM tokens access quotas
- [F23.003] - AI chat model selection
- [F23.003] - AI chat history

---

### F2 — REST API

#### F20 — REST Handlers (TOP-LEVEL FACT)

*Each handler = exactly one Fact*

- [F20.001] – POST /users
- [F20.002] – GET /users/{id}
- [F20.003] – DELETE /users/{id}
- [F20.004] – PUT /users/{id}
- [F20.005] – PATCH /users/{id}
- [F20.006] – GET /users (list/search)
- [F20.007] – GET /health
- [F20.008] – GET /metrics
- [F20.009] – GET /ready

**Attributes (NOT facts):**
- local-only params
- local-only headers
- handler-specific validation

**References:**
- request schema IDs
- response schema IDs
- shared headers
- auth facts
- error facts

#### F21 — REST Request Parameters (SHARED ONLY)

- [F21.001] – tenant_id query parameter
- [F21.002] – page query parameter
- [F21.003] – limit query parameter
- [F21.004] – offset query parameter
- [F21.005] – sort query parameter
- [F21.006] – filter query parameter

#### F22 — REST Path Parameters

- [F22.001] – {id} path parameter format
- [F22.002] – {tenant_id} path parameter format
- [F22.003] – Path parameter validation rules

#### F23 — REST Request Schemas

- [F23.001] – CreateUserRequest
- [F23.002] – UpdateUserRequest
- [F23.003] – PatchUserRequest
- [F23.004] – BulkCreateRequest
- [F23.005] – SearchRequest

#### F24 — REST Request Headers

- [F24.001] – Authorization
- [F24.002] – X-Request-Id
- [F24.003] – Content-Type
- [F24.004] – Accept
- [F24.005] – X-Tenant-Id
- [F24.006] – X-Correlation-Id
- [F24.007] – User-Agent

#### F25 — REST Response Schemas

- [F25.001] – UserResponse
- [F25.002] – ErrorResponse
- [F25.003] – ValidationErrorResponse
- [F25.004] – PaginatedResponse
- [F25.005] – HealthResponse
- [F25.006] – MetricsResponse

#### F26 — REST Response Headers

- [F26.001] – X-Request-Id
- [F26.002] – Retry-After
- [F26.003] – X-RateLimit-Limit
- [F26.004] – X-RateLimit-Remaining
- [F26.005] – X-RateLimit-Reset
- [F26.006] – Content-Type
- [F26.007] – Cache-Control
- [F26.008] – ETag

#### F27 — REST Status Codes

- [F27.001] – 401 Unauthorized
- [F27.002] – 403 Forbidden
- [F27.003] – 409 Conflict
- [F27.004] – 200 OK
- [F27.005] – 201 Created
- [F27.006] – 204 No Content
- [F27.007] – 400 Bad Request
- [F27.008] – 404 Not Found
- [F27.009] – 422 Unprocessable Entity
- [F27.010] – 429 Too Many Requests
- [F27.011] – 500 Internal Server Error
- [F27.012] – 503 Service Unavailable

#### F28 — REST Query Conventions

- [F28.001] – Pagination format
- [F28.002] – Sorting syntax
- [F28.003] – Filtering DSL
- [F28.004] – Field selection syntax
- [F28.005] – Expansion/embedding syntax

#### F29 — REST Middleware

- [F29.001] – Request logging middleware
- [F29.002] – Authentication middleware
- [F29.003] – Rate limiting middleware
- [F29.004] – CORS middleware
- [F29.005] – Compression middleware
- [F29.006] – Request timeout middleware
- [F29.007] – Recovery/panic middleware

---

### F3 — gRPC API (Mirror of REST)

#### F30 — gRPC Services / Methods

- [F30.001] – UserService.CreateUser
- [F30.002] – UserService.DeleteUser
- [F30.003] – UserService.GetUser
- [F30.004] – UserService.UpdateUser
- [F30.005] – UserService.ListUsers
- [F30.006] – HealthService.Check
- [F30.007] – HealthService.Watch

#### F31 — gRPC Messages

- [F31.001] – CreateUserRequest
- [F31.002] – UserResponse
- [F31.003] – UpdateUserRequest
- [F31.004] – ListUsersRequest
- [F31.005] – ListUsersResponse
- [F31.006] – Empty

#### F32 — gRPC Metadata

- [F32.001] – Auth metadata
- [F32.002] – Correlation ID
- [F32.003] – Tenant ID metadata
- [F32.004] – Trace context metadata

#### F33 — gRPC Error Mapping

- [F33.001] – Domain → gRPC status mapping
- [F33.002] – Error details encoding
- [F33.003] – Retry policy per error type

#### F34 — gRPC Interceptors

- [F34.001] – Authentication interceptor
- [F34.002] – Logging interceptor
- [F34.003] – Metrics interceptor
- [F34.004] – Recovery interceptor
- [F34.005] – Validation interceptor

#### F35 — gRPC Connection Management

- [F35.001] – Connection pool configuration
- [F35.002] – Keep-alive settings
- [F35.003] – Timeout configuration
- [F35.004] – Retry policy

---

### F4 — Security Model

#### F41 — Authentication

- [F41.001] – JWT authentication
- [F41.002] – mTLS authentication
- [F41.003] – API key authentication
- [F41.004] – OAuth2 flow
- [F41.005] – Token refresh mechanism

#### F42 — Authorization

- [F42.001] – Role model
- [F42.002] – Scope model
- [F42.003] – ABAC rules
- [F42.004] – Permission model
- [F42.005] – Resource ownership rules

#### F43 — Security Enforcement

- [F43.001] – Auth middleware order
- [F43.002] – Token validation rules
- [F43.003] – Rate limiting rules
- [F43.004] – IP allowlist/blocklist
- [F43.005] – Request size limits

#### F44 — Security Headers

- [F44.001] – HSTS configuration
- [F44.002] – CSP configuration
- [F44.003] – X-Frame-Options
- [F44.004] – X-Content-Type-Options

#### F45 — Input Validation

- [F45.001] – Request body size limits
- [F45.002] – Field length constraints
- [F45.003] – Type validation rules
- [F45.004] – SQL injection prevention
- [F45.005] – XSS prevention
- [F45.006] – Path traversal prevention

---

### F5 — Service Layer

#### F50 — Service Methods

- [F50.001] – CreateUser
- [F50.002] – DeleteUser
- [F50.003] – GetUser
- [F50.004] – UpdateUser
- [F50.005] – ListUsers
- [F50.006] – SearchUsers

#### F51 — Concurrency Model

- [F51.001] – Worker pool usage
- [F51.002] – Cancellation propagation
- [F51.003] – Timeout handling
- [F51.004] – Goroutine/thread management
- [F51.005] – Lock/mutex strategy
- [F51.006] – Async operation patterns

#### F52 — Caching

- [F52.001] – User cache
- [F52.002] – TTL rules
- [F52.003] – Cache invalidation strategy
- [F52.004] – Cache key format
- [F52.005] – Cache warming strategy
- [F52.006] – Cache miss handling

#### F53 — Observability

- [F53.001] – user_create_total metric
- [F53.002] – latency histogram
- [F53.003] – error_count metric
- [F53.004] – active_connections gauge
- [F53.005] – request_duration metric
- [F53.006] – database_query_duration metric

#### F54 — Error Handling

- [F54.001] – Error type hierarchy
- [F54.002] – Error wrapping strategy
- [F54.003] – Retry logic
- [F54.004] – Circuit breaker configuration
- [F54.005] – Fallback behavior
- [F54.006] – Error context enrichment

#### F55 — Business Logic

- [F55.001] – Validation rules
- [F55.002] – Business constraints
- [F55.003] – State machine definitions
- [F55.004] – Workflow orchestration
- [F55.005] – Compensation logic

#### F56 — Data Transformation

- [F56.001] – DTO to domain mapping
- [F56.002] – Domain to DTO mapping
- [F56.003] – Data enrichment rules
- [F56.004] – Data sanitization rules

---

### F6 — Outbound Gateways

#### F60 — HTTP Calls

- [F60.001] – Call to Billing API
- [F60.002] – HTTP client configuration
- [F60.003] – Connection pooling
- [F60.004] – Timeout configuration
- [F60.005] – Retry policy

#### F61 — gRPC Calls

- [F61.001] – Call to Identity Service
- [F61.002] – Client connection management
- [F61.003] – Load balancing strategy
- [F61.004] – Circuit breaker configuration

#### F62 — Domain Events

- [F62.001] – UserCreated
- [F62.002] – UserUpdated
- [F62.003] – UserDeleted
- [F62.004] – Event schema versioning
- [F62.005] – Event publishing mechanism

#### F63 — Audit Events

- [F63.001] – UserDeletedAudit
- [F63.002] – UserCreatedAudit
- [F63.003] – UserUpdatedAudit
- [F63.004] – Audit event format
- [F63.005] – Audit retention policy

#### F64 — Message Queue Integration

- [F64.001] – Queue connection configuration
- [F64.002] – Message serialization format
- [F64.003] – Dead letter queue handling
- [F64.004] – Message retry policy
- [F64.005] – Consumer group configuration

#### F65 — External Service Discovery

- [F65.001] – Service registry integration
- [F65.002] – DNS resolution strategy
- [F65.003] – Service endpoint caching

---

### F7 — Persistence

#### F70 — DB Connectivity

- [F70.001] – Connection pool config
- [F70.002] – Connection timeout
- [F70.003] – Connection retry logic
- [F70.004] – Connection health checks

#### F71 — Supported Databases

- [F71.001] – PostgreSQL 14+
- [F71.002] – MySQL 8+ (if applicable)
- [F71.003] – SQLite (for testing)

#### F72 — DB Access Methods

- [F72.001] – InsertUser
- [F72.002] – DeleteUserById
- [F72.003] – UpdateUser
- [F72.004] – GetUserById
- [F72.005] – ListUsers
- [F72.006] – SearchUsers

#### F73 — Schemas

- [F73.001] – users table
- [F73.002] – audit_log table
- [F73.003] – sessions table
- [F73.004] – Index definitions
- [F73.005] – Foreign key constraints
- [F73.006] – Check constraints

#### F74 — Access Filters

- [F74.001] – Tenant isolation filter
- [F74.002] – Soft delete filter
- [F74.003] – Row-level security policies

#### F75 — Migrations

- [F75.001] – Initial schema
- [F75.002] – Add indexes
- [F75.003] – Migration tooling
- [F75.004] – Rollback strategy
- [F75.005] – Data migration scripts

#### F76 — Retention

- [F76.001] – Soft delete policy
- [F76.002] – Hard delete schedule
- [F76.003] – Archive strategy
- [F76.004] – GDPR compliance rules

#### F77 — Fixtures

- [F77.001] – Test users
- [F77.002] – Test data seeding
- [F77.003] – Fixture cleanup strategy

#### F78 — Transaction Management

- [F78.001] – Transaction isolation level
- [F78.002] – Transaction timeout
- [F78.003] – Nested transaction handling
- [F78.004] – Savepoint usage
- [F78.005] – Distributed transaction strategy

#### F79 — Query Optimization

- [F79.001] – Query plan analysis
- [F79.002] – N+1 query prevention
- [F79.003] – Batch operation patterns
- [F79.004] – Prepared statement usage

---

### F9 — Triggers & Background Execution

#### F90 — Event Subscriptions

- [F80.001] – UserDeleted event listener
- [F80.002] – UserCreated event listener
- [F80.003] – Event handler retry logic
- [F80.004] – Event ordering guarantees
- [F80.005] – Event deduplication

#### F81 — Background Tasks

- [F81.001] – Cleanup job
- [F81.002] – Sync scheduler
- [F81.003] – Report generation job
- [F81.004] – Data aggregation job

#### F82 — Scheduled Jobs

- [F82.001] – Cron schedule format
- [F82.002] – Job locking mechanism
- [F82.003] – Job failure handling
- [F82.004] – Job monitoring

#### F83 — Async Processing

- [F83.001] – Task queue configuration
- [F83.002] – Worker pool sizing
- [F83.003] – Priority queue handling
- [F83.004] – Task timeout configuration

---

### F9 — Tests

#### F90 — Unit Tests

- [F80.001] – User service tests
- [F80.002] – Handler tests
- [F80.003] – Repository tests
- [F80.004] – Test coverage requirements
- [F80.005] – Mock/stub strategy

#### F71 — End-to-End Tests

- [F81.001] – Create user flow
- [F81.002] – Update user flow
- [F81.003] – Delete user flow
- [F81.004] – Test environment setup
- [F81.005] – Test data management

#### F72 — Performance Tests

- [F82.001] – User creation load test
- [F82.002] – Read throughput test
- [F82.003] – Concurrent user test
- [F82.004] – Performance baseline metrics

#### F73 — Security Tests

- [F83.001] – Auth bypass test
- [F83.002] – SQL injection test
- [F83.003] – XSS test
- [F83.004] – CSRF test
- [F83.005] – Rate limiting test

#### F74 — Integration Tests

- [F84.001] – Database integration tests
- [F84.002] – External API integration tests
- [F84.003] – Message queue integration tests
- [F84.004] – Cache integration tests

#### F75 — Contract Tests

- [F85.001] – API contract tests
- [F85.002] – Event schema tests
- [F85.003] – Consumer-driven contract tests

#### F76 — Chaos Tests

- [F86.001] – Network failure simulation
- [F86.002] – Database failure simulation
- [F86.003] – Dependency failure simulation
- [F86.004] – Resource exhaustion tests


---

## 3. Usage Guidelines

### 3.1 Creating New Facts

1. Check if the item is used by ≥2 consumers
2. Assign the next sequential ID in the appropriate category
3. Document all attributes and references
4. Never reuse or renumber existing IDs

### 3.2 Referencing Facts

- Always use Fact IDs (e.g., F20.001) when referencing
- Never duplicate fact content inline
- Maintain a clear dependency graph

### 3.3 Migration Workflow

When migrating from one language to another:

1. **Extract** all facts from the source service
2. **Map** facts to the taxonomy categories
3. **Identify** language-specific vs. language-agnostic facts
4. **Transform** language-specific implementations
5. **Preserve** all language-agnostic facts
6. **Validate** completeness using this taxonomy

### 3.4 Maintenance

- Review facts quarterly for accuracy
- Add new categories as needed (F20+)
- Mark deprecated facts but never delete them
- Version the taxonomy document itself

---

## 4. Missing Categories Analysis

The following areas may need facts depending on your service:

- **GraphQL API** (if applicable) - would be F3X series
- **WebSocket/SSE** handlers - real-time communication
- [File upload/download** handling
- **Multi-tenancy** isolation mechanisms
- [Feature flags** and A/B testing
- **Compliance** requirements (GDPR, HIPAA, etc.)
- **Disaster recovery** procedures
- **Backup and restore** strategies
- **Service mesh** integration (Istio, Linkerd)
- **API versioning** strategy
- **Deprecation policy** for APIs
- **SLA/SLO** definitions
- **Cost allocation** and billing integration

---

## 5. Appendix

### 5.1 Fact Template

```markdown
**F[CATEGORY][SUBCATEGORY].[ID]** – [Name]

**Description:** [What this fact represents]

**Attributes:**
- attribute1: value
- attribute2: value

**References:**
- F[X].[Y] – [Related fact]

**Implementation Notes:**
- [Language-specific considerations]
- [Migration gotchas]
```

### 5.2 Example Fact

```markdown
**F20.001] – POST /users

**Description:** REST endpoint for creating a new user

**Attributes:**
- method: POST
- path: /users
- timeout: 30s

**References:**
- F23.001 – CreateUserRequest (request schema)
- F25.001 – UserResponse (response schema)
- F24.001 – Authorization (required header)
- F27.005 – 201 Created (success status)
- F27.007 – 400 Bad Request (validation error)
- F41.001 – JWT authentication (auth method)

**Implementation Notes:**
- Go: Uses gorilla/mux router
- Rust: Uses axum framework
```

---

## Version History

- **v1.0] – Initial taxonomy structure
- **v1.1] – Added F07 (Configuration), F08 (Logging), enriched all categories
- **v1.2] – Added F9 (Domain Models), F29 (REST Middleware), F34 (gRPC Interceptors)
