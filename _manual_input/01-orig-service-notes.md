# Original Service Context & Reference Material

## Purpose

This file provides essential context about the original service that may not be evident from code analysis alone. Use this information when running `prompts/01-code-to-spec.md` to ensure accurate behavior extraction and fact generation.

You have to fill, update or remove any section of this file based on your analysis of the original service.

---

## 1. Service Overview

### 1.1 Service Identity

- **Service Name**: [e.g., User Management Service]
- **Version**: [e.g., v2.3.1]
- **Repository**: [Link or path to source code]
- **Primary Language/Framework**: [e.g., Go 1.21, Gin framework]
- **Deployment Environment**: [e.g., Kubernetes, AWS ECS]

### 1.2 Business Context

- **Purpose**: [Brief description of what this service does and why it exists]
- **Key Stakeholders**: [Teams/roles that depend on this service]
- **Critical Business Rules**: [Any domain-specific rules not obvious from code]

---

## 2. Architecture & Design Patterns

### 2.1 Architectural Style

- **Pattern**: [e.g., Layered architecture, Hexagonal, Clean Architecture]
- **Key Layers**: [e.g., Handler → Service → Repository → Database]
- **Design Decisions**: [Notable architectural choices and their rationale]

### 2.2 Cross-Domain Type Identifier (CTI)

- **CTI Specification**: [Link to CTI documentation or describe inheritance hierarchy]
- **Base Entities**: [List of base domain entities]
- **Derived Types**: [Specialized entity types and their discriminators]
- **Polymorphic Behavior**: [How different entity types behave differently]

**Example:**

```text
Base: Item
  ├─ StandardItem (type: "standard")
  ├─ CustomItem (type: "custom")
  └─ TemplateItem (type: "template")
```

### 2.3 State Machines

- **Entity**: [Entity name]
- **States**: [List valid states]
- **Transitions**: [Valid state transitions and triggers]
- **Business Rules**: [Constraints on state changes]

---

## 3. External Dependencies

### 3.1 Dependent Services

List all services this service depends on:

#### Service 1: [Service Name]

- **Purpose**: [What this service provides]
- **Protocol**: [REST, gRPC, GraphQL, Message Queue]
- **Endpoints Used**: [List key endpoints/operations]
- **Documentation**: [Link to API docs, Swagger, or internal wiki]
- **Authentication**: [How this service authenticates - API key, OAuth, mTLS]
- **Timeout/Retry**: [Known timeout values, retry policies]
- **Failure Handling**: [What happens when this service is unavailable]

#### Service 2: [Service Name]

- **Purpose**: [What this service provides]
- **Protocol**: [REST, gRPC, GraphQL, Message Queue]
- **Endpoints Used**: [List key endpoints/operations]
- **Documentation**: [Link to API docs]
- **Authentication**: [Auth method]
- **Timeout/Retry**: [Policies]
- **Failure Handling**: [Degradation strategy]

### 3.2 Third-Party Integrations

- **Service/API**: [e.g., Stripe, SendGrid, Twilio]
- **Purpose**: [What it's used for]
- **Documentation**: [Link to external docs]
- **API Keys/Credentials**: [Where they're stored - env vars, secrets manager]
- **Rate Limits**: [Known rate limits]

### 3.3 Message Queues / Event Streams

- **System**: [e.g., RabbitMQ, Kafka, AWS SQS]
- **Topics/Queues**: [List of topics this service publishes to or consumes from]
- **Message Formats**: [Link to schema definitions or describe payload structure]
- **Ordering Guarantees**: [FIFO, at-least-once, exactly-once]

---

## 4. Data & Persistence

### 4.1 Database Information

- **Type**: [e.g., PostgreSQL 14, MongoDB 5.0, Redis]
- **Schema Documentation**: [Link to ER diagrams, schema docs]
- **Key Tables/Collections**: [List primary data stores]
- **Indexes**: [Critical indexes for performance]
- **Partitioning/Sharding**: [If applicable]

### 4.2 Data Migration History

- **Migration Tool**: [e.g., Flyway, Liquibase, golang-migrate]
- **Notable Migrations**: [List any complex or breaking migrations]
- **Data Seeding**: [How test/dev data is populated]

### 4.3 Caching Strategy

- **Cache System**: [e.g., Redis, Memcached, in-memory]
- **Cached Entities**: [What data is cached]
- **TTL Policies**: [Cache expiration rules]
- **Invalidation Strategy**: [How cache is invalidated]

---

## 5. Security & Compliance

### 5.1 Authentication & Authorization

- **Auth Method**: [e.g., JWT, OAuth2, API Keys, mTLS]
- **Token Provider**: [e.g., Auth0, Keycloak, internal service]
- **Token Format**: [JWT claims structure, API key format]
- **Role Definitions**: [List of roles and their meanings]
- **Permission Model**: [RBAC, ABAC, custom]

### 5.2 Data Privacy & Compliance

- **Regulations**: [e.g., GDPR, HIPAA, SOC 2, PCI-DSS]
- **PII Fields**: [List fields containing personally identifiable information]
- **Data Retention**: [How long data is kept]
- **Encryption**: [At-rest, in-transit encryption methods]
- **Audit Requirements**: [What must be logged for compliance]

### 5.3 Security Constraints

- **Rate Limiting**: [Current rate limit policies]
- **IP Whitelisting**: [If applicable]
- **CORS Policies**: [Allowed origins]
- **Input Sanitization**: [XSS, SQL injection prevention methods]

---

## 6. Observability & Operations

### 6.1 Logging

- **Log Format**: [e.g., JSON structured logs, plain text]
- **Log Levels**: [DEBUG, INFO, WARN, ERROR usage]
- **Log Aggregation**: [e.g., ELK, Splunk, CloudWatch]
- **Critical Log Events**: [Events that must always be logged]

### 6.2 Metrics & Monitoring

- **Metrics System**: [e.g., Prometheus, Datadog, New Relic]
- **Key Metrics**: [List critical metrics tracked]
- **Alerting Rules**: [What triggers alerts]
- **SLIs/SLOs**: [Service level indicators and objectives]

### 6.3 Tracing

- **Tracing System**: [e.g., Jaeger, Zipkin, AWS X-Ray]
- **Trace Context Propagation**: [How trace IDs are passed between services]
- **Sampling Rate**: [Percentage of requests traced]

### 6.4 Health Checks

- **Liveness Endpoint**: [URL and expected response]
- **Readiness Endpoint**: [URL and expected response]
- **Dependency Checks**: [What external systems are checked]

---

## 7. Configuration & Environment

### 7.1 Environment Variables

List critical environment variables:

- `ENV_VAR_NAME`: [Description, default value, required/optional]
- `DATABASE_URL`: [Database connection string format]
- `API_KEY_SERVICE_X`: [Purpose and where to obtain]

### 7.2 Feature Flags

- **System**: [e.g., LaunchDarkly, custom]
- **Active Flags**: [List feature flags and their purpose]
- **Default States**: [On/off by default]

### 7.3 Configuration Files

- **Location**: [Path to config files in repo]
- **Format**: [YAML, JSON, TOML]
- **Environment Overrides**: [How configs differ per environment]

---

## 8. Performance & Scalability

### 8.1 Performance Characteristics

- **Expected Load**: [Requests per second, concurrent users]
- **Response Time SLAs**: [Target latency for key endpoints]
- **Throughput Requirements**: [Data volume processed]

### 8.2 Scalability Constraints

- **Horizontal Scaling**: [Can it scale horizontally? Limitations?]
- **Stateful Components**: [Session state, in-memory caches]
- **Database Bottlenecks**: [Known query performance issues]
- **Connection Pooling**: [Database, HTTP client pool sizes]

### 8.3 Resource Limits

- **Memory**: [Typical/max memory usage]
- **CPU**: [CPU requirements]
- **Disk I/O**: [If applicable]
- **Network**: [Bandwidth requirements]

---

## 9. Testing & Quality

### 9.1 Test Coverage

- **Unit Tests**: [Coverage percentage, key areas tested]
- **Integration Tests**: [What integrations are tested]
- **E2E Tests**: [End-to-end test scenarios]
- **Contract Tests**: [API contract testing approach]

### 9.2 Test Data

- **Test Fixtures**: [Location of test data]
- **Mock Services**: [What external services are mocked]
- **Test Environments**: [Available test/staging environments]

---

## 10. Known Issues & Technical Debt

### 10.1 Known Bugs

- **Issue**: [Description]
- **Workaround**: [Current mitigation]
- **Tracking**: [Link to issue tracker]

### 10.2 Technical Debt

- **Debt Item**: [Description of technical debt]
- **Impact**: [How it affects the system]
- **Priority**: [P0-P3]
- **Remediation Plan**: [If any]

### 10.3 Deprecated Features

- **Feature**: [What is deprecated]
- **Reason**: [Why it's deprecated]
- **Replacement**: [What to use instead]
- **Removal Timeline**: [When it will be removed]

---

## 11. Business Logic & Domain Rules

### 11.1 Complex Business Rules

Document any business rules that are not obvious from code:

- **Rule**: [Description]
- **Rationale**: [Why this rule exists]
- **Exceptions**: [When this rule doesn't apply]
- **Code Location**: [Where implemented]

### 11.2 Calculation Logic

- **Calculation**: [e.g., pricing, scoring, ranking]
- **Formula**: [Mathematical formula or algorithm]
- **Inputs**: [Required data]
- **Edge Cases**: [Special handling scenarios]

### 11.3 Workflow Orchestration

- **Workflow**: [Name of complex workflow]
- **Steps**: [High-level steps]
- **Decision Points**: [Where branching occurs]
- **Rollback/Compensation**: [How failures are handled]

---

## 12. Migration & Backward Compatibility

### 12.1 API Versioning

- **Versioning Strategy**: [URL path, header, query param]
- **Supported Versions**: [List of active API versions]
- **Deprecation Policy**: [How versions are deprecated]

### 12.2 Breaking Changes History

- **Change**: [Description of breaking change]
- **Date**: [When it was introduced]
- **Migration Guide**: [Link or description]

---

## 13. Documentation Links

### 13.1 Internal Documentation

- **Architecture Docs**: [Link to architecture diagrams, ADRs]
- **API Documentation**: [Swagger/OpenAPI spec link]
- **Runbooks**: [Operational procedures]
- **Onboarding Docs**: [Developer onboarding materials]

### 13.2 External References

- **Industry Standards**: [Links to relevant RFCs, specifications]
- **Framework Docs**: [Links to framework documentation]
- **Best Practices**: [Links to coding standards, style guides]

---

## 14. Contact & Ownership

- **Team**: [Team name responsible for this service]
- **Tech Lead**: [Name/contact]
- **On-Call**: [Rotation schedule or contact]
- **Slack Channel**: [Team communication channel]
- **JIRA/Issue Tracker**: [Link to project board]

---

## Notes for AI Analysis

When running `prompts/01-code-to-spec.md`, pay special attention to:

- [ ] CTI patterns and polymorphic behavior
- [ ] External service dependencies and their failure modes
- [ ] Security/compliance requirements that may not be in code
- [ ] Business rules that exist in documentation but not code comments
- [ ] Performance characteristics and scalability constraints
- [ ] Known technical debt that affects behavior
- [ ] Deprecated features still in the codebase

**Reminder**: This file supplements code analysis. If information here conflicts with code, the code is the source of truth for current behavior, but this file explains the "why" behind design decisions.

---

## Service-Specific Analysis Checklist

Use this section to document service-specific patterns and requirements that the AI should verify during analysis:

### CTI/Inheritance Specifications

- [ ] **CTI Specification URL**: [Link to external CTI specification, e.g., https://developer.acronis.com/doc/cyberapps/reference/cti.html]
- [ ] **CTI Version Implemented**: [Version number]
- [ ] **Inheritance Rules**: Document how inheritance works (e.g., tenant hierarchy, generic type fallbacks, self-service tenant stops)
- [ ] **Polymorphic Behavior**: List entity types and their discriminators (e.g., `type: "standard"`, `type: "custom"`)
- [ ] **CTI Sync Mechanisms**: How CTI data is synchronized across deployment modes

### Deployment Mode Variations

If this service supports multiple deployment targets, document behavioral differences:

#### Cloud Deployment

- **Authentication**: [Auth method specific to cloud]
- **Tenant Scope**: [Multi-tenant vs single-tenant behavior]
- **Data Sync**: [How data syncs with other services]
- **Feature Flags**: [Cloud-specific feature flags]

#### On-Premises/Local Agent Deployment

- **Authentication**: [Auth method specific to on-prem]
- **Tenant Scope**: [Local tenant constraints]
- **Data Sync**: [Offline/sync behavior]
- **Feature Flags**: [On-prem specific feature flags]

### Privileged Access Rules

- [ ] **Root/Admin Tenant Behavior**: [Document if root tenant bypasses normal restrictions]
- [ ] **Superuser Permissions**: [List operations that skip validation for privileged users]
- [ ] **Code Location**: [File references for privilege checks]

### Inheritance & Hierarchy Rules

- [ ] **Inheritance Paths**: Document how settings/configurations inherit through hierarchy
- [ ] **Generic Type Semantics**: If service uses "generic" types, explain fallback behavior
- [ ] **Inheritance Stops**: List conditions where inheritance is blocked (e.g., self-service tenants, compliance locks)
- [ ] **Code Location**: [File references for inheritance logic]

### Event Emission Configuration

- [ ] **Event Types**: [List all event types emitted: create, update, delete, state-change]
- [ ] **Configuration Flags**: Document flags controlling event emission
  - `notification_event_target`: [Purpose and values]
  - `audit_event_target`: [Purpose and values]
- [ ] **Queues/Exchanges**: [List message queues, topics, or exchanges used]
- [ ] **Producer Code**: [File references for event publishers]

### API Trait Implementations

Document how custom API traits (from RAML, OpenAPI, etc.) map to code:

- **Trait**: [e.g., `paging`, `errors`, `locks`]
- **Implementation**: [Handler, middleware, or validator file + function]
- **Code Location**: [File path and line numbers]

### Domain Model Naming Consistency

Track any naming inconsistencies found during analysis:

- **Inconsistency**: [e.g., `Setting` in code vs `Settings` in API]
- **Locations**: [List files where each variant appears]
- **Impact**: [Does this affect behavior or just naming?]
- **Resolution**: [Recommended fix or note to maintain as-is]

### Critical Anomalies

Document only critical-path issues found during analysis:

#### Anomaly 1: [Title]

- **Scenario**: [What operation/scenario is affected]
- **Issue**: [Description of the problem]
- **Impact**: [Security risk, data integrity, performance, etc.]
- **Code Location**: [File and line references]
- **Recommended Mitigation**: [Suggested fix]

#### Anomaly 2: [Title]

- **Scenario**: [What operation/scenario is affected]
- **Issue**: [Description of the problem]
- **Impact**: [Security risk, data integrity, performance, etc.]
- **Code Location**: [File and line references]
- **Recommended Mitigation**: [Suggested fix]

### Analysis Provenance Log

Track inconsistencies and discrepancies discovered during analysis:

- **Finding**: [Description]
- **Evidence**: [Code references, file paths]
- **Resolution**: [How it was handled in the spec]
- **Date**: [When discovered]
