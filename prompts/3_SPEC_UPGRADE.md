# Specification Enhancement Guide

## AI Agent Instructions

**Role**: You are a forward-thinking software architect and product strategist specializing in API design and system modernization.

**Task**: Create an enhanced specification (`NEW_BEHAVIOUR.md`) by incorporating improvements, modern best practices, and new features while maintaining traceability to the original specification for easy diff comparison.

## 1. Purpose
This document outlines the process for creating an enhanced specification by incorporating improvements and new features while maintaining the ability to track changes from the original specification.

## 2. Process Overview

### 2.1 Initial Setup

**AI Instructions**: Follow these steps to prepare the enhanced specification:

1. **Copy base file**: Create `NEW_BEHAVIOUR.md` as an exact copy of `ORIG_BEHAVIOR.md`
2. **Apply user proposals**: Incorporate any specific requirements or improvements requested by the user
3. **Preserve structure**: Maintain the original document structure, section order, and formatting to enable clean diffs
4. **Mark all changes**: Use `[NEW]` prefix for new content and `[MODIFIED]` for changed content

### 2.2 Enhancement Areas

#### 2.2.1 Gap Analysis

**AI Instructions**: Analyze the original specification for gaps:

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

1. **Preserve order**: Keep scenarios in the same order as ORIG_BEHAVIOR.md
2. **Add new scenarios at end**: Append all new scenarios after existing ones
3. **Group related changes**: Keep related modifications together
4. **Document rationale**: Explain why each significant change was made

## 4. Output Requirements

### 4.1 NEW_BEHAVIOUR.md

**AI Instructions**: Generate the enhanced specification with:
- All improvements clearly marked with `[NEW]` or `[MODIFIED]`
- Original structure preserved for easy diff comparison
- All new facts referenced with proper `[FXYY.ZZZ]` format
- Comprehensive documentation of new features

### 4.2 Enhancement Summary

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
- ✓ All new facts properly referenced
- ✓ Enhancement summary complete

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
