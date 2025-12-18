---
description: Analyze existing code and create behavioral specification
---

# Code to Spec Workflow

This workflow helps analyze an existing codebase and create comprehensive behavioral documentation for rewriting in a new technology stack.

## Steps

### 1. Review the Target Project

Review the specified project codebase to understand its structure, components, and overall architecture.

### 2. Create Behavioral Analysis

Create a complete behavioral analysis in `code-to-spec/ORIG_BEHAVIOR.md` that describes:
- All supported scenarios (API calls, async jobs, etc.)
- Primary execution paths
- Error handling paths
- Use the format that best describes each scenario type

### 3. Identify Rewrite Requirements

Document everything needed for complete rewriting using new technology:
- Supported scenarios and use cases
- Side effects and state changes
- API contracts (inbound)
- Database schemas and access patterns
- Outbound API communications
- Events and event handling
- Audit logs and logging patterns

### 4. Extract Reusable Facts

For every behavioral fact observed:
- Determine if it's a reusable pattern that maps to `code-to-spec/FACTS_TAXONOMY.md`
- Document it in `code-to-spec/ORIG_FACTS`
- Assign a unique identifier (e.g., F01.123)
- Reference these facts in `code-to-spec/ORIG_BEHAVIOR.md` using [F01.123] notation

This enables mapping facts to new technology stack implementations (e.g., changing DB access checks, logging approaches, etc.)

### 5. Validate Scenario Details

For every scenario in `code-to-spec/ORIG_BEHAVIOR.md`, verify and document:

**Security & Access:**
- ✓ Triple-check all API role checks
- ✓ Triple-check all DB access checks
- ✓ Verify license checks
- ✓ Verify quota checks
- ✓ Document all conditional access logic

**Data Handling:**
- ✓ Double-check soft-deleted item handling (used or excluded)
- ✓ Clarify all edge cases and corner scenarios

**External Communications:**
- ✓ Double-check all outbound communications are described:
  - Synchronous vs asynchronous
  - Timeout configurations
  - Error management strategies
- ✓ Document all timeouts and limitations with fact references

**Observability:**
- ✓ Double-check all events are mentioned
- ✓ Double-check all audit logs are documented
- ✓ Ensure logging is covered for all scenarios

### 6. Final Review

Review `code-to-spec/ORIG_BEHAVIOR.md` and `code-to-spec/ORIG_FACTS` for:
- Completeness of all scenarios
- Accuracy of security checks
- Proper fact referencing
- Clear error path documentation
- Complete external dependency documentation

## Output Files

All files are created in the `code-to-spec/` directory:

- `code-to-spec/ORIG_BEHAVIOR.md` - Complete behavioral specification with fact references
- `code-to-spec/ORIG_FACTS` - Extracted reusable behavioral facts with taxonomy mapping

## Reference Files

- `code-to-spec/FACTS_TAXONOMY.md` - Taxonomy of reusable behavioral facts (should exist before running workflow)
