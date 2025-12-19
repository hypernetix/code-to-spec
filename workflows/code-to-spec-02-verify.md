---
description: Verify and refine original behavioral specification
---

# Verify Original Spec Workflow

This workflow performs critical verification and refinement of the `code-to-spec/ORIG_BEHAVIOR.md` and `code-to-spec/ORIG_FACTS` files created by the code-to-spec workflow.

**IMPORTANT:** Make ONLY critical changes and improvements as specified below. DO NOT make style changes or format changes.

## Prerequisites

- `code-to-spec/ORIG_BEHAVIOR.md` must exist
- `code-to-spec/ORIG_FACTS` must exist

## Steps

### 1. Review All Scenarios

For every scenario in `code-to-spec/ORIG_BEHAVIOR.md`:

**Clarify Details:**
- ✓ Clarify all tiny details of the scenarios
- ✓ Put double attention on scenarios correctness

### 2. Triple-Check Security & Access Controls

**CRITICAL FOCUS AREA** - For every scenario:

- ✓ **Triple-check** all API ROLE checks
- ✓ **Triple-check** all DB access checks
- ✓ **Triple-check** license checks
- ✓ **Triple-check** quota checks
- ✓ **Triple-check** all other conditional checks

This is the key focus of this verification step.

### 3. Verify Soft-Delete Handling

For every scenario:
- ✓ **Double-check** that soft-deleted items handling is properly mentioned
- ✓ Explicitly state whether soft-deleted items are used or not used in each scenario

### 4. Verify Outbound Communications

For every scenario:
- ✓ **Double-check** all outbound communications are well described:
  - Synchronous vs asynchronous
  - Timeout configurations
  - Error management strategies

### 5. Verify Timeouts and Limitations

For every scenario:
- ✓ **Double-check** all timeouts are mentioned
- ✓ **Double-check** all limitations are mentioned
- ✓ Ensure proper references to facts are included

### 6. Verify Events and Audit Logs

For every scenario:
- ✓ **Double-check** all events are mentioned
- ✓ **Double-check** all audit logs are mentioned

### 7. Update Files

Update the following files with critical improvements only:
- `code-to-spec/ORIG_BEHAVIOR.md`
- `code-to-spec/ORIG_FACTS`

**Remember:** Make ONLY critical changes and improvements. DO NOT make style or format changes.

## Output

Updated versions of:
- `code-to-spec/ORIG_BEHAVIOR.md` - with verified and refined scenarios
- `code-to-spec/ORIG_FACTS` - with any additional facts discovered during verification
