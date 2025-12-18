---
description: Compare specifications and generate change report
---

# Compare Specs Workflow

This workflow compares two versions of behavioral specifications (ORIG vs NEW) and generates a comprehensive change report with importance ratings.

## Prerequisites

- `code-to-spec/ORIG_BEHAVIOR.md` must exist
- `code-to-spec/NEW_BEHAVIOR.md` must exist
- `code-to-spec/ORIG_FACTS` must exist

## Steps

### 1. Compare Specifications

Compare the provided specifications:
- BEHAVIOR files (ORIG vs NEW)
- FACTS files

### 2. Analyze API Changes

Put special attention to:
- API changes (endpoints, parameters, responses)
- Scenario changes (new, modified, removed)
- Exact FACTS changes

### 3. Triple-Check Security & Access Controls

**CRITICAL FOCUS AREA:**

- ✓ **Triple-check** all API ROLE checks changes
- ✓ **Triple-check** all DB access check changes
- ✓ **Triple-check** license checks changes
- ✓ **Triple-check** quota checks changes
- ✓ **Triple-check** all other conditional checks changes

This is the key focus of comparison.

### 4. Verify Soft-Delete Handling Changes

- ✓ **Double-check** soft-deleted items handling changes
- ✓ Verify proper documentation of soft-deleted items usage in every scenario

### 5. Verify Outbound Communications Changes

- ✓ **Double-check** all outbound communications changes:
  - Synchronous vs asynchronous
  - Timeout configurations
  - Error management strategies

### 6. Verify Timeouts and Limitations Changes

- ✓ **Double-check** all timeout changes
- ✓ **Double-check** all limitation changes
- ✓ Verify proper references to facts

### 7. Verify Events and Audit Logs Changes

- ✓ **Double-check** all event changes
- ✓ **Double-check** all audit log changes

### 8. Generate Changes Report

Create `code-to-spec/CHANGES.md` with comprehensive change descriptions.

### 9. Group Similar Changes

- Group similar changes together
- If broad change detected, create separate chapter
- Mention all affected places for grouped changes

### 10. Document Individual Changes

Keep individual changes as separate items when they don't fit into groups.

### 11. Assign Importance Ratings

For every change, assign importance factor:

**MAJOR Changes:**
- Any access control changes
- DB access check changes
- Configuration parameter changes
- API incompatible changes (breaking changes)

**MODERATE Changes:**
- Likely compatible changes
- New API parameters (backward compatible)
- New error types
- Config structure changes (non-drastic)

**MINOR Changes:**
- Other behavioral changes
- Documentation improvements
- Non-breaking enhancements

## Output File

`code-to-spec/CHANGES.md` with structure:

```markdown
# Changes Report

## MAJOR Changes

### [Group Name] (if applicable)
- Change description
- Affected locations: ...

### Individual MAJOR Change
- Change description
- Impact: ...

## MODERATE Changes

### [Group Name] (if applicable)
- Change description
- Affected locations: ...

## MINOR Changes

...
```

## Change Report Format

Each change should include:
- Clear description
- Importance rating (MAJOR/MODERATE/MINOR)
- Affected components/scenarios
- Impact assessment
- References to specific facts if applicable
