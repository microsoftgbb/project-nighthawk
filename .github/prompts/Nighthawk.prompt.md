---
agent: Nighthawk-Orchestrator
description: "Nighthawk is a research assistant that generates for the Field Engineering team comprehensive, fact-checked technical reports about Azure Kubernetes Service (AKS) and Azure Red Hat OpenShift (ARO) by analyzing official GitHub repositories and Microsoft Learn documentation. Originally designed to assist Microsoft GBB Solution Engineers working on AKS and ARO engagements, Nighthawk can be used by anyone seeking deep technical insights about these platforms without needing to manually sift through source code and documentation."
---

Run the full GBB Notes research pipeline for the question below.

Execute all stages in order:
0. **Cleanup** — before starting, overwrite any leftover temp and staging files with empty content using the `edit` tool (write an empty string to each file). Files to clear:
   - `notes/.research-staging.md`
   - `notes/.factcheck-temp.md`
   This step is executed by the invoking agent (not the Orchestrator). Do not skip this step.
1. **Classify** — call `@Nighthawk-Classifier` to determine AKS vs ARO and extract keywords
2. **Repo Check** — before invoking any researcher, verify that all required local repo clones exist under `repos/` using `list_dir`. Required repos depend on the classified service:
   - **AKS**: `repos/AKS/`, `repos/azure-cli/`, `repos/azure-cli-extensions/`, `repos/AgentBaker/`, `repos/cloud-provider-azure/`
   - **ARO**: `repos/ARO-RP/`, `repos/azure-cli/`
   
   If **any** required repo is missing, **stop and tell the user** which repos are absent and provide the exact clone commands needed before proceeding:
   ```bash
   # Clone missing AKS repos (run from workspace root)
   mkdir -p repos
   git clone --depth=1 https://github.com/Azure/AKS.git repos/AKS
   git clone --depth=1 https://github.com/Azure/azure-cli.git repos/azure-cli
   git clone --depth=1 https://github.com/Azure/azure-cli-extensions.git repos/azure-cli-extensions
   git clone --depth=1 https://github.com/Azure/AgentBaker.git repos/AgentBaker
   git clone --depth=1 https://github.com/kubernetes-sigs/cloud-provider-azure.git repos/cloud-provider-azure

   # Clone missing ARO repos (run from workspace root)
   mkdir -p repos
   git clone --depth=1 https://github.com/Azure/ARO-RP.git repos/ARO-RP
   git clone --depth=1 https://github.com/Azure/azure-cli.git repos/azure-cli
   ```
   Once all repos are present, ask the user to run `git pull` on them to ensure they are up to date, then proceed.

3. **Research** — call the appropriate researcher (`@Nighthawk-AKS-Researcher` or `@Nighthawk-ARO-Researcher`) with the original question and keywords
4. **Synthesize** — call `@Nighthawk-Synthesizer` with the research findings to produce a markdown report saved to `notes/Nighthawk-YYYY-MM-DD-<topic>.md`
5. **Fact-check** — call `@Nighthawk-FactChecker` to validate all claims against sources
6. **Report** — present the final report path, fact-check summary (verified/unverified/warning counts), and any flagged issues

**Question:** ${input:question:What do you want to research about AKS or ARO?}
