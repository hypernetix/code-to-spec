# Specification Enhancement Guide

## AI Agent Instructions

**Role**: You are a forward-thinking software architect and product strategist specializing in API design and system modernization.

**Task**: Create an enhanced specification (`new-behavior.md`) by incorporating improvements, modern best practices, and new features while maintaining traceability to the original specification for easy diff comparison.

**Formatting Reminder**: Use the canonical samples in `/samples/` as the visual contract for `new-behavior.md`, `new-design-facts-and-rationale.md`, and `new-tasks.md`. Before editing, paste the relevant sample snippet into your context and mirror its headings, tables, bullet order, and `[FXYY.ZZZ]` references unless the user explicitly directs otherwise.

## 1. Purpose

This document outlines the process for creating an enhanced specification by incorporating improvements and new features while maintaining the ability to track changes from the original specification.

**Framework Independence**: The enhanced specification (`new-behavior.md`) must remain a standalone Markdown document. It should be fully comprehensible without external tools. The optimization with OpenSpec comes from the structure and clarity of the content, not from tool-specific metadata.

## 2. Process Overview

### 2.1 Initial Setup

**AI Instructions**: Follow these steps to prepare the enhanced specification:

1. **Review change requests**: Read `_manual_input/02-new-service-feature-change.md` for developer-requested changes
2. **Copy base file**: 
   1. Create `new-behavior.md` as an exact copy of `orig-behavior.md`
   2. Create `new-design-facts-and-rationale.md` as an exact copy of `orig-design-facts-and-rationale.md`
   3. Create `new-tasks.md` as an exact copy of `orig-tasks.md`
3. **Apply user proposals**: Incorporate all requirements from the change intent file and any additional user requests in `new-behavior.md`, `new-design-facts-and-rationale.md`, and `new-tasks.md`
4. **Reference FACTS_TAXONOMY.md**: Use TAXONOMY for proper fact categorization and ID format when creating new facts
5. **Preserve structure**: Maintain the original document structure, section order, and formatting to enable clean diffs
6. **Mark all changes**: Use `[NEW]` prefix for new content and `[MODIFIED]` for changed content

### 2.2 Enhancement Areas

#### 2.2.1 Gap Analysis

**AI Instructions**: Analyze the original specification for gaps and review `_manual_input/02-new-service-feature-change.md` for requested changes:

- **Missing scenarios**: Identify common use cases not covered (e.g., bulk operations, search/filter, pagination)
- **Incomplete requirements**: Find scenarios with vague descriptions or missing error handling
- **Outdated patterns**: Identify legacy approaches that should be modernized

**Example gaps to look for**:

- No pagination on list endpoints
- Missing bulk delete/update operations
- No filtering or sorting capabilities
- Incomplete error responses
- Missing health check or status endpoints

#### 2.2.2 Industry Best Practices

**AI Instructions**: Propose modern improvements:

- **API Design**:
  - RESTful best practices (proper HTTP methods, status codes)
  - GraphQL considerations for complex queries
  - API versioning strategy (URL path, header, or content negotiation)
  - HATEOAS links for discoverability
  
- **Security**:
  - OAuth2/OIDC for authentication
  - Rate limiting per user/API key
  - Input sanitization and validation
  - CORS policies
  - API key rotation
  
- **Performance**:
  - Caching strategies (ETags, Cache-Control headers)
  - Async processing for long-running operations
  - Pagination and cursor-based navigation
  - Compression (gzip, brotli)
  
- **Observability**:
  - Structured logging (JSON format)
  - Distributed tracing (OpenTelemetry)
  - Metrics (Prometheus format)
  - Health checks (liveness, readiness)

#### 2.2.3 API Improvements

**AI Instructions**: Enhance API contracts:

- **Request/Response enhancements**:
  - Add missing optional parameters
  - Improve error response structure (RFC 7807 Problem Details)
  - Add request/response examples
  - Document all possible status codes
  
- **Versioning**:
  - Propose versioning strategy
  - Document breaking vs non-breaking changes
  
- **Documentation**:
  - OpenAPI/Swagger specifications
  - Request/response examples
  - Authentication flows
  - Rate limit information

### 2.3 Fact Management

**AI Instructions**: When adding new features or modifying existing ones:

1. **Reference FACTS_TAXONOMY.md** for proper fact categorization and ID format
2. **Create new facts** for any new reusable behavior, validation rules, or constraints
3. **Update new-design-facts-and-rationale.md** with:
   - New facts following TAXONOMY categories (FA through FJ)
   - Updated design decisions reflecting architectural changes
   - Trade-offs and rationale for new patterns
4. **Update new-tasks.md** with:
   - Implementation tasks for new features
   - References to new facts and scenarios
   - Updated acceptance criteria

**Fact Creation Rules**:

- Use next available ID in appropriate TAXONOMY category
- Format: `[FXYY.ZZZ]` (e.g., `[FC00.020]`, `[FE03.005]`)
- Document in both FACTS section and reference in scenarios
- Update Design Decisions section if architectural patterns change

## 3. Change Management

### 3.1 Marking Changes

**AI Instructions**: Use these exact prefixes to mark all changes:

1. **`[NEW]` prefix**: Add to any new scenario, endpoint, or major feature

   ```markdown
   ### [NEW] Scenario: Bulk Delete Items
   ```

2. **`[MODIFIED]` prefix**: Add to existing scenarios with significant changes

   ```markdown
   ### [MODIFIED] Scenario: Create Item
   ```

3. **Inline new steps**: For new steps within existing scenarios, use `[NEW]` inline

   ```markdown
   #### Primary Path:
   1. Validate request [FA01.001]
   2. [NEW] Check rate limit [FA01.050]
   3. Create item in database [FA10.001]
   ```

4. **Comments for context**: Add explanatory comments for non-obvious improvements

   ```markdown
   <!-- IMPROVEMENT: Added pagination to prevent performance issues with large datasets -->
   ```

### 3.2 Version Control

**AI Instructions**: Maintain diff-friendly structure:

1. **Preserve order**: Keep scenarios in the same order as orig-behavior.md
2. **Add new scenarios at end**: Append all new scenarios after existing ones
3. **Group related changes**: Keep related modifications together
4. **Document rationale**: Explain why each significant change was made

## 4. Output Requirements

### 4.1 new-behavior.md

**AI Instructions**: Produce an updated behavior spec with these characteristics:

1. Original structure preserved
2. All additions clearly marked with `[NEW]`
3. All modifications marked with `[MODIFIED]`
4. References link to facts using `[FXYY.ZZZ]` format per FACTS_TAXONOMY.md
5. Each scenario includes: trigger, flow, validation, errors, state changes, references, and code locations
6. All changes from `_manual_input/02-new-service-feature-change.md` incorporated

**Code Generation Readiness**: Ensure every scenario in this document is detailed enough to be fed into an AI code generator without additional human context. Ambiguities here will lead to bugs in the generated code.

**Design Reminder**: Facts are first-class design artifacts. When you introduce or alter a fact during the upgrade process, treat it like a design change—justify it, reference evidence, and ensure downstream scenarios stay consistent.

### 4.2 new-design-facts-and-rationale.md

**AI Instructions**: Update the facts and design document:

1. **Add new facts** for new features following FACTS_TAXONOMY.md structure
2. **Update design decisions** section with:
   - New architectural patterns introduced
   - Trade-offs for new approaches
   - Performance implications
   - Security considerations
3. **Maintain fact numbering** - append new facts, never renumber
4. **Cross-reference** - ensure all facts referenced in new-behavior.md exist

### 4.3 new-tasks.md

**AI Instructions**: Update the implementation tasks:

1. **Add tasks** for new features and enhancements
2. **Reference new facts** using correct `[FXYY.ZZZ]` format as per FACTS_TAXONOMY.md
3. **Reference new scenarios** from new-behavior.md
4. **Update acceptance criteria** to reflect new requirements
5. **Maintain task organization** by capability/phase

### 4.4 Enhancement Summary

**AI Instructions**: Create a summary document with this structure:

```markdown
# Enhancement Summary

## Overview
- Original scenarios: [number]
- New scenarios added: [number]
- Modified scenarios: [number]
- New facts added: [number]

## Key Improvements

### 1. [Improvement Category]
**Rationale**: [Why this improvement was needed]
**Changes**:
- [Specific change 1]
- [Specific change 2]

### 2. [Improvement Category]
**Rationale**: [Why this improvement was needed]
**Changes**:
- [Specific change 1]
- [Specific change 2]

## New Features

### [NEW] Feature Name
- **Description**: [What this feature does]
- **Use Case**: [Why users need this]
- **Implementation Notes**: [Key considerations]
- **Related Facts**: [FXYY.ZZZ], [FXYY.ZZZ]

## Breaking Changes
1. **Change**: [Description]
   - **Impact**: [Who/what is affected]
   - **Migration**: [How to adapt]

## Non-Breaking Enhancements
1. **Enhancement**: [Description]
   - **Benefit**: [Value provided]

## Recommendations for Implementation
1. [Recommendation with priority]
2. [Recommendation with priority]
```

## 5. Quality Assurance

**AI Instructions**: Before finalizing, verify:

### 5.1 Completeness Checklist

- ✓ All user-requested changes incorporated
- ✓ All gaps from section 2.2.1 addressed
- ✓ Industry best practices from section 2.2.2 applied where relevant
- ✓ API improvements from section 2.2.3 implemented
- ✓ All changes marked with `[NEW]` or `[MODIFIED]`
- ✓ Original structure preserved
- ✓ All new facts properly referenced with format `[FXYY.ZZZ]` per FACTS_TAXONOMY.md
- ✓ New facts added to new-design-facts-and-rationale.md
- ✓ Design decisions updated for architectural changes
- ✓ New tasks added to new-tasks.md with fact/scenario references

### 5.2 Diff-Friendliness Check

- ✓ Scenarios in same order as original
- ✓ New scenarios appended at end
- ✓ No unnecessary reformatting
- ✓ Clear visual markers for changes

### 5.3 Documentation Quality

- ✓ All new scenarios have complete documentation
- ✓ Rationale provided for significant changes
- ✓ Examples included for new features
- ✓ Breaking changes clearly identified
