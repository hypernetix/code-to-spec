# Specification Verification Guide

## AI Agent Instructions

**Role**: You are a meticulous quality assurance engineer and technical reviewer specializing in specification validation.

**Task**: Perform a comprehensive verification of the generated `ORIG_BEHAVIOR.md` and `ORIG_FACTS.md` files to ensure accuracy, completeness, and consistency. Make only critical corrections that address functional correctness.

## 1. Purpose
This document provides instructions for verifying and validating the accuracy and completeness of the original behavior specification and facts.

## 2. Verification Process

### 2.1 Scenario Validation

**AI Instructions**: For each scenario, perform these validation steps:

1. **Cross-reference with source code**: Verify each step matches actual code behavior
2. **Validate completeness**: Ensure all code paths are documented (success, errors, edge cases)
3. **Check fact references**: Verify all `[FXYY.ZZZ]` references exist in ORIG_FACTS.md
4. **Identify gaps**: Document missing scenarios, incomplete descriptions, or ambiguous statements
5. **Verify examples**: Ensure input/output examples are accurate and representative

### 2.2 Critical Checks

**AI Instructions**: For each scenario, verify these critical aspects are documented:

#### 2.2.1 Access Controls (**HIGHEST PRIORITY**)
- [ ] **API role checks**: Document specific roles (e.g., admin, user, guest) and where they're enforced
- [ ] **Database access permissions**: Document row-level security, table permissions, or ORM-level checks
- [ ] **License validation**: Document license types checked and validation logic
- [ ] **Quota enforcement**: Document limits (requests/day, storage, etc.) and enforcement points

**Verification Method**: Search code for authorization middleware, permission decorators, or access control functions.

#### 2.2.2 Data Management
- [ ] **Soft-delete handling**: Explicitly state if queries include/exclude soft-deleted records
- [ ] **Data validation**: Document all validation rules with fact references
- [ ] **State transitions**: Document valid state changes and constraints

**Verification Method**: Check for `deleted_at`, `is_deleted` fields and their usage in queries.

#### 2.2.3 Integration Points
- [ ] **Outbound communications**: For each external call, document:
  - Protocol and endpoint
  - Synchronous vs asynchronous
  - Timeout values (with code references)
  - Error handling strategy
  - Retry logic (attempts, backoff strategy)

**Verification Method**: Search for HTTP clients, gRPC clients, message queue publishers.

#### 2.2.4 Observability
- [ ] **Event logging**: Document event names, triggers, and payloads
- [ ] **Audit trails**: Document what actions are audited and where
- [ ] **Monitoring points**: Document metrics, traces, and alerts

**Verification Method**: Search for logging statements, event emitters, audit log calls.

## 3. Updates

### 3.1 File Updates
1. Update `ORIG_BEHAVIOR.md` with any critical corrections
2. Update `ORIG_FACTS.md` with any new or modified facts
3. Maintain existing formatting and style

### 3.2 Change Guidelines

**AI Instructions**: Follow these rules when making updates:

1. **Critical changes only**: Only modify content that is functionally incorrect or missing critical information
2. **No style changes**: Do not reformat, rephrase, or restructure unless it impacts clarity of critical information
3. **Document rationale**: For each change, add a comment explaining why it was necessary
4. **Preserve structure**: Maintain the existing document organization and formatting
5. **Fact references**: Always use format `[FXYY.ZZZ]` (e.g., `[FA01.001]`, `[FN00.010]`)

### 3.3 Change Documentation Format

**AI Instructions**: Document each change using this format:

```markdown
## Change Log

### Change 1: [Brief description]
- **File**: ORIG_BEHAVIOR.md / ORIG_FACTS.md
- **Section**: [Section name]
- **Type**: Addition / Correction / Clarification
- **Rationale**: [Why this change was necessary]
- **Code Reference**: [File:line where this was verified]
```

## 4. Output Requirements

### 4.1 Updated Files
- `ORIG_BEHAVIOR.md` with verified and corrected scenarios
- `ORIG_FACTS.md` with validated and corrected facts
- Change log documenting all modifications

### 4.2 Verification Report

**AI Instructions**: Generate a verification report with this structure:

```markdown
# Verification Report

## Summary
- Total scenarios reviewed: [number]
- Issues found: [number]
- Critical corrections made: [number]
- Warnings/recommendations: [number]

## Critical Issues Found
1. [Issue description] - **FIXED** / **NEEDS ATTENTION**
2. [Issue description] - **FIXED** / **NEEDS ATTENTION**

## Verification Results by Category

### Access Controls
- ✓ All API role checks documented: YES / NO
- ✓ All DB access checks documented: YES / NO
- ✓ All license checks documented: YES / NO
- ✓ All quota checks documented: YES / NO

### Data Management
- ✓ Soft-delete handling explicit: YES / NO
- ✓ Validation rules complete: YES / NO

### Integration Points
- ✓ All outbound calls documented: YES / NO
- ✓ Timeouts specified: YES / NO
- ✓ Error handling documented: YES / NO

### Observability
- ✓ Events documented: YES / NO
- ✓ Audit logs documented: YES / NO

## Recommendations
1. [Recommendation for improvement]
2. [Recommendation for improvement]
```
