with openspec:

For every scenario in the ORIG spec:

1. Clarify all the tiny details of the screnarios
2. Put double attention of scenarios correctness
3. Triple check all the API ROLE checks, DB access check, license checks, quota checks, other conditions in all the "Scenarios". This is key focus
4. double check you mention properly that soft deleted items are used or not used in every scenario
5. Double check all the outbound communications are well described (sync, async, timeout, error management)
6. Double check all the timeouts and limitations mentioned as well, with references to facts
7. Double check all the events and audit logs are mentioned in all the "Scenarios"

Also verify and correct these additional outputs (CRITICAL correctness only, no style/format changes):

8. Dead code detection:
   - Ensure ORIG_BEHAVIOR.md ends with a dedicated "Dead code / unused features" section
   - Ensure ORIG_FACTS.md ends with a dedicated "Dead code facts" section
   - Validate that items listed as dead code are actually unused/unreachable (feature flags default-off, unreferenced handlers, never-registered routes, etc)
9. Unit test facts and verbose test guidance:
   - Ensure ORIG_FACTS.md contains common pre-req fixtures (tenant hierarchy, roles, licenses/quotas, canonical entities)
   - Ensure scenario-level test guidance is present (testcase function name, purpose, step-by-step mutations with data being modified)
10. Task list similar to openspec tasks.md:
   - Ensure ORIG_TASKS.md exists and each task references the relevant ORIG_BEHAVIOR scenario(s) and ORIG_FACTS fact IDs
   - Verify acceptance criteria and non-functional requirements (timeouts/limits, idempotency, retries, observability)
11. Custom alterations record:
   - Ensure DECISIONS.md exists and contains the user-provided alteration instructions (or explicit "No additional instructions provided")
   - Ensure DECISIONS.md is written in simple English (avoid granular technical details) and focuses on what was done and decisions made
   - Ensure any OPTIONAL requirement like proposing new APIs to register domain entities is recorded as a decision in DECISIONS.md

Update ORIG_BEHAVIOR.md and ORIG_FACTS.md with only critical corrections.

MAKE ONLY CRITICAL CHANGES AND IMPROVEMENTS THAT MENTIONED ABOVE, DO NOT MAKE ANY STYLE CHANGES OR FORMAT CHANGES
