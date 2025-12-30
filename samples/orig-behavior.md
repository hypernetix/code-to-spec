# User Service - Original Behavior Analysis

## Service Overview

The User Service is a multi-tenant user identity and profile management system that provides CRUD operations for user entities across different tenants. It supports JWT-based authentication, role-based access control, hierarchical tenant relationships, and comprehensive event publishing for user lifecycle changes.

---

## Core Capabilities

### 1. User Management

Users are identity entities within tenants that represent individuals with authentication credentials and profile information.

### 2. Profile Management  

User profiles contain personal information, preferences, and metadata associated with user identities.

### 3. Multi-tenancy & Hierarchical Access

Supports tenant hierarchy with user isolation and cross-tenant user queries for authorized administrators.

### 4. Authentication Integration

Integrates with external Auth Provider for JWT token validation and session management.

---

## API Scenarios

### Scenario 1: Create User

**Endpoint:** `POST /api/users/v1/users`

**Primary Path:**

1. Client sends authenticated request with JWT token [FE01.001]
2. System extracts authorization context (tenant UUID, user ID, client ID, roles) [FE01.001]
3. System validates request body against user schema [FE05.003]
4. System validates tenant exists and is enabled [FB00.004]
5. System checks if email already exists within tenant [FB01.002]
6. System validates email format [FE05.003]
7. System validates username format (alphanumeric, 3-20 chars) [FB02.002]
8. System validates password complexity if provided [FE01.002]
9. System creates user record in database with status 'PENDING' [FH02.001]
10. System publishes UserCreated event to message queue [FG02.001]
11. System publishes audit event if configured [FG03.002]
12. System returns 201 Created

**Error Paths:**

- 400 Bad Request: Invalid email format, invalid username format, password complexity failure
- 401 Unauthorized: Missing or invalid JWT token [FC07.001]
- 403 Forbidden: Insufficient permissions to create users [FC07.002]
- 404 Not Found: Tenant not found
- 409 Conflict: Email already exists [FC07.003]
- 500 Internal Server Error: Database error, event publishing failure

**Access Controls:**

- Role check: Requires 'user:create' permission from token scopes [FE02.001]
- Tenant check: Requestor must have access to target tenant [FB00.004]
- License check: NOT IMPLEMENTED (no license validation)
- Quota check: NOT IMPLEMENTED (no quota enforcement)

**Side Effects:**

- Database: INSERT into users table
- Events: UserCreated notification event
- Events: UserCreated audit event (if audit_event_target != 'none')

**Configuration:**

- Event targets: notification_event_target, audit_event_target
- Password policy: Configurable complexity requirements

**Timeouts/Limits:**

- HTTP request timeout: 30 seconds
- Database query timeout: 30 seconds (via context)
- Event publishing timeout: 30 seconds

---

### Scenario 2: Get User by ID

**Endpoint:** `GET /api/users/v1/users/{id}`

**Primary Path:**

1. Client sends authenticated request with JWT token [FA00.001]
2. System extracts authorization context [FE01.001]
3. System validates UUID format for id parameter [FE05.003]
4. System validates tenant exists and is enabled [FB00.004]
5. System retrieves user entity by ID [FH02.004]
6. System validates user is not soft-deleted (deleted_at IS NULL) [FH04.002]
7. System checks requestor has permission to view user [FE02.001]
8. System returns 200 OK with user profile

**Error Paths:**

- 400 Bad Request: Invalid UUID format
- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions to view user
- 404 Not Found: User not found, user is soft-deleted, tenant not found
- 500 Internal Server Error: Database error

**Access Controls:**

- Role check: Requires 'user:read' permission from token scopes
- Tenant check: Requestor must have access to user's tenant
- Self-access: Users can always read their own profile
- License check: NOT IMPLEMENTED
- Quota check: NOT IMPLEMENTED

**Soft-deleted Items:** Excluded by default (deleted_at IS NULL)

**DB Access:**

- Query: SELECT from users table with filters
- Transaction: Read-only, no transaction required

**Timeouts/Limits:**

- Query timeout: 30 seconds
- No rate limiting

---

### Scenario 3: Update User

**Endpoint:** `PUT /api/users/v1/users/{id}`

**Primary Path:**

1. Client sends authenticated request with JWT token [FA00.001]
2. System extracts authorization context [FE01.001]
3. System validates request body [FE05.003]
4. System validates UUID format for id parameter [FE05.003]
5. System retrieves user entity by ID [FH02.004]
6. System validates tenant exists and is enabled [FB00.004]
7. System validates user is not soft-deleted
8. System checks requestor has permission to update user [FE02.001]
9. System validates email uniqueness if email is being changed [FB01.002]
10. System validates username format if username is being changed [FB02.002]
11. System updates user fields [FH02.003]
12. System publishes UserUpdated event [FG02.002]
13. System publishes audit event if configured [FG03.003]
14. System returns 204 No Content

**Error Paths:**

- 400 Bad Request: Invalid request body, validation failure
- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions
- 404 Not Found: User not found, tenant not found
- 409 Conflict: Email already exists
- 500 Internal Server Error: Database error, event publishing failure

**Access Controls:**

- Role check: Requires 'user:update' permission
- Tenant check: Requestor must have access to user's tenant
- Self-update: Users can update their own profile with limited fields
- License check: NOT IMPLEMENTED
- Quota check: NOT IMPLEMENTED

**Side Effects:**

- Database: UPDATE users table
- Events: UserUpdated notification event
- Events: UserUpdated audit event

**DB Access:**

- Query: UPDATE users SET ... WHERE id = ? AND deleted_at IS NULL
- Transaction: Single UPDATE statement, auto-commit

**Timeouts/Limits:**

- Query timeout: 30 seconds
- Event publishing timeout: 30 seconds

---

### Scenario 4: Delete User (Soft Delete)

**Endpoint:** `DELETE /api/users/v1/users/{id}`

**Primary Path:**

1. Client sends authenticated request with JWT token [FE01.001]
2. System extracts authorization context [FE01.001]
3. System validates UUID format for id parameter [FE05.003]
4. System retrieves user entity [FH02.004]
5. System validates tenant exists and is enabled [FB00.004]
6. System checks requestor has permission to delete user [FE02.001]
7. System soft-deletes user (sets deleted_at timestamp) [FH02.002]
8. System publishes UserDeleted event [FG02.003]
9. System publishes audit event if configured [FG03.003]
10. System returns 204 No Content

**Error Paths:**

- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions, cannot delete self
- 404 Not Found: User not found, tenant not found
- 500 Internal Server Error: Database error, event publishing failure

**Access Controls:**

- Role check: Requires 'user:delete' permission
- Tenant check: Requestor must have access to user's tenant
- Self-delete: Users cannot delete their own account

**Side Effects:**

- Database: UPDATE users SET deleted_at = NOW()
- Events: UserDeleted notification event
- Events: UserDeleted audit event

---

### Scenario 5: List Users

**Endpoint:** `GET /api/users/v1/users`

**Query Parameters:**

- `tenant_id`: Filter by tenant UUID (optional)
- `status`: Filter by status (PENDING, ACTIVE, INACTIVE, DELETED) - default: ALL
- `allow_deleted`: Include soft-deleted users (default: false)
- `email`: Filter by email (optional)
- `username`: Filter by username (optional)
- `limit`: Pagination limit (default: 100, max: 1000)
- `after`: Cursor for pagination

**Primary Path:**

1. Client sends authenticated request with JWT token [FE01.001]
2. System extracts authorization context [FE01.001]
3. System validates query parameters [FE05.003]
4. System validates tenant exists and is enabled if tenant_id provided [FB00.004]
5. System checks requestor has permission to list users [FE02.001]
6. System queries users table with filters [FH02.005]
7. System applies soft-delete filter (exclude deleted_at IS NOT NULL unless allow_deleted=true)
8. System applies status filter
9. System applies pagination (limit, cursor)
10. System returns 200 OK with user list and pagination cursor

**Error Paths:**

- 400 Bad Request: Invalid query parameters
- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions
- 404 Not Found: Tenant not found
- 500 Internal Server Error: Database error

**Access Controls:**

- Role check: Requires 'user:read' permission
- Tenant check: Requestor must have access to queried tenants
- Cross-tenant: Only admins can query across multiple tenants
- License check: NOT IMPLEMENTED
- Quota check: NOT IMPLEMENTED

**Soft-deleted Items:** Excluded by default, included if allow_deleted=true

**DB Access:**

- Query: SELECT from users table with filters
- Joins: tenants
- Transaction: Read-only, no transaction required

**Timeouts/Limits:**

- Query timeout: 30 seconds
- Pagination limit: Default 100, max 1000
- No rate limiting

---

### Scenario 6: Search Users

**Endpoint:** `GET /api/users/v1/users/search`

**Query Parameters:**

- `q`: Search query string (required)
- `tenant_id`: Filter by tenant UUID (optional)
- `fields`: Fields to search (email, username, full_name) - default: all
- `limit`: Pagination limit (default: 100, max: 1000)
- `after`: Cursor for pagination

**Primary Path:**

1. Client sends authenticated request with JWT token [FE01.001]
2. System extracts authorization context [FE01.001]
3. System validates query parameters [FE05.003]
4. System validates search query is not empty
5. System checks requestor has permission to search users [FE02.001]
6. System performs full-text search on specified fields [FH02.006]
7. System applies tenant filter if provided
8. System applies soft-delete filter
9. System applies pagination
10. System returns 200 OK with search results

**Error Paths:**

- 400 Bad Request: Empty search query, invalid parameters
- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions
- 500 Internal Server Error: Database error, search engine error

**Access Controls:**

- Role check: Requires 'user:read' permission
- Tenant check: Requestor must have access to queried tenants
- License check: NOT IMPLEMENTED
- Quota check: NOT IMPLEMENTED

**DB Access:**

- Query: Full-text search with LIKE or search engine integration
- Transaction: Read-only

**Timeouts/Limits:**

- Query timeout: 30 seconds
- Search result limit: Max 1000

---

### Scenario 7: Update User Status

**Endpoint:** `PATCH /api/users/v1/users/{id}/status`

**Request Body:**

```json
{
  "status": "ACTIVE"
}
```

**Primary Path:**

1. Client sends authenticated request with JWT token [FE01.001]
2. System extracts authorization context [FE01.001]
3. System validates request body [FE05.003]
4. System validates UUID format for id parameter [FE05.003]
5. System retrieves user entity [FH02.004]
6. System validates tenant exists and is enabled [FB00.004]
7. System checks requestor has permission to update user status [FE02.001]
8. System validates status transition is allowed [FB03.001]
9. System updates user status [FH02.003]
10. System publishes UserStatusChanged event [FG02.004]
11. System publishes audit event [FG03.003]
12. System returns 204 No Content

**Error Paths:**

- 400 Bad Request: Invalid status value, invalid status transition
- 401 Unauthorized: Missing or invalid JWT token
- 403 Forbidden: Insufficient permissions
- 404 Not Found: User not found
- 500 Internal Server Error: Database error

**Access Controls:**

- Role check: Requires 'user:update:status' permission
- Tenant check: Requestor must have access to user's tenant

**Side Effects:**

- Database: UPDATE users SET status = ?, updated_at = NOW()
- Events: UserStatusChanged notification event
- Events: UserStatusChanged audit event

**State Transitions:**

- PENDING → ACTIVE (user verification complete)
- ACTIVE → INACTIVE (user deactivation)
- INACTIVE → ACTIVE (user reactivation)
- Any → DELETED (soft delete via DELETE endpoint)

---

## Background Jobs

### Job: PruneInactiveUsers

**Trigger:** Scheduled Cron Job (Daily at 00:00 UTC)

**Preconditions:** None

**Primary Path:**

1. Job starts at scheduled time [FI02.001]
2. System queries users with status 'INACTIVE' and updated_at > 30 days ago [FH02.002]
3. For each user:
   - System performs hard delete (DELETE FROM users) [FH02.002]
   - System publishes UserPruned event [FG02.005]
4. System logs job outcome (count of pruned users) [FA08.001]
5. Job completes

**Error Paths:**

- Database timeout → Retry 3 times with exponential backoff, then log error [FF04.003]
- Event publishing failure → Store in dead letter queue [FG04.003]

**Configuration:**

- Schedule: Daily at 00:00 UTC (configurable via cron expression)
- Retention period: 30 days (configurable)
- Batch size: 100 users per batch

**Timeouts/Limits:**

- Job timeout: 1 hour
- Database query timeout: 30 seconds per query
- Retry attempts: 3

**Side Effects:**

- Database: DELETE FROM users WHERE id IN (...)
- Events: UserPruned events for each deleted user
- Logs: Structured log with count of pruned users

---

## Data Models

### Model: User

**Fields:**

- `id`: UUID (Primary Key)
- `tenant_id`: UUID (Foreign Key to tenants)
- `email`: VARCHAR(255) (Unique within tenant)
- `username`: VARCHAR(50)
- `password_hash`: VARCHAR(255) (Nullable)
- `full_name`: VARCHAR(255) (Nullable)
- `status`: ENUM (PENDING, ACTIVE, INACTIVE, DELETED)
- `created_at`: TIMESTAMP
- `updated_at`: TIMESTAMP
- `deleted_at`: TIMESTAMP (Nullable)
- `created_by`: UUID (Nullable)
- `updated_by`: UUID (Nullable)

**Relationships:**

- Many-to-One with `tenants` (tenant_id)
- One-to-Many with `audit_logs`
- One-to-Many with `user_sessions`

**Constraints:**

- Email must be unique within tenant [FB01.002]
- Username must be alphanumeric, 3-20 characters [FB02.002]
- UNIQUE INDEX on (tenant_id, email)
- INDEX on (tenant_id, status)
- INDEX on (deleted_at)

**Soft Delete:**

- Records are soft-deleted by setting deleted_at timestamp
- Soft-deleted records excluded from standard queries

---

## Integration Points

### External Service: Auth Provider

**Purpose:** Validate JWT tokens and manage user sessions

**Protocol:** HTTP/REST

**Endpoint Configuration:**

- Base URL: Configured via `config.yml` auth_provider.endpoint
- Connection timeout: 10 seconds [FA05.001]
- Request timeout: 10 seconds [FA05.001]

**Operations:**

- `POST /api/auth/v1/verify`: Validate JWT token [FG01.001]
  - Request: `{"token": "jwt_string"}`
  - Response: `{"valid": true, "claims": {...}}`
  - Timeout: 10 seconds
  - Retry: 3 attempts with exponential backoff (1s, 2s, 4s)
  - Error handling: Return 401 if validation fails
  
- `POST /api/auth/v1/revoke`: Revoke user sessions [FG01.002]
  - Request: `{"user_id": "uuid"}`
  - Response: `{"revoked": true}`
  - Timeout: 10 seconds
  - Retry: 3 attempts with exponential backoff
  - Error handling: Log error, continue operation (non-critical)

**Circuit Breaker:**

- Threshold: 50% failures over 10 requests
- Timeout: 30 seconds
- Sleep window: 5 seconds
- Half-open state: 1 test request

**Code Reference:** `internal/clients/auth_client.go`, `configs/config.yml` auth_provider section

---

### Message Queue: RabbitMQ

**Purpose:** Publish user lifecycle events for downstream consumers

**Protocol:** AMQP 0.9.1

**Connection Configuration:**

- Host/Port: Configured via `config.yml` rabbitmq section [FA05.002]
- Connection timeout: 30 seconds
- Heartbeat interval: 60 seconds
- Auto-reconnect: Enabled with exponential backoff

**Exchange:** users.events (topic exchange)

**Routing Keys:**

- `users.created`: UserCreated event
- `users.updated`: UserUpdated event
- `users.deleted`: UserDeleted event
- `users.status_changed`: UserStatusChanged event
- `users.pruned`: UserPruned event

**Events Published:**

- `users.created`: UserCreated event [FG02.001]
- `users.updated`: UserUpdated event [FG02.002]
- `users.deleted`: UserDeleted event [FG02.003]
- `users.status_changed`: UserStatusChanged event [FG02.004]
- `users.pruned`: UserPruned event [FG02.005]

**Event Format:**

```json
{
  "event_type": "UserCreated",
  "event_id": "uuid",
  "timestamp": "2024-01-01T00:00:00Z",
  "tenant_id": "uuid",
  "user_id": "uuid",
  "data": {
    "email": "user@example.com",
    "username": "username",
    "status": "PENDING"
  }
}
```

**Retry Mechanism:**

- Failed events stored in messages table [FG04.003]
- Background job retries every 5 minutes
- Retry attempts: 3 with exponential backoff
- Messages >24 hours marked as permanently failed
- Dead letter queue: messages.failed

**Code Reference:** `internal/mq/publisher.go`, `internal/jobs/retry_messages.go`

### External Service: Tenant Service

**Purpose:** Validate tenant existence and retrieve tenant hierarchy information

**Protocol:** HTTP/REST

**Endpoint Configuration:**

- Base URL: Configured via `config.yml` tenant_service.endpoint [FA05.003]
- Connection timeout: 10 seconds
- Request timeout: 30 seconds

**Operations:**

- `GET /api/tenants/v1/tenants/{id}`: Retrieve tenant details [FG01.003]
  - Response: `{"id": "uuid", "status": "enabled", "parent_id": "uuid"}`
  - Timeout: 30 seconds
  - Retry: 3 attempts with exponential backoff
  - Caching: 5 minutes TTL
  - Error handling: Return 404 if tenant not found

**Code Reference:** `internal/clients/tenant_client.go`, `configs/config.yml` tenant_service section

---

## Performance

### Database Performance

- **Connection pool**: Max 20 connections, Min 2 idle [FH00.001]
- **Query timeout**: 30 seconds per query
- **Index usage**: (tenant_id, email), (tenant_id, status), (deleted_at)
- **Bulk operations**: List users limited to 1000 per request

### Known Bottlenecks

- **Large tenant queries**: Tenants with >10,000 users may experience slow list operations
- **Search performance**: Full-text search degrades with >100,000 total users
- **Event publishing**: High-frequency updates (>100/sec) may cause message queue backlog

### Caching Strategy

- **Tenant validation**: 5-minute TTL in-memory cache
- **Auth token validation**: No caching (security requirement)
- **User lookups**: No caching (consistency requirement)

---

## Special Features

### Multi-tenancy

The User Service supports hierarchical multi-tenancy where users belong to specific tenants.

**How it works:**

1. Each user is associated with a single tenant via tenant_id
2. Users can only be accessed by requestors with access to that tenant
3. Administrators can query users across multiple tenants
4. Email uniqueness is enforced per tenant (not globally)

**Use cases:**

- Tenant isolation for SaaS applications
- Hierarchical organization structures
- Cross-tenant user management for administrators

---

### Self-Service User Management

Users can manage their own profiles with limited permissions.

**How it works:**

1. Users with 'self_manage' role can update their own profile
2. Self-updates are limited to specific fields (full_name, email, password)
3. Status changes and deletions require admin permissions
4. Self-updates trigger same events as admin updates

**Restrictions:**

- Cannot change own status
- Cannot delete own account
- Cannot change tenant_id
- Cannot modify created_at, updated_at timestamps

---

### Password Management

Password complexity requirements ensure secure user credentials.

**Requirements:**

- Minimum 12 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

**Implementation:**

- Passwords are hashed using bcrypt with cost factor 12
- Password hashes stored in password_hash field
- Plain text passwords never stored or logged

---

## Dead Code / Incomplete Features

**[F00.010] – Email verification**
Status: Partially implemented, not enforced. Location: email_verification table. Recommendation: Complete or remove.

**[F00.011] – Two-factor authentication**
Status: Table exists, no API/enforcement. Location: user_2fa table. Recommendation: Complete or remove.

**[F00.012] – User preferences**
Status: Column exists, no validation. Location: users.preferences JSONB. Recommendation: Define schema or remove.

**[F00.013] – Password reset tokens**
Status: Implemented but no expiration cleanup. Location: password_reset_tokens table. Recommendation: Implement cleanup job.

**[F00.014] – User roles**
Status: Deprecated, moved to Auth Provider. Location: user_roles table. Recommendation: Remove table after migration complete.
