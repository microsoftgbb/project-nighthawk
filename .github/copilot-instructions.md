# Nighthawk Workspace Instructions

When working in this repository, you are helping to create deep technical notes about Azure Services and related technologies.

## Using Nighthawk

**For solution engineers**: To generate a comprehensive, fact-checked technical report, use:

```
/Nighthawk <your question about AKS or ARO>
```

The orchestrator will automatically:
1. Classify your question (AKS vs ARO)
2. Research official GitHub repositories and Microsoft Learn
3. Synthesize findings into a comprehensive report
4. Fact-check all claims against sources
5. Deliver a validated report with source citations

**Individual agents** (for advanced usage):
- `@Nighthawk-Classifier` - Classify a question
- `@Nighthawk-AKS-Researcher` - Deep research on AKS
- `@Nighthawk-ARO-Researcher` - Deep research on ARO
- `@Nighthawk-Synthesizer` - Create report from research
- `@Nighthawk-FactChecker` - Validate a report

## Note Format

Your findings should be written in markdown format with relevant headings and subheadings. Leverage Mermaid diagrams where appropriate to illustrate complex concepts.

Write the notes in a way that is easy to understand for someone new to the topic, while still providing deep technical insights for experienced users.

## File Naming and Location

Save your notes in a file named `Nighthawk-$todaysDate-$subject.md` in the `notes/` directory, where `$todaysDate` is the current date and `$subject` is the topic/theme of the note. Update the notes as you discover new information, or when asked to add additional results to the topic.

Ensure to include references and links to the source materials you used to compile the notes.

## Skills

The following skills are available to Nighthawk researcher agents:

- **nighthawk-local-repos** — Required tool checklist, local repo paths, clone commands, and mandatory `git pull` procedure. Researcher agents must read this skill before every research run.
  - File: `.github/skills/Nighthawk-LocalRepos/SKILL.md`
- **nighthawk-report-templates** — Writing principles, three report templates (architecture / bug / guidance), Mermaid diagram guidelines, and file naming conventions. Synthesizer must read this skill before writing any report.
  - File: `.github/skills/Nighthawk-ReportTemplates/SKILL.md`
