# Nighthawk Testing Checklist

Use this checklist to verify the orchestrated agent system works correctly.

## Phase 1: Individual Agent Testing

### Test 1.1: Classifier

**Command:**
```
@Nighthawk-Classifier How does AKS implement KMS encryption with customer-managed keys?
```

**Expected Output:**
```
SERVICE: AKS
CONFIDENCE: high
KEYWORDS: KMS encryption, customer-managed keys, Key Vault, encryption at rest, etcd
REASONING: Explicit AKS mention with specific security feature question.
```

**Pass Criteria:**
- [ ] Correctly identifies service as AKS
- [ ] Confidence is high
- [ ] Relevant keywords extracted
- [ ] Reasoning makes sense

---

### Test 1.2: Classifier (Ambiguous)

**Command:**
```
@Nighthawk-Classifier How are storage accounts used in managed resource groups?
```

**Expected Output:**
```
SERVICE: ARO (or UNKNOWN with medium confidence)
CONFIDENCE: medium
KEYWORDS: storage accounts, managed resource group, cluster storage
REASONING: Managed resource group with storage accounts is ARO-specific pattern.
```

**Pass Criteria:**
- [ ] Recognizes potential ambiguity
- [ ] Provides reasoning
- [ ] Keywords are relevant

---

### Test 1.3: AKS Researcher

**Command:**
```
@Nighthawk-AKS-Researcher Research node bootstrapping process in AKS
```

**Expected Behavior:**
- [ ] Clones/updates AKS repos to `docs/` directory
- [ ] Searches AgentBaker for bootstrapping logic
- [ ] Returns structured findings with sources
- [ ] Citations include GitHub file paths
- [ ] No synthesis or report writing (just research)

**Verify:**
```bash
ls docs/
# Should see: AKS, AgentBaker, cloud-provider-azure, azure-cli, azure-cli-extensions
```

---

### Test 1.4: ARO Researcher

**Command:**
```
@Nighthawk-ARO-Researcher Research storage accounts in ARO managed resource groups
```

**Expected Behavior:**
- [ ] Clones/updates ARO repos to `docs/` directory
- [ ] Searches ARO-RP for storage account logic
- [ ] Returns structured findings with sources
- [ ] Citations include GitHub file paths
- [ ] References known patterns (cluster storage, image registry)

**Verify:**
```bash
ls docs/
# Should see: ARO-RP, azure-cli
```

---

### Test 1.5: Synthesizer (Manual Input)

**Command:**
```
@Nighthawk-Synthesizer Create a report from these findings: [paste sample research output]
```

**Expected Behavior:**
- [ ] Creates markdown file in `notes/` directory
- [ ] Includes mermaid diagrams if appropriate
- [ ] Cites all sources from research
- [ ] Does NOT add new research or web lookups
- [ ] Returns synthesis summary

**Pass Criteria:**
- [ ] File created: `notes/Nighthawk-YYYY-MM-DD-<topic>.md`
- [ ] All sections present (overview, deep dive, findings, references)
- [ ] Sources properly cited

---

### Test 1.6: FactChecker

**Command:**
```
@Nighthawk-FactChecker Validate the report: notes/Nighthawk-YYYY-MM-DD-<topic>.md
```

**Expected Behavior:**
- [ ] Reads the report file
- [ ] Extracts technical claims
- [ ] Verifies each claim against cited sources
- [ ] Checks GitHub links (file paths, line numbers)
- [ ] Returns validation summary with categories

**Pass Criteria:**
- [ ] Validation summary with counts
- [ ] Per-claim analysis (✓/⚠/✗/❌)
- [ ] Recommendation (APPROVE/WARNINGS/NEEDS REVISION)
- [ ] Specific corrections if claims unverified

---

## Phase 2: End-to-End Orchestration Testing

### Test 2.1: Simple AKS Question

**Command:**
```
/Nighthawk How does AKS use AgentBaker for node provisioning?
```

**Expected Workflow:**
1. Orchestrator calls Classifier → Returns "AKS"
2. Orchestrator calls AKS-Researcher → Returns findings
3. Orchestrator calls Synthesizer → Creates report file
4. Orchestrator calls FactChecker → Validates report
5. Orchestrator presents result to user

**Pass Criteria:**
- [ ] Complete workflow executes without errors
- [ ] Report file created in `notes/`
- [ ] Fact-check summary presented
- [ ] All verified claims or warnings noted
- [ ] Total time < 5 minutes (first run may be slower for cloning)

---

### Test 2.2: Simple ARO Question

**Command:**
```
/Nighthawk What operators run in an ARO cluster?
```

**Expected Workflow:**
1. Classifier → Returns "ARO"
2. ARO-Researcher → Searches ARO-RP for operator info
3. Synthesizer → Creates report
4. FactChecker → Validates
5. Final report delivered

**Pass Criteria:**
- [ ] Correctly routed to ARO researcher
- [ ] Report includes operator information from ARO-RP
- [ ] Sources cite pkg/operator/ directory
- [ ] Fact-check validates claims

---

### Test 2.3: Ambiguous Question

**Command:**
```
/Nighthawk Tell me about Kubernetes networking
```

**Expected Behavior:**
- [ ] Classifier returns UNKNOWN or low confidence
- [ ] Orchestrator asks user to clarify (AKS or ARO?)
- [ ] Workflow does not proceed until clarification

**Pass Criteria:**
- [ ] User asked for clarification
- [ ] No attempt to research without clear classification

---

### Test 2.4: Complex Technical Question

**Command:**
```
/Nighthawk How does AKS implement customer-managed keys for KMS encryption and what components are involved?
```

**Expected Workflow:**
1. Classifier → AKS, keywords: KMS, CMK, Key Vault, encryption
2. AKS-Researcher → Searches multiple repos (AgentBaker, cloud-provider-azure)
3. Synthesizer → Creates comprehensive report with architecture diagram
4. FactChecker → Validates all technical claims

**Pass Criteria:**
- [ ] Report covers multiple components (AgentBaker, kube-apiserver, KMS plugin)
- [ ] Includes Mermaid diagram showing flow
- [ ] All claims verified with GitHub sources
- [ ] References both code and Microsoft Learn docs

---

## Phase 3: Quality Validation

### Test 3.1: Fact-Checking Catches Errors

**Setup:** Manually edit a report to add an incorrect claim

**Command:**
```
@Nighthawk-FactChecker Validate notes/Nighthawk-YYYY-MM-DD-<topic>.md
```

**Expected:**
- [ ] FactChecker flags the incorrect claim as ❌ Incorrect
- [ ] Provides evidence of contradiction
- [ ] Recommendation: NEEDS REVISION

---

### Test 3.2: Source Link Validation

**Setup:** Report with GitHub links

**Expected:**
- [ ] FactChecker verifies file paths exist
- [ ] Checks line numbers point to relevant code
- [ ] Flags broken or incorrect links

---

### Test 3.3: No Hallucination

**Command:**
```
/Nighthawk <Question about obscure feature not well documented>
```

**Expected:**
- [ ] Report states "information not found" rather than making things up
- [ ] FactChecker marks unsupported claims as ✗ Unverified
- [ ] User warned about unverified content

---

## Phase 4: Performance & Reliability

### Test 4.1: Repository Caching

**First run:**
- [ ] Repos cloned (slower, 1-2 minutes)

**Second run (same question):**
- [ ] Repos already exist (much faster, < 1 minute)

---

### Test 4.2: Concurrent Usage

**Command:** Two engineers ask questions simultaneously

**Expected:**
- [ ] Both workflows complete successfully
- [ ] No file conflicts
- [ ] Separate report files created

---

### Test 4.3: Error Recovery

**Scenario:** GitHub is down or MCP unavailable

**Expected:**
- [ ] Graceful error message
- [ ] Clear indication of what failed
- [ ] Suggestion to retry or use cached data

---

## Phase 5: User Experience

### Test 5.1: Solution Engineer First Use

**Persona:** SE with no knowledge of the system

**Task:** Ask a question about AKS

**Pass Criteria:**
- [ ] Clear instructions available (USAGE.md)
- [ ] Simple command: `/Nighthawk <question>`
- [ ] No installation steps required
- [ ] Report is comprehensive and understandable
- [ ] Fact-check summary increases confidence
- [ ] Sources allow verification

---

### Test 5.2: Report Sharing

**Task:** Generate report and share with customer

**Pass Criteria:**
- [ ] Report is markdown (easy to convert/share)
- [ ] Diagrams render correctly
- [ ] Sources are linkable
- [ ] Professional formatting
- [ ] Fact-check summary gives confidence

---

## Issues & Notes

Document any issues encountered:

**Issue 1:**
- [ ] Describe issue
- [ ] Steps to reproduce
- [ ] Expected vs actual behavior
- [ ] Proposed fix

**Issue 2:**
- [ ] ...

---

## Sign-Off

### Individual Agents
- [ ] Classifier tested and working
- [ ] AKS-Researcher tested and working
- [ ] ARO-Researcher tested and working
- [ ] Synthesizer tested and working
- [ ] FactChecker tested and working

### Orchestration
- [ ] End-to-end workflow tested
- [ ] AKS questions working
- [ ] ARO questions working
- [ ] Error handling working

### Quality
- [ ] Fact-checking catches errors
- [ ] No hallucination observed
- [ ] Source links validated

### User Experience
- [ ] Documentation clear
- [ ] Easy to use
- [ ] Reports are valuable

**Date Tested:** _____________

**Tester:** _____________

**Status:** ⬜ PASS | ⬜ NEEDS WORK | ⬜ FAIL

**Notes:**
