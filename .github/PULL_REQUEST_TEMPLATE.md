## Pull Request: Module Submission

> **Before filling this out:** Read [CONTRIBUTING.md](../CONTRIBUTING.md) §4. If your PR description is mostly placeholder text from this template, it will be sent back.

---

### 1. What This Module Does (Your Own Words)
<!-- 
Write 3-5 sentences in YOUR words — not the AI's explanation, not a copy of the prompt.
If you can't describe what your code does without looking at it, you're not ready to submit.
-->



### 2. Algorithm / Method & Why You Chose It
<!--
Name the specific algorithm, approach, or design pattern. Explain why this approach over alternatives.
-->



### 3. What This Does NOT Handle (Required — Not Optional)
<!--
Every module has limits and edge cases. Name them honestly. This is a credibility signal, not a weakness.
-->



### 4. Evidence of Testing Beyond CI
<!--
Paste actual terminal output or describe specific test scenarios you ran manually.
"CI passed" is not enough. Show that you ran it on realistic inputs and inspected the output.
-->

```
# paste terminal output or test execution logs here
```

### 5. What You'd Do Differently With More Time
<!-- One or two sentences. Shows critical thinking about your own work. -->



---

### 6. Definition of Done Checklist

- [ ] **I can explain every line of this code** without looking at the AI prompt that generated it.
- [ ] **Contract & schema compliance:** Output validated against expected interfaces/contracts. Tested with both valid and malformed inputs.
- [ ] **No hardcoded secrets/keys/tokens.** All config via environment variables / `.env.example`.
- [ ] **No dead code:** No unused imports, no commented-out blocks, no placeholder functions.
- [ ] **No bare/generic catch-all exceptions:** All exception handling catches specific types or has explicit recovery logic.
- [ ] **Every threshold is documented:** Each magic number has a comment citing its source or derivation rationale.
- [ ] **Tested on realistic inputs:** Exercised against realistic data/scenarios (not just mock data constructed to pass assertions).
- [ ] **Tested edge cases:** Empty input, maximum payload/volume, malformed input, boundary states.
- [ ] **CI passed:** Linting and automated test suite.

---

### 7. Teach-Back Verification

**Teammate who received teach-back:** @<!-- github handle -->

**Teach-back confirmation** (the reviewer fills this in — not the author):
<!--
Don't just write "confirmed." Be specific. Example:
"Teach-back done. @username explained the module implementation to me. They correctly described 
the state management data structure, configuration parameters, and what happens on empty or malformed input. 
Known limitations are documented."
-->



---

### 8. Dependency Changes (if any)
<!-- List each new dependency with a one-line justification. No new deps = write "None." -->

| Package | Version | Why It's Needed |
|---|---|---|
|  |  |  |

---

### 9. Lead Review
- [ ] Approach is architecturally sound and integrates with upstream/downstream modules.
- [ ] Author demonstrated understanding during teach-back (cross-referenced with §7).
- [ ] Failure modes are acceptable and documented.
- [ ] Simplest viable approach — no over-engineering.
