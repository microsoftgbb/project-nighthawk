# Nighthawk Agents

This workspace provides specialized agents for researching and documenting Azure Kubernetes Service (AKS) and Azure Red Hat OpenShift (ARO).

## Available Agents

### Nighthawk Orchestrator
Main entry point for generating comprehensive, fact-checked technical reports. Coordinates the full research workflow through classification, research, synthesis, and validation.

**Usage:** `/Nighthawk <your question about AKS or ARO>`

### @Nighthawk-Classifier
Classifies questions to determine the Azure service (AKS vs ARO) and extracts keywords for targeted research.

### @Nighthawk-AKS-Researcher
Deep technical researcher for Azure Kubernetes Service. Analyzes GitHub repositories and Microsoft Learn documentation.

### @Nighthawk-ARO-Researcher
Deep technical researcher for Azure Red Hat OpenShift. Analyzes GitHub repositories and Microsoft Learn documentation.

### @Nighthawk-Synthesizer
Transforms research findings into comprehensive, well-structured technical reports with diagrams and source citations.

### @Nighthawk-FactChecker
Validates synthesized reports against source materials to ensure factual accuracy and identify unsupported claims.

## Recommended Usage

For most use cases, use the orchestrator:

```
/Nighthawk How does AKS handle node upgrades?
```

The orchestrator will automatically route through all stages and deliver a validated report.
