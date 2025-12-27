# Specification Verification Guide

## AI Agent Instructions

**Role**: You are a meticulous quality assurance engineer and technical reviewer specializing in specification validation.

**Task**: Perform a comprehensive verification of the generated `orig-behavior.md` and `orig-design-facts-and-rationale.md` files to ensure accuracy, completeness, and consistency. Make only critical corrections that address functional correctness.

**Formatting Reminder**: Keep the verified files aligned with the canonical samples in `/samples/`. When correcting structure or adding content, mirror the sample layouts (section order, bullet style, fact references) so downstream prompts receive consistently formatted input.

## 1. Purpose

This document provides instructions for verifying and validating the accuracy and completeness of the original behavior specification and facts.

**Framework Independence**: Ensure the verified files remain standalone Markdown documents. Do not introduce tool-specific syntax or dependencies that would prevent these files from being read by a human or processed by a generic LLM.

## 2. Verification Process

### 2.1 Scenario Validation

**AI Instructions**: For each scenario, perform these validation steps:

**Design Reminder**: Facts are part of the canonical system design. Validating or correcting a fact is a design review activity—require evidence from code and document rationale just as you would for architectural decisions.

1. **Cross-reference with source code**: Verify each step matches actual code behavior
2. **Validate completeness**: Ensure all code paths are documented (success, errors, edge cases)
3. **Check fact references**: Verify all `[FXYY.ZZZ]` references exist in orig-design-facts-and-rationale.md and follow FACTS_TAXONOMY.md format
4. **Validate fact categories**: Ensure facts use correct categories (FA, FB, FC, FD, FE, FF, FG, FH, FI, FJ) per FACTS_TAXONOMY.md
5. **Identify gaps**: Document missing scenarios, incomplete descriptions, or ambiguous statements
6. **Verify examples**: Ensure input/output examples are accurate and representative

**AI Instructions**: Refer to `FACTS_TAXONOMY.md` for:

- Complete fact categorization system and ID format
- Fact creation and validation rules
- Category definitions and subcategories
- Fact documentation templates and examples

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

#### 2.2.5 Implementation Tasks (if orig-tasks.md exists)

- [ ] **Task completeness**: All major implementation areas covered
- [ ] **Fact references**: Tasks reference valid fact IDs from orig-design-facts-and-rationale.md
- [ ] **Scenario references**: Tasks reference scenarios from orig-behavior.md
- [ ] **Task organization**: Tasks grouped logically by capability/phase
- [ ] **Acceptance criteria**: Each task has clear completion criteria

**Verification Method**: Cross-reference task fact IDs with facts file, verify scenario references.

#### 2.2.6 Design Decisions (in orig-design-facts-and-rationale.md)

- [ ] **Architecture patterns**: Accurately described with rationale
- [ ] **Trade-offs**: Documented with pros/cons
- [ ] **Technical debt**: Identified with recommendations
- [ ] **Design-fact alignment**: Design decisions align with extracted facts

**Verification Method**: Review Design Decisions section for accuracy and completeness.

## 3. Updates

### 3.1 File Updates

1. Update `orig-behavior.md` with any critical corrections
2. Update `orig-design-facts-and-rationale.md` with any new or modified facts
3. Update `orig-tasks.md` with corrected fact/scenario references (if file exists)
4. Maintain existing formatting and style

### 3.2 Change Guidelines

**AI Instructions**: Follow these rules when making updates:

1. **Critical changes only**: Only modify content that is functionally incorrect or missing critical information
2. **No style changes**: Do not reformat, rephrase, or restructure unless it impacts clarity of critical information
3. **Document rationale**: For each change, add a comment explaining why it was necessary
4. **Preserve structure**: Maintain the existing document organization and formatting
5. **Fact references**: Always use correct format `[FXYY.ZZZ]` per FACTS_TAXONOMY.md (e.g., `[FA01.001]`, `[FE02.001]`, `[FC00.015]`)

### 3.3 Change Documentation Format

**AI Instructions**: Document each change using this format:

```markdown
## Change Log

### Change 1: [Brief description]
- **File**: orig-behavior.md / orig-design-facts-and-rationale.md / orig-tasks.md
- **Section**: [Section name]
- **Type**: Addition / Correction / Clarification
- **Rationale**: [Why this change was necessary]
- **Code Reference**: [File:line where this was verified]
```

## 4. Output Requirements

### 4.1 Updated Files

- `orig-behavior.md` with verified and corrected scenarios
- `orig-design-facts-and-rationale.md` with validated and corrected facts and design decisions
- `orig-tasks.md` with corrected fact/scenario references (if file exists)
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

### Implementation Tasks (if orig-tasks.md exists)
- ✓ Task completeness: YES / NO
- ✓ Fact references valid: YES / NO
- ✓ Scenario references valid: YES / NO

### Design Decisions (in orig-design-facts-and-rationale.md)
- ✓ Architecture patterns documented: YES / NO
- ✓ Trade-offs documented: YES / NO
- ✓ Technical debt identified: YES / NO
- ✓ Design-fact alignment: YES / NO

## Recommendations
1. [Recommendation for improvement]
2. [Recommendation for improvement]
```
