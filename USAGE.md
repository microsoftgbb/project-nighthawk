# Nighthawk Quick Start Guide

## For Solution Engineers

### Asking a Question

To generate a comprehensive, fact-checked report about AKS or ARO:

```
/Nighthawk How does AKS implement KMS encryption with customer-managed keys?
```

The system will automatically:
1. Classify your question (AKS or ARO)
2. Research GitHub repos and Microsoft Learn via MCP
3. Write a comprehensive markdown report
4. Fact-check all claims against sources
5. Deliver a validated report in `notes/`

### What You'll Get

- **Markdown file**: `notes/Nighthawk-2026-03-27-<topic>.md`
- **Fact-check summary**: Verified/unverified claim counts
- **Source citations**: Every claim linked to GitHub or Microsoft Learn
- **Mermaid diagrams**: Visual explanations where helpful
- **Red flags**: Any unverified claims highlighted for your review

### Example Questions

**AKS:**
- "How does AKS bootstrap Windows nodes?"
- "What's the difference between kubenet and Azure CNI in AKS?"
- "How does the cloud-controller-manager manage load balancers?"
- "Where is the VHD build process for AKS nodes?"

**ARO:**
- "How does ARO manage storage accounts in the managed resource group?"
- "What operators run in an ARO cluster?"
- "How does ARO implement cluster networking?"
- "What's stored in the ARO cluster storage account?"

## Installation

**Zero installation required.** If you have:
- VS Code with GitHub Copilot
- Access to this repository
- MCP (Model Context Protocol) enabled for GitHub access

You're ready to use Nighthawk.

## How It Works

Nighthawk uses **MCP (Model Context Protocol)** to access GitHub repositories directly:
- No local cloning required
- Direct search and file access via GitHub API
- Real-time access to latest code and documentation
- Faster queries without local repo management

## Reviewing Reports

Reports are comprehensive and include:

1. **Overview**: Quick answer to your question
2. **Technical Deep Dive**: Implementation details from source code
3. **Diagrams**: Architecture and flow visualizations
4. **Key Findings**: Summary of discoveries
5. **References**: All sources organized by type

Before sharing with customers:
- ✅ Check the fact-check summary
- ✅ Review any flagged unverified claims
- ✅ Validate technical details match your environment
- ✅ Add your own context if needed

## Advanced Usage

### Individual Agents

For specific tasks, call agents directly:

**Classify a question:**
```
@Nighthawk-Classifier Is this about AKS or ARO: "managed identity setup"
```

**Research only (no report):**
```
@Nighthawk-AKS-Researcher Find information about node image caching
```

**Fact-check an existing report:**
```
@Nighthawk-FactChecker Validate notes/Nighthawk-2026-03-27-kms-encryption.md
```

**Generate report from existing research:**
```
@Nighthawk-Synthesizer Create report from these findings: [paste research]
```

## Troubleshooting

### "I got UNKNOWN service classification"
→ Your question might be too general. Try adding "AKS" or "ARO" explicitly.

### "The report has many unverified claims"
→ This is the fact-checker working correctly. Review those sections carefully before sharing.

### "Query is taking a while"
→ Normal. Searching large repositories and fetching files via MCP can take time depending on query complexity.

### "I need to research a different Azure service"
→ Currently only AKS and ARO are supported. For other services, research manually or request addition.

## Tips for Best Results

✅ **Be specific**: "How does AKS handle node upgrades?" is better than "Tell me about AKS"

✅ **One topic per query**: Ask about specific features, not broad overviews

✅ **Include context**: Mention version numbers, features, or scenarios when relevant

✅ **Review outputs**: The fact-checker catches most issues, but domain expertise matters

✅ **Update reports**: If you find new information, you can ask the orchestrator to update an existing report

## Getting Help

- **Architecture details**: See `.github/agents/README.md`
- **Agent behavior**: Check individual agent files in `.github/agents/`
- **Issues**: File an issue in this repo
- **Questions**: Ask in your team channel

## What's Happening Behind the Scenes

When you use `/Nighthawk`:

```
User Question
    ↓
Classifier (determines AKS/ARO)
    ↓
Researcher (searches GitHub via MCP, queries Microsoft Learn)
    ↓
Synthesizer (writes markdown report)
    ↓
FactChecker (validates every claim)
    ↓
Final Report (with fact-check summary)
```

Each stage is a specialized agent with specific tools and responsibilities. No stage can "cheat" — the fact-checker validates everything.

## Examples of Generated Reports

Check `notes/` folder for examples:
- `Nighthawk-2025-12-18-AKS-KMS-CMK-APIServer-VNet-Integration.md`

These show the depth and structure you can expect.

---

**Ready to start?** Just type:
```
/Nighthawk <your question about AKS or ARO>
```
