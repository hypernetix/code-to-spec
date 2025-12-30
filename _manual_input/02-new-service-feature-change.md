# Service Feature Change Intent

## Purpose

Document requested changes for the AI to generate comprehensive specs (behavior, facts, design, tasks). Keep entries minimal—the AI will infer implementation details, acceptance criteria, and technical approach from your codebase and samples.

## Instructions

1. Add one entry per change below
2. Use format: `[Priority] Type: Feature/Endpoint - Brief reason`
3. Priority: `P0` (critical), `P1` (high), `P2` (medium), `P3` (low)
4. Type: `ADD`, `MODIFY`, `REMOVE`
5. Optional: Add 1-2 line notes if context is needed

---

## Changes

### Format

```text
- [P1] ADD: Bulk item operations - Users need to process hundreds of items at once
- [P0] MODIFY: POST /items - Add duplicate name validation to prevent user confusion
- [P2] REMOVE: GET /items/legacy-format - No longer used by any clients
```

### Your Changes

<!-- Developers should manually add changes below this line, replace the example changes with your own -->

- [P1] ADD: Pagination for GET /items - Current endpoint times out with large datasets
- [P1] ADD: Rate limiting - Prevent API abuse and enable tiered pricing
- [P0] MODIFY: POST /items - Add uniqueness check for item names within category
- [P2] REMOVE: GET /items/legacy-format - Deprecated XML format, use JSON endpoint instead
- [P1] MODIFY: GET /items - Add `created_by_user_id` and `tags` fields for better tracking

<!-- Optional: Add detailed notes for complex changes -->

### Notes (Optional)

<!-- Developers should manually add notes below this line, replace the example notes with your own -->
- Bulk Operations Context: Need transaction handling for partial failures. Max 100 items per request.
- Rate Limiting Context: Standard users: 100 req/min, Premium: 1000 req/min. Requires Redis.

---

## AI Instructions

When processing this file:

1. **Expand each change** into full scenarios following `/samples/orig-behavior.md` structure
2. **Generate facts** for new validations, constraints, and behaviors per `FACTS_TAXONOMY.md`
3. **Update design decisions** in `new-design-facts-and-rationale.md` with architectural implications
4. **Create implementation tasks** in `new-tasks.md` referencing facts and scenarios
5. **Infer details** from existing codebase:
   - Acceptance criteria from similar existing features
   - Breaking change analysis from API contract changes
   - Migration steps from deprecation patterns
   - Technical approach from current architecture
   - Error handling from existing error patterns
   - Security considerations from current auth/authz model

6. **Mark outputs** with `[NEW]` or `[MODIFIED]` prefixes per `prompts/03-spec-upgrade.md`
