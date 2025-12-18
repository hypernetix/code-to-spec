with openspec:

1. Copy ORIG_BEHAVIOR.md to NEW_BEHAVIOR.md
2. Copy ORIG_FACTS.md to NEW_FACTS.md
3. Copy ORIG_TASKS.md to NEW_TASKS.md
4. Incorporate my direct proposals that I suggested
5. Review the NEW_BEHAVIOR.md spec regarding potential gaps and suggest what need to be improved in terms of scenarios and possible new APIs
6. Suggest cutting edge industry patterns, features and scenarios for given domain
7. For every NEW scenario or step put mandatory [NEW] prefix!
8. If you propose any new APIs (including optional APIs to register domain entities), record the decision in DECISIONS.md as OPTIONAL proposals (simple English, no granular technical details).
9. Add any new facts to NEW_FACTS.md ensuring they map to [FACTS_TAXONOMY.md] categories and use consistent fact ID format [FXX.YYY]
10. Keep the "Dead code / unused features" section from ORIG_BEHAVIOR.md at the end of NEW_BEHAVIOR.md (unless your changes explicitly revive a feature, in which case update it accordingly).
11. Update NEW_TASKS.md to include tasks for all [NEW] scenarios/steps and any API changes, with references back to NEW_BEHAVIOR.md scenarios and the relevant fact IDs.
12. Keep ORIG_BEHAVIOR structure, create new scenarios in the end, create new steps inline, I want to be able to make diff later.

Expected outputs after this step:
- NEW_BEHAVIOR.md (with [NEW] prefixes for additions)
- NEW_FACTS.md (with any new facts added)
- NEW_TASKS.md (updated with new tasks)
- DECISIONS.md (updated with optional proposals)
