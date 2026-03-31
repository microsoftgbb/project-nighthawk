---
name: nighthawk-report-templates
description: Markdown report templates for all three Nighthawk question types (architecture, bug, guidance). Use this skill when writing or evaluating a Nighthawk report.
---

# Nighthawk Report Templates

Three templates — one per question type. Pick the one matching the `QUESTION_TYPE` from the Classifier output.

## Writing Principles (all templates)

- **Lead with the answer** — TL;DR first, always. If you can't state the answer in 1-2 sentences, you don't understand it yet.
- **Cite code, not concepts** — File paths with line numbers (e.g., `ARO-RP/pkg/util/azurezones/azurezones.go#L85-L100`) over hand-waving.
- **Be direct and assertive** — State facts, not possibilities. "ARO enforces 3 AZs" not "ARO may require multiple AZs".
- **End with action** — Every report must tell the reader what to do next.
- **No filler** — Skip "In this document we will explore..." Just start.

---

## Template: `architecture`

Use when the user wants to understand how something works.

```markdown
# [Descriptive Title]

**Date**: YYYY-MM-DD
**Service**: AKS | ARO
**Topic**: [Key technical area]

## TL;DR

[1-2 sentence direct answer. No hedging.]

## Technical Details

[Main content organized by concept. Use subheadings.]

**Architecture**: [How components relate]
**Implementation**: [What the code actually does, with file paths and line numbers]
**Configuration**: [Relevant settings, API versions, CLI flags]

[Mermaid diagram if it clarifies architecture or flow]

## Code References

- [`repo/path/file.go#L123-L145`](https://github.com/org/repo/blob/main/path/file.go#L123-L145) — [what this code does]
- ...

## Next Steps

- [Actionable items — what should the reader do with this information?]

## References

### GitHub Source Code
- [Repo/File](URL) — Description

### Microsoft Learn
- [Article](URL) — Description
```

---

## Template: `bug`

Use when the user is reporting or investigating an issue.

```markdown
# [Descriptive Title — include "Bug" or "Issue"]

**Date**: YYYY-MM-DD
**Service**: AKS | ARO
**Topic**: [Key technical area]

## TL;DR

[1-2 sentence summary of the issue and its status/impact.]

## Summary

[What's happening, who reported it, what's affected.]

## Environment

- **Version**: [AKS/ARO version]
- **Configuration**: [Relevant cluster settings]

## Steps to Reproduce

1. [Step]
2. [Step]
3. ...

## Expected Behavior

[What should happen]

## Actual Behavior

[What actually happens. Include error messages verbatim.]

## Root Cause

[Technical explanation with code references. File paths and line numbers.]

## Workaround

[If any. Be specific about steps. State "None identified" if there isn't one.]

## Suggested Fix

[What should change in the code/service to resolve this.]

## Code References

- [`repo/path/file.go#L123-L145`](URL) — [what this code does]

## References

### GitHub Source Code
- [Repo/File](URL) — Description

### Microsoft Learn
- [Article](URL) — Description
```

---

## Template: `guidance`

Use when the user wants recommendations or best practices.

```markdown
# [Descriptive Title]

**Date**: YYYY-MM-DD
**Service**: AKS | ARO
**Topic**: [Key technical area]

## TL;DR

[1-2 sentence direct recommendation.]

## Recommendations

### [Topic Area 1]

[Prescriptive guidance with links to official docs, reference architectures, repos.]

### [Topic Area 2]

[Continue with clear sections per topic.]

[Mermaid diagram if it clarifies decision flow or architecture]

## Tools & Resources

- [Tool/Resource](URL) — [What it does and why it matters]
- ...

## Next Steps

- [Specific actions the reader should take]

## References

### Microsoft Learn
- [Article](URL) — Description

### GitHub
- [Repo/Resource](URL) — Description
```

---

## Mermaid Diagram Guidelines

Include Mermaid diagrams when they genuinely clarify something — architecture relationships, process flows, decision trees. Skip them when the text is already clear.

Keep diagrams:
- **Simple**: 5-10 nodes maximum
- **Focused**: One concept per diagram
- **Labeled**: Clear node names and relationship descriptions

---

## File Naming

```
notes/Nighthawk-YYYY-MM-DD-<topic>.md
```

- `YYYY-MM-DD` — today's date
- `<topic>` — kebab-case description (e.g., `AKS-KMS-Encryption`, `ARO-Storage-Accounts`, `ARO-SKU-Discrepancy`)
