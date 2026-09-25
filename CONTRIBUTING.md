# Team Contribution Guide & Branching Policy

Welcome to the repository. This guide sets the branch conventions, development workflow, and PR gate requirements for this project.

**Read the whole thing. It exists because AI-generated code is fast to produce and slow to debug when nobody understands it.**

---

## 1. Branch Naming Conventions

All development occurs on dedicated feature/task branches. Direct pushes to `main` are blocked. Squash merge only.

### Branch Types
| Branch Prefix | Purpose / Scope |
|---|---|
| `feat/<feature-name>` | New feature implementation or core functionality |
| `fix/<issue-name>` | Bug fixes for logic, data handling, or runtime issues |
| `docs/<topic>` | Documentation, architecture overviews, guides, diagrams |
| `test/<scenario>` | Test suites, test fixtures, integration scenarios, benchmarks |
| `refactor/<module>` | Refactoring existing code without changing external behavior |
| `chore/<task>` | Build tooling, dependency management, repository maintenance |

---

## 2. Code Quality Standards — What We Actually Mean

Passing CI and ticking checkboxes is the floor, not the ceiling. Code that technically works but is fragile, unreadable, or cargo-culted from AI output without the author understanding it **will be rejected.** This section defines what "quality" means concretely.

### 2.1 You Must Understand What You Submit

If you used AI to generate code, you are still the author. The standard is:

- **You can explain every line** — not "the AI put it there," but *why* this approach, *why* this data structure, *why* this threshold value.
- **You can predict what happens when inputs change** — what if the input is empty? What if the volume increases 100x? What if malformed data arrives?
- **You can name what your code does NOT handle** — and you documented that explicitly in comments or the PR description.
- **You can explain why you didn't do the simpler/alternative thing** — you need to have a defensible technical answer.

The teach-back (Section 5) is where this gets tested. It is not a formality.

### 2.2 Named Anti-Patterns That Will Get Your PR Rejected

These are the specific failure modes common in AI-assisted projects. If a reviewer spots any of these, the PR is sent back — no exceptions.

#### Cargo-Cult Code
- Code copied from AI output that the author can't explain.
- Imports that aren't used. Functions that are never called. Parameters that don't do anything.
- "Just in case" error handling that silently swallows failures (e.g., bare `except: pass` or empty catch blocks).

#### Fake Testing
- Tests that only validate hardcoded mock data you wrote yourself, without ever exercising real module logic.
- Tests where the assertion is trivially true by construction (e.g., asserting a field equals the literal value set two lines prior).
- "It passes CI" as a substitute for "I ran it on sample inputs and inspected the output."

#### Magic Numbers & Unexplained Thresholds
- `if score > 0.73:` — where did 0.73 come from? Is it from a standard specification? Did you benchmark it? On what dataset/scenario?
- Every threshold, buffer size, timeout, and scoring parameter must have a comment citing its source or explaining how it was derived.

#### Over-Engineering
- Adding abstractions "for future flexibility" that make the code harder to read now. Write code for today's requirements.
- Class hierarchies where a function would do. Factory patterns for two concrete types. Config systems for three variables.
- Wrapping everything in async/await or concurrency primitives when there's no I/O concurrency happening.

#### Decoupled Shells Without Real Data
- A beautiful UI or API shell that displays hardcoded or randomly generated data instead of real system state.
- "We'll wire it up later" — no. If the PR doesn't integrate with real state and data contracts from the underlying layer, it's not ready for review.

#### Silent Failure Modes
- Generic catch-all blocks (`catch (Exception e) {}` or `except Exception: pass`) around critical logic — this hides bugs, it doesn't handle errors.
- Functions that return `None`/`null` or empty collections on failure instead of raising, when the caller does not check for failure.
- Components that return empty results on malformed input instead of surfacing the problem.

### 2.3 What Good Code Looks Like Here

- **Functions do one thing.** A function should have a single clear responsibility, not mix processing, persistence, and presentation.
- **Errors are loud.** If data contracts fail, raise. If an unexpected failure occurs, raise. Let the caller decide what to do — don't hide it.
- **Thresholds and constants are documented.** Every magic number has a comment explaining the rationale or derivation.
- **Edge cases are named.** A comment noting limitations is worth more than code that silently produces garbage on edge cases.
- **The simplest approach that works is preferred.** A concise function with clear logic beats a complex class hierarchy that "could support future requirements."

### 2.4 Anti-Overengineering Guardrails (Strictly Forbidden Architectural Patterns)

To prevent runtime fragility and avoid unnecessary complexity:

1. **No Unnecessary External Server Daemons:** Favor local, embedded, or lightweight persistence options over heavy external database servers unless explicitly justified and approved.
2. **No Distributed Message Brokers:** Use native in-memory queues, streams, or standard language concurrency. Do NOT introduce external brokers (Kafka, RabbitMQ, Celery) unless high distributed scale is an explicit requirement.
3. **No Heavy Computational/ML Frameworks:** When deterministic mathematical baselines or lightweight standard libraries suffice, do NOT import multi-gigabyte heavy frameworks.
4. **No Heavy Frontend Build Toolchains:** Keep client interfaces lightweight and direct. Avoid multi-layered build systems, heavy bundling tools, and framework sprawl unless required.
5. **No Class Hierarchies for <3 Implementations:** Use plain functions, modules, and lightweight data structures. Do NOT create abstract base class trees for only two concrete implementations.

---

## 3. Antigravity / AI Codegen Ground Rules

These rules apply to **every team member** using any AI coding assistant.

### 3.1 Prompt Discipline
1. **One task per prompt** — one function, one module, one file. Never "build the whole system."
2. **Ask for the plan before the code** — have the AI explain its approach in ≤5 bullets first. Read that. If you don't understand the plan, you won't understand the code.
3. **Paste constraints & schemas into every relevant prompt** — context windows aren't reliable. Be explicit every time.
4. **Baseline method first** — establish a simple, verifiable baseline alongside any complex logic. If you can't explain why the baseline works, you can't explain why a complex approach is better.
5. **Specify what NOT to do** — "Do not add dependencies. Do not use async unless I/O bound. Do not create class hierarchies. Do not catch broad exceptions."

### 3.2 After AI Generates Code — Your Actual Job Starts
1. **Read every line.** If you can't explain a line, delete it or rewrite it until you can.
2. **Remove dead code.** AI loves generating "helpful" extras. Unused imports, commented-out alternatives, placeholder functions — delete them.
3. **Check the error handling.** AI defaults to generic catch-all blocks everywhere. Replace with specific exception types or remove them to let errors surface.
4. **Verify the logic/algorithm is correct, not just that it runs.** An implementation that produces output isn't necessarily correct. Verify calculations, state transitions, and boundary behavior.
5. **Run it on adversarial input, not just happy path.** What happens with empty input? All-zeros? Extremely large payloads? Malformed data?

### 3.3 Things AI Gets Wrong That You Must Catch
- **Hallucinated library APIs** — calls to functions that don't exist or have different signatures. Test it.
- **Statistically/logically wrong baselines** — formulas that look plausible but contain off-by-one errors, inverted conditions, or bad math.
- **Swallowed errors** — everything wrapped in catch-all try/except so it "never crashes" but also never reports problems.
- **Over-abstracted architecture** — factory patterns and abstract base classes for two simple implementations. Flatten it.
- **Confident but wrong comments** — authoritative-sounding comments or docstrings that contradict what the code actually does. Read the code, not the comments.

### 3.4 Reusable Prompt Template
```text
You are implementing ONE module: [module name], part of [system/feature].

Context: [paste the 2-3 relevant constraint lines / interface contract / data schema]

Task: implement [specific function].
Input: [describe].
Output must match this contract:
[paste interface / schema]

Constraints:
- Include a simple baseline implementation alongside any complex approach.
- Do not modify anything outside [target path].
- Do not add dependencies without listing them and asking me first.
- Do not use bare/generic catch-all exception blocks. Catch specific exceptions or let errors propagate.
- Do not create class hierarchies — plain functions and clean data structures only.
- Add a comment citing the source or derivation for every threshold or magic number.

Before writing code: explain your planned approach in 5 bullets max and ask ONE comprehension question testing my understanding. Wait for my go-ahead.
```

---

## 4. PR Requirements — Beyond the Checklist

The PR template has checkboxes. Checkboxes are the minimum. This section explains what the reviewer is actually evaluating.

### 4.1 Every PR Must Include (in the description, not just the diff)

1. **What this module does in your own words** — not a copy-paste of the AI prompt or the AI's explanation. Your words. 3-5 sentences.
2. **What algorithm / method you used and why** — name the specific method and explain why this approach over alternatives.
3. **What this does NOT handle** — every module has limits. Name them honestly. This is a strength, not a weakness — it shows you understand the problem.
4. **How you tested it beyond CI** — show the output. Paste terminal output, test results, or screenshots demonstrating real input processing.
5. **What you'd do differently with more time** — one sentence. Shows you're thinking critically, not just shipping.

### 4.2 What Gets a PR Sent Back

- **Empty or templated PR descriptions.** If the description is mostly the template's placeholder text, the PR is not ready.
- **"Works on my machine" with no evidence.** Show the output. Paste terminal output or test logs generated from real or simulated input.
- **Tests that don't test anything real.** If your test constructs mock data and then asserts trivial properties without exercising logic, that's not a test — it's a tautology.
- **Unexplained thresholds.** If a reviewer asks "why this threshold value?" and the answer is "the AI suggested it," the PR goes back.
- **Dead code, unused imports, commented-out blocks.** Clean it up before requesting review.
- **Broad exception handling.** Catch-all blocks with no re-raise or specific recovery logic.

### 4.3 The Reviewer's Job (Lead Review)

The reviewer is NOT re-reading every line for syntax — that's what linting and CI are for. The reviewer checks:

1. **Does the author understand what they submitted?** (Cross-referenced with teach-back.)
2. **Is the approach defensible under questioning?** Could anyone ask "why did you do X?" and get a real answer?
3. **Does it integrate correctly?** Does the output conform to the expected interface/contract? Does it consume the right input format?
4. **Are the failure modes acceptable?** What happens when this module receives garbage input? Is the failure mode loud (good) or silent (bad)?
5. **Is this the simplest approach that works?** If it can be done in 30 lines, why is it 150?

---

## 5. Teach-Back Protocol — The Real Quality Gate

The teach-back is where checkbox-gaming dies. It's an explanation that happens **before the PR is submitted for lead review.** It is not optional and it is not a formality.

### How It Works
1. **Author picks a teammate or peer reviewer.**
2. **No notes, no screen, no code visible.** The author explains from memory.
3. **The teammate asks at least 3 comprehension questions:**

#### Comprehension Questions (the teammate asks these)
- "Walk me through what happens when an input arrives at your module."
- "What data structure holds the intermediate state? Why that structure?"
- "Where does this threshold/configuration value come from? How would you adjust it?"
- "What happens if the input is empty? What happens under extreme volume or malformed inputs?"
- "What does your module NOT handle? What scenario would break it or bypass it?"
- "Explain the mathematical or logical derivation in your calculations."
- "Why is this the simplest viable approach?"

### Outcomes
- **Pass:** The author can answer clearly. Teammate writes confirmation in the PR: *"Teach-back done. [Author] explained [module] to me. They understand [specific component]. The approach is sound with known limitation [X] documented."*
- **Fail:** The author can't explain key parts. **The PR is not submitted.** The author goes back, re-reads the code, rewrites the parts they don't understand, and tries again. This is not punishment — this is the fastest path to actually learning what you built.

---

## 6. No Rogue Dependencies, No Rogue Changes

- **Do not add packages** without listing them in the PR description with a one-line justification for each.
- **Do not touch files outside your module's directory** without explicit approval.
- **Do not modify core contracts, schemas, or shared interfaces** without a team discussion.
- **Do not modify CI workflow files** without lead review.
- **Do not modify this file (`CONTRIBUTING.md`), `README.md`, or `AGENTS.md`** without lead review.

---

## 7. Commit Message Format

```
<type>(<scope>): <short description>

<optional body — what and why, not how>
```

Types: `feat`, `fix`, `test`, `docs`, `chore`, `refactor`
Scopes: subsystem or module name (e.g., `core`, `api`, `ui`, `sync`, `storage`)

Examples:
```
feat(sync): implement bounded queue for audio packet buffering
fix(api): correct boundary condition in message frame parsing
test(storage): add unit tests for concurrent state reconciliation
```
