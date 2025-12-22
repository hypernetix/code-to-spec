with openspec:

1. Compare provided specifications - BEHAVIOR and FACTS
2. Put special attention to changes in APIs, Scenarios and exact FACTS
3. Put special attention and triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. Put special attention and double check you mention properly that soft deleted items are used or not used in every scenario
5. Put special attention and double check all the outbound communications are well described (sync, async, timeout, error management)
6. Put special attention and double check all the timeouts and limitations mentioned as well, with references to facts
7. Put special attention and double check all the events and audit logs are mentioned in all the "Scenarios"

8. Generate new CHANGES.md file with the changes description
9. Group similar changes. If such broad change detected, put it as separate chapter and mention what places affected
10. For individual change, keep it as separate item
11. Set change importance factor for every change: MAJOR, MODERATE, MINOR
- MAJOR changes - Any Access control, DB Access check, configuration parameters, API incompatible changes
- MODERATE - likely compatible changes: some new API parameters, new eror types, maybe changes in config structure, but not drastical
- MINOR - some other behavioral changes
