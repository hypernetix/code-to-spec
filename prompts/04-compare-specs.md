# Specification Comparison and Change Management

## AI Agent Instructions

**Role**: You are a technical analyst and change management specialist with expertise in API evolution and impact analysis.

**Task**: Gather the following inputs before beginning the comparison:

1. `orig-behavior.md` + `orig-design-facts-and-rationale.md` + `orig-tasks.md`
2. `new-behavior.md` + `new-design-facts-and-rationale.md` + `new-tasks.md`
3. Latest code reference (if needed for validation)

**Formatting Reminder**: When generating `CHANGES.md`, impact reports, or matrices, mirror the structure and tone used in the `/samples/` directory (or user-provided examples). Copy the relevant sample block into your context and treat it as the canonical layout for headings, bullet ordering, impact labeling, and `[FXYY.ZZZ]` references.

**Design Reminder**: Treat both fact files as design baselines. Any difference between facts is a design delta and must be evaluated with the same rigor as API, domain, or architectural changes.

## 1. Purpose

This document outlines the process for comparing specifications, identifying differences, and documenting changes in a structured manner.

**Framework Independence**: The generated change documentation (`CHANGES.md`) and impact analysis must be standalone Markdown documents. They should be clear and actionable for any stakeholder, regardless of the tools used to generate them.

## 2. Comparison Process

### 2.1 Core Comparison Areas

**AI Instructions**: Systematically compare these aspects:

1. **API Contracts**
   - **Added endpoints**: New API endpoints in new-behavior.md
   - **Removed endpoints**: Endpoints present in original but not in new
   - **Modified endpoints**: Changes to existing endpoints (parameters, responses, status codes)
   - **Request/response schemas**: Field additions, removals, type changes
   - **Error handling**: New error codes, changed error responses

2. **Implementation Tasks**
   - **New tasks**: Tasks added in new-tasks.md
   - **Modified tasks**: Tasks with changed acceptance criteria or fact references
   - **Removed tasks**: Tasks present in original but not in new
   - **Task scope changes**: Changes in effort estimate or dependencies

3. **Scenarios**
   - **New scenarios**: Scenarios marked with `[NEW]` prefix
   - **Modified scenarios**: Scenarios marked with `[MODIFIED]` prefix
   - **Removed scenarios**: Scenarios in original but not in new
   - **Workflow changes**: Altered business logic or process flows
   - **Validation changes**: New or modified validation rules

4. **Facts**
   - **New facts**: Facts added in new-design-facts-and-rationale.md
   - **Modified facts**: Facts with changed descriptions or implementations
   - **Deprecated facts**: Facts no longer referenced
   - **Fact reference changes**: Scenarios now referencing different facts

### 2.2 Critical Review Areas

#### 2.2.1 Access Controls (**CRITICAL - Always MAJOR if changed**)

**AI Instructions**: Pay special attention to changes in:

- [ ] **API role checks**: Any change to role requirements or permissions
- [ ] **Database access permissions**: Changes to row-level security or query filters
- [ ] **License validation**: New license types or validation logic changes
- [ ] **Quota enforcement**: Changes to rate limits, quotas, or enforcement mechanisms

**Impact Assessment**: Any change to access controls is automatically **MAJOR** due to security implications.

#### 2.2.2 Data Management

**AI Instructions**: Identify changes in:

- [ ] **Soft-delete handling**: Changes to how deleted records are queried or filtered
- [ ] **Data validation**: New validation rules or modified constraints
- [ ] **State transitions**: Changes to valid state changes or workflows

**Impact Assessment**:

- MAJOR: Changes that affect data integrity or existing data queries
- MODERATE: New validation rules that don't break existing data
- MINOR: Clarifications or documentation improvements

### 2.2.3 Integration Points

**AI Instructions**: Document changes in:

- [ ] **Outbound communications**: New external service calls or modified endpoints
- [ ] **Protocols**: Changes from sync to async or vice versa
- [ ] **Timeouts**: Modified timeout values
- [ ] **Error handling**: New error handling strategies or retry logic

**Impact Assessment**:

- MAJOR: Changes to existing integration contracts
- MODERATE: New integrations or optional parameters
- MINOR: Timeout adjustments or retry logic improvements

#### 2.2.4 Observability

**AI Instructions**: Track changes in:

- [ ] **Event logging**: New events or changed event payloads
- [ ] **Audit trails**: Changes to what actions are audited
- [ ] **Monitoring**: New metrics or changed alerting thresholds

**Impact Assessment**:

- MAJOR: Removal of audit logs or critical events
- MODERATE: New events or metrics
- MINOR: Log format changes or additional context

## 3. Change Documentation

### 3.1 Impact Level Classification

**AI Instructions**: Categorize each change using these criteria:

#### MAJOR Changes (Breaking Changes)

- **Access control changes**: Any modification to authentication, authorization, roles, or permissions
- **Database schema changes**: Changes affecting existing data or queries
- **Task scope expansion**: Major changes to implementation effort or dependencies
- **API contract breaking changes**:
  - Removed endpoints or parameters
  - Changed parameter types or requirements
  - Changed response structures (removed fields)
  - Changed HTTP status codes for existing scenarios
- **Configuration changes**: Required configuration updates
- **Behavioral changes**: Changes to business logic that affect existing functionality

#### MODERATE Changes (Backward Compatible)

- **New optional API parameters**: Additive changes that don't break existing clients
- **New endpoints**: Additional functionality without affecting existing endpoints
- **Additional error types**: New error codes or error details
- **Enhanced responses**: New optional fields in responses
- **Configuration additions**: New optional configuration parameters
- **Performance improvements**: Changes that improve performance without affecting behavior

#### MINOR Changes (Non-Breaking)

- **Documentation improvements**: Clarifications, examples, or additional details
- **Internal refactoring**: Code changes with no external impact
- **Logging enhancements**: Additional logging or improved log messages
- **Minor bug fixes**: Fixes that don't change expected behavior

### 3.2 CHANGES.md Structure

**AI Instructions**: Generate CHANGES.md with this exact structure:

```markdown
# Change Log: ORIG → NEW

## Summary
- **Total Changes**: [number]
- **MAJOR**: [number] - Breaking changes requiring migration
- **MODERATE**: [number] - Backward compatible enhancements
- **MINOR**: [number] - Non-breaking improvements

## MAJOR Changes

### [MAJOR-001] Change Title
**Category**: Access Control / API Contract / Data Management / Integration
**Impact**: [Detailed description of who/what is affected]
**Change Details**:
- **Before**: [Original behavior]
- **After**: [New behavior]
**Affected Scenarios**: [List of scenario names]
**Affected Facts**: [FXYY.ZZZ], [FXYY.ZZZ]
**Migration Required**: YES / NO
**Migration Steps**:
1. [Step 1]
2. [Step 2]
**Testing Focus**: [What needs to be tested]

## MODERATE Changes

### [MODERATE-001] Change Title
**Category**: API Enhancement / New Feature / Configuration
**Benefit**: [Value provided by this change]
**Change Details**:
- [Description of enhancement]
**Affected Scenarios**: [List of scenario names]
**Affected Facts**: [FXYY.ZZZ], [FXYY.ZZZ]
**Backward Compatible**: YES
**Adoption Notes**: [How to use the new feature]

## MINOR Changes

### [MINOR-001] Change Title
**Category**: Documentation / Logging / Internal
**Description**: [Brief description]
**Affected Scenarios**: [List if applicable]
```

## 4. Output Requirements

### 4.1 CHANGES.md

**AI Instructions**: Generate comprehensive change documentation following the structure in section 3.2

### 4.2 Impact Analysis Report

**AI Instructions**: Create an impact analysis report with this structure:

```markdown
# Impact Analysis Report

## Executive Summary
- **Risk Level**: LOW / MEDIUM / HIGH
- **Breaking Changes**: [number]
- **Recommended Timeline**: [Immediate / Planned / Long-term]
- **Estimated Migration Effort**: [Hours/Days]

## Risk Assessment

### High-Risk Changes
1. **[Change ID]**: [Brief description]
   - **Risk**: [What could go wrong]
   - **Mitigation**: [How to reduce risk]

### Medium-Risk Changes
1. **[Change ID]**: [Brief description]
   - **Consideration**: [What to watch for]

## Testing Strategy

### Critical Test Areas
1. **[Area]**: [Why it needs testing]
   - Test scenarios: [List]
   - Expected outcomes: [List]

### Regression Testing Focus
- [Area 1]: [Rationale]
- [Area 2]: [Rationale]

### Integration Testing
- [External system 1]: [What to verify]
- [External system 2]: [What to verify]

## Deployment Recommendations

### Pre-Deployment
1. [Action item]
2. [Action item]

### Deployment Strategy
- **Recommended approach**: [Blue-green / Canary / Rolling / Big bang]
- **Rationale**: [Why this approach]
- **Rollback plan**: [How to revert if needed]

### Post-Deployment
1. [Monitoring checkpoint]
2. [Validation step]

## Stakeholder Communication

### Development Team
- [Key information they need]

### QA Team
- [Testing focus areas]

### Operations Team
- [Deployment and monitoring considerations]

### Product/Business Team
- [Feature changes and user impact]
```

### 4.3 Comparison Matrix

**AI Instructions**: Generate a side-by-side comparison table in new comparison-matrix.md file:

```markdown
# Specification Comparison Matrix

| Aspect                | ORIG          | NEW           | Change Type | Impact   |
|-----------------------|---------------|---------------|-------------|----------|
| Endpoint: POST /items | [Description] | [Description] | MODIFIED    | MODERATE |
| Fact: [FA01.001]      | [Description] | [Description] | MODIFIED    | MAJOR    |
| Scenario: Create Item | [Summary]     | [Summary]     | MODIFIED    | MODERATE |
```

## 5. Quality Assurance

**AI Instructions**: Before finalizing, verify:

### 5.1 Completeness Checklist

- ✓ All differences identified (endpoints, scenarios, facts)
- ✓ Every change has impact level (MAJOR/MODERATE/MINOR)
- ✓ All MAJOR changes have migration steps, if any
- ✓ All access control changes flagged as MAJOR
- ✓ All affected scenarios and facts listed
- ✓ Risk assessment complete
- ✓ Testing strategy provided

### 5.2 Accuracy Checklist

- ✓ Change categorization follows section 3.1 criteria
- ✓ Impact descriptions are specific and actionable
- ✓ Migration steps are complete and testable, if any
- ✓ No changes missed or duplicated
- ✓ Fact references use correct format [FXYY.ZZZ]
- ✓ API role checks validated against code
- ✓ License validation logic is present where applicable
- ✓ Quota enforcement mechanisms checked

### 5.3 Code Generation Readiness

- ✓ **Final Facts**: `new-design-facts-and-rationale.md` is complete and self-consistent (no missing references)
- ✓ **Final Behavior**: `new-behavior.md` contains enough detail for an AI to write the code without guessing
- ✓ **Final Tasks**: `new-tasks.md` accurately reflects all changes and includes proper fact/scenario references
- ✓ **Independence**: Files rely only on each other, not on external context or tools
- ✓ All validation rules explicitly stated
- ✓ Error handling is present where applicable

### 5.4 Clarity Checklist

- ✓ Each change has clear before/after description
- ✓ Technical terms are explained
- ✓ Stakeholder impact is clear
- ✓ Testing requirements are specific
- ✓ Migration steps are unambiguous, if any
- ✓ Business logic changes clearly articulated
- ✓ Integration contract changes highlighted
- ✓ Performance implications documented
- ✓ Monitoring and alerting updated
- ✓ Security implications assessed, if any
- ✓ Data privacy impacts evaluated, if any
- ✓ Compliance requirements addressed, if any
- ✓ Rollback procedures documented, if any
