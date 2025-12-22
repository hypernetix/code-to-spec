with openspec:

For every scenario in the ORIG spec:

1. Clarify all the tiny details of the scenarios
2. Put double attention of scenarios correctness
3. triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. double check you mention properly that soft deleted items are used or not used in every scenario
5. Double check all the outbound communications are well described (sync, async, timeout, error management)
6. Double check all the timeouts and limitations mentioned as well, with references to facts
7. Double check all the events and audit logs are mentioned in all the "Scenarios"

Update ORIG_BEHAVIOR.m and ORIG_FACTS.md

MAKE ONLY CRITICAL CHANGES AND IMPROVEMENTS THAT MENTIONED ABOVE, DO NOT MAKE ANY STYLE CHANGES OR FORMAT CHANGES
