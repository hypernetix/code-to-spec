## code-to-spec (spec-first service conversion)

This repository is a **spec-driven workflow** for converting a typical REST API microservice (like in Go-lang or Rust) into a specification:

- Maintaining a **shared logic taxonomy** (Facts) that is stable across implementations.
- Extracting the **observed behavior** of an existing service into structured specs.
- Cross-checking and correcting those specs using multiple LLM passes.
- Proposing upgrades/improvements to the spec before re-implementation.
- Comparing specs and producing final **FACTS** + **BEHAVIOR** documents to feed code generation.

---

## Core idea: Facts Taxonomy

- **Taxonomy**: see [FACTS_TAXONOMY.md](FACTS_TAXONOMY.md)
- A **Fact** is a reusable, independently-verifiable behavior/constraint that can be referenced by multiple scenarios using the format `[FXX.YYY]`.
- The goal is to turn “service logic” into an explicit, portable inventory that can be re-implemented in a new stack.

---

## Workflow (end-to-end)

### Step 0) Install OpenSpec and initialize this workspace

This workflow is designed to work with **any spec-driven development framework**, but is optimized for and documented using **OpenSpec** as the primary tool.

OpenSpec is a CLI that helps scaffold and validate specs. If you prefer a different spec-driven framework, you can adapt the workflow accordingly - the core outputs (`NEW_BEHAVIOR.md`, `NEW_FACTS.md`, `NEW_TASKS.md`) are framework-agnostic Markdown files.

**Install OpenSpec CLI (Node.js required):**

```bash
node --version
npm install -g @fission-ai/openspec@latest
openspec --version
```

**Initialize OpenSpec in this repo:**

```bash
cd /path/to/code-to-spec
openspec init
```

During `openspec init`, select whichever AI tools you plan to use (Cursor/Windsurf/Antigravity). This will set up the `openspec/` folder and tool-specific slash commands/agent handoffs as needed.

### Step 1) Add your legacy service code as a symlink

Keep this repo clean by adding the legacy codebase as a symlink (so the analysis output lives here, but the source stays in its own repo).

Example:

```bash
cd /path/to/code-to-spec
ln -s /absolute/path/to/your-orig-service ./legacy-service
```

From here on, point the agent at `./legacy-service` (or rename it to match your conventions).

### Step 1) Review and update service logic taxonomy if needed

Use `FACTS_TAXONOMY.md` as the canonical categorization system for:
- domain entities
- scenarios
- access control patterns
- persistence behavior
- outbound calls
- background jobs
- timeouts and limits
- events and audit logs

### Step 2) Assess existing service behavior (OpenSpec + prompt)

Open your favorite IDE with agentic mode. Your first instruction should explicitly point it at the symlinked legacy repo and the prompt to use.

Example instruction:
- "With openspec: Review my old Go-lang service in `./legacy-service` and do what I describe in `prompts/1_CODE_TO_SPEC.md`. Output into a new folder for that capability."

**Before starting, the agent will ask for additional instructions on alterations** (e.g., domain migrations, converting to module/library, test coverage levels, dropping legacy features, compatibility constraints). All instructions and decisions are recorded in `DECISIONS.md`.

Output per analyzed capability/module:
- `ORIG_BEHAVIOR.md`: complete behavioral analysis with scenarios (API calls, async jobs, side effects, API contracts, DB schemas, outbound communication, events, audit logs), including primary and error paths, plus a "Dead code / unused features" section
- `ORIG_FACTS.md`: reusable facts with IDs `[FXX.YYY]` mapping to FACTS_TAXONOMY.md categories, including:
  - Behavioral facts referenced in ORIG_BEHAVIOR.md
  - Common test fixtures (tenant hierarchy, roles, licenses/quotas, canonical entities)
  - Scenario-level test guidance (function names, purpose, step-by-step mutations, expected errors/side-effects)
  - "Dead code facts" section
- `ORIG_TASKS.md`: OpenSpec-style implementation tasks with:
  - References to related scenarios and fact IDs
  - Acceptance criteria and non-functional requirements (timeouts/limits, idempotency, retries, observability)
  - Checkboxes for tracking progress
- `DECISIONS.md`: user-provided alteration instructions and decisions (simple English, no granular technical details)

### Step 3) Verify and correct the ORIG spec using cross-checks (multiple LLM passes)

Use prompt:
- `prompts/2_VERIFY_ORIG_SPEC.md`

Goal:
- make **only critical corrections** (no style/format changes) to `ORIG_BEHAVIOR.md`, `ORIG_FACTS.md`, `ORIG_TASKS.md`, and `DECISIONS.md`
- explicitly verify, per scenario:
  - **API role checks**
  - **DB access checks**
  - **license checks**
  - **quota checks**
  - **timeouts / limits**
  - **events / audit logs**
  - **outbound API communication**
  - whether **soft-deleted items** are included or excluded
- validate dead code detection (unused endpoints, handlers, jobs, data models, feature flags, unreachable code)
- verify unit test facts and verbose test guidance are complete
- ensure ORIG_TASKS.md references scenarios and fact IDs correctly
- confirm DECISIONS.md captures all user instructions and optional proposals

### Step 4) Propose improvements and missing scenarios (spec upgrade)

Use prompt:
- `prompts/3_SPEC_UPGRADE.md`

Process:
- Copy `ORIG_BEHAVIOR.md` → `NEW_BEHAVIOR.md`
- Copy `ORIG_FACTS.md` → `NEW_FACTS.md`
- Copy `ORIG_TASKS.md` → `NEW_TASKS.md`
- Incorporate direct proposals and suggest cutting-edge industry patterns
- Review for potential gaps and propose new scenarios/APIs
- Every newly introduced step or scenario MUST be marked with a `[NEW]` prefix
- Keep ORIG_BEHAVIOR structure (new scenarios at end, new steps inline) to enable easy diffing
- Preserve "Dead code / unused features" section (update if features are revived)
- Record any optional API proposals in DECISIONS.md

Output:
- `NEW_BEHAVIOR.md`: enhanced behavior spec with `[NEW]` prefixes
- `NEW_FACTS.md`: updated facts with new IDs following `[FXX.YYY]` format
- `NEW_TASKS.md`: updated tasks including work for `[NEW]` scenarios
- `DECISIONS.md`: updated with optional proposals

### Step 5) Compare specs and prepare final FACTS/BEHAVIOR for code generation

Use prompt:
- `prompts/4_COMPARE_SPECS.md`

At this stage you have:
- **ORIG**: audited snapshot of current system behavior
- **NEW**: improved target behavior (still spec-only)

Process:
- Compare **ORIG** vs **NEW** with special attention to:
  - APIs (paths, request/response schemas, streaming formats)
  - scenarios (steps, conditions, fact references)
  - **API role checks**, **DB access checks**, **license checks**, **quota checks**
  - **soft-deleted items** handling
  - **outbound communications** (sync, async, timeout, error management)
  - **timeouts and limitations**
  - **events and audit logs**
- Validate dead code sections are preserved and consistent
- Ensure NEW_TASKS.md maps to CHANGES.md (each change has implementation work)
- Verify DECISIONS.md requirements are reflected in NEW specs

Generate `CHANGES.md` (technical diff between ORIG and NEW):
- Group similar changes into chapters when a broad change affects many places
- Keep each individual change as a separate item
- Assign change importance:
  - **MAJOR**: access control, DB access checks, configuration parameters, API incompatible changes
  - **MODERATE**: compatible changes (new API parameters, new error types, config structure changes)
  - **MINOR**: other behavioral changes

Finalize:
- `NEW_BEHAVIOR.md`: reconciled with consistent fact references
- `NEW_FACTS.md`: finalized with consistent `[FXX.YYY]` IDs
- `NEW_TASKS.md`: complete implementation tasks with scenario/fact references and checkboxes
- `DECISIONS.md`: all user instructions and decisions (simple English)
- `CHANGES.md`: technical diff with importance ratings

### Step 6) Use the finalized specs to implement the new service

**At this point, you have completed the spec-driven analysis workflow.** Here's what you have and how to use it:

#### Final Outputs Summary

| File | Purpose | How to Use |
|------|---------|------------|
| **`NEW_BEHAVIOR.md`** | Complete behavioral specification with scenarios, fact references, and `[NEW]` additions | **Source of truth** for what the new service must do |
| **`NEW_FACTS.md`** | Reusable facts catalog with `[FXX.YYY]` IDs mapped to FACTS_TAXONOMY | **Reference guide** for implementation details and test fixtures |
| **`NEW_TASKS.md`** | Implementation task list with checkboxes, scenario/fact references | **Work breakdown** for building the new service |
| **`DECISIONS.md`** | User instructions and architectural decisions | **Context** for why certain choices were made |
| **`CHANGES.md`** | Technical diff between ORIG and NEW with importance ratings | **Migration guide** showing what changed and why |
| `ORIG_BEHAVIOR.md` | Original service behavior (for reference) | Compare against NEW to understand evolution |
| `ORIG_FACTS.md` | Original service facts (for reference) | Compare against NEW to see what was preserved/changed |

#### Relationship to Spec-Driven Development Frameworks

- **`NEW_BEHAVIOR.md` + `NEW_FACTS.md` = Your specification** (language-agnostic, portable, framework-independent)
- **`NEW_TASKS.md` = Your implementation plan** (structured Markdown with checkboxes and references)
- **Framework workspace (e.g., `openspec/`)** = Optional tooling/scaffolding to help you implement

The `NEW_*.md` files are **framework-independent** - they are your source-of-truth specs that can be used with:
- **OpenSpec** (recommended, this README is tailored for it)
- **Other spec-driven frameworks** (Kiro, spec-kit, etc.)
- **Manual implementation** (no framework needed)
- **Custom tooling** (your own code generation pipeline)

#### Implementation Options

**Option A: Use OpenSpec for implementation scaffolding (Recommended)**

This README is tailored for OpenSpec, which provides:
- Task management and tracking
- Code scaffolding from behavior specs
- Validation that implementation matches specs
- IDE integration (Cursor/Windsurf/Antigravity)

Steps:
1. Use `NEW_TASKS.md` as your task list directly, or import/translate it into OpenSpec's task management format
2. Use OpenSpec CLI commands to scaffold code based on `NEW_BEHAVIOR.md` scenarios
3. Reference `NEW_FACTS.md` when implementing specific behaviors
4. Use OpenSpec's validation/spec-check features to verify implementation matches specs

**Option B: Feed to LLM or code generation tools**

1. Export `NEW_BEHAVIOR.md` + `NEW_FACTS.md` to your preferred code generation tool (Claude, GPT, Copilot, template engine, etc.)
2. Use `NEW_TASKS.md` to track generated code completion
3. Reference `DECISIONS.md` for architectural constraints

#### Recommended Next Steps

1. **Review finalized specs**: Read through `NEW_BEHAVIOR.md`, `NEW_FACTS.md`, and `CHANGES.md` to ensure completeness
2. **Set up target project**: Initialize your new service repository in the target language/framework
3. **Start implementation**: Work through `NEW_TASKS.md` systematically, referencing facts and scenarios
4. **Validate against specs**: Ensure each implemented feature matches the behavior described in `NEW_BEHAVIOR.md`
5. **Track progress**: Check off tasks in `NEW_TASKS.md` as you complete them

---

## Notes / constraints

- This workflow is **language-agnostic** in principle: any “legacy service” can be analyzed into Facts + Scenarios and then re-implemented elsewhere.
- Whether something is “supported” must be grounded in code: if **license checks**, **quota checks**, **events**, or **audit logs** are not implemented, the spec should say so explicitly.
- Facts are **append-only** (never renumber IDs); scenarios should reference facts via `[FXX.YYY]` rather than duplicating behavior text.

---

## Repo map (high-level)

- **Taxonomy**: `FACTS_TAXONOMY.md`
- **Prompts**:
  - `prompts/1_CODE_TO_SPEC.md`
  - `prompts/2_VERIFY_ORIG_SPEC.md`
  - `prompts/3_SPEC_UPGRADE.md`
  - `prompts/4_COMPARE_SPECS.md`
- **OpenSpec workspace**: `openspec/` (created by `openspec init` based on the tools you select)
- **The flow outputs**:
  - `ORIG_BEHAVIOR.md` - original source code behavior
  - `ORIG_FACTS.md`  - original source code facts
  - `ORIG_TASKS.md` - initial implementation tasks with scenario/fact references and checkboxes
  - `NEW_BEHAVIOR.md` - new source code behavior and proposals comparable with ORIG version
  - `NEW_FACTS.md` - new source code facts and proposals comparable with ORIG version
  - `NEW_TASKS.md` - updated implementation tasks including [NEW] scenarios
  - `DECISIONS.md` - user instructions and decisions made (simple English)
  - `CHANGES.md` - technical diff between ORIG and NEW specs (with importance ratings)
****