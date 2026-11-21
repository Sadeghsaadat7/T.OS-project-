# TOS — Execution Environment Feasibility
## Security Test Progress Report v1.0

**Date:** 2026-11-21  
**Document Status:** FINAL — PROGRESS RECORD  
**Scope:** Local Execution Environment — Security and Evidence Infrastructure Feasibility  
**Project:** Trading Operations System (TOS)

---

## 1. Purpose

This document records the verified results of selected technical and security feasibility tests conducted on the primary TOS development environment.

Its purpose is to:

- Preserve actual test results and their limitations.
- Distinguish demonstrated capabilities from untested assumptions.
- Provide traceable evidence for subsequent environment evaluation.
- Prevent unnecessary repetition of completed tests.
- Identify the next security feasibility investigation.

This document does not constitute final execution-environment selection or production-readiness approval.

## 2. Test Environment

Primary test device:

- Device: Microsoft Surface Pro X
- Operating System: Windows 11 Pro (reported configuration; exact OS edition metadata remains subject to reconciliation)
- Processor: Qualcomm SQ1
- Architecture: ARM64
- RAM: 16 GB
- Python: 3.14.0 ARM64
- OpenAI Agents SDK: 0.25.0
- Test environment: Local Windows environment

All tests described in this document used synthetic data.

No real trading data, credentials, production secrets, or live trading operations were involved.

No external model API calls or independent production Agent executions were performed.

## 3. DPAPI Feasibility Tests

### 3.1 Single-Process Encryption and Recovery

**Test:** DPAPI CurrentUser encryption/decryption.

**Result:** PASS

Verified:

- `System.Security.Cryptography.ProtectedData` was available.
- `DataProtectionScope.CurrentUser` was available.
- Synthetic data was successfully encrypted.
- Encrypted output differed from the original data.
- Decryption recovered the original data correctly.

This demonstrated basic local availability and functionality of DPAPI CurrentUser.

### 3.2 Cross-Process Recovery

**Test ID:** TOS-SEC-DPAPI-002

**Test:** Independent process recovery using DPAPI CurrentUser.

**Result:** PASS

Procedure:

1. Process 1 encrypted synthetic data using DPAPI CurrentUser.
2. The encrypted result was written to a file.
3. A separate PowerShell process was started.
4. Process 2 independently read the encrypted file.
5. Process 2 decrypted the data.
6. Recovered data was compared with the original value.

Verified output:

`RECOVERED DATA MATCH: PASS`

`CROSS-PROCESS DECRYPTION: PASS`

`CROSS-PROCESS DPAPI TEST: PASS`

Temporary files were removed after the test.

**Conclusion:**

DPAPI CurrentUser supports encryption in one process and recovery in an independent process operating under the same Windows user identity.

### 3.3 Cross-User Isolation

**Test:** DPAPI CurrentUser recovery under a different Windows user identity.

**Result:** NOT TESTED

Local account inventory:

| Account | Enabled |
|---|---|
| Administrator | No |
| DefaultAccount | No |
| Guest | No |
| Microsoft | Yes |
| WDAGUtilityAccount | No |

Current identity:

`desktop-hnhq2k7\microsoft`

Only one normal local user account was enabled.

No additional account was created, and no Windows security configuration was changed solely for this test.

**Conclusion:**

Cross-user isolation remains unverified.

DPAPI CurrentUser must not yet be treated as a demonstrated Agent-level security boundary.

## 4. Important Security Limitations

The successful DPAPI tests do not demonstrate:

- Isolation between Agents sharing the same Windows account.
- Protection against malicious code executing under the same user identity.
- Cross-user access denial.
- Protection against privileged Administrator access.
- Secure long-term key lifecycle management.
- Hardware-backed or TPM-protected key storage.
- Complete protection of production secrets.

DPAPI is a potential component of a secrets-protection strategy, not a complete TOS security architecture.

## 5. Other Previously Demonstrated Security and Evidence Capabilities

The following results were obtained in earlier synthetic local tests during this feasibility assessment.

| Capability | Result | Important limitation |
|---|---|---|
| HMAC-SHA256 file verification | PASS | Secure key management not established |
| HMAC verification across processes | PASS | Tested with a temporary shared key file |
| Evidence manifest integrity | PASS | Synthetic package only |
| Same-size file tampering detection | PASS | SHA-256 mismatch detected |
| Missing evidence file detection | PASS | Tested with an independent checker |
| Path traversal rejection | PASS | Tested cases only |
| Windows Junction escape detection | PASS | TOCTOU protection not tested |
| Windows ACL inspection | COMPLETED | No independent user-access test |
| DPAPI CurrentUser recovery | PASS | Same user identity only |

These results establish limited technical feasibility, not production security.

## 6. Windows Identity and ACL Findings

The test directory was inspected.

Observed permissions included:

- SYSTEM: Full Control
- Administrators: Full Control
- Current user: Full Control

No explicit Users or Everyone ACEs were observed on the inspected test directory.

The current PowerShell session was not elevated, despite the account belonging to the Administrators group.

Important limitation:

ACL inspection alone does not establish effective isolation between Agents. If all Agents execute under the same Windows identity, filesystem ACLs generally do not provide independent identity boundaries between those Agents.

## 7. Current Security Feasibility Assessment

| Area | Assessment |
|---|---|
| Local secret encryption | Demonstrated |
| Same-user cross-process recovery | Demonstrated |
| Cross-user DPAPI isolation | Unknown |
| Agent identity separation | Not yet demonstrated |
| Per-Agent filesystem restrictions | Not yet demonstrated |
| Per-Agent tool permissions | Not yet demonstrated |
| Run-level data isolation | Not yet demonstrated |
| Network access restrictions | Not yet demonstrated |
| Secure key lifecycle | Incomplete |
| Evidence integrity mechanisms | Partially demonstrated |

Unknown or untested capabilities must not be treated as PASS.

## 8. Architectural Implications for TOS

The following principles remain applicable:

1. Secret protection and Agent isolation are separate security concerns.
2. A shared Windows user identity must not be assumed to provide independent Agent security boundaries.
3. Agents should operate with least privilege.
4. Each Agent's access to files, tools, services, and Run data must be explicitly controlled.
5. Evidence integrity and Evidence access control must be independently addressed.
6. Sensitive operations must remain subject to TOS authority and approval rules.
7. The Evidence & Quality layer must preserve independent, reviewable records.

A future execution design may require a separately controlled identity or service for sensitive operations such as secret access and evidence signing.

No implementation decision is made by this document.

## 9. Next Investigation

**Proposed next test area: Agent Identity & Permission Isolation**

The next feasibility investigation should examine whether a candidate execution environment can demonstrate:

- Distinct Agent execution identities.
- Least-privilege access.
- Restricted filesystem access.
- Controlled tool permissions.
- Run-level data separation.
- Prevention of unauthorized actions.
- Auditable denial of unauthorized access.

This investigation should use synthetic data and controlled test fixtures.

No production credentials, real trading data, or unrestricted external actions should be introduced.

## 10. Governance and Scope Boundaries

This document:

- Records completed technical tests and their limitations.
- Does not select a final execution platform.
- Does not authorize actual TOS Agent execution.
- Does not modify the approved TOS architecture.
- Does not reopen frozen C1–C7 or D0–D14 stages.
- Does not authorize production deployment.
- Does not replace the final Independent Execution Environment Feasibility Assessment.

The broader environment-feasibility stage remains OPEN.

## 11. Final Status

**DPAPI CurrentUser Basic Functionality:** DEMONSTRATED

**Cross-Process Recovery Under Same User:** PASS

**Cross-User Isolation:** NOT TESTED

**Agent Identity & Permission Isolation:** PENDING

**Security Feasibility Investigation:** IN PROGRESS

**Execution Environment Selection:** NOT DECIDED

**Production Execution Authorization:** NOT GRANTED

---

**End of Document**
