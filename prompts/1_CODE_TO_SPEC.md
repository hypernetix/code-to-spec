This is prompt template

With openspec:

0. Before you start, ask me for any additional instructions on alterations. Examples:
   - conversion of unique identifier system to GTS (or other naming/domain migrations)
   - converting the code to be a module/library instead of a self-running service
   - level of testcases to be covered (unit/integration/contract/e2e)
   - dropping legacy REST APIs and/or legacy features
   - any required compatibility constraints or migration phases
   Record every additional instruction and decision into a new file: DECISIONS.md
   - DECISIONS.md must be written in simple English (no granular technical details), focusing on what was done and what decisions were made.
   - If I provide no additional instructions, still create DECISIONS.md and explicitly say "No additional instructions provided".

1. Review my old go-lang project I specified
2. Create complete behavioral analysis of what the service is doing in ORIG_BEHAVIOR.md file, use whatever format works better to describe all the supported scenarios - API calls, async jobs, etc
3. Identify everything I need to know for complete rewriting of the origianal code using a new technology in a new lang
4. Discover all the supported scenarios, side effects, API contracts, DB schemas, outbound API communication, events, audit logs and such, describe them well in the ORIG_BEHAVIOR.md file as well
5. Describe the service behaviour in a structured behaviroal way explaining both primary path and potential error path
6. For every fact you observe try to understand the fact nature - if it's a useful behavioral fact that is reused inside the project and maps to [FACTS_TAXONOMY.md] facts, write it down to a separate ORIG_FACTS.md file.
7. When you describe behavioral scenario in ORIG_BEHAVIOR.md file, for every fact you identified, put a reference to the fact like [F01.123], so that later I would be able to map some of the facts onto the new technological stack and, say, change the way I implement access check on DB layer, or change the way I do logging, or change the way I handle errors.
8. Add reusable unit-test facts into ORIG_FACTS.md to make it easy to implement testcases later:
   - Common pre-req data / fixtures (e.g. a common tenant hierarchy, common roles, common licenses/quotas, canonical domain entities)
   - Common setup/teardown assumptions
   - Canonical IDs / example payloads that are reused across scenarios
   - For each scenario, add test case guidance with verbose details:
     - test function name (suggested)
     - purpose of the test
     - step-by-step actions and what data is about to be modified at each step (show before/after state or the exact fields being mutated)
     - expected errors and expected side-effects (events/audit logs/outbound calls)
9. Create an OpenSpec-style ORIG_TASKS.md that can drive implementation:
   - Each task must reference the related scenario(s) in ORIG_BEHAVIOR.md and the exact fact IDs from ORIG_FACTS.md (e.g. [F01.123])
   - Include acceptance criteria and non-functional requirements (timeouts/limits, idempotency, retries, observability)
   - If a task depends on a common fixture or shared test data, reference the relevant test-related facts
   - Add checkboxes for tracking progress
10. Detect dead code and document it:
   - Identify unused/dead endpoints, handlers, jobs, data models, feature flags, legacy branches, unused configuration, unreachable code paths, or unused domain entities
   - Append a dedicated "Dead code / unused features" section at the end of ORIG_BEHAVIOR.md
   - Append a dedicated "Dead code facts" section at the end of ORIG_FACTS.md (with fact IDs where helpful)
11. Optional requirement (record decision in DECISIONS.md): propose new APIs to register the required domain entities.
   - If you propose any, make it explicit they are OPTIONAL and list them as proposals, not facts

Expected outputs after this step:
- ORIG_BEHAVIOR.md (complete behavioral analysis with scenarios)
- ORIG_FACTS.md (reusable facts with IDs, including test fixtures and dead code facts)
- ORIG_TASKS.md (implementation tasks with scenario/fact references and checkboxes for tracking progress)
- DECISIONS.md (user instructions and decisions made)

For every scenario:

1. Clarify all the tiny details of the screnarios in ORIG_BEHAVIOR.md
2. Put double attention of scenarios correctness
3. Triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. double check you mention properly that soft deleted items are used or not used in every scenario
5. Double check all the outbound communications are well described (sync, async, timeout, error management)
6. Double check all the timeouts and limitations mentioned as well, with references to facts
7. Double check all the events and audit logs are mentioned in all the "Scenarios"
