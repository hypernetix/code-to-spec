# Facts Taxonomy for Software Application Migration

## AI Agent Instructions

**Role**: You are a technical analyst and knowledge engineer specializing in extracting, categorizing, and documenting reusable behavioral facts from software application (e.g. microservices) codebases.

**Task**: Use this taxonomy as a reference guide when extracting facts from source code. Each fact should be independently verifiable, reusable across scenarios, and properly categorized according to this taxonomy.

> **Design Principle**: Facts are design assets. Creating, modifying, or referencing a fact changes the architectural understanding of the service and must be treated with the same rigor, traceability, and review discipline as any other design artifact.

## Purpose

This document contains a comprehensive taxonomy of knowledge about software applications (e.g. microservices) that is useful for:

- Service-to-service comparison
- Language migration (e.g., Go to Rust, Java to Go)
- Technology stack modernization
- Behavioral documentation and specification

**Framework Independence**: This taxonomy is a logical classification system. It is not tied to any specific tool or framework. The "facts" derived from this taxonomy are intended to be universal design atoms that can be fed into any AI code generation workflow or human architectural review process.

The facts taxonomy provides a structured framework for AI agents to understand what aspects of a service to document and how to categorize them

---

## 1. Core Principles

### 1.1 What is a Fact?

A **Fact** is:

> Something that can change independently and must be referenced by more than one consumer OR verified independently.

**If it doesn't meet this bar → it's an attribute, not a Fact.**

**AI Instructions**: When analyzing code, ask yourself:

- Is this behavior/rule used in multiple places? → **Fact**
- Is this specific to one handler/function? → **Attribute**
- Can this be tested independently? → **Fact**
- Is this a structural detail of one component? → **Attribute**

**Examples**:

- ✓ **Fact**: JWT token validation (used by all authenticated endpoints)
- ✗ **Attribute**: Specific parameter name in one endpoint
- ✓ **Fact**: Soft-delete filter logic (applied to multiple queries)
- ✗ **Attribute**: Local variable in a single function

### 1.2 Promotion Rule (Shared vs Local)

| Item                   | Representation            |
|------------------------|---------------------------|
| Used by ≥2 handlers    | Own Fact ID               |
| Used by 1 handler only | Attribute of handler fact |
| Semantically reusable  | Fact                      |
| Purely structural      | Attribute                 |

### 1.3 Fact Reference Rule

**Facts may only reference other facts by ID, never inline duplication.**

### 1.4 ID Format

```text
F X YY . ZZZ
│ │  │    └── sequential atomic fact ID (001-999)
│ │  └─────── subcategory (numeric 00-99)
│ └────────── category (alphabetic A-Z)
└──────────── Fact prefix
```

**Format Rules**:

- Always use alphabetic category (e.g., A, B, C)
- Always use 2-digit subcategory (e.g., 00, 01, 15)
- Always use 3-digit fact ID (e.g., .001, .050, .999)
- Complete format: `[FA01.001]`, `[FN00.010]`, `[FC07.099]`

**AI Instructions**:

- When creating new facts, use the next available sequential number in the appropriate category
- **CRITICAL**: IDs are append-only. Never renumber existing facts
- If a category is full (999 facts), create a new subcategory
- Always include the brackets when referencing: `[FA01.001]` not `FA01.001`

**Examples**:

- ✓ Correct: `[FA01.001]`, `[FC00.015]`, `[FN00.100]`
- ✗ Wrong: `[F1.1]`, `[F20.15]`, `FA01.001` (missing brackets)

---

## 2. Category Map

**AI Instructions**: This section provides the complete taxonomy of fact categories. When extracting facts from code:

1. Identify which category the fact belongs to
2. Assign the next available ID in that category
3. Document all attributes and references
4. Cross-reference related facts

### FA — Context & System Definition

**AI Instructions**: Facts in this category define the overall service context, architecture, and system-level decisions.

#### FA00 — Overview

**Purpose**: High-level service definition and boundaries

**AI Instructions**: Extract these by analyzing:

- README files, architecture documents
- Service interfaces and API definitions by scanning the source code
- Dependency declarations (go.mod, package.json, etc.)

**Facts**:

- [FA00.001] – Service mission statement
- [FA00.002] – Bounded context definition
- [FA00.003] – Non-goals / explicitly unsupported behavior
- [FA00.004] – Service dependencies map
- [FA00.005] – Data ownership boundaries

**Example**:

```markdown
[FA00.001] Service Mission Statement
**Description**: User management service responsible for CRUD operations on user entities
**Attributes**:
- Primary responsibility: User lifecycle management
- Bounded context: User domain
- Not responsible for: Authentication (handled by Auth service)
```

#### FA01 — Project Facts

- [FA01.001] – Programming language & runtime
- [FA01.002] – Project layout conventions
- [FA01.003] – Coding standards
- [FA01.004] – Versioning strategy
- [FA01.005] – Dependency management approach
- [FA01.006] – Code generation tools

#### FA02 — External Dependencies

- [FA02.001] – PostgreSQL dependency
- [FA02.002] – Redis dependency
- [FA02.003] – External auth provider
- [FA02.004] – Message queue (Kafka/RabbitMQ/etc.)
- [FA02.005] – Object storage (S3/MinIO/etc.)
- [FA02.006] – External monitoring services

#### FA03 — Deployment

- [FA03.001] – Deployment topology
- [FA03.002] – Kubernetes resources
- [FA03.003] – Scaling rules
- [FA03.004] – Health check endpoints
- [FA03.005] – Readiness probe configuration
- [FA03.006] – Liveness probe configuration
- [FA03.007] – Resource limits (CPU/Memory)
- [FA03.008] – Environment-specific configurations

#### FA04 — Build

- [FA04.001] – Build toolchain
- [FA04.002] – CI pipeline
- [FA04.003] – Artifact format
- [FA04.004] – Build optimization flags
- [FA04.005] – Multi-stage build process
- [FA04.006] – Dependency caching strategy

#### FA05 — Secrets Handling

- [FA05.001] – Secret storage mechanism
- [FA05.002] – Rotation rules
- [FA05.003] – Secret injection method
- [FA05.004] – Encryption at rest for secrets

#### FA06 — Crypto Primitives

- [FA06.001] – Hashing algorithm
- [FA06.002] – Signing algorithm
- [FA06.003] – Encryption at rest
- [FA06.004] – Key derivation function
- [FA06.005] – Random number generation

#### FA07 — Configuration Management

- [FA07.001] – Configuration sources (env vars, files, remote)
- [FA07.002] – Configuration validation rules
- [FA07.003] – Hot reload support
- [FA07.004] – Default values strategy
- [FA07.005] – Feature flags mechanism

#### FA08 — Logging & Tracing

- [FA08.001] – Log format (JSON/structured/plain)
- [FA08.002] – Log levels and their usage
- [FA08.003] – Distributed tracing implementation
- [FA08.004] – Trace sampling strategy
- [FA08.005] – Log aggregation destination
- [FA08.006] – Sensitive data redaction rules

---

### FB — Domain Model

#### FB00 — Domain Entities

- [FB00.001] – User entity
- [FB00.002] – Session entity
- [FB00.003] – AuditLog entity
- [FB00.004] – Entity lifecycle rules
- [FB00.005] – Entity validation rules
- [FB00.006] - Aggregates

#### FB01 — Value Objects

- [FB01.001] – Email value object
- [FB01.002] – PhoneNumber value object
- [FB01.003] – Address value object
- [FB01.004] – Money value object

#### FB02 — End-to-End Scenarios

**Please note:** These are orchestrations, not APIs

- [FB02.001] – Create user scenario
- [FB02.002] – Update user scenario
- [FB02.003] – Delete user scenario
- [FB02.004] – Bulk import scenario
- [FB02.005] – Data export scenario

Each scenario references:

- REST / gRPC handlers
- Service methods
- DB operations
- Events

#### FB03 — Formal Product-Management Features

- [FB03.001] - Hierarchical multi-tenancy support
- [FB03.002] - Per-tenant and per-user LLM tokens access quotas
- [FB03.003] - AI chat model selection
- [FB03.004] - AI chat history

---

### FC — REST API

**AI Instructions**: Facts in this category document REST API endpoints, contracts, and HTTP-specific behavior.

#### FC00 — REST Handlers (TOP-LEVEL FACT)

**Purpose**: Document each REST endpoint as a distinct fact

**AI Instructions**:

- Each unique endpoint (method + path) gets its own fact
- Extract by analyzing route definitions, HTTP handlers, controller methods
- Look for: `http.HandleFunc`, `@GetMapping`, `app.get()`, etc.

**Fact Structure**:

- One fact per endpoint
- Document method, path, purpose
- Reference related schemas, auth, and error facts

**Facts**:

- [FC00.001] – POST /users
- [FC00.002] – GET /users/{id}
- [FC00.003] – DELETE /users/{id}
- [FC00.004] – PUT /users/{id}
- [FC00.005] – PATCH /users/{id}
- [FC00.006] – GET /users (list/search)
- [FC00.007] – GET /health
- [FC00.008] – GET /metrics
- [FC00.009] – GET /ready

**What to Document as Attributes (NOT separate facts)**:

- Handler-specific local variables
- Endpoint-specific parameter names used only once
- Internal implementation details

**What to Reference as Facts**:

- Request/response schemas (FC03.xxx, FC05.xxx)
- Shared query parameters (FC01.xxx)
- Authentication methods (FE01.xxx)
- Status codes (FC07.xxx)
- Middleware (FC09.xxx)

**Example**:

```markdown
[FC00.001] POST /users
**Description**: Creates a new user in the system
**Attributes**:
- Method: POST
- Path: /users
- Timeout: 30s
- Handler: handlers.CreateUser
**References**:
- [FC03.001] CreateUserRequest (request body)
- [FC05.001] UserResponse (success response)
- [FC04.001] Authorization header (required)
- [FC07.005] 201 Created (success)
- [FC07.007] 400 Bad Request (validation error)
- [FE01.001] JWT authentication
- [FE02.001] Requires 'user:create' permission
**Code Location**: handlers/user.go:45-78
```

#### FC01 — REST Request Parameters (SHARED ONLY)

- [FC01.001] – tenant_id query parameter
- [FC01.002] – page query parameter
- [FC01.003] – limit query parameter
- [FC01.004] – offset query parameter
- [FC01.005] – sort query parameter
- [FC01.006] – filter query parameter

#### FC02 — REST Path Parameters

- [FC02.001] – {id} path parameter format
- [FC02.002] – {tenant_id} path parameter format
- [FC02.003] – Path parameter validation rules

#### FC03 — REST Request Schemas

- [FC03.001] – CreateUserRequest
- [FC03.002] – UpdateUserRequest
- [FC03.003] – PatchUserRequest
- [FC03.004] – BulkCreateRequest
- [FC03.005] – SearchRequest

#### FC04 — REST Request Headers

- [FC04.001] – Authorization
- [FC04.002] – X-Request-Id
- [FC04.003] – Content-Type
- [FC04.004] – Accept
- [FC04.005] – X-Tenant-Id
- [FC04.006] – X-Correlation-Id
- [FC04.007] – User-Agent

#### FC05 — REST Response Schemas

- [FC05.001] – UserResponse
- [FC05.002] – ErrorResponse
- [FC05.003] – ValidationErrorResponse
- [FC05.004] – PaginatedResponse
- [FC05.005] – HealthResponse
- [FC05.006] – MetricsResponse

#### FC06 — REST Response Headers

- [FC06.001] – X-Request-Id
- [FC06.002] – Retry-After
- [FC06.003] – X-RateLimit-Limit
- [FC06.004] – X-RateLimit-Remaining
- [FC06.005] – X-RateLimit-Reset
- [FC06.006] – Content-Type
- [FC06.007] – Cache-Control
- [FC06.008] – ETag

#### FC07 — REST Status Codes

- [FC07.001] – 401 Unauthorized
- [FC07.002] – 403 Forbidden
- [FC07.003] – 409 Conflict
- [FC07.004] – 200 OK
- [FC07.005] – 201 Created
- [FC07.006] – 204 No Content
- [FC07.007] – 400 Bad Request
- [FC07.008] – 404 Not Found
- [FC07.009] – 422 Unprocessable Entity
- [FC07.010] – 429 Too Many Requests
- [FC07.011] – 500 Internal Server Error
- [FC07.012] – 503 Service Unavailable

#### FC08 — REST Query Conventions

- [FC08.001] – Pagination format
- [FC08.002] – Sorting syntax
- [FC08.003] – Filtering DSL
- [FC08.004] – Field selection syntax
- [FC08.005] – Expansion/embedding syntax

#### FC09 — REST Middleware

- [FC09.001] – Request logging middleware
- [FC09.002] – Authentication middleware
- [FC09.003] – Rate limiting middleware
- [FC09.004] – CORS middleware
- [FC09.005] – Compression middleware
- [FC09.006] – Request timeout middleware
- [FC09.007] – Recovery/panic middleware

---

### FD — gRPC API (Mirror of REST)

#### FD00 — gRPC Services / Methods

- [FD00.001] – UserService.CreateUser
- [FD00.002] – UserService.DeleteUser
- [FD00.003] – UserService.GetUser
- [FD00.004] – UserService.UpdateUser
- [FD00.005] – UserService.ListUsers
- [FD00.006] – HealthService.Check
- [FD00.007] – HealthService.Watch

#### FD01 — gRPC Messages

- [FD01.001] – CreateUserRequest
- [FD01.002] – UserResponse
- [FD01.003] – UpdateUserRequest
- [FD01.004] – ListUsersRequest
- [FD01.005] – ListUsersResponse
- [FD01.006] – Empty

#### FD02 — gRPC Metadata

- [FD02.001] – Auth metadata
- [FD02.002] – Correlation ID
- [FD02.003] – Tenant ID metadata
- [FD02.004] – Trace context metadata

#### FD03 — gRPC Error Mapping

- [FD03.001] – Domain → gRPC status mapping
- [FD03.002] – Error details encoding
- [FD03.003] – Retry policy per error type

#### FD04 — gRPC Interceptors

- [FD04.001] – Authentication interceptor
- [FD04.002] – Logging interceptor
- [FD04.003] – Metrics interceptor
- [FD04.004] – Recovery interceptor
- [FD04.005] – Validation interceptor

#### FD05 — gRPC Connection Management

- [FD05.001] – Connection pool configuration
- [FD05.002] – Keep-alive settings
- [FD05.003] – Timeout configuration
- [FD05.004] – Retry policy

---

### FE — Security Model

#### FE01 — Authentication

- [FE01.001] – JWT authentication
- [FE01.002] – mTLS authentication
- [FE01.003] – API key authentication
- [FE01.004] – OAuth2 flow
- [FE01.005] – Token refresh mechanism

#### FE02 — Authorization

- [FE02.001] – Role model
- [FE02.002] – Scope model
- [FE02.003] – ABAC rules
- [FE02.004] – Permission model
- [FE02.005] – Resource ownership rules

#### FE03 — Security Enforcement

- [FE03.001] – Auth middleware order
- [FE03.002] – Token validation rules
- [FE03.003] – Rate limiting rules
- [FE03.004] – IP allowlist/blocklist
- [FE03.005] – Request size limits

#### FE04 — Security Headers

- [FE04.001] – HSTS configuration
- [FE04.002] – CSP configuration
- [FE04.003] – X-Frame-Options
- [FE04.004] – X-Content-Type-Options

#### FE05 — Input Validation

- [FE05.001] – Request body size limits
- [FE05.002] – Field length constraints
- [FE05.003] – Type validation rules
- [FE05.004] – SQL injection prevention
- [FE05.005] – XSS prevention
- [FE05.006] – Path traversal prevention

---

### FF — Service Layer

#### FF00 — Service Methods

- [FF00.001] – CreateUser
- [FF00.002] – DeleteUser
- [FF00.003] – GetUser
- [FF00.004] – UpdateUser
- [FF00.005] – ListUsers
- [FF00.006] – SearchUsers

#### FF01 — Concurrency Model

- [FF01.001] – Worker pool usage
- [FF01.002] – Cancellation propagation
- [FF01.003] – Timeout handling
- [FF01.004] – Goroutine/thread management
- [FF01.005] – Lock/mutex strategy
- [FF01.006] – Async operation patterns

#### FF02 — Caching

- [FF02.001] – User cache
- [FF02.002] – TTL rules
- [FF02.003] – Cache invalidation strategy
- [FF02.004] – Cache key format
- [FF02.005] – Cache warming strategy
- [FF02.006] – Cache miss handling

#### FF03 — Observability

- [FF03.001] – user_create_total metric
- [FF03.002] – latency histogram
- [FF03.003] – error_count metric
- [FF03.004] – active_connections gauge
- [FF03.005] – request_duration metric
- [FF03.006] – database_query_duration metric

#### FF04 — Error Handling

- [FF04.001] – Error type hierarchy
- [FF04.002] – Error wrapping strategy
- [FF04.003] – Retry logic
- [FF04.004] – Circuit breaker configuration
- [FF04.005] – Fallback behavior
- [FF04.006] – Error context enrichment

#### FF05 — Business Logic

- [FF05.001] – Validation rules
- [FF05.002] – Business constraints
- [FF05.003] – State machine definitions
- [FF05.004] – Workflow orchestration
- [FF05.005] – Compensation logic

#### FF06 — Data Transformation

- [FF06.001] – DTO to domain mapping
- [FF06.002] – Domain to DTO mapping
- [FF06.003] – Data enrichment rules
- [FF06.004] – Data sanitization rules

---

### FG — Outbound Gateways

#### FG00 — HTTP Calls

- [FG00.001] – Call to Billing API
- [FG00.002] – HTTP client configuration
- [FG00.003] – Connection pooling
- [FG00.004] – Timeout configuration
- [FG00.005] – Retry policy

#### FG01 — gRPC Calls

- [FG01.001] – Call to Identity Service
- [FG01.002] – Client connection management
- [FG01.003] – Load balancing strategy
- [FG01.004] – Circuit breaker configuration

#### FG02 — Domain Events

- [FG02.001] – UserCreated
- [FG02.002] – UserUpdated
- [FG02.003] – UserDeleted
- [FG02.004] – Event schema versioning
- [FG02.005] – Event publishing mechanism

#### FG03 — Audit Events

- [FG03.001] – UserDeletedAudit
- [FG03.002] – UserCreatedAudit
- [FG03.003] – UserUpdatedAudit
- [FG03.004] – Audit event format
- [FG03.005] – Audit retention policy

#### FG04 — Message Queue Integration

- [FG04.001] – Queue connection configuration
- [FG04.002] – Message serialization format
- [FG04.003] – Dead letter queue handling
- [FG04.004] – Message retry policy
- [FG04.005] – Consumer group configuration

#### FG05 — External Service Discovery

- [FG05.001] – Service registry integration
- [FG05.002] – DNS resolution strategy
- [FG05.003] – Service endpoint caching

---

### FH — Persistence

#### FH00 — DB Connectivity

- [FH00.001] – Connection pool config
- [FH00.002] – Connection timeout
- [FH00.003] – Connection retry logic
- [FH00.004] – Connection health checks

#### FH01 — Supported Databases

- [FH01.001] – PostgreSQL 14+
- [FH01.002] – MySQL 8+ (if applicable)
- [FH01.003] – SQLite (for testing)

#### FH02 — DB Access Methods

- [FH02.001] – InsertUser
- [FH02.002] – DeleteUserById
- [FH02.003] – UpdateUser
- [FH02.004] – GetUserById
- [FH02.005] – ListUsers
- [FH02.006] – SearchUsers

#### FH03 — Schemas

- [FH03.001] – users table
- [FH03.002] – audit_log table
- [FH03.003] – sessions table
- [FH03.004] – Index definitions
- [FH03.005] – Foreign key constraints
- [FH03.006] – Check constraints

#### FH04 — Access Filters

- [FH04.001] – Tenant isolation filter
- [FH04.002] – Soft delete filter
- [FH04.003] – Row-level security policies

#### FH05 — Migrations

- [FH05.001] – Initial schema
- [FH05.002] – Add indexes
- [FH05.003] – Migration tooling
- [FH05.004] – Rollback strategy
- [FH05.005] – Data migration scripts

#### FH06 — Retention

- [FH06.001] – Soft delete policy
- [FH06.002] – Hard delete schedule
- [FH06.003] – Archive strategy
- [FH06.004] – GDPR compliance rules

#### FH07 — Fixtures

- [FH07.001] – Test users
- [FH07.002] – Test data seeding
- [FH07.003] – Fixture cleanup strategy

#### FH08 — Transaction Management

- [FH08.001] – Transaction isolation level
- [FH08.002] – Transaction timeout
- [FH08.003] – Nested transaction handling
- [FH08.004] – Savepoint usage
- [FH08.005] – Distributed transaction strategy

#### FH09 — Query Optimization

- [FH09.001] – Query plan analysis
- [FH09.002] – N+1 query prevention
- [FH09.003] – Batch operation patterns
- [FH09.004] – Prepared statement usage

---

### FI — Triggers & Background Execution

#### FI00 — Event Subscriptions

- [FI00.001] – UserDeleted event listener
- [FI00.002] – UserCreated event listener
- [FI00.003] – Event handler retry logic
- [FI00.004] – Event ordering guarantees
- [FI00.005] – Event deduplication

#### FI01 — Background Tasks

- [FI01.001] – Cleanup job
- [FI01.002] – Sync scheduler
- [FI01.003] – Report generation job
- [FI01.004] – Data aggregation job

#### FI02 — Scheduled Jobs

- [FI02.001] – Cron schedule format
- [FI02.002] – Job locking mechanism
- [FI02.003] – Job failure handling
- [FI02.004] – Job monitoring

#### FI03 — Async Processing

- [FI03.001] – Task queue configuration
- [FI03.002] – Worker pool sizing
- [FI03.003] – Priority queue handling
- [FI03.004] – Task timeout configuration

---

### FJ — Tests

#### FJ00 — Unit Tests

- [FJ00.001] – User service tests
- [FJ00.002] – Handler tests
- [FJ00.003] – Repository tests
- [FJ00.004] – Test coverage requirements
- [FJ00.005] – Mock/stub strategy

#### FJ01 — End-to-End Tests

- [FJ01.001] – Create user flow
- [FJ01.002] – Update user flow
- [FJ01.003] – Delete user flow
- [FJ01.004] – Test environment setup
- [FJ01.005] – Test data management

#### FJ02 — Performance Tests

- [FJ02.001] – User creation load test
- [FJ02.002] – Read throughput test
- [FJ02.003] – Concurrent user test
- [FJ02.004] – Performance baseline metrics

#### FJ03 — Security Tests

- [FJ03.001] – Auth bypass test
- [FJ03.002] – SQL injection test
- [FJ03.003] – XSS test
- [FJ03.004] – CSRF test
- [FJ03.005] – Rate limiting test

#### FJ04 — Integration Tests

- [FJ04.001] – Database integration tests
- [FJ04.002] – External API integration tests
- [FJ04.003] – Message queue integration tests
- [FJ04.004] – Cache integration tests

#### FJ05 — Contract Tests

- [FJ05.001] – API contract tests
- [FJ05.002] – Event schema tests
- [FJ05.003] – Consumer-driven contract tests

#### FJ06 — Chaos Tests

- [FJ06.001] – Network failure simulation
- [FJ06.002] – Database failure simulation
- [FJ06.003] – Dependency failure simulation
- [FJ06.004] – Resource exhaustion tests

---

## 3. Usage Guidelines

### 3.1 Creating New Facts

1. Check if the item is used by ≥2 consumers
2. Assign the next sequential ID in the appropriate category
3. Document all attributes and references
4. Never reuse or renumber existing IDs

### 3.2 Referencing Facts

- Always use Fact IDs (e.g., FC00.001) when referencing
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
- Add new categories as needed (FK, FL, etc.)
- Mark deprecated facts but never delete them
- Version the taxonomy document itself

---

## 4. Missing Categories Analysis

The following areas may need facts depending on your service:

- **GraphQL API** (if applicable) - would be FK series
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
(e.g., FC00.001, FE01.005)

**Description:** [What this fact represents]

**Attributes:**
- attribute1: value
- attribute2: value

**References:**
- F[XYY].[ZZZ] – [Related fact]

**Implementation Notes:**
- [Language-specific considerations]
- [Migration gotchas]
```

### 5.2 Example Fact

```markdown
**FC00.001** – POST /users

**Description:** REST endpoint for creating a new user

**Attributes:**
- method: POST
- path: /users
- timeout: 30s

**References:**
- FC03.001 – CreateUserRequest (request schema)
- FC05.001 – UserResponse (response schema)
- FC04.001 – Authorization (required header)
- FC07.005 – 201 Created (success status)
- FC07.007 – 400 Bad Request (validation error)
- FE01.001 – JWT authentication (auth method)

**Implementation Notes:**
- Go: Uses gorilla/mux router
- Rust: Uses axum framework
```

---

## Version History

- **v1.0** – Initial taxonomy structure
- **v1.1** – Added FA07 (Configuration), FA08 (Logging), enriched all categories
- **v1.2** – Added FB (Domain Models), FC09 (REST Middleware), FD04 (gRPC Interceptors)
- **v2.0** – Migrated to FXYY.ZZZ format with alphabetic categories
