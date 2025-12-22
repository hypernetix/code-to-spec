This is prompt template

With openspec:

1. Review my old go-lang project I specified
2. Create complete behavioral analysis of what the service is doing in ORIG_BEHAVIOR.md file, use whatever format works better to describe all the supported scenarios - API calls, async jobs, etc
3. Identify everything I need to know for complete rewriting of the original code using a new technology in a new lang
4. Discover all the supported scenarios, side effects, API contracts, DB schemas, outbound API communication, events, audit logs and such, describe them well in the ORIG_BEHAVIOR.md file as well
5. Describe the service behavior in a structured behavioral way explaining both primary path and potential error path
6. For every fact you observe try to understand the fact nature - if it's a useful behavioral fact that is reused inside the project and maps to [FACTS_TAXONOMY.md] facts, write it down to a separate ORIG_FACTS file.
7. When you describe behavioral scenario in ORIG_BEHAVIOR.md file, for every fact you identified, put a reference to the fact like [FA01.123], so that later I would be able to map some of the facts onto the new technological stack and, say, change the way I implement access check on DB layer, or change the way I do logging, or change the way I


For every scenario:

1. Clarify all the tiny details of the scenarios in ORIG_BEHAVIOR.md
2. Put double attention of scenarios correctness
3. triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. double check you mention properly that soft deleted items are used or not used in every scenario
5. Double check all the outbound communications are well described (sync, async, timeout, error management)
6. Double check all the timeouts and limitations mentioned as well, with references to facts
7. Double check all the events and audit logs are mentioned in all the "Scenarios"
