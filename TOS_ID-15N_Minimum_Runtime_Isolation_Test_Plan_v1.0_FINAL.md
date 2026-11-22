# TOS ID-15N — Minimum Runtime Isolation Test Plan

**Document ID:** TOS-ID-15N-MRITP-001  
**Version:** 1.0  
**Status:** FINAL — READY FOR USER REVIEW / GITHUB UPLOAD  
**Assessment Track:** ID-15 — Independent Execution Environment Feasibility Assessment  
**Prepared:** 2026-11-22  
**Authority:** TOS Human Authority / System Owner

---

## 1. Purpose

This document defines the minimum, decision-oriented test plan for the next local Runtime Isolation assessment. It selects the smallest useful set of tests that can produce new evidence about operating-system identity separation, direct filesystem access control, and process resource controls.

The plan is intended to avoid repeating the application-level and process-observation tests already performed under ID-01 through ID-12. It does not select a final Runtime, authorize Agent execution, authorize Production use, or authorize any system change.

## 2. Current Decision Context

The following evidence and limitations govern this plan:

- Earlier tests demonstrated that different PIDs do not, by themselves, establish different Windows security identities. ID-11 and ID-12 observed separate processes with the same Windows SID/security context.
- ID-05 demonstrated that separate application workspaces do not guarantee OS-level filesystem isolation when the processes share the same Windows identity.
- Earlier application-level authorization and write-control tests passed within their tested scope; they did not prove that direct OS access is denied.
- Windows Sandbox was enabled and investigated during ID-15M, but its runtime was not demonstrated. The Sandbox investigation is on hold; the tests are not to be repeated without new evidence or a specific diagnostic reason.
- The Surface Pro X remains Windows 11 Pro ARM64 (Qualcomm SQ1, 16 GB RAM). WSL is not installed; VirtualMachinePlatform and Hyper-V optional features were reported Disabled. A Linux ARM64 VM has not been executed on this device.
- Windows servicing remains unresolved: `DISM /CheckHealth` reported that the component store is repairable, and the .NET rollup package `Package_for_DotNetRollup_481~31bf3856ad364e35~arm64~~10.0.10009.1` remained `InstallPending` in the latest supplied output. No causal link to Sandbox or Hyper-V has been established.

**Current environment decision:** Windows User Isolation is retained as the low-cost baseline test environment. It is not considered a strong kernel/runtime isolation boundary. Linux VM remains a conditional candidate and is outside the execution scope of this test plan until Host Readiness and separate authorization are established.

## 3. Scope

### Included

1. RT-09 — Windows Sandbox readiness disposition (evidence reconciliation only; no retest currently).
2. RT-10 — Windows OS identity enforcement using two non-administrative test identities.
3. RT-11 — Direct filesystem access denial between the two test identities in a dedicated temporary test area.
4. RT-12 — Bounded process resource-control feasibility using Windows Job Objects or an equivalent documented OS mechanism, only after RT-10/RT-11 and only with separate authorization.

### Excluded

- Running any of the five TOS Agents or an operational Agent prompt.
- Connecting to an external model provider, API, MCP server, or production tool.
- Using real credentials, personal data, project secrets, or valuable source material.
- Enabling Hyper-V, WSL2, VirtualMachinePlatform, Docker, Windows Sandbox, or other optional Windows features.
- Repairing Windows, changing Windows servicing state, editing pending servicing files/keys, or changing firmware settings.
- Claiming VM, container, microVM, or Production-level isolation from results obtained in this Windows baseline.
- Stress testing, deliberate host exhaustion, destructive access attempts against non-test files, or testing outside the dedicated test directory.

## 4. Test Strategy and Order

| Order | Test | Current disposition | Purpose | Execution gate |
|---:|---|---|---|---|
| 0 | RT-09 | HOLD / no retest | Preserve the previous Sandbox findings and avoid low-value repetition | Reopen only with new servicing/registration evidence or a documented diagnostic reason |
| 1 | RT-10 | PLANNED | Prove distinct Windows account SIDs and process security contexts | Explicit user authorization for temporary local test accounts and process launches |
| 2 | RT-11 | PLANNED | Prove OS-enforced denial of cross-identity file access in a temporary workspace | RT-10 passes; explicit authorization for temporary ACL changes |
| 3 | RT-12 | DEFERRED / CONDITIONAL | Determine whether bounded process resource controls can be enforced and observed | RT-10/11 evidence reviewed; separate authorization and a bounded workload specification |

RT-10 must precede RT-11 because filesystem access checks use the security context represented by the process/thread access token. A different PID or application-level label is not sufficient evidence of a different security principal.

RT-12 is intentionally deferred. A resource accounting result is not proof of a hard resource limit; a Job Object limit is not equivalent to a separate kernel or VM boundary.

## 5. Safety and Change-Control Rules

1. The test must use a dedicated, clearly named temporary root directory outside Windows system directories and outside the TOS repository/workspace.
2. Test identities must be ordinary local standard-user accounts. They must not be members of local Administrators or other privileged groups.
3. No existing user profile, personal folder, TOS directory, Git repository, Python environment, credential store, or system directory may be used as a test target.
4. No broad permission changes are allowed. ACL changes must be restricted to the dedicated test root and its test subdirectories.
5. The initial ACL/security descriptor must be captured before any modification. The rollback procedure must be prepared and reviewed before applying the first ACL change.
6. Test files must be synthetic, non-sensitive, and disposable. Cross-account tests must not delete or overwrite another account's data; use pre-created canary files and non-destructive access attempts.
7. No test may attempt elevation, privilege escalation, credential extraction, token theft, persistence, lateral movement, or access to unrelated files.
8. No system feature or Windows servicing operation may be modified as part of this plan.
9. Stop immediately if an unexpected access grant, unexpected privilege, system instability, servicing activity requiring intervention, or test activity outside the dedicated test root is observed.
10. No execution instruction in this document is itself an authorization. Each state-changing test requires explicit approval from the Human Authority immediately before execution.

## 6. RT-09 — Windows Sandbox Readiness Disposition

**Objective:** Determine whether the previous Sandbox investigation should be repeated.

**Existing evidence:** The Sandbox optional feature was enabled and the device restarted, but the expected runtime executable/package registration was not demonstrated in the supplied checks. Component Store health remained Repairable and servicing remained unresolved. No causal relationship between servicing state and Sandbox behavior was established.

**Expected disposition:** HOLD — NO RETEST.

**Reopen criteria (at least one required):**
- New, verified Windows servicing evidence shows the pending package has reached a terminal state and the component store state has materially changed; or
- A supported, device-specific Microsoft diagnostic path identifies a new, testable reason to revisit Sandbox; or
- A distinct architectural decision depends on Sandbox and the expected value of a retest is documented.

**Prohibited action:** Do not run RestoreHealth/SFC repair, remove pending files/keys, reinstall Sandbox, or enable additional virtualization features under this test ID without a separate approved change plan.

## 7. RT-10 — OS Identity Enforcement

### 7.1 Objective

Establish whether two disposable test processes can run under two genuinely distinct Windows local account identities and whether the operating system exposes those identities in their process security contexts.

### 7.2 Preconditions

- Human Authority explicitly authorizes temporary local test-account provisioning and process launches.
- Device state, OS build/architecture, current servicing caveat, and absence/presence of active servicing activity are recorded in the Run record. A known pending servicing state is not silently treated as resolved; the test must not run during active installation, servicing, or a required-restart operation.
- Two unique disposable local test accounts are provisioned only for this test; no real Agent identities are created.
- Account provisioning/removal may require an administrator. Any elevated administrative action is restricted to account provisioning, approved ACL configuration, evidence inspection, and cleanup. **The test workload and both test processes must run with standard, non-elevated tokens.**
- Each test account's local group membership and effective token groups are inspected and recorded. Neither account may be a member of Administrators, Backup Operators, or another group that grants unintended access to the test target.
- A harmless, deterministic test program is reviewed before execution. It must perform no external communication and no system or project changes. Its purpose is to keep a process alive long enough for an independent inspector to examine its OS security context; self-reported identity is supplemental only.
- No test-account password or other authentication secret may be written to command history, scripts, logs, screenshots, or the Evidence Package.

### 7.3 Procedure Outline

1. Record the test account names and their resolved SIDs.
2. Launch the same harmless test program once under each account through a supported Windows logon/run-as mechanism.
3. Record each process ID and inspect the process's primary access token through an OS-backed method. The preferred evidence path is a reviewed inspector using `OpenProcessToken` and `GetTokenInformation` to retrieve at least the user SID, group SIDs/attributes, privileges, token type, elevation state, and integrity level where available.
4. Confirm that the test process is not elevated and that it is running under the intended standard-user SID. Do not rely solely on process command line, environment variables, account-name display, or self-reported application labels.
5. For the RT-11 file-access operation, ensure the operation is not running under an unexpected thread impersonation token. If impersonation is possible, inspect the effective thread token using an OS-backed method (`OpenThreadToken` / `GetTokenInformation`) or treat the result as inconclusive.
6. Compare the two process security contexts and retain the independent inspector output.
7. Repeat only once if the first capture is incomplete or technically invalid; do not repeat a valid pass merely to accumulate more samples.

### 7.4 Expected Results

- The two test accounts resolve to different user SIDs.
- Each test process is demonstrably running under the intended account SID.
- Both processes are non-administrative and have no unintended privileged group membership.
- The evidence distinguishes PID, account SID, group membership, and token/elevation properties.

### 7.5 Pass / Fail / Inconclusive

**PASS:** Two different account SIDs are verified, and each process token is independently shown to carry the corresponding intended SID with no unintended administrative membership.

**FAIL:** The processes run under the same account SID, a process runs under an unintended identity, or a test account has unauthorized privilege/group membership.

**INCONCLUSIVE:** The process token cannot be independently inspected, evidence is incomplete, or the test harness reports identity without OS-backed corroboration.

A PASS proves distinct Windows account/process security identities for this limited test. It does not prove kernel isolation, protection against a privileged administrator, secure credential storage, complete Agent permission enforcement, or isolation between VMs.

## 8. RT-11 — Direct Filesystem Access Denial

### 8.1 Objective

Verify that Windows access control denies one test identity direct access to the other test identity's dedicated workspace, independently of any TOS application authorization layer.

### 8.2 Preconditions

- RT-10 has passed and its evidence is accepted.
- Human Authority explicitly authorizes the temporary test directory and ACL changes.
- A dedicated test root is selected and confirmed not to contain existing user or project data.
- Initial owner, DACL, inheritance state, and relevant effective-access/group context are captured before modification.
- Both test accounts remain standard users.

### 8.3 Test Layout

The test root contains separate disposable workspaces, for example:

- `Identity-A` — accessible to Test-A and designated administrators/SYSTEM only.
- `Identity-B` — accessible to Test-B and designated administrators/SYSTEM only.
- `Evidence` — accessible to the test operator for evidence collection; test identities receive no write permission unless explicitly required and approved.

The exact path and ACL entries must be recorded in the Run record before execution. The common ancestor path must be configured so both identities can reach the intended test location without receiving data/list/write access to the other identity's workspace. Capture and inspect the security descriptors for the root, all relevant parent directories, each workspace directory, and each canary file. This prevents an access denial caused only by an unrelated parent-path permission from being misreported as proof of the target workspace's DACL.

Avoid broad deny ACEs where a minimal allow-list ACL can establish the expected boundary. Inspect inherited access entries, owner, enabled group SIDs, and effective access so an unintended group grant does not invalidate the test.

### 8.4 Procedure Outline

1. Capture and preserve the initial security descriptor/ACL for the test root.
2. Apply the minimal planned ACLs only to the dedicated test directories.
3. Create one synthetic canary file in each workspace under its intended owner account.
4. Under Test-A, attempt non-destructive directory-list and canary-read operations against Test-B's workspace, followed by a single uniquely named `create-new` probe if that operation is part of the approved test case. Record the exact operation, API/tool context, process token identity, and native OS result/error.
5. Repeat the reciprocal operations under Test-B against Test-A.
6. Verify that each identity can perform the approved read and create-new operations in its own workspace.
7. If a prohibited cross-workspace create-new attempt unexpectedly succeeds, stop the test immediately, preserve the evidence, and do not perform further writes. The uniquely named probe file may be removed only through the pre-approved cleanup procedure.
8. Re-read the ACLs and effective access context to confirm the test did not accidentally broaden permissions.
9. Do not delete, overwrite, rename, or modify the other account's pre-created canary during negative tests. Where returned by the API, record the native access-denied result (commonly `ERROR_ACCESS_DENIED`, code 5); do not rely on a translated error message alone.

### 8.5 Expected Results

- Each test identity can access its own workspace according to the declared permission scope.
- The other identity receives OS-level access denial for each prohibited read/list/create operation included in the approved test case.
- The denial is attributable to the effective process/thread security context and the relevant workspace/file DACL, not to an application-level decision or an unrelated parent-path denial.
- No general-purpose group or inherited ACE unintentionally grants cross-workspace access.
- The process can still perform the approved operations in its own workspace, demonstrating that the test is not merely failing because the test process has no access to the test root.

### 8.6 Pass / Fail / Inconclusive

**PASS:** Reciprocal cross-identity access attempts are denied by Windows while intended own-workspace operations succeed, and the captured tokens and DACLs support the conclusion.

**FAIL:** Either identity can access the other workspace beyond the approved scope, or the ACL/group configuration unintentionally grants cross-access.

**INCONCLUSIVE:** The access attempt is blocked by the test harness rather than the OS, token identity is uncertain, ACL inheritance/effective access is not understood, or the error cannot be attributed to Windows access control.

A PASS demonstrates filesystem access control for the tested directories and operations. It does not prove that all host paths are isolated, that administrators cannot access the data, or that a process is protected from kernel-level compromise.

## 9. RT-12 — Runtime Resource-Control Feasibility

**Status:** DEFERRED / CONDITIONAL — not authorized for execution in this version.

### 9.1 Objective

Determine whether a bounded test process can be assigned enforceable and observable CPU/memory/process limits, and distinguish accounting, limiting, and isolation.

### 9.2 Candidate Mechanism

Windows Job Objects are a documented candidate for grouping processes and applying selected limits. Microsoft documentation describes limits including working-set size, process priority, time limits, CPU rate control, and extended memory limits. The exact available controls and behavior must be validated against the selected Windows build and chosen API/configuration before a test is approved.

### 9.3 Required Future Test Design

- Use a deterministic synthetic workload with a hard duration and conservative resource ceiling.
- Record baseline host CPU, memory, and process count before the test.
- Record the configured Job Object limits and the actual process membership.
- Observe whether the configured CPU/memory limits are enforced, merely reported, or not enforced.
- Include a safe stop condition and operator-controlled termination.
- Do not deliberately exhaust host memory, saturate all CPU cores, or impair the Windows desktop.

### 9.4 Interpretation Boundary

Even a successful Job Object test would demonstrate only the tested process resource controls. It would not establish a separate kernel, VM, container, or strong Runtime security boundary. Resource accounting, resource limiting, and security isolation must remain separate claims.

RT-12 requires a separate detailed test specification and explicit authorization before execution.

## 10. Evidence Package Requirements

Each executed test must receive a unique Run ID and a separate evidence package. At minimum:

- Run ID, test ID, date/time, operator, device identifier (non-sensitive label), OS build/architecture, and relevant servicing caveats.
- Test plan version and test harness/script hash/version.
- Preconditions and authorization reference.
- Test identities and SIDs, with secrets/passwords excluded.
- Process identifiers and OS-backed token/security-context evidence.
- For RT-11: exact test directory layout, before/after ACL/security descriptor, operation attempted, process identity, and OS result/error.
- For RT-12 (future): configured limits, workload specification, baseline measurements, observed measurements, and stop-condition record.
- Raw outputs preserved without silent edits; derived interpretation stored separately.
- PASS/FAIL/INCONCLUSIVE decision with rationale and limitations.
- Cleanup/rollback evidence and final residual-state check.

Screenshots may supplement evidence but must not replace machine-readable outputs where those are available. No credential values, password material, API keys, or unrelated personal files may be included.

## 11. Rollback and Cleanup Plan

Rollback is mandatory after the approved RT-10/RT-11 test window:

1. Stop all test processes and confirm no test process remains active.
2. Remove only the disposable local test accounts created for this Run, after confirming they own no required data and are not used elsewhere.
3. Check whether Windows user profiles were created for the test accounts. Remove a profile only through a supported profile-management path after confirming its exact association with the disposable account and after evidence preservation. **Do not manually delete a `C:\Users\...` profile directory or profile registry entry.** If safe profile removal cannot be established, leave it intact and record the residual state.
4. Remove the temporary test root and synthetic canary/probe files after evidence has been preserved and verified.
5. If ACLs were applied to a pre-existing path by mistake, stop immediately and restore only from the captured pre-test security descriptor after Human Authority review. The preferred design avoids modifying pre-existing paths entirely.
6. Verify that no test accounts, active test processes, temporary directories, test ACL entries, or unexpected profile remnants remain.
7. Record cleanup success or any residual state. Do not claim rollback complete until the residual-state check is captured.

If cleanup cannot be completed safely, leave the evidence intact, stop further work, and report the exact residual state rather than improvising additional system changes.

## 12. Stop Conditions

Stop the test immediately if any of the following occurs:

- An account unexpectedly has administrative privileges, an unexpected enabled privileged group, an elevated process token, or an unexpected SID/token context.
- The effective thread token is unknown or unexpected during a filesystem access attempt.
- A cross-workspace access attempt unexpectedly succeeds.
- Any command targets a path outside the approved test root.
- ACL inheritance or group-based access cannot be confidently interpreted.
- The test requires enabling Windows features, repairing servicing, changing firmware, or installing software not covered by a separate authorization.
- Unexpected system instability, sustained resource pressure, servicing activity, or loss of operator control occurs.
- Evidence capture fails in a way that prevents reliable attribution.

## 13. Decision Criteria and Exit Conditions

Finalizing this document means the **test plan document** is ready for review and GitHub upload. It does **not** mean the tests have run, runtime isolation has been demonstrated, or the wider ID-15 assessment is closed.

The ID-15N test/assessment work may be considered complete only when:

1. This test plan is reviewed and accepted by the Human Authority.
2. The user explicitly authorizes each state-changing test immediately before execution.
3. RT-10 and RT-11 are either executed within the approved scope or formally deferred with a documented rationale accepted by the Human Authority. A deferral is not a PASS and leaves the corresponding evidence gap open.
4. Each executed test has a complete Evidence Package and independent review.
5. Cleanup and residual-state checks are verified.
6. Results are reconciled against the outstanding ID-15 security gaps and Runtime decision criteria.
7. A final ID-15N results/closure report is prepared, uploaded to GitHub, and confirmed by the user.

RT-12 may remain deferred if its value is not yet sufficient to justify the workload or if the selected runtime direction changes. Such deferral must be explicit and must not be represented as a pass. Document finalization and assessment closure are separate lifecycle events.

## 14. Current Authorization State

| Action | Current state |
|---|---|
| Read-only planning and evidence review | Allowed within project scope |
| Create temporary local test accounts | NOT AUTHORIZED |
| Launch processes under temporary accounts | NOT AUTHORIZED |
| Create test directories or modify ACLs | NOT AUTHORIZED |
| Execute resource-limiting workload / Job Object test | NOT AUTHORIZED |
| Enable Hyper-V, WSL2, Docker, or other Windows features | NOT AUTHORIZED |
| Repair Windows servicing / Component Store | NOT AUTHORIZED |
| Execute TOS Agents or Production workloads | NOT AUTHORIZED |

No PowerShell command is required for the planning/documentation stage. Commands for RT-10/RT-11 must be supplied only after the user reviews this plan and explicitly authorizes the specific test scope.

## 15. References

The following Microsoft documentation supports the Windows security, account-management, ACL, and resource-control concepts used in this plan. These sources document operating-system mechanisms; they do not demonstrate that a test has passed on the Surface Pro X.

1. Microsoft Learn — Access Tokens (including `OpenProcessToken`, `OpenThreadToken`, and `GetTokenInformation`): https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens
2. Microsoft Learn — Security Identifiers: https://learn.microsoft.com/en-us/windows/win32/secauthz/security-identifiers
3. Microsoft Learn — How AccessCheck Works / How DACLs Control Access to an Object: https://learn.microsoft.com/en-us/windows/win32/secauthz/how-dacls-control-access-to-an-object
4. Microsoft Learn — `icacls` command and DACL inspection/modification: https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls
5. Microsoft Learn — Job Objects (process grouping, resource limits, and accounting): https://learn.microsoft.com/en-us/windows/win32/procthread/job-objects
6. Microsoft Learn — Local Accounts (account administration and the authority of local administrators): https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/local-accounts

---

## 16. Test-to-Gap Mapping and Interpretation

| Test | Primary ID-15 gap | Evidence it may add | What it does not close by itself |
|---|---|---|---|
| RT-09 | No gap closure | Reconciles the previously established Sandbox hold state | Does not demonstrate Sandbox runtime readiness or close a runtime-isolation gap |
| RT-10 | GAP-01 — Independent OS identity | Demonstrates whether separate local Windows account SIDs and process token identities can be established on this host | Does not prove Agent-to-OS identity enforcement, kernel isolation, credential protection, or resistance to administrators |
| RT-11 | GAP-02 — Direct filesystem isolation | Demonstrates whether Windows access checks deny tested cross-identity operations on the designated test paths | Does not prove whole-host filesystem isolation, administrator resistance, or VM/container isolation |
| RT-12 | GAP-03 — Runtime/resource isolation (limited sub-scope) | Demonstrates selected process resource accounting/limiting behavior under a bounded workload | Does not prove a separate kernel or strong security boundary |

A PASS in RT-10 or RT-11 is evidence about the tested Windows mechanisms only. It does not automatically close the broader TOS security gap; integration into the eventual Agent Runtime, independent enforcement, audit, and QC still require separate evidence. RT-12 is a resource-control test, not a substitute for runtime isolation.

## Document Control

**Document status:** FINAL — READY FOR USER REVIEW / GITHUB UPLOAD  
**Plan review/acceptance:** PENDING USER REVIEW  
**Test execution status:** NOT EXECUTED  
**State-changing test authorization:** NOT GRANTED  
**System changes authorized by this document:** NONE  
**Runtime selection:** NOT FINALIZED  
**Production authorization:** NOT GRANTED
