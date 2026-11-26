# TOS RT-16 — Filesystem Access Isolation Test Results

## Document Control

| Field | Value |
|---|---|
| Document Title | TOS RT-16 — Filesystem Access Isolation Test Results |
| Version | 1.0 |
| Status | FINAL — UPLOADED AND VERIFIED ON MAIN; USER ACCEPTANCE PENDING |
| Test Plan | `TOS_RT-16_Filesystem_Access_Isolation_Test_Plan_v1.0_FINAL.md` |
| Test Date | 2026-11-26 |
| Execution Environment | Windows 11 ARM64 / Podman 6.2.1 / WSL2 |
| Overall Test Result | PASS |
| Cleanup Result | PASS |
| Runtime Selection | NOT FINALIZED |
| Production Authorization | NOT GRANTED |
| Stage Closure | PENDING USER ACCEPTANCE |

---

## 1. Executive Summary

The RT-16 Filesystem Access Isolation Test was executed against the approved RT-16 test plan.

The test evaluated the following behaviors in the selected rootless Podman configuration:

- Container root filesystem separation.
- Private named-volume attachment isolation.
- Private-volume access positive controls.
- Shared named-volume read access.
- Shared named-volume write denial.
- Shared fixture integrity.
- Final container mount configuration.
- Controlled removal of test resources.

All planned functional checks within the defined RT-16 scope passed.

Controlled cleanup was completed successfully. Final verification confirmed that:

- No RT-16 test containers remained.
- No RT-16 test volumes remained.
- The Podman machine remained running.
- Podman remained in rootless mode.
- The reported execution architecture remained ARM64.

**Overall RT-16 Test Result: PASS**

This result applies only to the configurations and behaviors tested. It does not establish comprehensive runtime security, production readiness, or authorization for production deployment.

## 2. Authorization and Execution Control

The approved RT-16 test plan was uploaded and verified on the repository's `main` branch before execution.

The user explicitly authorized execution after plan acceptance on 2026-11-26.

Execution remained within the approved scope and operating constraints:

- Rootless default Podman connection only.
- Windows PowerShell 5.1.
- Existing local Fedora ARM64 image only.
- No image pull.
- No elevated PowerShell session.
- No rootful Podman execution.
- No host, WSL, or global Podman configuration changes.
- No broad prune or unrelated resource deletion.
- Only reserved RT-16 test containers and volumes were created and removed.

The execution authorization applied to the approved RT-16 test scope. It did not grant production authorization or finalize runtime selection.

## 3. Test Environment

| Attribute | Observed Value |
|---|---|
| Host Name | `DESKTOP-HNHQ2K7` |
| Host Operating System | Windows 11 Pro |
| Host Architecture | ARM64 |
| Execution Shell | Windows PowerShell 5.1 |
| Podman Client Version | 6.2.1 |
| Podman Server Version | 6.2.1 |
| Podman Machine | `podman-machine-default` |
| Machine Type | WSL |
| Machine State at Final Verification | Currently running |
| Machine CPU Allocation | 2 CPUs |
| Machine Memory Allocation | 4 GiB |
| Machine Disk Allocation | 50 GiB |
| Podman Rootless | `true` |
| Podman Architecture | `arm64` |
| Local Image | `quay.io/fedora/fedora:latest` |
| Image ID | `e838c6ec3829f921dd0345e04e301df6b3fa8017fad4d7e7c889697bb5800a58` |
| Image OS / Architecture | Linux / ARM64 |
| Image Pull | Not performed |

The preflight checks confirmed that the PowerShell session was not elevated and that the selected Podman connection was rootless.

The CPU, memory, and disk values above describe the Podman machine configuration. They are not measurements of per-container resource limits.

The existing local image was used with `--pull=never`.

## 4. Test Resources

### 4.1 Subject Containers

| Resource | Container ID |
|---|---|
| `tos-rt16-a` | `fd9ef708c239eb73f1ec5ec836b8b71b9e2544afcf0b48bd24976f90211f05be` |
| `tos-rt16-b` | `e2bf18dac57f91732da6853cdc487de3857b2b6c3a1edec0d89bfe89bf3b1740` |

Both canonical subject containers were created from the approved local image.

### 4.2 Named Volumes

The following named volumes were created for the test:

- `tos-rt16-private-a`
- `tos-rt16-private-b`
- `tos-rt16-shared-ro`

### 4.3 Helper Containers

The following temporary helper containers were used:

- `tos-rt16-seed`
- `tos-rt16-readcheck`

Both helpers were executed with automatic removal. Their absence was verified after execution and again during final cleanup verification.

## 5. Test Results

### 5.1 RT-16.1 — Preflight

**Result: PASS**

Preflight checks confirmed:

- Podman client and server availability.
- Rootless default connection.
- ARM64 execution architecture.
- Availability of the existing local Fedora ARM64 image.
- No requirement to pull an image.
- Initial availability of the reserved RT-16 resource names.
- Non-elevated PowerShell execution.

The environment was suitable for proceeding with the approved test scope.

### 5.2 RT-16.2 — Fixture Preparation

**Result: PASS**

The shared-volume fixture was created and independently read back.

Exact fixture content:

`RT16_SHARED_RO_FIXTURE`

The seed helper completed successfully.

A separate read-only helper confirmed that the fixture could be read through a read-only volume attachment.

Both helper containers were automatically removed, and their absence was verified.

### 5.3 RT-16.3 — Subject Container Creation

**Result: PASS**

Both canonical subject containers were created successfully and reached the running state.

The containers were configured using:

- The existing local Fedora ARM64 image.
- `--pull=never`.
- `--arch arm64`.
- `--network=none`.
- The reserved RT-16 resource names and labels.
- The approved named-volume mount configuration.

### 5.4 RT-16.4 — Mount Configuration

**Result: PASS**

Container inspection confirmed the intended mount configuration.

**Container A**

| Volume | Container Path | Access |
|---|---|---|
| `tos-rt16-private-a` | `/tos-private` | Read/Write |
| `tos-rt16-shared-ro` | `/tos-shared` | Read-Only |

**Container B**

| Volume | Container Path | Access |
|---|---|---|
| `tos-rt16-private-b` | `/tos-private` | Read/Write |
| `tos-rt16-shared-ro` | `/tos-shared` | Read-Only |

Both containers also reported:

- `Privileged=false`
- `NetworkMode=none`

Neither container had the other container's private volume attached.

### 5.5 RT-16.5 — Root Filesystem Separation

**Result: PASS**

The test used independent marker files in the container root filesystems.

Container A successfully created and read:

- Path: `/rt16-rootfs-a.txt`
- Content: `RT16_ROOTFS_A`

The A marker was not visible in container B.

Container B successfully created and read:

- Path: `/rt16-rootfs-b.txt`
- Content: `RT16_ROOTFS_B`

The B marker was not visible in container A.

Both directions passed after the command-construction issue described in Section 6 was corrected.

### 5.6 RT-16.6 — Private Volume Positive Controls

**Result: PASS**

Container A successfully wrote and read its private-volume marker:

- Path: `/tos-private/private-a.txt`
- Content: `RT16_PRIVATE_A`

Container B successfully wrote and read its private-volume marker:

- Path: `/tos-private/private-b.txt`
- Content: `RT16_PRIVATE_B`

These checks confirmed that each subject container could access its own attached private volume.

### 5.7 RT-16.7 — Private Volume Non-Visibility

**Result: PASS**

The cross-container visibility checks returned the expected results:

- Container A could not see container B's private-volume marker.
- Container B could not see container A's private-volume marker.

The result is consistent with the approved configuration: each private volume was attached only to its designated container.

**Interpretation limitation:** This test demonstrates non-visibility resulting from separate volume attachment configurations. It does not demonstrate POSIX DAC/ACL access denial when the same private volume is mounted into both containers.

### 5.8 RT-16.8 — Shared Read-Only Volume

**Result: PASS**

Both subject containers successfully read the shared fixture.

Each container then attempted to create a separate write-probe file in the shared mount.

Both write attempts failed with the expected read-only filesystem error.

Subsequent integrity checks confirmed:

- The original fixture content remained unchanged.
- Container A's write-probe file was absent.
- Container B's write-probe file was absent.

Final container inspection reconfirmed that the shared volume was mounted read-only in both containers.

## 6. Execution Deviations and Recovery

Several command-construction and setup issues occurred during execution. They were diagnosed, corrected, and documented.

The successful corrected executions and independent verification results were used as the final test evidence.

These issues did not require changes to the approved test scope or host/runtime configuration.

### 6.1 Initial Incorrect Subject Container Configuration

An initial container creation attempt used incorrect mount paths and omitted the required RT-16 labels.

Both incorrectly created subject containers were in the created state and had not been started.

They were removed before the canonical containers were created.

The associated named volumes were not removed or modified during this corrective action.

The canonical subject containers were subsequently created with the approved configuration.

### 6.2 PowerShell and Shell Quoting Issues

Some initial commands involving nested shell quoting, `printf`, command substitution, and Go-template formatting did not execute as intended.

The affected commands were replaced with simpler commands or alternative inspection methods.

Where a test operation was affected, the relevant check was repeated and independently verified before its result was accepted.

### 6.3 Fixture Creation Correction

The original nested `printf` command produced an incorrect trailing character in the fixture content.

The fixture was repaired using a simpler command.

The corrected fixture content was independently verified through the seed and read-only helper executions:

`RT16_SHARED_RO_FIXTURE`

Only the verified fixture was used for the final shared-volume tests.

### 6.4 Root Filesystem Test Retry

The first root filesystem write attempt using nested command substitution failed.

The test was repeated using direct `printf` writes followed by separate read-back and cross-container visibility checks.

Both directions passed on the successful retry.

The unsuccessful initial attempt was not counted as a passing test result.

### 6.5 Inspection Command Corrections

Initial inspection formatting attempts encountered PowerShell/Go-template quoting errors.

Raw inspection output and a simpler valid inspection template were used instead.

The final mount configuration for both containers was successfully verified.

### 6.6 Deviation Assessment

The observed deviations were limited to command construction, inspection formatting, and test setup.

They did not require:

- Changes to the approved test scope.
- Host operating system configuration changes.
- WSL configuration changes.
- Global Podman configuration changes.
- Rootful execution.
- Unrelated resource deletion.

All affected checks were successfully corrected, repeated where necessary, and verified.

## 7. Controlled Cleanup

**Result: PASS**

Cleanup was performed in a controlled sequence using only the reserved RT-16 resource names.

No broad prune operation was used.

### 7.1 Pre-Cleanup Inventory

Pre-cleanup inventory was recorded at:

`2026-11-26T15:53:27.8781883+03:30`

The inventory showed:

- `tos-rt16-a` and `tos-rt16-b` running.
- All three RT-16 named volumes present.
- `tos-rt16-seed` absent.
- `tos-rt16-readcheck` absent.

No unexpected RT-16 resources were shown in the inventory.

### 7.2 Subject Container Removal

Both subject containers were removed successfully.

| Container | Removal Exit Code | Absence Verification |
|---|---:|---:|
| `tos-rt16-a` | 0 | Exit 1 |
| `tos-rt16-b` | 0 | Exit 1 |

The subsequent RT-16 container listing was empty.

### 7.3 Named Volume Removal

All three reserved named volumes were removed successfully.

| Volume | Removal Exit Code | Absence Verification |
|---|---:|---:|
| `tos-rt16-private-a` | 0 | Exit 1 |
| `tos-rt16-private-b` | 0 | Exit 1 |
| `tos-rt16-shared-ro` | 0 | Exit 1 |

The subsequent RT-16 volume listing was empty.

### 7.4 Final Cleanup Verification

Final cleanup verification completed at:

`2026-11-26T15:55:53.9965307+03:30`

The verification confirmed:

- No RT-16 containers remained.
- No RT-16 volumes remained.
- All four container existence checks returned exit code `1`.
- All three volume existence checks returned exit code `1`.
- The Podman machine remained running.
- Podman remained rootless.
- The reported architecture remained ARM64.

For the `podman container exists` and `podman volume exists` checks, exit code `1` was the expected result indicating that the named resource no longer existed.

No unrelated container, volume, or Podman resource was targeted for deletion.

## 8. Overall Results Summary

| Test Area | Result |
|---|---|
| RT-16.1 — Preflight | PASS |
| RT-16.2 — Fixture Preparation | PASS |
| RT-16.3 — Subject Container Creation | PASS |
| RT-16.4 — Mount Configuration | PASS |
| RT-16.5 — Root Filesystem Separation | PASS |
| RT-16.6 — Private Volume Positive Controls | PASS |
| RT-16.7 — Private Volume Non-Visibility | PASS |
| RT-16.8 — Shared Read-Only Volume | PASS |
| RT-16.9 — Controlled Cleanup | PASS |
| Post-Cleanup Environment Verification | PASS |

**Overall RT-16 Test Result: PASS**

All defined test checks passed within the approved RT-16 scope.

## 9. Scope Limitations

RT-16 does not establish or test the following:

- POSIX DAC/ACL isolation for a private volume mounted into both containers.
- Host bind-mount isolation.
- Resistance to container escape or host compromise.
- Kernel exploit resistance.
- Network egress security.
- Multi-agent workload capacity or performance.
- Runtime resilience under production workloads.
- Comprehensive runtime security.
- Production readiness.
- Final production runtime selection.

These subjects remain outside the evidence established by RT-16. Any further assessment must have its own justified scope and acceptance criteria.

## 10. Runtime and Production Decision

RT-16 provides positive evidence for the specific filesystem and named-volume access behaviors tested in the selected rootless Podman configuration.

The test result does not independently authorize the runtime for production.

The following decisions remain unchanged:

| Decision | Status |
|---|---|
| RT-16 Functional Test Result | PASS |
| RT-16 Cleanup | PASS |
| Runtime Selection | NOT FINALIZED |
| Production Authorization | NOT GRANTED |
| Comprehensive Runtime Security | NOT ESTABLISHED BY RT-16 |

No production-readiness claim is made by this report.

## 11. Evidence and Traceability

The execution evidence consists of the contemporaneous PowerShell outputs, container inspection results, command exit codes, and final resource-state checks recorded during the 2026-11-26 test session.

The evidence was reviewed against the approved test plan and consolidated into this results report.

No separate raw-console-transcript artifact is included as part of this document.

### 11.1 Key Execution Evidence Times

All timestamps below use the test environment's local time zone, UTC+03:30.

| Activity | Recorded Time |
|---|---|
| Preflight | 2026-11-26 15:21:57–15:23:05 |
| Fixture preparation verification | 2026-11-26 15:39:40 |
| Canonical subject container creation | 2026-11-26 15:41:35–15:41:40 |
| Root filesystem separation retry | 2026-11-26 15:47–15:48 |
| Private-volume positive controls | 2026-11-26 15:49:08–15:49:15 |
| Private-volume non-visibility | 2026-11-26 15:49:49–15:49:53 |
| Shared-volume read checks | 2026-11-26 15:50:51–15:50:55 |
| Shared-volume write-denial checks | 2026-11-26 15:52:04–15:52:08 |
| Final mount verification | 2026-11-26 15:52:47–15:52:56 |
| Pre-cleanup inventory | 2026-11-26 15:53:27 |
| Subject container removal verification | 2026-11-26 15:54:00–15:54:23 |
| Volume removal verification | 2026-11-26 15:54:59–15:55:05 |
| Final cleanup and environment verification | 2026-11-26 15:55:42–15:55:53 |

### 11.2 Approved Test Plan Reference

| Field | Value |
|---|---|
| Repository | `Sadeghsaadat7/T.OS-project-` |
| Branch | `main` |
| Plan File | `TOS_RT-16_Filesystem_Access_Isolation_Test_Plan_v1.0_FINAL.md` |
| Verified Plan Commit | `9fc573c80962d2bd432b511dc66b7dbcfc005095` |
| Plan Blob SHA | `98eb315331ac460f9bc187754ce6d85dba95d02e` |

The approved test plan remains the governing source for the test scope and expected behavior.

## 12. Document Status and Stage Acceptance

**Document Status: FINAL — UPLOADED AND VERIFIED ON MAIN**

This report records the completed RT-16 execution, results, deviations, and cleanup.

The final results document has been uploaded to the repository and verified on the `main` branch. The user has confirmed completion of the upload. Formal acceptance of the uploaded results report remains the final user-controlled stage-closure condition.

Current status:

- Document upload status: UPLOADED.
- Repository verification: VERIFIED ON `main`.
- User acceptance of results report: PENDING.
- RT-16 stage closure: PENDING USER ACCEPTANCE.

The technical test execution and controlled cleanup are complete. RT-16 must not be marked fully closed until the user explicitly accepts this uploaded results report.

---

**End of Report**
