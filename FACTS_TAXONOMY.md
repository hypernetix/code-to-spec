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

#### F23 — Domain Services

- [F23.001] – UserService
- [F23.002] – AuthenticationService
- [F23.003] – NotificationService
- [F23.004] – PaymentService
- [F23.005] – ReportingService

#### F24 — Repositories

- [F24.001] – UserRepository
- [F24.002] – SessionRepository
- [F24.003] – AuditLogRepository
- [F24.004] – Repository interface patterns
- [F24.005] – Repository error handling

#### F25 — Business Rules

- [F25.001] – User validation rules
- [F25.002] – Password complexity requirements
- [F25.003] – Email uniqueness constraint
- [F25.004] – Account status transitions
- [F25.005] – Data retention policies

#### F26 — Domain Events

- [F26.001] – UserRegistered
- [F26.002] – UserActivated
- [F26.003] – UserDeactivated
- [F26.004] – PasswordChanged
- [F26.005] – Event versioning strategy

#### F27 — Formal Product Features

- [F27.001] – Hierarchical multi-tenancy support
- [F27.002] – Per tenant and per user quotas
- [F27.003] – Feature flag management
- [F27.004] – License management
- [F27.005] – Usage tracking and metering

---

### F3 — REST API

#### F30 — REST Handlers (TOP-LEVEL FACT)

*Each handler = exactly one Fact*

**Examples:**
- [F30.001] – POST /users
- [F30.002] – GET /users/{id}
- [F30.003] – DELETE /users/{id}
- [F30.004] – PUT /users/{id}
- [F30.005] – PATCH /users/{id}
- [F30.006] – GET /users (list/search)
- [F30.007] – GET /health
- [F30.008] – GET /metrics
- [F30.009] – GET /ready

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

#### F31 — REST Request Parameters (SHARED ONLY)

**Examples:**
- [F31.001] – tenant_id query parameter
- [F31.002] – page query parameter
- [F31.003] – limit query parameter
- [F31.004] – offset query parameter
- [F31.005] – sort query parameter
- [F31.006] – filter query parameter
- [F31.007] – search query parameter
- [F31.008] – include_deleted query parameter

#### F32 — REST Path Parameters

**Examples:**
- [F32.001] – {id} path parameter format
- [F32.002] – {tenant_id} path parameter format
- [F32.003] – Path parameter validation rules
- [F32.004] – UUID format validation
- [F32.005] – Slug format validation

#### F33 — REST Request Schemas

**Examples:**
- [F33.001] – CreateUserRequest
- [F33.002] – UpdateUserRequest
- [F33.003] – PatchUserRequest
- [F33.004] – BulkCreateRequest
- [F33.005] – SearchRequest
- [F33.006] – FilterRequest
- [F33.007] – BatchOperationRequest

#### F34 — REST Request Headers

**Examples:**
- [F34.001] – Authorization
- [F34.002] – X-Request-Id
- [F34.003] – Content-Type
- [F34.004] – Accept
- [F34.005] – X-Tenant-Id
- [F34.006] – X-Correlation-Id
- [F34.007] – User-Agent
- [F34.008] – X-API-Version
- [F34.009] – If-Match (ETag)
- [F34.010] – If-None-Match

#### F35 — REST Response Schemas

**Examples:**
- [F35.001] – UserResponse
- [F35.002] – ErrorResponse
- [F35.003] – ValidationErrorResponse
- [F35.004] – PaginatedResponse
- [F35.005] – HealthResponse
- [F35.006] – MetricsResponse
- [F35.007] – BatchOperationResponse
- [F35.008] – AsyncOperationResponse

#### F36 — REST Response Headers

**Examples:**
- [F36.001] – X-Request-Id
- [F36.002] – Retry-After
- [F36.003] – X-RateLimit-Limit
- [F36.004] – X-RateLimit-Remaining
- [F36.005] – X-RateLimit-Reset
- [F36.006] – Content-Type
- [F36.007] – Cache-Control
- [F36.008] – ETag
- [F36.009] – Location
- [F36.010] – Link (pagination)

#### F37 — REST Status Codes

- [F37.001] – 401 Unauthorized
- [F37.002] – 403 Forbidden
- [F37.003] – 409 Conflict
- [F37.004] – 200 OK
- [F37.005] – 201 Created
- [F37.006] – 204 No Content
- [F37.007] – 400 Bad Request
- [F37.008] – 404 Not Found
- [F37.009] – 422 Unprocessable Entity
- [F37.010] – 429 Too Many Requests
- [F37.011] – 500 Internal Server Error
- [F37.012] – 503 Service Unavailable
- [F37.013] – 202 Accepted
- [F37.014] – 304 Not Modified
- [F37.015] – 412 Precondition Failed

#### F38 — REST Query Conventions

- [F38.001] – Pagination format
- [F38.002] – Sorting syntax
- [F38.003] – Filtering DSL
- [F38.004] – Field selection syntax
- [F38.005] – Expansion/embedding syntax
- [F38.006] – Search syntax
- [F38.007] – Aggregation syntax

#### F39 — REST Middleware

- [F39.001] – Request logging middleware
- [F39.002] – Authentication middleware
- [F39.003] – Rate limiting middleware
- [F39.004] – CORS middleware
- [F39.005] – Compression middleware
- [F39.006] – Request timeout middleware
- [F39.007] – Recovery/panic middleware
- [F39.008] – Request validation middleware
- [F39.009] – Response transformation middleware

---

### F4 — gRPC API

#### F40 — gRPC Services / Methods

**Examples:**
- [F40.001] – UserService.CreateUser
- [F40.002] – UserService.DeleteUser
- [F40.003] – UserService.GetUser
- [F40.004] – UserService.UpdateUser
- [F40.005] – UserService.ListUsers
- [F40.006] – HealthService.Check
- [F40.007] – HealthService.Watch

#### F41 — gRPC Messages

**Examples:**
- [F41.001] – CreateUserRequest
- [F41.002] – UserResponse
- [F41.003] – UpdateUserRequest
- [F41.004] – ListUsersRequest
- [F41.005] – ListUsersResponse
- [F41.006] – Empty

#### F42 — gRPC Metadata

- [F42.001] – Auth metadata
- [F42.002] – Correlation ID
- [F42.003] – Tenant ID metadata
- [F42.004] – Trace context metadata

#### F43 — gRPC Error Mapping

- [F43.001] – Domain → gRPC status mapping
- [F43.002] – Error details encoding
- [F43.003] – Retry policy per error type

#### F44 — gRPC Interceptors

- [F44.001] – Authentication interceptor
- [F44.002] – Logging interceptor
- [F44.003] – Metrics interceptor
- [F44.004] – Recovery interceptor
- [F44.005] – Validation interceptor

#### F45 — gRPC Connection Management

- [F45.001] – Connection pool configuration
- [F45.002] – Keep-alive settings
- [F45.003] – Timeout configuration
- [F45.004] – Retry policy

---

### F5 — Security Model

#### F50 — Authentication

- [F50.001] – JWT authentication
- [F50.002] – mTLS authentication
- [F50.003] – API key authentication
- [F50.004] – OAuth2 flow
- [F50.005] – Token refresh mechanism
- [F50.006] – SSO integration
- [F50.007] – Multi-factor authentication

#### F51 — Authorization

- [F51.001] – Role model
- [F51.002] – Scope model
- [F51.003] – ABAC rules
- [F51.004] – Permission model
- [F51.005] – Resource ownership rules
- [F51.006] – Hierarchical permissions
- [F51.007] – Dynamic permission evaluation

#### F52 — Security Enforcement

- [F52.001] – Auth middleware order
- [F52.002] – Token validation rules
- [F52.003] – Rate limiting rules
- [F52.004] – IP allowlist/blocklist
- [F52.005] – Request size limits
- [F52.006] – Session management
- [F52.007] – Brute force protection

#### F53 — Security Headers

- [F53.001] – HSTS configuration
- [F53.002] – CSP configuration
- [F53.003] – X-Frame-Options
- [F53.004] – X-Content-Type-Options
- [F53.005] – Referrer-Policy
- [F53.006] – Permissions-Policy

#### F54 — Input Validation

- [F54.001] – Request body size limits
- [F54.002] – Field length constraints
- [F54.003] – Type validation rules
- [F54.004] – SQL injection prevention
- [F54.005] – XSS prevention
- [F54.006] – Path traversal prevention
- [F54.007] – Command injection prevention
- [F54.008] – File upload validation

---

### F6 — Service Layer

#### F60 — Service Methods

- [F60.001] – CreateUser
- [F60.002] – DeleteUser
- [F60.003] – GetUser
- [F60.004] – UpdateUser
- [F60.005] – ListUsers
- [F60.006] – SearchUsers

#### F61 — Concurrency Model

- [F61.001] – Worker pool usage
- [F61.002] – Cancellation propagation
- [F61.003] – Timeout handling
- [F61.004] – Goroutine/thread management
- [F61.005] – Lock/mutex strategy
- [F61.006] – Async operation patterns

#### F62 — Caching

- [F62.001] – User cache
- [F62.002] – TTL rules
- [F62.003] – Cache invalidation strategy
- [F62.004] – Cache key format
- [F62.005] – Cache warming strategy
- [F62.006] – Cache miss handling

#### F63 — Observability

- [F63.001] – user_create_total metric
- [F63.002] – latency histogram
- [F63.003] – error_count metric
- [F63.004] – active_connections gauge
- [F63.005] – request_duration metric
- [F63.006] – database_query_duration metric

#### F64 — Error Handling

- [F64.001] – Error type hierarchy
- [F64.002] – Error wrapping strategy
- [F64.003] – Retry logic
- [F64.004] – Circuit breaker configuration
- [F64.005] – Fallback behavior
- [F64.006] – Error context enrichment

#### F65 — Business Logic

- [F65.001] – Validation rules
- [F65.002] – Business constraints
- [F65.003] – State machine definitions
- [F65.004] – Workflow orchestration
- [F65.005] – Compensation logic

#### F66 — Data Transformation

- [F66.001] – DTO to domain mapping
- [F66.002] – Domain to DTO mapping
- [F66.003] – Data enrichment rules
- [F66.004] – Data sanitization rules

---

### F7 — Outbound Gateways

#### F70 — HTTP Calls

- [F70.001] – Call to Billing API
- [F70.002] – HTTP client configuration
- [F70.003] – Connection pooling
- [F70.004] – Timeout configuration
- [F70.005] – Retry policy

#### F71 — gRPC Calls

- [F71.001] – Call to Identity Service
- [F71.002] – Client connection management
- [F71.003] – Load balancing strategy
- [F71.004] – Circuit breaker configuration

#### F72 — Domain Events

- [F72.001] – UserCreated
- [F72.002] – UserUpdated
- [F72.003] – UserDeleted
- [F72.004] – Event schema versioning
- [F72.005] – Event publishing mechanism

#### F73 — Audit Events

- [F73.001] – UserDeletedAudit
- [F73.002] – UserCreatedAudit
- [F73.003] – UserUpdatedAudit
- [F73.004] – Audit event format
- [F73.005] – Audit retention policy

#### F74 — Message Queue Integration

- [F74.001] – Queue connection configuration
- [F74.002] – Message serialization format
- [F74.003] – Dead letter queue handling
- [F74.004] – Message retry policy
- [F74.005] – Consumer group configuration

#### F75 — External Service Discovery

- [F75.001] – Service registry integration
- [F75.002] – DNS resolution strategy
- [F75.003] – Service endpoint caching

---

### F8 — Persistence

#### F80 — DB Connectivity

- [F80.001] – Connection pool config
- [F80.002] – Connection timeout
- [F80.003] – Connection retry logic
- [F80.004] – Connection health checks

#### F81 — Supported Databases

- [F81.001] – PostgreSQL 14+
- [F81.002] – MySQL 8+ (if applicable)
- [F81.003] – SQLite (for testing)

#### F82 — DB Access Methods

- [F82.001] – InsertUser
- [F82.002] – DeleteUserById
- [F82.003] – UpdateUser
- [F82.004] – GetUserById
- [F82.005] – ListUsers
- [F82.006] – SearchUsers

#### F83 — Schemas

- [F83.001] – users table
- [F83.002] – audit_log table
- [F83.003] – sessions table
- [F83.004] – Index definitions
- [F83.005] – Foreign key constraints
- [F83.006] – Check constraints

#### F84 — Access Filters

- [F84.001] – Tenant isolation filter
- [F84.002] – Soft delete filter
- [F84.003] – Row-level security policies

#### F85 — Migrations

- [F85.001] – Initial schema
- [F85.002] – Add indexes
- [F85.003] – Migration tooling
- [F85.004] – Rollback strategy
- [F85.005] – Data migration scripts

#### F86 — Retention

- [F86.001] – Soft delete policy
- [F86.002] – Hard delete schedule
- [F86.003] – Archive strategy
- [F86.004] – GDPR compliance rules

#### F87 — Fixtures

- [F87.001] – Test users
- [F87.002] – Test data seeding
- [F87.003] – Fixture cleanup strategy

#### F88 — Transaction Management

- [F88.001] – Transaction isolation level
- [F88.002] – Transaction timeout
- [F88.003] – Nested transaction handling
- [F88.004] – Savepoint usage
- [F88.005] – Distributed transaction strategy

#### F89 — Query Optimization

- [F89.001] – Query plan analysis
- [F89.002] – N+1 query prevention
- [F89.003] – Batch operation patterns
- [F89.004] – Prepared statement usage

---

### F9 — Triggers & Background Execution

#### F90 — Event Subscriptions

- [F90.001] – UserDeleted event listener
- [F90.002] – UserCreated event listener
- [F90.003] – Event handler retry logic
- [F90.004] – Event ordering guarantees
- [F90.005] – Event deduplication

#### F91 — Background Tasks

- [F91.001] – Cleanup job
- [F91.002] – Sync scheduler
- [F91.003] – Report generation job
- [F91.004] – Data aggregation job

#### F92 — Scheduled Jobs

- [F92.001] – Cron schedule format
- [F92.002] – Job locking mechanism
- [F92.003] – Job failure handling
- [F92.004] – Job monitoring

#### F93 — Async Processing

- [F93.001] – Task queue configuration
- [F93.002] – Worker pool sizing
- [F93.003] – Priority queue handling
- [F93.004] – Task timeout configuration

---

### F10 — Reporting & Analytics

#### F100 — Report Types

- [F100.001] – User activity report
- [F100.002] – System usage report
- [F100.003] – Financial report
- [F100.004] – Audit report
- [F100.005] – Performance report
- [F100.006] – Custom report definitions

#### F101 — Report Generation

- [F101.001] – Synchronous report generation
- [F101.002] – Asynchronous report generation
- [F101.003] – Scheduled report generation
- [F101.004] – Report caching strategy
- [F101.005] – Report expiration policy

#### F102 — Report Formats

- [F102.001] – PDF export
- [F102.002] – CSV export
- [F102.003] – Excel export
- [F102.004] – JSON export
- [F102.005] – HTML preview

#### F103 — Data Aggregation

- [F103.001] – Real-time aggregation
- [F103.002] – Pre-computed aggregation
- [F103.003] – Time-series aggregation
- [F103.004] – Dimensional aggregation
- [F103.005] – Aggregation refresh strategy

#### F104 — Analytics Queries

- [F104.001] – User behavior analytics
- [F104.002] – Business metrics calculation
- [F104.003] – Trend analysis
- [F104.004] – Cohort analysis
- [F104.005] – Funnel analysis

#### F105 — Dashboards

- [F105.001] – Executive dashboard
- [F105.002] – Operational dashboard
- [F105.003] – Custom dashboard definitions
- [F105.004] – Dashboard refresh intervals
- [F105.005] – Dashboard access control

---

### F11 — File Handling

#### F110 — File Upload

- [F110.001] – Single file upload
- [F110.002] – Multi-file upload
- [F110.003] – Chunked upload
- [F110.004] – Resumable upload
- [F110.005] – File size limits
- [F110.006] – Allowed file types
- [F110.007] – Virus scanning

#### F111 — File Storage

- [F111.001] – Local filesystem storage
- [F111.002] – S3-compatible storage
- [F111.003] – CDN integration
- [F111.004] – Storage path conventions
- [F111.005] – File versioning

#### F112 — File Download

- [F112.001] – Direct download
- [F112.002] – Streaming download
- [F112.003] – Signed URL generation
- [F112.004] – Download expiration
- [F112.005] – Bandwidth throttling

#### F113 — File Processing

- [F113.001] – Image resizing
- [F113.002] – Image format conversion
- [F113.003] – Document preview generation
- [F113.004] – Metadata extraction
- [F113.005] – Content indexing

---

### F12 — Multi-tenancy

#### F120 — Tenant Isolation

- [F120.001] – Database-per-tenant
- [F120.002] – Schema-per-tenant
- [F120.003] – Row-level tenant isolation
- [F120.004] – Tenant context propagation
- [F120.005] – Cross-tenant data access prevention

#### F121 — Tenant Management

- [F121.001] – Tenant provisioning
- [F121.002] – Tenant deprovisioning
- [F121.003] – Tenant configuration
- [F121.004] – Tenant hierarchy
- [F121.005] – Tenant migration

#### F122 — Tenant Resources

- [F122.001] – Per-tenant quotas
- [F122.002] – Per-tenant rate limits
- [F122.003] – Per-tenant feature flags
- [F122.004] – Per-tenant customization
- [F122.005] – Resource usage tracking

---

### F13 — Compliance & Privacy

#### F130 — GDPR Compliance

- [F130.001] – Right to access
- [F130.002] – Right to erasure
- [F130.003] – Right to portability
- [F130.004] – Consent management
- [F130.005] – Data processing records

#### F131 — Data Privacy

- [F131.001] – PII identification
- [F131.002] – Data anonymization
- [F131.003] – Data pseudonymization
- [F131.004] – Privacy by design
- [F131.005] – Data minimization

#### F132 — Audit & Compliance

- [F132.001] – Compliance audit logs
- [F132.002] – Data access logs
- [F132.003] – Compliance reporting
- [F132.004] – Regulatory requirements tracking
- [F132.005] – Compliance certification

---

### F14 — Real-time Communication

#### F140 — WebSocket

- [F140.001] – WebSocket connection management
- [F140.002] – WebSocket authentication
- [F140.003] – WebSocket message routing
- [F140.004] – Connection heartbeat
- [F140.005] – Reconnection strategy

#### F141 — Server-Sent Events (SSE)

- [F141.001] – SSE endpoint configuration
- [F141.002] – Event stream format
- [F141.003] – SSE authentication
- [F141.004] – Connection timeout
- [F141.005] – Event buffering

#### F142 — Push Notifications

- [F142.001] – Push notification providers
- [F142.002] – Device token management
- [F142.003] – Notification templates
- [F142.004] – Notification scheduling
- [F142.005] – Delivery tracking

---

### F15 — Tests

#### F150 — Unit Tests

- [F150.001] – User service tests
- [F150.002] – Handler tests
- [F150.003] – Repository tests
- [F150.004] – Test coverage requirements
- [F150.005] – Mock/stub strategy

#### F151 — End-to-End Tests

- [F151.001] – Create user flow
- [F151.002] – Update user flow
- [F151.003] – Delete user flow
- [F151.004] – Test environment setup
- [F151.005] – Test data management

#### F152 — Performance Tests

- [F152.001] – User creation load test
- [F152.002] – Read throughput test
- [F152.003] – Concurrent user test
- [F152.004] – Performance baseline metrics

#### F153 — Security Tests

- [F153.001] – Auth bypass test
- [F153.002] – SQL injection test
- [F153.003] – XSS test
- [F153.004] – CSRF test
- [F153.005] – Rate limiting test

#### F154 — Integration Tests

- [F154.001] – Database integration tests
- [F154.002] – External API integration tests
- [F154.003] – Message queue integration tests
- [F154.004] – Cache integration tests

#### F155 — Contract Tests

- [F155.001] – API contract tests
- [F155.002] – Event schema tests
- [F155.003] – Consumer-driven contract tests

#### F156 — Chaos Tests

- [F156.001] – Network failure simulation
- [F156.002] – Database failure simulation
- [F156.003] – Dependency failure simulation
- [F156.004] – Resource exhaustion tests


---

## 3. Usage Guidelines

### 3.1 Creating New Facts

#### When to Create a Fact

Create a fact when:
1. The item is used by ≥2 consumers (handlers, services, tests)
2. The item represents reusable behavior or configuration
3. The item needs independent verification or testing
4. The item may change independently from its consumers

#### When NOT to Create a Fact

Do NOT create a fact when:
- The item is used by only 1 consumer (make it an attribute instead)
- The item is purely structural with no behavioral significance
- The item is a one-off implementation detail

#### Fact Creation Process

1. **Identify the category**: Determine which F-category best fits (F0-F15)
2. **Check for duplicates**: Search existing facts to avoid duplication
3. **Assign sequential ID**: Use the next available ID in the subcategory
4. **Document thoroughly**: Include description, attributes, and references
5. **Never reuse IDs**: IDs are append-only and permanent

#### Example: Creating a REST Handler Fact

```markdown
**[F30.042] – POST /users/{id}/avatar**

**Description:** Upload user avatar image

**Attributes:**
- method: POST
- path: /users/{id}/avatar
- timeout: 60s
- max_file_size: 5MB

**References:**
- [F32.001] – {id} path parameter
- [F33.008] – FileUploadRequest schema
- [F34.001] – Authorization header
- [F37.005] – 201 Created
- [F37.007] – 400 Bad Request
- [F50.001] – JWT authentication
- [F110.001] – Single file upload
```

### 3.2 Referencing Facts

#### Best Practices

- **Always use Fact IDs** (e.g., [F30.001]) when referencing
- **Never duplicate fact content** inline in scenarios or documentation
- **Maintain clear dependency graphs** to understand fact relationships
- **Reference facts at appropriate granularity** (not too broad, not too narrow)

#### Example: Scenario with Fact References

```markdown
**Scenario: Create User**

1. Client sends [F30.001] POST /users with [F33.001] CreateUserRequest
2. System validates [F50.001] JWT authentication
3. System checks [F51.004] permission model for user.create
4. System validates [F25.002] password complexity requirements
5. System checks [F25.003] email uniqueness constraint
6. System executes [F82.001] InsertUser
7. System publishes [F72.001] UserCreated event
8. System logs [F73.002] UserCreatedAudit event
9. System returns [F37.005] 201 Created with [F35.001] UserResponse
```

### 3.3 Migration Workflow

When migrating from one language/stack to another:

#### Phase 1: Extraction

1. **Analyze source code** systematically by category (F0-F15)
2. **Extract all facts** from the source service
3. **Document attributes** specific to current implementation
4. **Capture dependencies** between facts
5. **Identify dead code** and mark it explicitly

#### Phase 2: Classification

1. **Map facts to taxonomy categories** (F0-F15)
2. **Identify language-specific facts** (e.g., Go-specific concurrency patterns)
3. **Identify language-agnostic facts** (e.g., business rules, API contracts)
4. **Mark facts for transformation** vs. preservation

#### Phase 3: Transformation

1. **Transform language-specific implementations** to target language idioms
2. **Preserve all language-agnostic facts** (business logic, API contracts)
3. **Update fact attributes** to reflect new implementation details
4. **Maintain fact IDs** throughout the migration

#### Phase 4: Validation

1. **Validate completeness** using this taxonomy as a checklist
2. **Verify all scenarios** still reference correct facts
3. **Test fact implementations** in target language
4. **Document migration decisions** in DECISIONS.md

#### Example: Migrating Authentication Fact

**Original (Go):**
```markdown
[F50.001] – JWT authentication
- Library: github.com/golang-jwt/jwt/v5
- Algorithm: RS256
- Token expiry: 1h
- Implementation: middleware/auth.go
```

**Migrated (Rust):**
```markdown
[F50.001] – JWT authentication
- Library: jsonwebtoken 9.x
- Algorithm: RS256
- Token expiry: 1h
- Implementation: middleware/auth.rs
```

### 3.4 Fact Dependency Management

#### Understanding Dependencies

- **Direct dependency**: Fact A explicitly references Fact B
- **Transitive dependency**: Fact A depends on Fact B, which depends on Fact C
- **Circular dependency**: Avoid at all costs; refactor if detected

#### Dependency Graph Example

```
[F30.001] POST /users
  └─ [F33.001] CreateUserRequest
  └─ [F35.001] UserResponse
  └─ [F50.001] JWT authentication
  └─ [F60.001] CreateUser service method
      └─ [F25.002] Password complexity
      └─ [F25.003] Email uniqueness
      └─ [F82.001] InsertUser DB method
      └─ [F72.001] UserCreated event
```

### 3.5 Fact Versioning and Evolution

#### When Facts Change

- **Minor changes**: Update attributes in place (e.g., timeout value change)
- **Major changes**: Create a new fact with new ID, mark old fact as deprecated
- **Breaking changes**: Always create new fact, document migration path

#### Deprecation Process

1. Create new fact with updated behavior
2. Mark old fact as **[DEPRECATED]** in description
3. Add reference to replacement fact
4. Document deprecation reason and timeline
5. Never delete the deprecated fact

#### Example: Deprecating a Fact

```markdown
**[F30.015] – GET /users (v1) [DEPRECATED]**

**Deprecated:** 2024-01-15  
**Replacement:** [F30.089] GET /v2/users  
**Reason:** Pagination format changed to cursor-based  
**Removal:** 2024-07-15
```

### 3.6 Testing Facts

#### Fact-Based Testing Strategy

1. **Unit tests**: Test individual facts in isolation
2. **Integration tests**: Test fact interactions and dependencies
3. **Contract tests**: Verify API facts match published contracts
4. **Migration tests**: Verify fact behavior consistency across implementations

#### Example: Testing a Fact

```markdown
**Test for [F25.002] – Password complexity requirements**

**Test Cases:**
- Valid password: 12+ chars, uppercase, lowercase, digit, special
- Invalid: too short (< 12 chars)
- Invalid: missing uppercase
- Invalid: missing digit
- Invalid: missing special character
- Invalid: common password (from blacklist)
```

### 3.7 Documentation Best Practices

#### Fact Documentation Template

```markdown
**[FXX.YYY] – [Fact Name]**

**Description:** [Clear, concise description of what this fact represents]

**Category:** [F-category name]

**Attributes:**
- attribute1: value
- attribute2: value

**References:**
- [FXX.YYY] – Related fact

**Implementation Notes:**
- [Language-specific considerations]
- [Performance characteristics]
- [Known limitations]

**Migration Notes:**
- [Gotchas when migrating]
- [Alternative implementations]
```

### 3.8 Maintenance

#### Regular Reviews

- **Quarterly review**: Validate facts for accuracy and relevance
- **Post-migration review**: Update facts based on implementation learnings
- **Deprecation review**: Identify candidates for deprecation

#### Adding New Categories

- New categories start at F16+ (F0-F15 are defined)
- Follow the same subcategory pattern (FXX.YYY)
- Document the category purpose and scope
- Update this taxonomy document version

#### Version Control

- Version the taxonomy document itself
- Track changes in version history section
- Use semantic versioning (major.minor.patch)
- Major version: structural changes to categories
- Minor version: new categories or subcategories
- Patch version: fact additions or clarifications

---

## 4. Additional Categories to Consider

The following areas may need facts depending on your service requirements:

### Potential Future Categories

- **F16 — GraphQL API** (if applicable)
  - Queries, Mutations, Subscriptions
  - Schema definitions
  - Resolvers
  - DataLoaders

- **F17 — API Versioning & Lifecycle**
  - Versioning strategy
  - Deprecation policy
  - Sunset timelines
  - Backward compatibility rules

- **F18 — Disaster Recovery**
  - Backup strategies
  - Restore procedures
  - Failover mechanisms
  - RTO/RPO definitions

- **F19 — Service Mesh Integration**
  - Istio/Linkerd configuration
  - Traffic management
  - Service discovery
  - Circuit breaking

- **F20 — Cost & Billing**
  - Cost allocation
  - Usage metering
  - Billing integration
  - Resource tagging

- **F21 — SLA/SLO Definitions**
  - Availability targets
  - Latency targets
  - Error rate budgets
  - Monitoring and alerting

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

- **v1.0** – Initial taxonomy structure
- **v1.1** – Added F07 (Configuration), F08 (Logging), enriched all categories
- **v1.2** – Added F9 (Domain Models), F29 (REST Middleware), F34 (gRPC Interceptors)
- **v2.0** – Major restructure and enhancements, including:
  - Reorganized numbers to avoid conflicts
  - Enhanced F2 (Domain Model)
  - Expanded F3 (REST API)
  - Added F10 (Reporting & Analytics), F11 (File Handling), F12 (Multi-tenancy), F13 (Compliance & Privacy), F14 (Real-time Communication)
  - Enhanced Usage Guidelines with detailed fact creation guidance, best practices, migration workflow, test strategies, and documentation templates
  - Added Section 4 (Additional Categories to Consider) for future expansion
