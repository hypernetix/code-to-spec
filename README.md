# Code-to-Spec (Spec-First Service Conversion)

## 1. Document Index

1. [FACTS_TAXONOMY.md](FACTS_TAXONOMY.md) - Core taxonomy of reusable facts and behaviors
2. [prompts/01-code-to-spec.md](prompts/01-code-to-spec.md) - Initial code analysis and spec generation
3. [prompts/02-verify-orig-spec.md](prompts/02-verify-orig-spec.md) - Specification verification and validation
4. [prompts/03-spec-upgrade.md](prompts/03-spec-upgrade.md) - Spec improvement and enhancement
5. [prompts/04-compare-specs.md](prompts/04-compare-specs.md) - Specification comparison and change tracking
6. [samples/](samples/) - Example output files to guide your generation

## 2. Project Overview

This repository is a **spec-driven workflow** for converting a typical REST API software application (e.g. microservices) (like in Go-lang or Rust) into a specification.

**Framework Independence**: The core output of this workflow (`orig-behavior.md`, `orig-design-facts-and-rationale.md`, `orig-tasks.md`,etc.) is standard Markdown. These files are designed to be standalone, human-readable, and portable to any LLM or code generation tool.

**OpenSpec Optimization**: While the specs are independent, this workflow is optimized for use with **OpenSpec** to provide structured validation, scaffolding, and agentic capabilities. You can map the generated files to OpenSpec's format if desired.

### Core Goals

- Maintaining a **shared logic taxonomy** (Facts) that is stable across implementations.
- Extracting the **observed behavior** of an existing service into structured specs.
- Cross-checking and correcting those specs using multiple LLM passes.
- Proposing upgrades/improvements to the spec before re-implementation.
- Comparing specs and producing final **FACTS** + **BEHAVIOR** documents to feed code generation.

---

## 3. Core Concepts

### Facts

A **Fact** is a reusable, independently-verifiable behavior/constraint that can be referenced by multiple scenarios using the format `[FXYY.ZZZ]` where:

- **F** = Fact prefix
- **X** = Major category (A-J)
- **YY** = Subcategory (00-99)
- **ZZZ** = Sequential fact ID (001-999)

> **Design Principle**: Facts are part of the service design artifact set. Adding, modifying, or referencing a fact is equivalent to changing the design—require the same rigor, traceability, and evidence you would for architecture decisions.

**Fact Categories**: Use `FACTS_TAXONOMY.md` as a reference for complete category definitions, subcategories, ID assignment rules, and examples

The goal is to turn "service logic" into an explicit, portable inventory that can be re-implemented in a new stack.

---

## 4. Workflow (End-to-End)

### Step1: Install OpenSpec and Initialize Workspace

OpenSpec is the CLI used to scaffold and validate specs in this repo.

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

During `openspec init`, select whichever AI tools you plan to use (Cursor/Windsurf/Antigravity). This will set up the `openspec/` folder if and tool-specific slash commands/agent handoffs as needed.

### Step2: Add Legacy Service Code

Keep this repo clean by adding the legacy codebase as a symlink (so the analysis output lives here, but the source stays in its own repo).

Example:

```bash
cd /path/to/code-to-spec
ln -s /absolute/path/to/your-orig-service ./legacy-service
```

Symlink all the related repos associated to the service within `./legacy-service` folder Consider below examples:

- API specifications - `./legacy-service/.apis/`
- CTI type identifiers - `./legacy-service/.ctis/`
- Documentation - `./legacy-service/.docs`

From here on, point the agent at `./legacy-service` (or rename it to match your conventions).

### Step3: Review Service Logic Taxonomy and Assess Existing Service Behavior (OpenSpec + prompt)

Open you favorite IDE with agentic mode. Your first instruction should explicitly point it at the symlinked legacy repo and the prompt to use.

Example instruction:

```markdown

Review `FACTS_TAXONOMY.md` as the canonical categorization system. This document defines:

- **major fact categories** with subcategories
- **Fact ID format** `[FXYY.ZZZ]` and numbering rules
- **Creation guidelines** for new facts
- **Validation rules** for fact references
- **Category templates** with examples

All prompts and outputs must follow this taxonomy for consistency

"With openspec":

- Review my old Go-lang service in `./legacy-service`, associated technical material like API specifications, CTI type identifiers, Documentation etc in `./legacy-service/.*` folders and do what I describe in `prompts/01-code-to-spec.md`. Use `FACTS_TAXONOMY.md` for fact categorization. 
- Output `orig-behavior.md`, `orig-tasks.md` and `orig-design-facts-and-rationale.md` into a new folder for that capability."

Output per analyzed capability/module is typically:

- `orig-behavior.md`: "what the current service does" (scenarios, primary/error paths)
- `orig-design-facts-and-rationale.md`: reusable facts extracted from that behavior, categorized per `FACTS_TAXONOMY.md`, referenced by ID `[FXYY.ZZZ]` in `orig-behavior.md`
- `orig-tasks.md`: implementation tasks with fact/scenario references and acceptance criteria

```

### Step4: Verify and Correct Specifications (multiple LLM passes)

Use the following prompt:

```markdown

Do what I describe in `prompts/02-verify-orig-spec.md` to achieve below **goal**:

- make **only critical corrections** to `orig-behavior.md`, `orig-tasks.md` and `orig-design-facts-and-rationale.md`
- double check below for each scenario:
  - **API role checks**
  - **DB access checks**
  - **license checks**
  - **quota checks**
  - **timeouts / limits**
  - **events / audit logs**
  - **outbound API communication**
  - **concurrency / thread pooling**
  - and whether **soft-deleted items** are included or excluded

```

### Step5: Propose Improvements (spec upgrade)

**Before running the upgrade**, document your requested changes in `_manual_input/02-new-service-feature-change.md`:

- Use the simplified format: `[Priority] Type: Feature/Endpoint - Brief reason`
- Priority: `P0` (critical), `P1` (high), `P2` (medium), `P3` (low)
- Type: `ADD`, `MODIFY`, `REMOVE`
- Add optional 1-2 line notes for complex changes
- The AI will expand these into full specs using `FACTS_TAXONOMY.md` and existing patterns

```markdown

Do what I describe in `prompts/03-spec-upgrade.md` to achieve below **goal**:

- `new-behavior.md` created by copying `orig-behavior.md` and applying improvements from change intent file
- `new-design-facts-and-rationale.md` copying `orig-design-facts-and-rationale.md` and  updated with new facts following `FACTS_TAXONOMY.md`
- `new-tasks.md` copying `orig-tasks.md` and updated with implementation tasks for new features
- Every newly introduced step or scenario MUST be marked with a `[NEW]` prefix (so diffs are easy)
- Modified scenarios marked with `[MODIFIED]` prefix
- Remove scenarios, design, facts and tasks thats mentioned in the change intent file 

```

### Step6: Compare specs and prepare final FACTS/BEHAVIOR/TASKS/DESIGN for code generation

Use the following prompt:

```markdown

Do what I describe in `prompts/04-compare-specs.md`.

At this stage you have:

- **ORIG**: audited snapshot of current system behavior
- **NEW**, **MODIFIED**: improved target behavior (still spec-only)

Then:

- compare **ORIG** vs **NEW** & **MODIFIED** with special attention to changes in:
  - APIs (paths, request/response schemas, streaming formats)
  - scenarios (steps, conditions, fact references)
  - **API role checks**, **DB access checks**, **license checks**, **quota checks**
- generate `CHANGES.md`:
  - group similar changes into chapters when a broad change affects many places
  - keep each individual change as a separate item
  - assign change importance: **MAJOR**, **MODERATE**, **MINOR**
- reconcile and finalize:
  - update facts and references to keep `[FXYY.ZZZ]` consistent
  - update the final `new-design-facts-and-rationale.md`, `new-tasks.md` + `new-behavior.md` as the source-of-truth for rewriting and/or code generation

```

## 5. Developer Guide: How to Use the Samples

These samples exist primarily so your **AI agent** knows the exact structure to target. Before you run Step 4 (or rerun verification/upgrade steps), skim the samples and paste relevant snippets into the agent prompt so it can mirror the formatting without guesswork.

### 5.1 Using the Samples as a Template

1. **`samples/orig-behavior.md`** → shows the expected scenario structure (triggers, primary/error paths, validations, state changes, references, code locations).
2. **`samples/orig-design-facts-and-rationale.md`** → demonstrates fact cataloging across taxonomy categories with `[FXYY.ZZZ]` IDs, implementation notes, and related facts.
3. **`samples/orig-tasks.md`** → illustrates how implementation tasks link back to facts and scenarios with acceptance criteria.

### 5.2 Your Workflow

When you run Step 4 (or any later prompt), explicitly point the AI to the samples:

1. Paste the relevant sample snippet (e.g., full scenario or fact block) into the prompt as the "golden format".
2. Tell the AI to **strictly match that structure** for headings, checklists, fact references, and acceptance criteria.
3. Remind the AI that all IDs must follow `[FXYY.ZZZ]` from `FACTS_TAXONOMY.md`.

**Example Prompt Addition:**
> "Use `samples/orig-behavior.md` as the canonical layout. Mirror the section order, bullets, and fact references exactly, and reference `FACTS_TAXONOMY.md` for `[FXYY.ZZZ]` IDs."

## 6. Mapping to OpenSpec (Optional)

If you choose to use OpenSpec for automation, here is how the independent Markdown files map to OpenSpec's structured format:

| Markdown Component    | OpenSpec Concept  | Mapping Strategy                                                                                                            |
|-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **Fact** `[FXYY.ZZZ]` | **Atom/Fact**     | Each fact in `orig-design-facts-and-rationale.md` becomes a structured Fact object with `id`, `category`, and `attributes`. |
| **Scenario**          | **Flow/Scenario** | Each scenario in `orig-behavior.md` maps to a Flow, with steps referencing Fact IDs as dependencies.                         |
| **Behavior.md**       | **Spec File**     | The Behavior document parses into a `spec.yaml` or `spec.json` definition.                                                  |
| **Changes.md**        | **Diff Report**   | Major/Moderate changes map to breaking/non-breaking version bumps in OpenSpec.                                              |

This mapping allows you to start with human-readable Markdown and "upgrade" to machine-enforceable OpenSpec validation later without losing work.

---

## 7. Notes / constraints

- This workflow is **language-agnostic** in principle: any “legacy service” can be analyzed into Facts + Scenarios and then re-implemented elsewhere.
- Whether something is “supported” must be grounded in code: if **license checks**, **quota checks**, **events**, or **audit logs** are not implemented, the spec should say so explicitly.
- Facts are **append-only** (never renumber IDs); scenarios should reference facts via `[FXYY.ZZZ]` (e.g., `[FA01.001]`, `[FC00.005]`) rather than duplicating behavior text.
- All fact categorization, ID assignment, and validation must follow `FACTS_TAXONOMY.md`.

---

## 8. Repo map (high-level)

- **Taxonomy**: `FACTS_TAXONOMY.md`
- **Change Intent**: `_manual_input/02-new-service-feature-change.md` - Developer input for spec upgrades
- **Prompts**:
  - `prompts/01-code-to-spec.md`
  - `prompts/02-verify-orig-spec.md`
  - `prompts/03-spec-upgrade.md`
  - `prompts/04-compare-specs.md`
- **The flow outputs**:
  - `orig-behavior.md` - original source code behavior
  - `orig-design-facts-and-rationale.md`  - original source code facts
  - `orig-tasks.md`  - original source code tasks
  - `new-behavior.md` - new source code behavior and proposals comparable with original version
  - `new-design-facts-and-rationale.md` - new source code facts and proposals comparable with original version
  - `new-tasks.md`  - new source code tasks
  - `CHANGES.md` - the difference
