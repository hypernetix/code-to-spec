# Code to Specification Analysis

## AI Agent Instructions

**Role**: You are an expert software architect and technical analyst specializing in reverse engineering service behavior from source code.

**Task**: Analyze the provided Go-lang service source code and generate comprehensive behavioral specifications and reusable facts that can be used for reimplementation in a different technology stack.

**Formatting Reminder**: Before producing any output, open the samples in `/samples/` (especially `orig-behavior.md`, `orig-design-facts-and-rationale.md`, and `orig-tasks.md`). Paste the relevant sample snippets into your working context and mirror their structure exactly—section headings, bullet order, checklists, fact references, and acceptance criteria must match the samples unless the user explicitly asks for a deviation.

## 1. Purpose

This document provides instructions for analyzing an existing Go-lang service source code and generating comprehensive behavioral specifications and reusable facts.

**Framework Independence**: The generated output files (`orig-behavior.md` and `orig-design-facts-and-rationale.md`) must be standalone, standard Markdown documents. They should not contain proprietary metadata or dependency on specific tools unless explicitly requested. However, they should be structured in a way that allows easy mapping to structured data formats (like OpenSpec) if needed later.

## 2. Analysis Process

### 2.1 Initial Code Review

**AI Instructions**: Systematically analyze the codebase by:

1. **Identify entry points**: Locate all HTTP handlers, gRPC services, CLI commands, and background job definitions
2. **Trace execution flows**: Follow the code path from entry point through business logic to data persistence
3. **Extract contracts**: Document API schemas, request/response structures, and data models
4. **Map dependencies**: Identify all external service calls, database operations, and third-party integrations

**Output to `orig-behavior.md`**:

- API endpoints with full request/response specifications
- Asynchronous jobs and background processes with triggers and schedules
- Data models and database schemas with relationships
- Outbound API communications with protocols and payloads
- Event emissions and audit logs with triggers and formats

### 2.2 Behavior Documentation

**AI Instructions**: For each identified scenario, document using this structure:

```markdown
### Scenario: [Scenario Name]

**Trigger**: [What initiates this scenario]
**Preconditions**: [Required state/data before execution]

#### Primary Path:
1. [Step 1 with fact references [FXYY.ZZZ]]
2. [Step 2 with fact references [FXYY.ZZZ]]
3. [Step 3 with fact references [FXYY.ZZZ]]

**Success Response**: [Expected output]

#### Error Paths:
- **Error Condition 1**: [Description] → [Response/Handling] [FXYY.ZZZ]
- **Error Condition 2**: [Description] → [Response/Handling] [FXYY.ZZZ]

#### Validation Rules:
- [Rule 1] [FXYY.ZZZ]
- [Rule 2] [FXYY.ZZZ]

#### State Changes:
- [State transition description] [FXYY.ZZZ]
```

### 2.3 Fact Extraction

**Design Reminder**: Facts are the canonical, reusable design units for this service. Creating, updating, or referencing a fact is a design activity—treat every fact with the same rigor you would apply to architectural specifications.

**AI Instructions**: Extract reusable facts that represent cross-cutting concerns:

#### What is a Fact?

A **Fact** is something that:

- Can change independently
- Is referenced by multiple scenarios OR can be verified independently
- Represents reusable behavior, rules, or constraints

**Examples**:

- ✓ **Fact**: JWT token validation (used by all authenticated endpoints)
- ✗ **Not a Fact**: Specific parameter name in one endpoint (use as attribute)
- ✓ **Fact**: Soft-delete filter logic (applied to multiple queries)
- ✗ **Not a Fact**: Local variable in a single function

#### Fact Categories

**AI Instructions**: Refer to `FACTS_TAXONOMY.md` for the complete fact categorization system. The taxonomy defines:

- Fact ID format: `[FXYY.ZZZ]` where F=Fact, X=alphabetic category (A-Z), YY=2-digit subcategory (00-99), ZZZ=3-digit fact ID (001-999)
- Complete category map (FA through FJ) with subcategories
- Detailed examples and usage guidelines
- Fact creation and referencing rules

**Key categories include**:

- **FA** — Context & System Definition
- **FB** — Domain Model
- **FC** — REST API
- **FD** — gRPC API
- **FE** — Security Model
- **FF** — Service Layer
- **FG** — Outbound Gateways
- **FH** — Persistence
- **FI** — Triggers & Background Execution
- **FJ** — Tests

See `FACTS_TAXONOMY.md` for complete subcategory details and examples.

#### Fact Documentation Format

**AI Instructions**: Document facts in `orig-design-facts-and-rationale.md` following the format and guidelines specified in `FACTS_TAXONOMY.md`.

**Key requirements**:

- Use correct fact ID format: `[FXYY.ZZZ]` (e.g., `[FA01.001]`, `[FC00.015]`, `[FE01.001]`)
- IDs are append-only - never renumber existing facts
- Always include brackets when referencing: `[FA01.001]` not `FA01.001`
- Facts may only reference other facts by ID, never inline duplication
- Each fact should be independently verifiable and reusable

Refer to `FACTS_TAXONOMY.md` section 5.1 for the complete fact template and section 5.2 for detailed examples.

## 3. Verification Checklist

For each scenario, verify and document:

### 3.1 Access Controls

- [ ] API role-based access controls
- [ ] Database access permissions
- [ ] License validation checks
- [ ] Quota and rate limiting

### 3.2 Data Management

- [ ] Soft-delete handling (inclusion/exclusion)
- [ ] Data validation rules
- [ ] State management and transitions

### 3.3 Integration Points

- [ ] Outbound API communications
  - [ ] Synchronous calls
  - [ ] Asynchronous operations
  - [ ] Timeout configurations
  - [ ] Error handling and retries

### 3.4 Observability

- [ ] Event logging
- [ ] Audit trails
- [ ] Performance metrics
- [ ] Monitoring points

### 3.5 Anomaly Detection

Identify software anomalies in existing source code that require special attention during specification generation for new code implementation:

- [ ] **Dead code detection**: Unused functions, variables, or imports that may indicate incomplete refactoring
- [ ] **Unhandled errors**: Missing error handling paths that could cause silent failures or panics
- [ ] **Unimplemented features**: Placeholder functions or TODO comments indicating incomplete functionality
- [ ] **Inconsistent error patterns**: Different error handling approaches across similar code paths
- [ ] **Resource leaks**: Unclosed connections, files, or other resources that need proper lifecycle management
- [ ] **Race conditions**: Concurrent access patterns that may lead to data corruption
- [ ] **Hardcoded values**: Configuration or environment-specific values embedded in code
- [ ] **Deprecated API usage**: Use of outdated libraries or methods that should be updated
- [ ] **Missing input validation**: Endpoints or functions lacking proper input sanitization
- [ ] **Inconsistent logging**: Varied logging patterns that complicate debugging and monitoring

### 3.6 Any other relevant tehnical points

## 4. Critical Verification Requirements

**AI Instructions**: Before finalizing output, verify each scenario includes:

### 4.0 Completeness Checklist

1. ✓ All scenario details are explicit and unambiguous
2. ✓ Every scenario has been validated for correctness
3. ✓ **CRITICAL**: All API role checks are documented with specific roles
4. ✓ **CRITICAL**: All DB access checks are documented with permissions
5. ✓ **CRITICAL**: All license checks are documented with validation logic
6. ✓ **CRITICAL**: All quota checks are documented with limits and enforcement
7. ✓ Soft-delete handling is explicitly stated (included/excluded/N/A)
8. ✓ All outbound communications include:
   - Protocol (HTTP/gRPC/message queue)
   - Sync vs async behavior
   - Timeout values
   - Error handling strategy
   - Retry logic
9. ✓ All timeouts and limitations are documented with fact references
10. ✓ All events and audit logs are documented with:
    - Event type/name
    - Trigger conditions
    - Payload structure
    - Destination (log file/service/queue)
11. ✓ All state transitions are documented with valid state changes
12. ✓ All data validation rules are documented with constraints
13. ✓ All background jobs and scheduled tasks are documented
14. ✓ All file operations include proper error handling and cleanup
15. ✓ All concurrent access patterns are documented with synchronization
16. ✓ All environment-specific configurations are documented
17. ✓ All security headers and CORS policies are documented
18. ✓ All rate limiting configurations are documented

**Review Action**: Cross-reference this checklist against all sections in `orig-behavior.md` and `orig-design-facts-and-rationale.md` to ensure complete coverage. Update any sections that lack documentation for checked items.

## 5. Output Format

### 5.1 orig-behavior.md Structure

**AI Instructions**: Use this exact structure:

```markdown
# Service Behavior Specification: [Service Name]

## Overview
[Brief description of service purpose and capabilities]

## API Endpoints

### Endpoint: [Method] [Path]
[Use scenario format from section 2.2]

## Background Jobs

### Job: [Job Name]
[Use scenario format from section 2.2]

## Data Models

### Model: [Model Name]
- **Fields**: [field definitions with types]
- **Relationships**: [relationships to other models]
- **Constraints**: [validation rules with fact references]

## Integration Points

### External Service: [Service Name]
- **Purpose**: [Why we call this service]
- **Protocol**: [HTTP/gRPC/etc]
- **Operations**: [List of operations with fact references]
```

### 5.2 orig-design-facts-and-rationale.md Structure

**AI Instructions**: Follow FACTS_TAXONOMY.md categories exactly. Add sections as needed to capture all design decisions and technical facts. Cross-reference Facts and Design sections to ensure comprehensive coverage of architectural patterns, implementation choices, and technical constraints.

```markdown
# [Service Name] - Original Facts & Design

This document contains reusable facts extracted from the [Service Name] and design decisions, categorized according to FACTS_TAXONOMY.md. Each fact is assigned a unique ID in the format [FXX.YYY].

---

## FACTS

### FA — Fact1

**[FA00.001] – Fact Title**
[Fact description]

**[FA01.001] – Fact Title**
[Fact description]

### FB — Fact2

**[FB00.001] – Fact Title**
[Fact description]

---

## DESIGN DECISIONS

### 1. Architecture Overview
[Architecture description, layered design, service boundaries]

### 2. Data Model Design
[Entities, Entity relationships, multi-tenancy, soft-delete patterns, trade-offs]

### 3. API Design
[RESTful principles, versioning, query parameters, error handling]

### 4. Security Design
[Authentication, authorization, input validation]

### 5. Event-Driven Architecture
[Event publishing, retry mechanisms, dead letter queues]

### 6. Performance Optimizations
[Database optimizations, connection pooling, caching strategy]

### 7. Observability Design
[Logging strategy, metrics, context propagation]

### 8. Testing Strategy
[Test pyramid, unit/integration/e2e tests]

### 9. Trade-offs & Technical Debt
[Known limitations, technical debt items, recommendations]

### 10. Summary
[Overall assessment, strengths, areas for improvement]
```

### 5.3 orig-tasks.md Structure

**AI Instructions**: Generate implementation tasks organized by capability and phase:

```markdown
# [Service Name] - Implementation Tasks

This document outlines the implementation tasks required to rebuild the [Service Name] from scratch, organized by capability and referencing scenarios and facts from orig-behavior.md and orig-design-facts-and-rationale.md.

---

## 1. Foundation & Infrastructure

### 1.1 Project Setup
- [ ] 1.1.1 Initialize project with language/framework [FXXX.YYY]
- [ ] 1.1.2 Set up project structure
- [ ] 1.1.3 Configure linting and formatting
- [ ] 1.1.4 Add core dependencies [FXXX.YYY]

### 1.2 Configuration Management
- [ ] 1.2.1 Implement config loading [FXXX.YYY]
- [ ] 1.2.2 Add environment variable support

### 1.3 Logging & Tracing
- [ ] 1.3.1 Set up structured logging [FXXX.YYY]
- [ ] 1.3.2 Implement log levels

### 1.4 Database Setup
- [ ] 1.4.1 Configure database connection [FXXX.YYY]
- [ ] 1.4.2 Implement connection pooling

### 1.5 Message Queue Setup (if applicable)
- [ ] 1.5.1 Configure message queue connection [FXXX.YYY]

## 2. Database Schema & Migrations
- [ ] 2.1.1 Create core tables [FXXX.YYY]
- [ ] 2.2.1 Add indexes and constraints [FXXX.YYY]

## 3. Domain Model
- [ ] 3.1.1 Define entity structs [FXXX.YYY]
- [ ] 3.2.1 Implement value objects [FXXX.YYY]
- [ ] 3.3.1 Implement repositories [FXXX.YYY]
- [ ] 3.4.1 Implement domain services [Scenario X]
- [ ] 3.5.1 Implement business rules [FXXX.YYY]

## 4. REST API
- [ ] 4.1.1 Set up HTTP server [FXXX.YYY]
- [ ] 4.2.1 Implement authentication middleware [FXXX.YYY]
- [ ] 4.3.1 Implement endpoints [FXXX.YYY, Scenario X]
- [ ] 4.4.1 Implement request validation [FXXX.YYY]
- [ ] 4.5.1 Implement response handling [FXXX.YYY]

## 5. External Integrations
- [ ] 5.1.1 Implement external service clients [FXXX.YYY]

## 6. Event Publishing
- [ ] 6.1.1 Implement event publisher [FXXX.YYY]
- [ ] 6.2.1 Implement domain events [FXXX.YYY]
- [ ] 6.3.1 Implement audit events [FXXX.YYY]
- [ ] 6.4.1 Implement retry mechanism [FXXX.YYY]

## 7. Background Jobs
- [ ] 7.1.1 Implement scheduled jobs [FXXX.YYY, Scenario X]

## 8. Observability
- [ ] 8.1.1 Set up metrics [FXXX.YYY]
- [ ] 8.2.1 Implement health checks [FXXX.YYY]

## 9. Security
- [ ] 9.1.1 Implement input validation [FXXX.YYY]
- [ ] 9.2.1 Implement secret management [FXXX.YYY]

## 10. Testing
- [ ] 10.1.1 Write unit tests [FXXX.YYY]
- [ ] 10.2.1 Write integration tests [FXXX.YYY]
- [ ] 10.3.1 Write scenario tests [Scenario X]

## 11. Deployment
- [ ] 11.1.1 Create containerization [FXXX.YYY]
- [ ] 11.2.1 Create Kubernetes resources [FXXX.YYY]
- [ ] 11.4.1 Set up CI/CD pipeline [FXXX.YYY]

## 12. Documentation
- [ ] 12.1.1 Document API endpoints
- [ ] 12.2.1 Document operational procedures
- [ ] 12.3.1 Document development guidelines

## 13. Optional/Future Enhancements
- [ ] 13.1.1 Address technical debt items [FXXX.YYY]

---

## Task Completion Checklist
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] Code coverage >80%
- [ ] All linting checks passing
- [ ] Documentation complete
- [ ] Deployment successful in dev/staging
- [ ] Performance testing completed
- [ ] Security review completed
```

You may add any other relevant tasks.

### 5.4 Quality Standards

**AI Instructions**: Ensure your output meets these standards:

- ✓ All fact references use correct format `[FXYY.ZZZ]`
- ✓ Every scenario includes all critical checks from section 4.0
- ✓ Code references include file paths and line numbers
- ✓ Technical terms are precise (use actual class/function names from code)
- ✓ No assumptions - if something is unclear from code, mark it as `[UNCLEAR: description]`
- ✓ Include code snippets for complex logic
- ✓ Cross-reference related scenarios and facts
- ✓ Tasks reference specific facts and scenarios for traceability

You may add any other relevant standards.
