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
- A **Fact** is a reusable, independently-verifiable behavior/constraint that can be referenced by multiple scenarios using the format `[FXYY.ZZZ]` where X is an alphabetic category (e.g., FA, FB, FC), YY is a numeric subcategory (00-99), and ZZZ is the sequential fact ID (001-999).
- The goal is to turn “service logic” into an explicit, portable inventory that can be re-implemented in a new stack.

---

## Workflow (end-to-end)

### Step 0) Install OpenSpec and initialize this workspace

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

Open you favorite IDE with agentic mode. Your first instruction should explicitly point it at the symlinked legacy repo and the prompt to use.

Example instruction:
- “With openspec: Review my old Go-lang service in `./legacy-service` and do what I describe in `prompts/1_CODE_TO_SPEC.md`. Output `ORIG_BEHAVIOR.md` and `ORIG_FACTS.md` into a new folder for that capability.”

Output per analyzed capability/module is typically:
- `ORIG_BEHAVIOR.md`: “what the current service does” (scenarios, primary/error paths)
- `ORIG_FACTS.md`: reusable facts extracted from that behavior, referenced by ID in `ORIG_BEHAVIOR.md`

### Step 3) Verify and correct the ORIG spec using cross-checks (multiple LLM passes)

Use prompt:
- `prompts/2_VERIFY_ORIG_SPEC.md`

Goal:
- make **only critical corrections** to `ORIG_BEHAVIOR.md` and `ORIG_FACTS.md`
- explicitly verify, per scenario:
  - **API role checks**
  - **DB access checks**
  - **license checks**
  - **quota checks**
  - **timeouts / limits**
  - **events / audit logs**
  - **outbound API communication**
  - **concurrency / thread pooling**
  - and whether **soft-deleted items** are included or excluded

### Step 4) Propose improvements and missing scenarios (spec upgrade)

Use prompt:
- `prompts/3_SPEC_UPGRADE.md`

Output:
- `NEW_BEHAVIOUR.md` created by copying `ORIG_BEHAVIOR.md` and applying improvements.
- Every newly introduced step or scenario MUST be marked with a `[NEW]` prefix (so diffs are easy).

### Step 5) Compare specs and prepare final FACTS/BEHAVIOR for code generation

Use prompt:
- `prompts/4_COMPARE_SPECS.md`

At this stage you have:
- **ORIG**: audited snapshot of current system behavior
- **NEW**: improved target behavior (still spec-only)

Then:
- compare **ORIG** vs **NEW** with special attention to changes in:
  - APIs (paths, request/response schemas, streaming formats)
  - scenarios (steps, conditions, fact references)
  - **API role checks**, **DB access checks**, **license checks**, **quota checks**
- generate `CHANGES.md`:
  - group similar changes into chapters when a broad change affects many places
  - keep each individual change as a separate item
  - assign change importance: **MAJOR**, **MODERATE**, **MINOR**
- reconcile and finalize:
  - update facts and references to keep `[FXYY.ZZZ]` consistent
  - produce the final `NEW_FACTS.md` + `NEW_BEHAVIOR.md` as the source-of-truth for rewriting and/or code generation

---

## Notes / constraints

- This workflow is **language-agnostic** in principle: any “legacy service” can be analyzed into Facts + Scenarios and then re-implemented elsewhere.
- Whether something is “supported” must be grounded in code: if **license checks**, **quota checks**, **events**, or **audit logs** are not implemented, the spec should say so explicitly.
- Facts are **append-only** (never renumber IDs); scenarios should reference facts via `[FXYY.ZZZ]` (e.g., `[FA01.001]`, `[FC00.005]`) rather than duplicating behavior text.

---

## Repo map (high-level)

- **Taxonomy**: `FACTS_TAXONOMY.md`
- **Prompts**:
  - `prompts/1_CODE_TO_SPEC.md`
  - `prompts/2_VERIFY_ORIG_SPEC.md`
  - `prompts/3_SPEC_UPGRADE.md`
  - `prompts/4_COMPARE_SPECS.md`
- **OpenSpec guidance**: `openspec/AGENTS.md`
- **The flow outputs**:
  - `ORIG_BEHAVIOR.md` - original source code behavior
  - `ORIG_FACTS.md`  - original source code facts
  - `NEW_BEHAVIOR.md` - new source code behavior and proposals comparable with ORIG version
  - `NEW_FACTS.md` - new source code facts and proposals comparable with ORIG version
  - `CHANGES.md` - the difference
