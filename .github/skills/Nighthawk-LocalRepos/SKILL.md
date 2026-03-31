---
name: nighthawk-local-repos
description: Local repository setup, required tools, and mandatory git pull procedure for all Nighthawk researcher agents. Read this skill before starting any research run.
---

# Nighthawk Local Repository Setup

This skill covers tool requirements, local repo paths, initial clone commands, and the mandatory git pull step that every Nighthawk researcher agent must perform before starting research.

## Required Tools

| Tool | Purpose | Verify |
|------|---------|--------|
| `git` | Clone and update local repos | `git --version` |
| GitHub Copilot in VS Code | Agent runtime (`grep_search`, `file_search`, `read_file`, `semantic_search`, `edit`) | Active in workspace |
| Microsoft Learn MCP (`microsoft-learn/*`) | Official documentation, ARM template references | Available as deferred tool |
| GitHub MCP (`github/search_code` etc.) | Remote code search — fallback only if local clone is absent | Available as deferred tool |

## Local Repository Paths

All repos are cloned under `repos/` in the workspace root. **Always search local clones first** using `grep_search`, `file_search`, `read_file`, and `semantic_search`. Only fall back to GitHub MCP if the local clone is missing and cannot be created.

### ARO repos

| Repo | Local Path | Remote |
|------|-----------|--------|
| ARO-RP | `repos/ARO-RP/` | `https://github.com/Azure/ARO-RP` |
| azure-cli (ARO) | `repos/azure-cli/` | `https://github.com/Azure/azure-cli` |

### AKS repos

| Repo | Local Path | Remote |
|------|-----------|--------|
| AKS | `repos/AKS/` | `https://github.com/Azure/AKS` |
| AgentBaker | `repos/AgentBaker/` | `https://github.com/Azure/AgentBaker` |
| cloud-provider-azure | `repos/cloud-provider-azure/` | `https://github.com/kubernetes-sigs/cloud-provider-azure` |
| azure-cli (AKS) | `repos/azure-cli/` | `https://github.com/Azure/azure-cli` |
| azure-cli-extensions | `repos/azure-cli-extensions/` | `https://github.com/Azure/azure-cli-extensions` |

> `azure-cli` is shared — clone it once, it covers both `az aro` and `az aks`.

## Initial Clone (one-time setup)

If any repo is missing, clone it with `--depth=1` to keep it fast:

```bash
mkdir -p repos

# Shared
git clone --depth=1 https://github.com/Azure/azure-cli.git repos/azure-cli

# ARO
git clone --depth=1 https://github.com/Azure/ARO-RP.git repos/ARO-RP

# AKS
git clone --depth=1 https://github.com/Azure/AKS.git repos/AKS
git clone --depth=1 https://github.com/Azure/AgentBaker.git repos/AgentBaker
git clone --depth=1 https://github.com/kubernetes-sigs/cloud-provider-azure.git repos/cloud-provider-azure
git clone --depth=1 https://github.com/Azure/azure-cli-extensions.git repos/azure-cli-extensions
```

## Step 0: Pull Latest Code (mandatory before every research run)

Run `git pull` on the relevant repos in a terminal **before** starting a research session. This must be done manually by the user or via a terminal outside the agent:

```bash
# ARO research
git -C repos/ARO-RP pull --ff-only
git -C repos/azure-cli pull --ff-only

# AKS research
git -C repos/AKS pull --ff-only
git -C repos/azure-cli pull --ff-only
git -C repos/azure-cli-extensions pull --ff-only
# Priority 3 only:
git -C repos/AgentBaker pull --ff-only
git -C repos/cloud-provider-azure pull --ff-only
```

> The agent does not run terminal commands. Pull must be done before invoking a researcher agent.

## Search Priority

**RULE: Local repos before MCP — no exceptions for code.**

| Question type | Tool to use |
|--------------|-------------|
| Source code behavior, file paths, logic | `grep_search` / `read_file` against `repos/` |
| Official docs, how-to guides, ARM schema | `microsoft-learn/*` MCP tools |
| Commit history / blame not available locally | `github/list_commits` / `github/get_file_contents` (last resort) |
| Local clone missing and unrecoverable | GitHub MCP (last resort) |

> `repos/` is gitignored — always set `includeIgnoredFiles: true` when using `grep_search` against it.
