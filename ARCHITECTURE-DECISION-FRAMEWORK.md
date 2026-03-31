# AI Agent Architecture Decision Framework

This document provides the decision framework used to select the orchestration architecture for Nighthawk.

Nighthawk implements the **Agent Handoff Pattern** as described in [Azure Architecture Center - AI Agent Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns#agent-handoff-pattern-example), where specialized agents complete distinct tasks and pass results to the next agent through well-defined contracts.

## The Four Agent Architectures

### 1. Coding Harness
**Problem Type:** Software-shaped (build/modify code)
**Quality Gate:** Human judgment
**Pattern:** Single agent with code tools + human review loop
**Examples:** Claude Code, Cursor, GitHub Copilot
**Governing Principle:** "Decompose on boundaries of isolation"
**Key Precondition:** Human must review outputs

### 2. Dark Factory
**Problem Type:** Software-shaped (build/modify code)
**Quality Gate:** Automated validation (tests, specs)
**Pattern:** Agent generates code → tests validate → no human in loop
**Examples:** Test-driven code generation, CI/CD with auto-validation
**Governing Principle:** "The specification is the product"
**Key Precondition:** Automated test suite must be comprehensive

### 3. Auto Research
**Problem Type:** Metric-shaped (improve existing system)
**Quality Gate:** Computable scoring function
**Pattern:** Agent proposes changes → system measures improvement → agent iterates
**Examples:** Hyperparameter tuning, A/B test optimization
**Governing Principle:** "Metric plus guardrail"
**Key Precondition:** Clear metric that measures "better"

### 4. Orchestration Framework
**Problem Type:** Workflow-shaped (multi-step pipeline)
**Quality Gate:** Staged validation (different capabilities per stage)
**Pattern:** Specialized agents → handoffs → quality gates between stages
**Examples:** CrewAI, LangGraph, AutoGen, multi-agent research pipelines
**Governing Principle:** "Design the handoffs first"
**Key Precondition:** Stages have distinct capabilities/responsibilities

---

## Decision Logic

Use this flowchart to select the right architecture:

```
Is the problem SOFTWARE-SHAPED?
(Build or modify code as the output)
│
├─ YES → Is the quality gate HUMAN JUDGMENT?
│        │
│        ├─ YES → CODING HARNESS
│        │        Single agent + tools + human review
│        │
│        └─ NO → Is validation AUTOMATED?
│                 │
│                 └─ YES → DARK FACTORY
│                          Agent + tests, no human loop
│
├─ NO → Is the problem METRIC-SHAPED?
│       (Improve measurable system performance)
│       │
│       ├─ YES → Is there a COMPUTABLE SCORE?
│       │        │
│       │        └─ YES → AUTO RESEARCH
│       │                 Agent proposes → measure → iterate
│       │
│       └─ NO → Is the problem WORKFLOW-SHAPED?
│               (Multi-step pipeline, different needs per stage)
│               │
│               └─ YES → ORCHESTRATION FRAMEWORK
│                        Specialized agents + handoffs
```

---

## The One-Question Test

**"What are you optimizing against?"**

Your answer reveals the architecture:

| Answer | Architecture |
|--------|--------------|
| "Human says it's good code" | Coding Harness |
| "Tests pass automatically" | Dark Factory |
| "Metric improves (latency, accuracy, etc.)" | Auto Research |
| "Each stage completes its contract successfully" | Orchestration |

---

## Nighthawk Decision Walkthrough

### Step 1: Problem Classification

**Question:** What does the end result look like when it's working?

**Answer:** A comprehensive, fact-checked markdown report that solution engineers can share.

**Analysis:** This is NOT software (no code to build). It's a multi-step information pipeline:
1. Classify question
2. Research sources
3. Write report
4. Validate claims

**Conclusion:** WORKFLOW-SHAPED ✓

---

### Step 2: Quality Gate

**Question:** How do you know if the output is good?

**Answer:** A second LLM validates all claims against sources + human can review.

**Analysis:** 
- NOT purely human judgment (there's automated validation)
- NOT purely automated tests (humans review after fact-check)
- YES staged validation (fact-checker validates synthesizer output)

**Conclusion:** STAGED VALIDATION ✓

---

### Step 3: Stage Capabilities

**Question:** Do different stages need different capabilities?

**Answer:** Yes:
- Classifier: Pattern matching, no research needed
- Researcher: Full access (web, repos, MCP)
- Synthesizer: Writing, but no new research
- FactChecker: Verification, but no editing

**Analysis:** Stages have distinct responsibilities and tool requirements.

**Conclusion:** DISTINCT CAPABILITIES PER STAGE ✓

---

### Step 4: Architecture Selection

**Problem Type:** Workflow-shaped ✓  
**Quality Gate:** Staged validation ✓  
**Stage Capabilities:** Distinct ✓

**Selected Architecture:** ORCHESTRATION FRAMEWORK

---

## Why Other Architectures Don't Fit

### ❌ Coding Harness

**Why we considered it:** Original implementation was a single agent with tools.

**Why it doesn't fit:**
- Problem is not software-shaped (no code to build)
- Workflow has multiple stages with different needs
- Mixing research and synthesis in one agent → hallucination
- No automated validation step

**Failure mode:** Agent writes plausible-sounding reports with unverified claims. Human must fact-check everything manually.

---

### ❌ Dark Factory

**Why we considered it:** We have automated validation (fact-checker).

**Why it doesn't fit:**
- Problem is not software-shaped (no code, no tests)
- Humans DO review outputs (not fully automated loop)
- Validation is qualitative, not pass/fail tests
- Need staged pipeline, not generate-validate-loop

**Failure mode:** Can't fully automate quality gate—reports need human domain expertise to assess value.

---

### ❌ Auto Research

**Why we considered it:** We're doing "research".

**Why it doesn't fit:**
- Not optimizing a metric (e.g., accuracy score)
- Not improving an existing system iteratively
- Output is a one-time report, not system tuning
- No clear "better" metric to maximize

**Failure mode:** No computable score to guide iteration. Can't A/B test "better research."

---

## Key Precondition that Must Be True

For orchestration to work, Nighthawk requires:

**"The fact-checker must have structured validation criteria."**

Specifically:
- ✅ Does claim X appear in source Y?
- ✅ Is this file path accurate?
- ✅ Do line numbers point to relevant code?
- ✅ Are API versions correct?

**Not sufficient:**
- ❌ "Review this report for accuracy" (too vague)
- ❌ "Does this sound right?" (subjective)
- ❌ Rubber-stamp approval

**Why this matters:** Without clear validation logic, the fact-checker becomes a pass-through, eliminating the quality gate that justifies the orchestration architecture.

---

## Common Mismatches (Warning Signs)

### Sign 1: Prompt grows to hundreds of lines
**What's happening:** You're encoding workflow in prompts.  
**Fix:** Break into orchestrated stages with clear handoffs.

### Sign 2: Agent sometimes researches, sometimes synthesizes
**What's happening:** Mixing concerns in one agent.  
**Fix:** Separate research agent from synthesis agent.

### Sign 3: You catch hallucinations in output
**What's happening:** No validation gate.  
**Fix:** Add fact-checker agent that validates before delivery.

### Sign 4: "Just make it more careful" doesn't help
**What's happening:** Wrong architecture, not wrong prompt.  
**Fix:** Reassess architecture selection using decision logic above.

---

## Decision Table Reference

| Architecture | Problem Shape | Quality Gate | Tool Access | Human Role | Best For |
|--------------|---------------|--------------|-------------|------------|----------|
| **Coding Harness** | Software | Human judgment | All tools to one agent | Reviews every output | Most coding problems |
| **Dark Factory** | Software | Automated tests | Generate + validate | None (or exceptions only) | Spec-driven code gen |
| **Auto Research** | Metric | Computable score | Propose + measure | Sets constraints | Hyperparameter tuning |
| **Orchestration** | Workflow | Staged validation | Different per stage | Reviews final stage | Multi-step pipelines |

---

## Application to Nighthawk

| Aspect | Our Case |
|--------|----------|
| **Problem Shape** | Workflow (classify → research → synthesize → validate) |
| **Quality Gate** | FactChecker validates claims + human reviews |
| **Tool Access** | Researcher has web/MCP, Synthesizer doesn't, FactChecker has web/MCP but not edit |
| **Human Role** | Reviews fact-checked report, focuses on flagged items |
| **Architecture** | ✅ Orchestration Framework |

---

## Testing Your Architecture Choice

Ask these diagnostic questions:

### Question 1: Can one stage cheat?
**Nighthawk:**
- ❌ Synthesizer can't do web research (tool restriction)
- ❌ FactChecker can't modify report (no edit access)
- ✅ Stage boundaries enforced

**Test:** Stages can only use their designated tools.

### Question 2: Is quality automatic or human-dependent?
**Nighthawk:**
- ⚠️ Hybrid: FactChecker automates validation, human reviews result
- ✅ Fact-check summary helps human focus on issues

**Test:** If you remove the human, can the system still reject bad outputs? (Yes, via FactChecker NEEDS REVISION)

### Question 3: Could you test stages independently?
**Nighthawk:**
- ✅ Classifier: Give it questions, check classification
- ✅ Researcher: Give it topics, verify findings
- ✅ Synthesizer: Give it research, check report format
- ✅ FactChecker: Give it report, check validation logic

**Test:** Each agent can be invoked and tested alone.

### Question 4: Do handoffs have clear contracts?
**Nighthawk:**
- ✅ Classifier outputs: SERVICE, KEYWORDS, CONFIDENCE
- ✅ Researcher outputs: FINDINGS, SOURCES, RAW NOTES
- ✅ Synthesizer outputs: File path, claims list
- ✅ FactChecker outputs: Verification counts, recommendation

**Test:** Downstream agents receive structured data, not prose.

---

## When to Reconsider Architecture

Reconsider if:

1. **Stages start mixing again**
   - E.g., Synthesizer doing web research
   - Fix: Enforce tool restrictions

2. **Handoffs become fuzzy**
   - E.g., "Here's some stuff, figure it out"
   - Fix: Define structured output formats

3. **Quality gate becomes rubber stamp**
   - E.g., FactChecker always approves
   - Fix: Tighten validation criteria

4. **Human burden doesn't decrease**
   - E.g., Still manually checking everything
   - Fix: Improve FactChecker specificity

5. **Problem shape changes**
   - E.g., Now generating code, not reports
   - Fix: Reassess using decision logic

---

## References

- Decision framework based on "Four Agent Architectures" pattern
- The One-Question Test: "What are you optimizing against?"
- Governing principles from software architecture patterns
- Tool restriction pattern from principle of least privilege
- Quality gate pattern from staged deployment practices

---

**Last Updated:** March 27, 2026  
**Applied To:** Nighthawk orchestration architecture  
**Result:** ✅ Orchestration framework selected and implemented
