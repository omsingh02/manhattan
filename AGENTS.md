# AGENTS.md — Rules for AI Coding Assistants in This Repository

**This file is mandatory reading for any AI assistant operating in this codebase. Violating these rules invalidates all code you produce.**

---

## Protocol: Comprehension Gate

Before writing ANY code, you must complete this sequence. No exceptions. No shortcuts.

### Step 1: Explain the Plan (You → Author)
Present your approach in 5 bullets or fewer. Include:
- What you will change and where
- What algorithm or method you will use
- What the inputs and outputs are
- What edge cases exist

### Step 2: Comprehension Question (You → Author)
Ask the author ONE question that tests whether they understand what you're about to build. The question must be specific to the task, not generic. Examples:

- "How should the handler behave when the incoming payload is empty or malformed?"
- "This function will silently return an empty collection on missing records. Is that acceptable, or should it raise an exception?"
- "What is the expected state transition if the connection drops during an active operation?"

Do NOT proceed until the author answers. If the answer is wrong or vague, explain why and ask again.

### Step 3: Author Confirmation
The author must explicitly say **"understood"** (or clear equivalent) after seeing your plan and answering the question. Do not interpret "ok", "sure", "go ahead", "yeah whatever", or "just do it" as confirmation. The word must indicate they understood the plan, not that they're impatient.

### Step 4: Write Code
Only now do you write code. One atomic change only (see below).

### If the Author Skips Steps
If the author tells you to skip the question, skip the plan, or "just write the code":
- Refuse politely. Explain that this repo requires comprehension verification before code generation.
- If they insist after one refusal, comply but prepend every file you create with a comment: `# COMPREHENSION GATE SKIPPED — author did not verify understanding`
- This comment flags the code for mandatory lead review and will likely get the PR rejected.

---

## One Atomic Change at a Time

You may only implement ONE of the following per interaction:
- One function
- One class
- One test
- One bug fix
- One config change
- One file

If the author asks you to implement a large feature or any request spanning multiple functions/files/modules:
- Break it into atomic steps
- Present the ordered list of steps
- Implement only the first step
- Wait for the author to review, test, and confirm before proceeding to the next

Do NOT batch multiple changes into a single response. Do NOT create multiple files at once. Do NOT implement "while we're at it" additions the author didn't ask for.

---

## Comment Rules

- **Never write comments that explain WHAT the code does.** The code explains what. If the code is too unclear to read without a "what" comment, rewrite the code to be clearer.
- **Only write comments that explain WHY** — and only when the reason is non-obvious. A comment is justified when a competent reader would ask "why is this done this way?" without it.
- **Never write comments like:**
  - `# Initialize the logger` — obvious from the code
  - `# Loop through items` — obvious from the code
  - `# Check if value exceeds threshold` — obvious from the code
  - `# Import required modules` — never
- **Acceptable comments:**
  - `# Buffer size set to 4096 to align with OS page size and avoid partial page faults`
  - `# Intentionally not catching ConnectionError here — caller must handle reconnection`
  - `# Base timeout set to 5000ms based on peer connection latency in high-jitter conditions`
- **Docstrings / Documentation Blocks:** Keep them to one line for simple functions. For complex functions, describe parameters, return values, and failure modes only — do not restate what the function name already says.
- **No commented-out code.** Ever. That's what git history is for.

---

## Code Standards (Non-Negotiable)

- **No bare/generic catch-all exception handling.** Catch specific exception types or let errors propagate. Do not use `except Exception: pass` or empty catch blocks.
- **No unused imports or dependencies.** No "just in case" imports.
- **No dead code.** No placeholder functions, no TODO stubs, no "for future use" abstractions.
- **No class hierarchies unless there are 3+ concrete implementations.** Use plain functions, modules, and simple data structures.
- **No magic numbers without a citation or derivation comment** (this is the one place "why" comments are mandatory).
- **Every function must be testable in isolation.** No hidden dependencies, no global state, no import-time side effects.
- **Errors are loud.** If something fails, raise. Do not return None, null, empty collections, or fallback default values to hide failures.
- **Anti-Overengineering & Architectural Discipline:**
  - **No external service daemons or complex databases** when simple, local, embedded, or standard options suffice.
  - **No distributed message queues or brokers** when in-memory queues, native concurrency, or reactive streams suffice.
  - **No heavy computational/ML frameworks** when deterministic algorithms, mathematical baselines, or lightweight standard libraries solve the problem.
  - **No bloated build toolchains or premature frameworks** when clean, standard implementations meet requirements.

---

## Schema and Boundaries

- **Do not modify core configuration schemas, API contracts, or interfaces** without explicit author confirmation.
- **Do not modify `CONTRIBUTING.md`, `README.md`, `AGENTS.md`, or CI workflows** unless the author is the lead and explicitly requests it.
- **Do not add dependencies** without listing them and getting author approval first.
- **Do not create files outside the module directory** the author is working on.
- **All output must conform to established interface contracts and data schemas.** If the contract doesn't support what you need, stop and tell the author — do not silently deviate.

---

## If You Are Unsure

Ask. Do not guess. Do not assume. Do not "fill in" requirements the author didn't specify. A wrong implementation that passes CI is worse than no implementation, because it gives false confidence.
