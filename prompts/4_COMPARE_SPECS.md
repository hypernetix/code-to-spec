with openspec:

1. Compare provided specifications - BEHAVIOR and FACTS
2. Put special attention to changes in APIs, Scenarios and exact FACTS
3. Put special attention and triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. Put special attention and double check you mention properly that soft deleted items are used or not used in every scenario
5. Put special attention and double check all the outbound communications are well described (sync, async, timeout, error management)
6. Put special attention and double check all the timeouts and limitations mentioned as well, with references to facts
7. Put special attention and double check all the events and audit logs are mentioned in all the "Scenarios"
8. Ensure dead code information is preserved and consistent:
   - ORIG/NEW behavior docs must end with "Dead code / unused features" sections
   - ORIG/NEW facts docs must end with "Dead code facts" sections
   - If a feature moved from dead to live (or vice-versa), ensure DECISIONS.md calls it out explicitly
9. Ensure NEW_TASKS.md is consistent with the compared specs:
   - Each change in CHANGES.md should map to at least one NEW_TASKS.md item (implementation work)
   - Tasks must reference the relevant scenario(s) and fact IDs
10. Ensure DECISIONS.md is respected:
   - Any user-provided alteration instructions must be treated as requirements for NEW spec
   - Any optional proposal like new APIs to register domain entities must be recorded in DECISIONS.md and reflected (or explicitly not reflected) in NEW specs and tasks
   - DECISIONS.md should remain in simple English focusing on what was done and decisions made (no granular technical details)

11. Generate CHANGES.md as a technical diff between ORIG and NEW specs:
   - Group similar changes. If such broad change detected, put it as separate chapter and mention what places affected
   - For individual change, keep it as separate item
   - Set change importance factor for every change: MAJOR, MODERATE, MINOR
   - MAJOR changes - Any Access control, DB Access check, configuration parameters, API incompatible changes
   - MODERATE - likely compatible changes: some new API parameters, new error types, maybe changes in config structure, but not drastic
   - MINOR - some other behavioral changes

Expected outputs after this step:
- NEW_BEHAVIOR.md (finalized with all changes reconciled)
- NEW_FACTS.md (finalized with consistent fact IDs)
- DECISIONS.md (user instructions and decisions in simple English)
- CHANGES.md (technical diff with importance ratings)
- NEW_TASKS.md (finalized implementation tasks with checkboxes)
