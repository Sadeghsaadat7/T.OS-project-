# TOS RT-14 — Container Security Hardening Test Plan

**Document ID:** TOS-RT-14-CSH-TP-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — ACCEPTED  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, rootless  
**Target Image:** Existing local `quay.io/fedora/fedora:latest` image; pull prohibited  
**Test Class:** Controlled, non-production runtime validation

---

## 1. Purpose

Evaluate whether selected container-level hardening controls can be configured and observably enforced in the current rootless Podman 6.2.1 / WSL2 / ARM64 test environment. The test is limited to disposable containers and does not change host, WSL, Podman-machine, or global security configuration.

## 2. Scope

### In scope

1. Drop all Linux capabilities from the hardened container.
2. Enable and verify `no-new-privileges`.
3. Enable a read-only container root filesystem and verify expected write behavior.
4. Configure and verify a per-container PID limit of 64 using Podman inspection and the visible cgroup value, where available.
5. Verify the default seccomp filter remains active.
6. Record privileged mode, rootless state, AppArmor/SELinux availability, and relevant runtime details.
7. Verify cleanup of all RT-14 test containers.

### Out of scope

- Enabling or configuring AppArmor, SELinux, host LSM policy, or custom seccomp profiles.
- Changing Podman machine, WSL, `.wslconfig`, kernel, cgroup, subordinate-ID, or host settings.
- Installing packages, pulling images, editing `containers.conf`, or changing global defaults.
- Container escape attempts, exploit testing, privilege-escalation payloads, or destructive tests.
- CPU/memory enforcement and load/capacity testing; these belong to RT-15.
- Production certification, final runtime selection, or proof of five-agent capacity.

## 3. Known Baseline and Constraints

The preceding RT-13 execution established Podman Client/Server 6.2.1, Windows ARM64 client, Linux ARM64 server, WSL provider, rootless operation, cgroup v2, and a local Fedora ARM64 image. Previous inspection reported seccomp enabled, AppArmor disabled, and SELinux disabled. These are historical baseline facts and must be rechecked during RT-14 preflight rather than assumed unchanged.

The RT-13 user-namespace inspection discrepancy remains unresolved. RT-14 must not change user-namespace settings or attempt to resolve that discrepancy. Capture the effective user-namespace mode as context only.

## 4. Safety and Change-Control Rules

- Execute only after the user approves this plan.
- Use Windows PowerShell 5.1. Commands below are intended for the Windows PowerShell prompt connected to the existing Podman machine.
- Use only the existing local image with `--pull=never`; do not contact a registry.
- Use unique names `tos-rt14-baseline` and `tos-rt14-hardened`. Do not remove or modify containers with other names.
- Do not use `--privileged`, host mounts, host networking, published ports, added capabilities, or external volumes.
- Do not change host or global configuration. If a command proposes or requires such a change, stop and report it.
- Do not conduct PID exhaustion or stress testing. RT-14 checks the configured cgroup limit without attempting to reach it.
- Capture command, complete output, exit code, and timestamp for each evidence item. Redact secrets if any unexpectedly appear.
- If a test container cannot be created or behaves unexpectedly, stop further test execution, collect diagnostic output, and perform only the scoped cleanup.

## 5. Preconditions and Preflight

Run in Windows PowerShell 5.1:

```powershell
$ErrorActionPreference = 'Stop'
$Image = 'quay.io/fedora/fedora:latest'
$Names = @('tos-rt14-baseline', 'tos-rt14-hardened')

'===== DATE / HOST CONTEXT ====='
Get-Date -Format o
$env:COMPUTERNAME

'===== PODMAN VERSION ====='
podman version
if ($LASTEXITCODE -ne 0) { throw 'Podman version check failed.' }

'===== PODMAN INFO ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman info check failed.' }

'===== PODMAN MACHINE ====='
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Podman machine list failed.' }

'===== LOCAL IMAGE ====='
podman image exists $Image
if ($LASTEXITCODE -ne 0) { throw 'Required local image is missing; stop. Do not pull it.' }
podman image inspect --format 'ID={{.Id}}; OS={{.Os}}; Architecture={{.Architecture}}' $Image
if ($LASTEXITCODE -ne 0) { throw 'Local image inspection failed.' }

'===== TEST-NAME COLLISION CHECK ====='
foreach ($Name in $Names) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "Container name already exists: $Name. Do not remove it; stop and report." }
    if ($CheckExit -ne 1) { throw "Could not reliably check container name: $Name (exit $CheckExit). Stop and report." }
}
'PREFLIGHT_COMPLETE'
```

**Preflight acceptance:** Podman client/server are reachable; the existing machine is running and rootless; local image exists and reports ARM64; neither reserved container name is already in use. Record the actual versions and image ID shown. If any requirement is unmet, classify the test as BLOCKED and do not create containers.

## 6. Controlled Test Procedure

### 6.1 Create an unmodified baseline control

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt14-baseline $Image sleep 900
if ($LASTEXITCODE -ne 0) { throw 'Baseline container creation failed.' }
```

### 6.2 Create the hardened test container

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt14-hardened --cap-drop=all --security-opt=no-new-privileges --read-only --pids-limit=64 $Image sleep 900
if ($LASTEXITCODE -ne 0) { throw 'Hardened container creation failed. Stop, capture the error, and clean up the baseline container.' }
```

No explicit seccomp option is supplied: the test deliberately evaluates the runtime's existing default seccomp confinement and verifies that it remains enabled.

### 6.3 Capture runtime configuration

```powershell
'===== BASELINE INSPECT ====='
podman inspect --format 'Name={{.Name}}; Privileged={{.HostConfig.Privileged}}; CapAdd={{.HostConfig.CapAdd}}; CapDrop={{.HostConfig.CapDrop}}; ReadonlyRootfs={{.HostConfig.ReadonlyRootfs}}; PidsLimit={{.HostConfig.PidsLimit}}; SecurityOpt={{.HostConfig.SecurityOpt}}' tos-rt14-baseline
if ($LASTEXITCODE -ne 0) { throw 'Baseline container inspection failed.' }

'===== HARDENED INSPECT ====='
podman inspect --format 'Name={{.Name}}; Privileged={{.HostConfig.Privileged}}; CapAdd={{.HostConfig.CapAdd}}; CapDrop={{.HostConfig.CapDrop}}; ReadonlyRootfs={{.HostConfig.ReadonlyRootfs}}; PidsLimit={{.HostConfig.PidsLimit}}; SecurityOpt={{.HostConfig.SecurityOpt}}' tos-rt14-hardened
if ($LASTEXITCODE -ne 0) { throw 'Hardened container inspection failed.' }
```

Record the complete output. Inspection output is configuration evidence; it does not, by itself, prove that a kernel control is effective.

### 6.4 Verify process security state and seccomp

```powershell
'===== BASELINE PROCESS SECURITY STATE ====='
podman exec tos-rt14-baseline sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs|Seccomp|Seccomp_filters):" /proc/1/status'
if ($LASTEXITCODE -ne 0) { throw 'Could not read baseline process security state.' }

'===== HARDENED PROCESS SECURITY STATE ====='
podman exec tos-rt14-hardened sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs|Seccomp|Seccomp_filters):" /proc/1/status'
if ($LASTEXITCODE -ne 0) { throw 'Could not read hardened process security state.' }
```

Expected hardened observations:

- `CapInh`, `CapPrm`, `CapEff`, `CapBnd`, and `CapAmb` are all zeroes after `--cap-drop=all`. Record the exact hexadecimal values for both baseline and hardened containers.
- `NoNewPrivs` is `1`.
- `Seccomp` is `2` and `Seccomp_filters` is at least `1`.

If one or more capability fields are unavailable in `/proc/1/status`, record the affected capability check as unverified rather than inventing a result. A non-zero value in any of the five capability sets for the hardened process must be explicitly assessed against the requested `--cap-drop=all` control.

### 6.5 Verify read-only root filesystem

```powershell
podman exec tos-rt14-hardened sh -c 'if touch /etc/rt14-root-write-probe 2>/dev/null; then echo ROOTFS_WRITE_ALLOWED; rm -f /etc/rt14-root-write-probe; else echo ROOTFS_WRITE_BLOCKED; fi; if touch /tmp/rt14-tmp-write-probe 2>/dev/null; then echo TMP_WRITE_ALLOWED; rm -f /tmp/rt14-tmp-write-probe; else echo TMP_WRITE_BLOCKED; fi'
if ($LASTEXITCODE -ne 0) { throw 'Read-only filesystem probe command failed.' }
```

Expected result: `ROOTFS_WRITE_BLOCKED`. `/tmp` may remain writable because Podman's read-only mode normally provides temporary writable tmpfs locations by default. Record the actual result; do not treat `/tmp` writability alone as a failure. The root filesystem must also report `ReadonlyRootfs=true` in inspection.

### 6.6 Verify PID-limit configuration

```powershell
'===== PID LIMIT FROM PODMAN INSPECT ====='
podman inspect --format 'PidsLimit={{.HostConfig.PidsLimit}}' tos-rt14-hardened
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect configured PID limit.' }

'===== PID LIMIT FROM CONTAINER CGROUP ====='
podman exec tos-rt14-hardened sh -c 'if test -r /sys/fs/cgroup/pids.max; then cat /sys/fs/cgroup/pids.max; else echo PID_MAX_NOT_VISIBLE; fi'
if ($LASTEXITCODE -ne 0) { throw 'Could not execute PID-limit cgroup check.' }
```

Expected result: Podman inspection reports `64`; where the cgroup file is visible, it reports `64`. If the file is absent or inaccessible, preserve the output and classify effective cgroup enforcement as unverified (PARTIAL PASS unless another check independently demonstrates failure). If the file is readable and reports `max` or a numeric value other than `64`, classify the effective limit as failed. Do not spawn processes to approach the limit.

### 6.7 Verify privileged and LSM context

```powershell
'===== PRIVILEGE / CAPABILITY CONFIGURATION ====='
podman inspect --format 'Privileged={{.HostConfig.Privileged}}; CapAdd={{.HostConfig.CapAdd}}; CapDrop={{.HostConfig.CapDrop}}' tos-rt14-hardened
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect privilege/capability configuration.' }

'===== HOST SECURITY CONTEXT ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Could not read host security context.' }
```

Expected configuration: `Privileged=false`, no capabilities added, and all capabilities requested to be dropped. Record seccomp, AppArmor, and SELinux availability exactly as reported by `podman info`. AppArmor/SELinux being disabled is a host/runtime limitation to document, not a reason to modify or enable them during this test.

## 7. Cleanup and Post-Test Verification

Run after evidence collection, including when a test fails:

```powershell
'===== REMOVE ONLY RT-14 TEST CONTAINERS ====='
foreach ($Name in @('tos-rt14-baseline', 'tos-rt14-hardened')) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) {
        podman rm -f $Name
        if ($LASTEXITCODE -ne 0) { throw "Cleanup failed for $Name. Stop and report." }
    }
    elseif ($CheckExit -eq 1) {
        Write-Output "Already absent: $Name"
    }
    else {
        throw "Could not determine whether $Name exists (exit $CheckExit). Stop and report."
    }
}

'===== VERIFY RT-14 CONTAINERS ABSENT ====='
podman ps -a --filter name=tos-rt14
if ($LASTEXITCODE -ne 0) { throw 'Could not list containers for cleanup verification.' }
foreach ($Name in @('tos-rt14-baseline', 'tos-rt14-hardened')) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "RT-14 container still exists after cleanup: $Name" }
    if ($CheckExit -ne 1) { throw "Could not verify RT-14 container absence: $Name (exit $CheckExit)" }
    Write-Output "Verified absent: $Name"
}

'===== VERIFY MACHINE STILL RUNNING ====='
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Could not verify Podman machine status.' }

'===== VERIFY ROOTLESS / SERVER REACHABILITY ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman did not remain reachable after cleanup.' }
```

Acceptance: both RT-14 containers are absent; Podman remains reachable; the existing machine remains running; no unrelated containers were touched. Record cleanup output and exit codes. If a container does not exist, record that fact; do not broaden cleanup to other names.

## 8. Evidence Register

The execution record must include:

| Evidence ID | Required evidence |
|---|---|
| RT14-E01 | Date/time, host context, Podman version, machine status, Podman info |
| RT14-E02 | Local image ID, OS/architecture, and name-collision check |
| RT14-E03 | Baseline and hardened container creation outputs and IDs |
| RT14-E04 | Full `podman inspect` output for both containers |
| RT14-E05 | Baseline and hardened `/proc/1/status` security fields |
| RT14-E06 | Read-only filesystem probe output |
| RT14-E07 | Podman PIDsLimit inspection and cgroup `pids.max` output |
| RT14-E08 | Privileged/capability configuration and LSM context |
| RT14-E09 | Cleanup output, absence check, and post-test machine/Podman status |

For every command, preserve the exact command, output, exit code, and timestamp. Do not mark an evidence item PASS when its output is missing.

## 9. Acceptance Criteria and Result Classification

### PASS

A PASS requires all applicable hardened-container checks to be evidenced:

- Container starts with the requested hardening options.
- `Privileged=false`; no capabilities are added; all five capability sets (`CapInh`, `CapPrm`, `CapEff`, `CapBnd`, and `CapAmb`) are zero for the hardened test process.
- `NoNewPrivs=1`.
- Root filesystem inspection reports read-only and the `/etc` write probe is blocked.
- Podman reports a PIDs limit of 64 and the visible cgroup value also reports 64. If the cgroup value is not visible, this item is not a full PASS; a readable value other than 64 is a failed control.
- Seccomp remains active (`Seccomp=2`, with at least one filter).
- Cleanup succeeds and the runtime remains operational.

### PARTIAL PASS

Use PARTIAL PASS when the principal hardening options are accepted and some effective controls are verified, but one or more items cannot be verified due to an environment, visibility, or tooling limitation, without evidence that a requested control was ineffective.

### FAIL

Use FAIL if a requested control is explicitly rejected or shown ineffective, such as any non-zero capability set remaining after `--cap-drop=all`, `NoNewPrivs` remaining `0`, root filesystem writes succeeding despite read-only configuration, a contradictory effective PID limit, or seccomp unexpectedly being disabled in the hardened container.

### BLOCKED

Use BLOCKED if preflight cannot establish the approved test environment or the required local image/container runtime is unavailable. Do not proceed to container creation in that case.

AppArmor/SELinux being unavailable is a recorded limitation and does not independently force FAIL because enabling either is outside the approved scope.

## 10. Stop Conditions

Stop execution immediately if:

- Podman reports a different server/runtime than the approved target without an explained, previously authorized change.
- The local image is missing or would require pulling.
- Either reserved test name is already occupied.
- A test requires changing host, WSL, Podman-machine, global configuration, or LSM policy.
- A command unexpectedly requests privileged execution, broad host access, or an external mount.
- A container or process outside the RT-14 names appears to be affected.

Collect diagnostic evidence and perform only scoped RT-14 cleanup. Do not improvise alternate commands that expand scope.

## 11. Limitations

This is a bounded configuration and runtime-observation test, not a penetration test. It does not prove resistance to container escape, complete kernel isolation, production security, application compatibility under every workload, or TOS multi-agent capacity. It does not test CPU/memory enforcement. The default seccomp profile is checked for active use, not independently audited for completeness. AppArmor/SELinux remain outside the test's configuration authority.

## 12. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — ACCEPTED |
| Test execution | NOT STARTED |
| Test result | NOT YET ASSESSED |
| GitHub upload | Upload and verify this accepted, frozen version on `main`; upload verification is tracked as a separate stage gate |
| User acceptance | ACCEPTED — USER CONFIRMED |
| Next stage | Upload this version to GitHub and verify on `main`; only then begin RT-14 execution |

---

**End of document**

## References

- Podman 6.2.0 `podman-run` documentation (versioned reference for the 6.2.x runtime): https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
- Podman 6.2.0 `podman-container-inspect` documentation: https://docs.podman.io/en/v6.2.0/markdown/podman-container-inspect.1.html
- Linux kernel `no_new_privs` documentation: https://docs.kernel.org/userspace-api/no_new_privs.html
