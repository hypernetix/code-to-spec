# Specification Verification Guide

## AI Agent Instructions

**Role**: You are a meticulous quality assurance engineer and technical reviewer specializing in specification validation.

**Task**: Perform a comprehensive verification of the generated `orig-behavior.md`, `orig-tasks.md` and `orig-design-facts-and-rationale.md` files to ensure accuracy, completeness, and consistency. Make only critical corrections that address functional correctness.

**Formatting Reminder**: Keep the verified files aligned with the canonical samples in `/samples/`. When correcting structure or adding content, mirror the sample layouts (section order, bullet style, fact references) so downstream prompts receive consistently formatted input.

**Context Reference**: Review `_manual_input/01-orig-service-notes.md` before verification to understand specifications, dependent services, deployment nuances, and business constraints that may not be obvious from code. Treat the notes as supplemental intent; if they conflict with observed behavior in the source code, the code is the source of truth. Document discrepancies in the change log with references to both the notes and the relevant code.

## 1. Purpose

This document provides instructions for verifying and validating the accuracy and completeness of the original behavior specification and facts.

**Framework Independence**: Ensure the verified files remain standalone Markdown documents. Do not introduce tool-specific syntax or dependencies that would prevent these files from being read by a human or processed by a generic LLM.

### 2. Pre-Verification: Manual Notes Review (**MANDATORY FIRST STEP**)

**AI Instructions**: BEFORE starting verification, thoroughly review [_manual_input/01-orig-service-notes.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/guidelines/code-to-spec/_manual_input/01-orig-service-notes.md:0:0-0:0):

1. **External Dependencies section**: Note ALL listed services
   - Create a checklist of services to verify in specs
   - Pay special attention to "Domain Type Service" subsections
   - Note which services validate which domain types
2. **Service-Specific Analysis Checklist**: Review any domain-specific patterns
   - Multi-domain services often have validation services per domain type
   - Note any service naming clarifications (e.g., "GRPM for Resource domain type")
3. **Discrepancy tracking**: If manual notes mention a service not in specs:
   - Search code to verify service existence
   - If found in code, add to specs with fact ID
   - If not found in code, note discrepancy in change log

**Output**: Create a dependency checklist BEFORE verification:
External Services Checklist (from manual notes). Example:
[ ] Account Server - tenant management, OAuth2, Accounts
[ ] Specification Service - CTI Entities
[ ] Agent Manager - AGENT domain validation
[ ] Storage Manager - STORAGE domain validation
[ ] GRPM - RESOURCE domain validation
[ ] Outbound API Gateway - OAGW domain validation
[ ] Event Manager - notification events
[ ] RabbitMQ - event publishing

### 2.1 Scenario Validation

**AI Instructions**: For each scenario, perform these validation steps:

**Design Reminder**: Facts are part of the canonical system design. Validating or correcting a fact is a design review activity—require evidence from code and document rationale just as you would for architectural decisions.

1. **Cross-reference with source code**: Verify each step matches actual code behavior
2. **Validate completeness**: Ensure all code paths are documented (success, errors, edge cases)
3. **Check fact references**: Verify all `[FXYY.ZZZ]` references exist in orig-design-facts-and-rationale.md and follow FACTS_TAXONOMY.md format
4. **Validate fact categories**: Ensure facts use correct categories (FA, FB, FC, FD, FE, FF, FG, FH, FI, FJ) per FACTS_TAXONOMY.md
5. **Identify gaps**: Document missing scenarios, incomplete descriptions, or ambiguous statements
6. **Verify examples**: Ensure input/output examples are accurate and representative

**AI Instructions**: Refer to `FACTS_TAXONOMY.md` for:

- Complete fact categorization system and ID format
- Fact creation and validation rules
- Category definitions and subcategories
- Fact documentation templates and examples

### 2.2 Critical Checks

**AI Instructions**: For each scenario, verify these critical aspects are documented:

#### 2.2.1 Access Controls (**HIGHEST PRIORITY**)

- [ ] **API role checks**: Document specific roles (e.g., admin, user, guest) and where they're enforced
- [ ] **Database access permissions**: Document row-level security, table permissions, or ORM-level checks
- [ ] **License validation**: Document license types checked and validation logic
- [ ] **Quota enforcement**: Document limits (requests/day, storage, etc.) and enforcement points

**Verification Method**: Search code for authorization middleware, permission decorators, or access control functions.

#### 2.2.2 Data Management

- [ ] **Soft-delete handling**: Explicitly state if queries include/exclude soft-deleted records
- [ ] **Data validation**: Document all validation rules with fact references
- [ ] **State transitions**: Document valid state changes and constraints

**Verification Method**: Check for `deleted_at`, `is_deleted` fields and their usage in queries.

#### 2.2.3 Integration Points

- [ ] **Outbound communications**: For each external call, document:
  - Protocol and endpoint
  - Synchronous vs asynchronous
  - Timeout values (with code references)
  - Error handling strategy
  - Retry logic (attempts, backoff strategy)

**Verification Method**: Search for HTTP clients, gRPC clients, message queue publishers.

#### 2.2.4 Observability

- [ ] **Event logging**: Document event names, triggers, and payloads
- [ ] **Audit trails**: Document what actions are audited and where
- [ ] **Monitoring points**: Document metrics, traces, and alerts

**Verification Method**: Search for logging statements, event emitters, audit log calls.

#### 2.2.5 Implementation Tasks (if orig-tasks.md exists)

- [ ] **Task completeness**: All major implementation areas covered
- [ ] **Fact references**: Tasks reference valid fact IDs from orig-design-facts-and-rationale.md
- [ ] **Scenario references**: Tasks reference scenarios from orig-behavior.md
- [ ] **Task organization**: Tasks grouped logically by capability/phase
- [ ] **Acceptance criteria**: Each task has clear completion criteria

**Verification Method**: Cross-reference task fact IDs with facts file, verify scenario references.

#### 2.2.6 Dependency checks (**HIGHEST PRIORITY**)

**AI Instructions**: External dependencies are frequently incomplete in initial spec generation. Perform exhaustive verification:

- [ ] **Service dependencies enumeration**: List ALL external services the system depends on
  - Identity/Auth services (OAuth2, SSO, etc.)
  - Domain validation services (e.g. per domain type: AGENT, STORAGE, RESOURCE, USER, etc.)
  - Message queues and event buses
  - Application/configuration management services
  - Monitoring and observability services
  
- [ ] **Domain validation services**: For services with multiple domain types, verify EACH domain type has its validation service documented
  - Example: If service supports AGENT, STORAGE, RESOURCE domain types, verify Agent Manager, Storage Manager, AND GRPM are all documented
  - Cross-reference with manual notes for complete domain type list
  - Search code for domain validation logic patterns (e.g., `ValidateAgent`, `ValidateStorage`, `ValidateResource`)
- [ ] **Technical details per dependency**: For EACH external service, document:
  - Purpose and what it validates/provides
  - Protocol (REST, gRPC, AMQP, etc.)
  - Endpoint/connection details
  - Timeout values (with config file references)
  - Retry logic (attempts, intervals, backoff)
  - Error handling strategy
  - Optional vs mandatory (e.g., "optional for local agent mode")
- [ ] **Fact consistency**: Ensure dependencies are documented in ALL three files:
  - [orig-design-facts-and-rationale.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-design-facts-and-rationale.md:0:0-0:0): FA05 section with complete details
  - [orig-behavior.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-behavior.md:0:0-0:0): Integration Points section with endpoints
  - [orig-tasks.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-tasks.md:0:0-0:0): Implementation tasks for each client
- [ ] **Configuration cross-reference**: Verify timeout/endpoint facts reference actual config files
  - Example: `[FA05.004]` should reference `configs/config.yml services.agentMgr endpoint, 60s timeout`

**Verification Method**:

1. Search code for: HTTP clients, gRPC clients, message queue publishers, validation functions
2. Search config files for: `endpoint`, `timeout`, `services.*`, external service names
3. Review manual notes [_manual_input/01-orig-service-notes.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/guidelines/code-to-spec/_manual_input/01-orig-service-notes.md:0:0-0:0) section on "Dependent Services"
4. Create a checklist of all domain types → verify each has validation service documented
5. Search for patterns like: `Validate*`, `*Manager`, `*Client`, external API calls

#### 2.2.7 Domain Type Validation Matrix (**CRITICAL FOR MULTI-DOMAIN SERVICES**)

**AI Instructions**: For services that support multiple domain types, create and verify a validation matrix:

- [ ] **List all domain types**: Extract from API specs, code enums, or manual notes
  - Example: GLOBAL, TENANT, USER, AGENT, STORAGE, RESOURCE, APPLICATION, BRAND, OAGW
- [ ] **Map validation services**: For each domain type, identify which external service validates it Domain Type → Validation Service
- [ ] **Verify completeness**: Ensure NO domain type is missing its validation service
  - If a domain type lacks validation, document this explicitly as "no validation" with rationale
- [ ] **Check for confusion**: Watch for services with similar names
  - Example: Storage Manager (STORAGE) vs GRPM (RESOURCE) - these are DIFFERENT services
  - [ ] Verify manual notes for clarification on service responsibilities

**Verification Method**:

- Search code for domain type enums or constants
- Search for validation dispatcher/router logic
- Cross-reference with manual notes "Domain Type Service" section
- Grep for each domain type name + "validate" or "manager"

#### 2.2.8 Hierarchical Data & Inheritance Verification (**CRITICAL FOR HIERARCHICAL SERVICES**)

**AI Instructions**: For services with parent-child relationships, tenant hierarchies, or organizational trees, verify comprehensive inheritance documentation. Use the following points and add any other points that you think are relevant based on the source code:

- [ ] **Inheritance section exists**: Check for dedicated "Value Inheritance Behavior" or similar section in `orig-behavior.md`
- [ ] **Inheritance hierarchy documented**: Overview of how inheritance works (path calculation, barriers, resolution order)
- [ ] **Rules by entity/domain type**: Each type has documented inheritance behavior
  - What inherits (values, settings, permissions, etc.)
  - What doesn't inherit and why
  - Domain-specific rules clearly stated
- [ ] **Control flags documented**: All inheritance control mechanisms identified
  - Entity/namespace-level flags (e.g., `is_inheritable`, `is_value_inheritable`, `barrier_inherit`)
  - Query-level parameters (e.g., `explicit_only`, `include_inherited`)
  - Effect of each flag clearly explained
- [ ] **Path calculation documented**: How hierarchical paths are calculated and used
  - Path format and structure
  - Multiple path types if applicable (normal path, barrier path)
  - Code references to path calculation functions
- [ ] **Override behavior documented**: Complete priority chain explained
  - Explicit vs inherited values
  - Specific vs generic values
  - Default value fallback
  - Examples of override scenarios
- [ ] **Inheritance isolation**: Barrier/boundary mechanisms documented
  - How inheritance is prevented/isolated
  - Self-service or branch isolation patterns
- [ ] **Code references complete**: All inheritance logic has file/function references
  - Value resolution logic
  - Path calculation
  - Inheritance queries
  - Override handling

**Verification Method**:

1. Search code for inheritance patterns: `inherit`, `parent`, `path`, `hierarchy`, `resolve`, `barrier`
2. Check manual notes for inheritance explanations
3. Verify value resolution logic in domain/service layer
4. Cross-reference with database schema (path columns, parent_id fields)
5. Ensure facts exist for all inheritance mechanisms (FF category for service layer logic)

**Common Issues**:

- Inheritance mentioned in fragments but no consolidated section
- Missing domain-specific rules (different types have different inheritance)
- Control flags not documented or effects unclear
- Path calculation logic not explained
- Override priority not specified

#### 2.2.9 Design Decisions (in orig-design-facts-and-rationale.md)

- [ ] **Architecture patterns**: Accurately described with rationale
- [ ] **Trade-offs**: Documented with pros/cons
- [ ] **Alternative approaches**: Considered and rejected options documented
- [ ] **Technical debt**: Known limitations and future improvements

**Verification Method**: Review Design Decisions section for accuracy and completeness.

### 2.3 Fact Numbering Consistency

**AI Instructions**: After adding new facts, verify numbering:

1. **Sequential numbering**: Within each subcategory (e.g., FA05), facts must be sequential
   - FA05.001, FA05.002, FA05.003... (no gaps, no duplicates)
2. **Cross-document consistency**: Fact IDs must match across all three files
   - If [orig-behavior.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-behavior.md:0:0-0:0) references [FA05.004], it must exist in [orig-design-facts-and-rationale.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-design-facts-and-rationale.md:0:0-0:0)
   - If [orig-tasks.md](cci:7://file:///Users/hitengajjar_1/workspace/github.com/~hiten.gajjar/hypernetix/hyperspot_using_aa_method/openspec/specs/settings-service-core/orig-tasks.md:0:0-0:0) references [FA05.004], it must match the same fact
3. **Renumbering cascade**: If inserting a fact mid-sequence:
   - Renumber all subsequent facts in that subcategory
   - Update ALL references in all three documents
   - Document the renumbering in change log

**Verification Method**:

- Grep for `\[F[A-Z][0-9]{2}\.[0-9]{3}\]` pattern in each file
- Sort and check for duplicates or gaps
- Cross-reference fact IDs between all three files

## 3. Updates

### 3.1 File Updates

1. Update `orig-behavior.md` with any critical corrections
2. Update `orig-design-facts-and-rationale.md` with any new or modified facts
3. Update `orig-tasks.md` with corrected fact/scenario references (if file exists)
4. Maintain existing formatting and style

### 3.2 Change Guidelines

**AI Instructions**: Follow these rules when making updates:

1. **Critical changes only**: Only modify content that is functionally incorrect or missing critical information
2. **No style changes**: Do not reformat, rephrase, or restructure unless it impacts clarity of critical information
3. **Document rationale**: For each change, add a comment explaining why it was necessary
4. **Preserve structure**: Maintain the existing document organization and formatting
5. **Fact references**: Always use correct format `[FXYY.ZZZ]` per FACTS_TAXONOMY.md (e.g., `[FA01.001]`, `[FE02.001]`, `[FC00.015]`)

### 3.3 Change Documentation Format

**AI Instructions**: Document each change using this format:

```markdown
## Change Log

### Change 1: [Brief description]
- **File**: orig-behavior.md / orig-design-facts-and-rationale.md / orig-tasks.md
- **Section**: [Section name]
- **Type**: Addition / Correction / Clarification
- **Rationale**: [Why this change was necessary]
- **Code Reference**: [File:line where this was verified]
```

## 4. Output Requirements

### 4.1 Updated Files

- `orig-behavior.md` with verified and corrected scenarios
- `orig-design-facts-and-rationale.md` with validated and corrected facts and design decisions
- `orig-tasks.md` with corrected fact/scenario references (if file exists)
- Change log documenting all modifications

### 4.2 Verification Report

**AI Instructions**: Generate a verification report with this structure:

```markdown
# Verification Report

## Summary
- Total scenarios reviewed: [number]
- Issues found: [number]
- Critical corrections made: [number]
- Warnings/recommendations: [number]

## Critical Issues Found
1. [Issue description] - **FIXED** / **NEEDS ATTENTION**
2. [Issue description] - **FIXED** / **NEEDS ATTENTION**

## Verification Results by Category

### Access Controls
- ✓ All API role checks documented: YES / NO
- ✓ All DB access checks documented: YES / NO
- ✓ All license checks documented: YES / NO
- ✓ All quota checks documented: YES / NO

### Data Management
- ✓ Soft-delete handling explicit: YES / NO
- ✓ Validation rules complete: YES / NO

### Integration Points
- ✓ All outbound calls documented: YES / NO
- ✓ Timeouts specified: YES / NO
- ✓ Error handling documented: YES / NO

### Observability
- ✓ Events documented: YES / NO
- ✓ Audit logs documented: YES / NO

### Implementation Tasks (if orig-tasks.md exists)
- ✓ Task completeness: YES / NO
- ✓ Fact references valid: YES / NO
- ✓ Scenario references valid: YES / NO

### Design Decisions (in orig-design-facts-and-rationale.md)
- ✓ Architecture patterns documented: YES / NO
- ✓ Trade-offs documented: YES / NO
- ✓ Technical debt identified: YES / NO
- ✓ Design-fact alignment: YES / NO

## Recommendations
1. [Recommendation for improvement]
2. [Recommendation for improvement]
```
