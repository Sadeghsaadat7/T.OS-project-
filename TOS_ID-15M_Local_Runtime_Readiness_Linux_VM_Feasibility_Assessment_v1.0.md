# TOS ID-15M — Local Runtime Readiness & Linux VM Feasibility Assessment

| Document control | Value |
|---|---|
| Document ID | `TOS-ID-15M` |
| Version | `1.0` |
| Assessment date | 2026-11-22 |
| Evidence cutoff | 2026-11-22 |
| Status | **FINAL — READY FOR GITHUB UPLOAD** |
| Recommended GitHub path | `TOS_External_Memory/TOS_ID-15M_Local_Runtime_Readiness_Linux_VM_Feasibility_Assessment_v1.0.md` |
| Scope | Surface Pro X local runtime readiness; Windows Sandbox and Linux ARM64 VM feasibility |
| Decision authority | TOS Human Authority / System Owner |
| Stage closure | **PENDING GITHUB UPLOAD AND EXPLICIT USER CONFIRMATION** |

---

## 1. Purpose and Scope

This assessment determines whether the Surface Pro X can reasonably remain a candidate host for a local Linux virtual machine intended for controlled TOS runtime experimentation.

The assessment covers:

- The observed Windows Sandbox outcome and the related Windows servicing state.
- Documented Hyper-V and Linux ARM64 compatibility on Windows 11 ARM64.
- The actual Hyper-V-related feature and processor-property outputs collected from the Surface Pro X.
- The implications for TOS runtime isolation and the remaining readiness gaps.

This is a feasibility and readiness assessment only. It does not select the final TOS execution runtime and does not authorize Agent execution, Production use, Windows repair, feature activation, installation, firmware changes, or VM creation.

## 2. Executive Decision

> **LOCAL LINUX ARM64 VM ON SURFACE PRO X: CONDITIONALLY FEASIBLE — HOST READINESS NOT ESTABLISHED — EXECUTION NOT DEMONSTRATED.**

Official Arm documentation describes creating and running Linux ARM64 virtual machines with Hyper-V on physical Windows 11 ARM devices. It identifies Windows 11 version 22H2 or newer as the OS baseline and provides a concrete Ubuntu 24.04 ARM64 example. This establishes a documented ARM64 path, but it does not demonstrate successful Hyper-V operation on this specific SQ1 Surface Pro X. [1][2]

The current disposition is:

- Retain **Hyper-V + Linux ARM64** as a conditional local strong-isolation candidate.
- Retain **Windows User Isolation** as the current local baseline; it is not equivalent to VM-level isolation.
- Keep **Windows Sandbox** on hold: its optional feature was enabled, but a usable Sandbox runtime was not demonstrated.
- Keep **WSL2 and Docker** installation/activation on hold.
- Do not attempt Windows servicing repair or manually alter pending packages as part of this assessment.
- Do not select a final TOS runtime based on this assessment.

## 3. Device Baseline and Evidence Provenance

### 3.1 Device baseline

| Item | Observed state |
|---|---|
| Device | Surface Pro X |
| Processor | Qualcomm Microsoft SQ1 @ 3.0 GHz |
| Architecture | ARM64 (`Win32_Processor.Architecture = 12`) |
| CPU cores / logical processors | 8 / 8 |
| Installed RAM | Approximately 16 GB |
| Free storage | Approximately 284 GB (previous inventory; not a fresh measurement in the final gate) |
| Operating-system basis | Windows 11 Pro ARM64, build 26300.9974 |
| Hypervisor | Previously detected by `systeminfo` |
| VBS / HVCI / Secure Launch | Previously reported Running by `systeminfo` |
| `Microsoft-Hyper-V-All` | Disabled |
| `VirtualMachinePlatform` | Disabled |
| WSL | Not installed |
| Docker | Not installed |

### 3.2 Operating-system metadata discrepancy

A metadata inconsistency was observed during the assessment: `Get-ComputerInfo` reported Windows 10 Pro / version 2009, while `Win32_OperatingSystem` and `systeminfo` identified Windows 11 Pro with build 26300.9974. The Windows 11 Pro ARM64 baseline is used because it is supported by the latter two sources. The conflicting `Get-ComputerInfo` result is retained as an unresolved metadata discrepancy; it is not silently reconciled.

Arm's Hyper-V-on-Arm guide states that Windows 11 version 22H2 or newer is required. The reported build is consistent with a newer Windows 11 build, but the precise version label should still be checked with `winver` before any future feature activation. [1]

### 3.3 Evidence provenance

The device-specific findings in this document are based on user-executed PowerShell, DISM, `systeminfo`, Windows feature/package queries, and Event Log outputs shared during ID-15M T1–T16 and the final host-readiness gate on 2026-11-22. The document distinguishes those observed outputs from external documentation and engineering interpretation.

## 4. Windows Sandbox Assessment

### 4.1 Actions and observations

The Windows Sandbox optional feature (`Containers-DisposableClientVM`) was enabled with the user's authorization, and the device was restarted.

After restart:

- DISM continued to report the feature as `Enabled`.
- `WindowsSandbox.exe` was not found in the checked System32 and Sysnative locations.
- `where.exe WindowsSandbox.exe` did not locate the executable.
- The checked Appx package and Start Menu queries returned no Sandbox registration.
- Additional searches for Sandbox-related files, capabilities, and package registrations did not establish a usable client/runtime.
- No Windows Sandbox session was successfully launched.

These checks did not conclusively establish why the runtime was unavailable. In particular, the absence of the checked Appx or Start Menu registrations alone is not treated as proof that every possible Sandbox packaging/registration path is absent.

### 4.2 Windows servicing observations

The component store was reported as `Repairable` by DISM `/CheckHealth` and `/ScanHealth` during the assessment. The final host-readiness check again returned:

`The component store is repairable.`

The following .NET rollup package was confirmed as `InstallPending`:

`Package_for_DotNetRollup_481~31bf3856ad364e35~arm64~~10.0.10009.1`

Relevant package properties:

| Property | Observed value |
|---|---|
| Description | Fix for KB5137006 |
| PackageState | `InstallPending` |
| Applicable | `True` |
| InstallTime | Unset (`1/1/0001 12:00:00 AM`) |
| RestartRequired | `Possible` |
| RestartNeeded | `False` in the returned DISM PowerShell object |

Earlier Event Log evidence showed that KB5137006 successfully reached the `Staged` state and that the `Staged`-to-`Installed` transition was initiated. The available records did not establish final completion or failure. A separate KB5132057 staging failure was recorded with status `0x80070306`; no causal relationship between that event, KB5137006, the component-store health result, or Windows Sandbox was established.

### 4.3 Sandbox conclusion

**Feature Enabled / Runtime Not Demonstrated / Servicing Unresolved.**

The evidence does not establish whether the unavailable Sandbox runtime is related to Windows servicing, ARM-specific packaging, the installed Windows build, or another factor. It is not justified to conclude that the Surface Pro X is inherently unsupported, nor that the component-store state is definitively the cause.

No repair operation was performed. Pending files, servicing registry keys, and package state were not manually modified.

## 5. Hyper-V and Linux ARM64 Feasibility

### 5.1 Documented ARM64 path

Arm's documentation explicitly describes enabling Hyper-V on a physical Windows 11 ARM device and creating a Linux VM from an ARM-specific Ubuntu 24.04 ISO. The Linux VM guide specifies Windows 11 version 22H2 or newer, advises against using Hyper-V Quick Create on Windows on Arm, and demonstrates a manually created Generation 2 VM. [1][2]

For that specific documented first-boot path, the example is **Ubuntu 24.04 Desktop ARM64**. This is therefore the reference guest for a possible future proof-of-boot test. It is not a final TOS server/runtime distribution selection. An Ubuntu Server ARM64 guest may be considered separately after the first ARM64 Hyper-V path is established; it is not treated here as having been demonstrated on this exact host.

### 5.2 Host feature state

The final read-only PowerShell queries confirmed:

| Windows optional feature | State |
|---|---|
| `Microsoft-Hyper-V-All` | Disabled |
| `VirtualMachinePlatform` | Disabled |

No Hyper-V feature was enabled during this assessment.

`VirtualMachinePlatform` is recorded separately. Its Disabled state must not automatically be interpreted as proof that the full Hyper-V role is unavailable: the cited Arm Hyper-V installation path instructs enabling Hyper-V and does not identify `VirtualMachinePlatform` as a prerequisite for that Hyper-V Linux VM path. [1][2]

### 5.3 Processor and virtualization-property observations

The latest `Win32_Processor` query reported:

| Property | Value |
|---|---|
| Name | Microsoft SQ1 @ 3.0 GHz |
| Architecture | 12 |
| NumberOfCores | 8 |
| NumberOfLogicalProcessors | 8 |
| VMMonitorModeExtensions | `True` |
| VirtualizationFirmwareEnabled | `False` |
| SecondLevelAddressTranslationExtensions | `False` |

Previous `systeminfo` output reported a hypervisor detected, VBS Running, Hypervisor-enforced Code Integrity Running, and Secure Launch Running.

These observations do not provide a conclusive host-readiness result:

- The `VMMonitorModeExtensions=True` property is a positive observation.
- The two `False` CIM properties remain unresolved and must not be ignored.
- The previously observed hypervisor/VBS state does not, by itself, demonstrate that the Hyper-V management/runtime components can create and boot the required Linux VM.
- General Hyper-V documentation identifies SLAT and VM Monitor Mode extensions among host requirements. However, the available general requirement material does not explain how the reported CIM properties map to this SQ1 ARM64 platform. The ARM-specific guides establish the documented Windows-on-Arm route, but they do not establish the runtime capability of this exact SQ1 device. [1][3]

**Virtualization capability status: CONFLICTING / NOT FULLY VERIFIED.** No definitive claim is made that the SQ1 lacks the required capability, or that the capability has been proven by the current outputs.

### 5.4 Candidate guest and test-configuration boundary

If a future, separately authorized readiness gate passes, the first proof-of-boot candidate is:

| Item | Candidate / constraint |
|---|---|
| Guest | Ubuntu 24.04 Desktop ARM64, using the Arm-specific image referenced in [2] |
| VM generation | Generation 2, as used by the Arm guide |
| Network | Not enabled by default for the initial boot proof; any later network use must be separately scoped and authorized |
| vCPU, RAM, and virtual disk | **Not fixed by this assessment**; determine only after checking current host resource availability and the exact test image |
| Secure Boot | Do not apply generic x64 settings by assumption. Arm's cited Ubuntu Desktop ARM64 guide says to disable Secure Boot for its demonstrated image; this image-specific setting requires a separate review before any actual VM configuration. [2] |

Arm's guide recommends allocating approximately half of host memory for its example VM. On a 16 GB Surface Pro X, that would be approximately 8 GB. This is a guide recommendation, not a demonstrated safe allocation for this device; earlier available-memory readings were time-specific and do not establish current headroom. No fixed RAM allocation is approved by this assessment. [2]

## 6. TOS Security and Architecture Implications

A successfully booted Linux VM would establish that a guest environment with its own kernel can run under the host hypervisor. This is a stronger isolation boundary than Windows account separation alone, but it would not, by itself, satisfy TOS security, governance, evidence, or independent-QC requirements.

The following remain TOS responsibilities and require separate design and evidence:

- Independent Agent identities and permission scopes.
- Workflow and state control outside Agent authority.
- Restricted filesystem, network, and tool access.
- Credential protection and least privilege.
- Evidence capture and tamper protection outside Agent control.
- An independent QC trust boundary, particularly for A4.
- Run-level traceability, audit, export, retention, and cleanup.
- Human approval for sensitive actions and knowledge promotion.

A successful VM boot test must not be interpreted as closure of any of the nine previously identified security gaps (`GAP-01` through `GAP-09`). Those gaps require their own enforcement and independent-verification evidence.

## 7. Readiness Matrix

| Criterion | Current assessment |
|---|---|
| Windows 11 ARM64 baseline | Observed; consistent with the documented Windows 11 ARM path, subject to version-label check before activation |
| Hyper-V on Windows 11 ARM | Documented by Arm |
| Linux ARM64 guest path on Windows 11 ARM | Documented by Arm |
| Ubuntu 24.04 Desktop ARM64 | Documented candidate for a future proof-of-boot test |
| CPU VM Monitor Mode property | `True` observed |
| Hypervisor / VBS | Previously observed active; not proof of VM creation capability |
| SLAT / firmware virtualization via CIM | Conflicting / unresolved |
| `Microsoft-Hyper-V-All` | Disabled |
| `VirtualMachinePlatform` | Disabled; not independently treated as a blocker for the cited Hyper-V path |
| Windows component store | `Repairable` |
| .NET rollup package | `InstallPending` |
| Windows Sandbox runtime | Not demonstrated |
| Hyper-V VM creation on this device | Not tested |
| Linux VM boot on this device | Not tested |
| Five-Agent workload capacity | Not tested |

## 8. Risks, Limitations, and Explicit Unknowns

1. Practical Hyper-V readiness of this exact SQ1 Surface Pro X remains unverified.
2. CIM virtualization properties remain unresolved against prior hypervisor/VBS observations; their ARM64 interpretation has not been established.
3. Windows servicing remains unresolved: the component store is `Repairable` and the identified .NET package remains `InstallPending`.
4. No successful Windows Sandbox runtime or Linux VM execution has been demonstrated.
5. No resource benchmark has been performed for concurrent Agents or multiple VMs.
6. No Linux-VM tests have been performed for network restriction, credential isolation, filesystem access control, evidence protection, or independent QC enforcement.
7. Documentation-based compatibility must not be represented as demonstrated capability on this device.
8. The first documented Ubuntu ARM64 path is a Desktop proof-of-boot example; an eventual Server/runtime image requires a separate compatibility and resource decision.

## 9. Actions Not Taken

The following actions were not performed as part of ID-15M:

- No `DISM /RestoreHealth` or `SFC /scannow` operation.
- No manual deletion or modification of `pending.xml`, servicing registry keys, or package state.
- No activation of Hyper-V or Virtual Machine Platform.
- No WSL installation or activation.
- No Docker installation.
- No Linux distribution download or VM creation.
- No API model call or independent Agent execution.
- No Production authorization.

## 10. Recommendation and Next Decision Gate

Retain **Hyper-V + Linux ARM64** as a conditional local candidate, while retaining **Windows User Isolation** as the present local baseline. Do not activate Hyper-V until a separate decision gate establishes that proceeding is justified and acceptable given the unresolved Windows servicing and host-capability evidence.

The next decision should select one of these paths:

1. Defer local VM execution until Windows servicing is independently assessed and any proposed repair is separately justified and expressly authorized.
2. Investigate a separate host/device for Linux VM feasibility, without changing the Surface Pro X.
3. Continue limited Windows-based baseline work while preserving Linux VM as an untested candidate.

If a later proof-of-boot test is approved, it must be limited to one VM and must not be treated as Agent execution or as proof that the TOS security gaps are closed. Any servicing repair, feature activation, installation, firmware change, network enablement, or VM creation requires explicit user authorization before execution.

## 11. Final Assessment Status

| Item | Final status |
|---|---|
| ID-15M analytical assessment | **COMPLETE** |
| Local Linux ARM64 VM feasibility | **CONDITIONALLY FEASIBLE** |
| Practical Hyper-V host readiness | **NOT ESTABLISHED** |
| Linux VM execution | **NOT DEMONSTRATED** |
| Runtime selection | **NOT MADE** |
| Production authorization | **NOT GRANTED** |
| Document | **FINAL — READY FOR GITHUB UPLOAD** |
| Stage closure | **NOT CLOSED — upload and explicit user confirmation pending** |

## 12. References

The following external documentation supports the platform-level statements. These sources establish a documented Windows-on-Arm Hyper-V path; none demonstrates execution on this specific Surface Pro X.

1. **Arm, “Hyper-V on Arm”** (last updated 22 June 2026). States that Hyper-V can create and run VMs on Windows 11 on Arm, requires Windows 11 version 22H2 or newer, and must be enabled on a physical computer.  
   <https://learn.arm.com/install-guides/hyper-v/>

2. **Arm, “Create a Linux virtual machine using Hyper-V.”** Describes Linux VMs on Windows on Arm, the Windows 11 22H2+ requirement, use of an Ubuntu 24.04 Arm ISO, manual VM creation, Generation 2, a memory recommendation, and image-specific Secure Boot guidance.  
   <https://learn.arm.com/learning-paths/laptops-and-desktops/hyper-v/hyper-v/>

3. **Microsoft Learn, “System Requirements for Hyper-V on Windows and Windows Server.”** Lists general Hyper-V host requirements, including SLAT, VM Monitor Mode extensions, and sufficient memory. The general page does not resolve how the Surface Pro X's CIM virtualization properties represent its ARM64 capabilities.  
   <https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows>

4. **Microsoft Learn, “Install Hyper-V.”** Describes Hyper-V installation on supported Windows editions and the optional-feature installation path. Its general Windows instructions are not a substitute for a device-specific ARM64 runtime test.  
   <https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/get-started/install-hyper-v>

---

**Document control note:** This document is final and prepared for repository upload. The ID-15M stage remains open until the user confirms that the final file has been uploaded to the designated GitHub location.
