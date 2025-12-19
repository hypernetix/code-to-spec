---
description: Upgrade original spec with improvements and new scenarios
---

# Spec Upgrade Workflow

This workflow upgrades the original behavioral specification by incorporating improvements, suggesting enhancements, and adding new cutting-edge scenarios.

## Prerequisites

- `code-to-spec/ORIG_BEHAVIOR.md` must exist
- `code-to-spec/ORIG_FACTS` must exist

## Steps

### 1. Create New Specification

Copy `code-to-spec/ORIG_BEHAVIOR.md` to `code-to-spec/NEW_BEHAVIOR.md`

### 2. Incorporate Direct Proposals

Incorporate any direct proposals or improvements suggested by the user into `code-to-spec/NEW_BEHAVIOR.md`

### 3. Review for Gaps

Review `code-to-spec/NEW_BEHAVIOR.md` for potential gaps and suggest improvements:
- Missing scenarios
- Possible new APIs
- Enhanced error handling
- Better integration patterns

### 4. Suggest Cutting-Edge Patterns

For the given domain, suggest:
- Cutting-edge industry patterns
- Modern features
- Advanced scenarios
- Best practices

### 5. Mark New Content

**CRITICAL:** For every NEW scenario or step, put mandatory **[NEW]** prefix!

This allows for easy diff comparison later.

## Structure Requirements

**IMPORTANT:**
- KEEP ORIG_BEHAVIOR structure intact
- CREATE NEW scenarios at the END of the file
- CREATE NEW steps INLINE within existing scenarios
- Structure must allow for easy diff comparison later

## Output Files

- `code-to-spec/NEW_BEHAVIOR.md` - Upgraded behavioral specification with [NEW] markers
- `code-to-spec/ORIG_FACTS` - May be updated with new facts (mark new facts appropriately)

## Example Marking

```markdown
### Existing Scenario
1. Existing step
2. [NEW] New step added
3. Existing step

### [NEW] Completely New Scenario
1. [NEW] First step of new scenario
2. [NEW] Second step of new scenario
```
