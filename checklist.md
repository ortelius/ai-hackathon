# Ortelius AI Hackathon — Preparation & Execution Checklist

**Date:** October 22, 2026  
**Track:** SECURE — Trust, Safety & Compliance  
**Application:** Python / FastAPI

**Test Repositories:**
- `fastapi-vuln-demo` — Successful CVE remediation
- `fastapi-vuln-demo-unsafe` — Unsafe / unsuccessful CVE remediation

## 1. Define Test Cases

### Test Case 1 — Successful CVE Remediation

- [X] Select Python dependency with a known CVE
      
      claude-agent-sdk (METADATA)
- [X] Identify CVE ID, severity, and vulnerability description
      
      CVE-2026-96620 │ CRITICAL   
- [X] Identify vulnerable package version
      
      0.2.118           
- [X] Identify patched package version
      
      0.2.121 
- [X] Verify patched version is compatible with the application
      
      No coding changes needed
- [X] Define any required dependency or Python source code changes

      claude-agent-sdk needs to have dependency updated to 0.2.121 in requirements.txt
- [ ] Define automated tests to verify application functionality
- [ ] Define expected AI CLI behavior
- [ ] Define expected successful remediation criteria
- [ ] Document test case in `test-case-1.md`

**Expected Result:** AI CLI identifies the vulnerability, upgrades the dependency, makes any required compatible code changes, passes validation, and creates a GitHub Pull Request.

### Test Case 2 — Unsafe / Unsuccessful CVE Remediation

- [X] Select Python dependency with a known CVE

      h11
- [X] Identify CVE ID, severity, and vulnerability description

      CVE-2025-43859 │ CRITICAL
- [X] Identify vulnerable package version

      0.14.0
- [X] Identify patched package version

      0.16.0
- [X] Identify dependency conflicts or breaking API changes

      0.16.0
- [X] Define why the proposed upgrade is unsafe

      The conflict is caused by:
          The user requested h11==0.16.0
          uvicorn 0.54.0 depends on h11>=0.8
          httpcore 1.0.5 depends on h11<0.15 and >=0.13
- [ ] Define automated tests that expose the incompatibility
- [ ] Define expected AI CLI failure detection and explanation
- [ ] Define expected rejection or escalation criteria
- [ ] Document test case in `test-case-2.md`

**Expected Result:** AI CLI identifies or attempts the remediation, detects the incompatibility, rejects the unsafe change, and creates a GitHub Issue documenting the problem and recommended next steps.

## 2. GitHub Repository Setup

- [X] Create and configure `fastapi-vuln-demo`
- [X] Create and configure `fastapi-vuln-demo-unsafe`
- [X] Confirm Python version and dependency management (`requirements.txt`)
- [X] Implement the vulnerable dependency defined for each test case
- [X] Implement FastAPI application functionality needed for testing
- [X] Add Docker Build and Helm Release Workflows
- [X] Verify applications run with their original vulnerable dependencies
- [ ] Implement automated tests for each scenario
- [ ] Verify baseline tests pass
- [ ] Commit vulnerable baseline for each repository
- [ ] Configure GitHub authentication for AI CLI
- [ ] Verify permissions for branches, Pull Requests, and Issues

## 3. Ortelius Repository Onboarding

- [X] Onboard `fastapi-vuln-demo` into Ortelius
- [X] Onboard `fastapi-vuln-demo-unsafe` into Ortelius
- [X] Trigger release discovery and SBOM generation
- [X] Verify each repository has a release in Ortelius
- [X] Verify Python packages appear in release SBOMs
- [X] Verify expected CVEs are associated with each release
- [ ] Record Ortelius release identifiers for both test cases

## 4. Ortelius API / MCP Dependencies

- [X] Identify or implement Ortelius API endpoint to retrieve vulnerabilities for a release

```bash
curl -s -X POST "https://app.deployhub.com/api/v1/graphql" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "query($name: String!, $version: String!) { release(name: $name, version: $version) { name version vulnerabilities { cve_id package affected_version full_purl fixed_in severity_rating severity_score } } }",
    "variables": { "name": "ortelius/frontend", "version": "12.0.1652-g333bbf" }
  }' \
  | jq '.data.release.vulnerabilities[]
        | select((.severity_rating | ascii_downcase) == "critical")
        | {cve_id, package, affected_version, fixed_in}'
```
      
- [X] Verify API response includes:
  - [X] Release identifier
  - [X] Package name and ecosystem (`pypi`)
  - [X] Installed package version
  - [X] CVE ID, severity, and CVSS
  - [X] Fixed package version, if available

```json
{
  "cve_id": "GHSA-vcvr-r3jv-pc5j",
  "package": "pkg:npm/next@16.2.1",
  "affected_version": "16.2.1",
  "fixed_in": [
    "16.3.6"
  ]
}
{
  "cve_id": "GHSA-2xp9-vwfh-vxw4",
  "package": "pkg:npm/next@16.2.1",
  "affected_version": "16.2.1",
  "fixed_in": [
    "16.3.3"
  ]
}
{
  "cve_id": "GHSA-p293-qw3h-jr36",
  "package": "pkg:npm/next@16.2.1",
  "affected_version": "16.2.1",
  "fixed_in": [
    "16.3.3"
  ]
}

```

- [X] Verify API authentication

      No authentication needed
- [ ] Test API against both releases
- [ ] Expose functionality through Ortelius MCP if required
- [ ] Verify AI CLI can retrieve vulnerability information
- [ ] Save sample API responses for backup testing

## 5. AI CLI Setup and Integration

- [ ] Verify AI CLI installation and configuration
- [ ] Configure AI model access
- [ ] Configure Ortelius API / MCP connectivity
- [ ] Configure GitHub authentication
- [ ] Verify Python dependency detection
- [ ] Verify targeted dependency upgrades using known patched versions
- [ ] Verify dependency compatibility validation
- [ ] Configure build, pytest, and security scanning
- [ ] Configure bounded AI code-fix attempts
- [ ] Verify GitHub PR and Issue generation
- [ ] Enable execution logs and remediation reports

## 6. Execute Test Case 1 — Successful Remediation

- [ ] Reset `fastapi-vuln-demo` to vulnerable baseline
- [ ] Query Ortelius for release vulnerabilities
- [ ] Execute AI CLI against repository
- [ ] Verify AI identifies CVE and patched version
- [ ] Verify dependency files are updated
- [ ] Verify any required Python source changes
- [ ] Run dependency resolution
- [ ] Run application build and tests
- [ ] Run `pytest`
- [ ] Run security scan (`pip-audit` or equivalent)
- [ ] Confirm targeted CVE is resolved
- [ ] Verify GitHub Pull Request is created
- [ ] Verify PR contains remediation explanation and test results
- [ ] Save CLI logs, scan results, and PR URL

## 7. Execute Test Case 2 — Unsafe Remediation

- [ ] Reset `fastapi-vuln-demo-unsafe` to vulnerable baseline
- [ ] Query Ortelius for release vulnerabilities
- [ ] Execute AI CLI against repository
- [ ] Verify AI identifies proposed remediation
- [ ] Verify dependency validator or tests detect incompatibility
- [ ] Verify AI analyzes and explains failure
- [ ] Verify unsafe remediation is rejected
- [ ] Verify no unauthorized changes are committed
- [ ] Verify GitHub Issue is created
- [ ] Verify Issue documents CVE, failure, and recommended next steps
- [ ] Save CLI logs, validation output, and Issue URL

## 8. Independent Verification

- [ ] Compare vulnerable and remediated dependency versions
- [ ] Confirm Test Case 1 CVE is absent from post-remediation scan
- [ ] Confirm FastAPI application functionality remains intact
- [ ] Confirm Test Case 2 correctly blocks unsafe remediation
- [ ] Verify no unauthorized modifications occurred
- [ ] Verify GitHub PR and Issue match actual execution results
- [ ] Verify all security decisions are logged
- [ ] Repeat both scenarios to demonstrate reproducibility

## 9. Security Guardrails

- [ ] Independently validate AI-recommended dependency versions
- [ ] Prevent arbitrary AI-generated shell command execution
- [ ] Sandbox build and test execution without exposed credentials
- [ ] Restrict AI source code modification scope
- [ ] Enforce bounded remediation attempts
- [ ] Require human review before merging PRs
- [ ] Verify prompt-injection attempts cannot bypass policies
- [ ] Log AI recommendations, validation outcomes, and approval status

## 10. Hackathon Documentation

- [ ] `README.md` — Setup and execution instructions
- [ ] `test-case-1.md` — Successful CVE remediation
- [ ] `test-case-2.md` — Unsafe CVE remediation
- [ ] `architecture.md` — Ortelius → AI CLI → GitHub
- [ ] `threat-model.md` — Security threats and mitigations
- [ ] `security-risk-register.md`
- [ ] `ai-system-card.md`
- [ ] `test-results.md` — Expected versus actual results
- [ ] Save CLI execution logs and security scan results
- [ ] Document AI model/tool usage
- [ ] Prepare hackathon presentation and submission materials

## 11. Final Demo Readiness

- [ ] Both test cases defined and documented
- [ ] Both GitHub repositories configured and accessible
- [ ] Both vulnerable test cases reproducible
- [ ] Both repositories onboarded into Ortelius
- [ ] Ortelius release vulnerability API working
- [ ] AI CLI connected to Ortelius and GitHub
- [ ] Test Case 1 produces validated remediation PR
- [ ] Test Case 2 rejects unsafe remediation and produces Issue
- [ ] Independent verification completed
- [ ] Demo rehearsed end-to-end
- [ ] SECURE-track submission ready

## Critical Dependencies

| Priority | Dependency | Deliverable |
|---|---|---|
| P0 | Test Case 1 definition | CVE, vulnerable version, patched version, expected successful outcome |
| P0 | Test Case 2 definition | CVE, conflicting upgrade, expected rejection outcome |
| P0 | GitHub Test Case 1 | `fastapi-vuln-demo` with known fixable CVE |
| P0 | GitHub Test Case 2 | `fastapi-vuln-demo-unsafe` with incompatible upgrade |
| P0 | Ortelius onboarding | Both repositories and vulnerable releases visible |
| P0 | Ortelius API / MCP | Retrieve CVEs and fixed versions for a release |
| P0 | AI CLI integration | Read vulnerabilities and perform remediation |
| P0 | Verification | Build, pytest, security scan, PR/Issue |
| P1 | Security guardrails | Reject unsafe modifications |
| P1 | Test documentation | Expected results and reproducible steps |
| P1 | Evidence | Logs, CVE scans, PR/Issue links |
| P2 | Presentation | Architecture, demo, and results |

