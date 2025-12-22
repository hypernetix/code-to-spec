# Code to Specification Analysis

## AI Agent Instructions

**Role**: You are an expert software architect and technical analyst specializing in reverse engineering service behavior from source code.

**Task**: Analyze the provided Go-lang service source code and generate comprehensive behavioral specifications and reusable facts that can be used for reimplementation in a different technology stack.

## 1. Purpose
This document provides instructions for analyzing an existing Go-lang service source code and generating comprehensive behavioral specifications and reusable facts.

## 2. Analysis Process

### 2.1 Initial Code Review

**AI Instructions**: Systematically analyze the codebase by:

1. **Identify entry points**: Locate all HTTP handlers, gRPC services, CLI commands, and background job definitions
2. **Trace execution flows**: Follow the code path from entry point through business logic to data persistence
3. **Extract contracts**: Document API schemas, request/response structures, and data models
4. **Map dependencies**: Identify all external service calls, database operations, and third-party integrations

**Output to `ORIG_BEHAVIOR.md`**:
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

**AI Instructions**: Extract reusable facts that represent cross-cutting concerns:

1. **Identify patterns**: Look for repeated logic, validation rules, access checks, and business rules
2. **Categorize facts**: Map each fact to a category in [FACTS_TAXONOMY.md](../FACTS_TAXONOMY.md)
3. **Assign IDs**: Use format `[FXYY.ZZZ]` where XXX is category (e.g., FA01, FN00) and YYY is sequential number (e.g., FA01.001, FN00.010)
4. **Document in `ORIG_FACTS.md`** using this format:

```markdown
## [FXYY.ZZZ] Fact Title

**Category**: [Category from FACTS_TAXONOMY.md]
**Description**: [Clear, concise description of the fact]
**Implementation**: [How it's currently implemented in code]
**References**: [Where this fact is used - file paths and line numbers]
**Related Facts**: [FXYY.ZZZ], [FXYY.ZZZ]
```

**Important**: Always use the format `[FXYY.ZZZ]` (e.g., `[FA01.001]`, `[FN00.010]`) when referencing facts in scenarios.

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
- [ ] Dead code detection
- [ ] Unhandled errors
- [ ] Unimplemented features

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

## 5. Output Format

### 5.1 ORIG_BEHAVIOR.md Structure

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

### 5.2 ORIG_FACTS.md Structure

**AI Instructions**: Use this exact structure:

```markdown
# Facts Catalog: [Service Name]

## Access Control Facts (F001.xxx)

### [FA01.001] Fact Title
[Use fact format from section 2.3]

## Data Validation Facts (F010.xxx)

### [F010.001] Fact Title
[Use fact format from section 2.3]

## [Continue with other categories from FACTS_TAXONOMY.md]
```

### 5.3 Quality Standards

**AI Instructions**: Ensure your output meets these standards:
- ✓ All fact references use correct format `[FXYY.ZZZ]`
- ✓ Every scenario includes all critical checks from section 4.0
- ✓ Code references include file paths and line numbers
- ✓ Technical terms are precise (use actual class/function names from code)
- ✓ No assumptions - if something is unclear from code, mark it as `[UNCLEAR: description]`
- ✓ Include code snippets for complex logic
- ✓ Cross-reference related scenarios and facts
