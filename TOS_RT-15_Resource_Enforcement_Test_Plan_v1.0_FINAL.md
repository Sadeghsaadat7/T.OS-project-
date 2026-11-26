# TOS RT-15 — Resource Enforcement Test Plan

**Document ID:** TOS-RT-15-RE-TP-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — ACCEPTED  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, rootless  
**Target Image:** Existing local `quay.io/fedora/fedora:latest` image; pull prohibited  
**Test Class:** Controlled, non-production cgroup resource-limit validation

---

## 1. Purpose

Evaluate whether per-container CPU and memory resource limits can be configured and observed as effective in the current rootless Podman 6.2.1 / WSL2 / ARM64 environment using cgroup v2.

This test is limited to disposable containers and bounded configuration/read-back checks. It does not change host, WSL, Podman-machine, or global resource configuration and does not deliberately drive a container into CPU saturation or memory exhaustion.

## 2. Scope

### In scope

1. Reconfirm Podman client/server version, rootless operation, machine status, cgroup v2, and availability of CPU and memory controllers.
2. Create an unmodified baseline container using the existing local Fedora ARM64 image.
3. Create a test container with an explicit CPU quota equivalent to 0.5 CPU (`cpu.max` target `50000 100000`).
4. Apply a hard memory limit of 256 MiB (`memory.max` target `268435456` bytes).
5. Compare Podman inspection metadata with the cgroup v2 values visible inside the limited container.
6. Record current usage/statistics as informational evidence only; do not treat idle usage as proof of the configured limits.
7. Remove only RT-15 test containers and verify the Podman machine remains running and reachable.

### Out of scope

- CPU saturation, benchmark, stress, load, or throttling-generation tests.
- Memory allocation near or beyond the limit, OOM triggering, OOM-kill testing, or swap-exhaustion tests.
- Simultaneous multi-agent capacity testing or five-agent scheduling.
- WSL `.wslconfig`, Podman machine CPU/RAM sizing, cgroup parent, global defaults, or host resource changes.
- CPU pinning (`--cpuset-cpus`), CPU shares/weights, memory reservation/high, swap-limit tuning, block I/O limits, network limits, and device I/O limits. The 256 MiB target applies to the cgroup `memory.max` hard limit only; total memory-plus-swap behavior is not evaluated.
- Production suitability, final runtime selection, or proof of application performance under resource contention.

## 3. Known Baseline and Constraints

The preceding RT-14 execution confirmed Podman Client/Server 6.2.1, Windows ARM64 client, Linux ARM64 server, WSL provider, rootless operation, cgroup v2, and the local Fedora ARM64 image. The reported cgroup controllers included `cpu`, `memory`, and `pids`. These are historical facts and must be rechecked during RT-15 preflight.

The `podman machine list` output previously displayed 2 CPUs and 4 GiB. Those machine-list values are not accepted as evidence of independent per-container limits. RT-15 evaluates per-container cgroup controls only. No global WSL or Podman machine limits will be changed.

## 4. Resource Limit Values and Interpretation

| Resource | Requested limit | Expected cgroup v2 value | Meaning |
|---|---:|---|---|
| CPU | 0.5 CPU equivalent | `cpu.max = 50000 100000` | Up to 50,000 µs of fair-scheduler CPU time per 100,000 µs period |
| Memory | 256 MiB | `memory.max = 268435456` | Hard memory ceiling in bytes |

The CPU setting uses an explicit period and quota rather than relying on an implicit default. In cgroup v2, `cpu.max` is expressed as `MAX PERIOD`; `50000 100000` therefore permits up to 50,000 microseconds of fair-scheduler CPU time in each 100,000-microsecond period. This is a bandwidth limit equivalent to 0.5 CPU in aggregate; it does not pin the container to a particular physical/logical CPU.

The memory check confirms the configured cgroup `memory.max` hard limit. The test deliberately does not attempt to allocate memory up to the limit because reaching the cgroup hard limit can invoke the cgroup OOM mechanism and would add unnecessary risk. The test does not set or verify `memory.swap.max` or total memory-plus-swap behavior.

## 5. Safety and Change-Control Rules

- Execute only after the user approves this plan.
- Use Windows PowerShell 5.1. Do not run PowerShell as Administrator unless the approved procedure explicitly requires it; this plan does not.
- Use only the existing local image with `--pull=never`; do not contact a registry.
- Use unique names `tos-rt15-baseline` and `tos-rt15-limited`. Do not remove or modify containers with other names.
- Do not use `--privileged`, host mounts, host networking, published ports, added capabilities, or external volumes.
- Do not change host, WSL, Podman-machine, `.wslconfig`, cgroup, global configuration, or system security settings.
- Do not run stress tools, CPU busy loops, memory allocators, OOM tests, benchmarks, or workload simulations.
- Capture the exact command, complete output, exit code, and timestamp for each evidence item. Each execution block below emits a timestamp; preserve the complete block output.
- If a resource limit cannot be applied because the rootless environment rejects the operation, stop; do not switch to rootful mode or change permissions/settings.
- If a test container cannot be created or behaves unexpectedly, stop further test execution, collect diagnostic output, and perform only the scoped cleanup.

## 6. Preconditions and Preflight

Run in Windows PowerShell 5.1:

```powershell
$ErrorActionPreference = 'Stop'
$Image = 'quay.io/fedora/fedora:latest'
$Names = @('tos-rt15-baseline', 'tos-rt15-limited')

'===== RT-15 PREFLIGHT TIMESTAMP ====='
Get-Date -Format o

'===== PODMAN VERSION ====='
podman version
if ($LASTEXITCODE -ne 0) { throw 'Podman version check failed.' }

'===== PODMAN INFO ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman info check failed.' }

'===== ROOTLESS / CGROUP VERSION / CONTROLLER CHECKS ====='
$Rootless = podman info --format '{{.Host.Security.Rootless}}'
if ($LASTEXITCODE -ne 0) { throw 'Could not read Podman rootless status.' }
"$Rootless".Trim()
if ("$Rootless".Trim() -ne 'true') { throw 'Podman is not operating rootless; stop.' }

$CgroupVersion = podman info --format '{{.Host.CgroupsVersion}}'
if ($LASTEXITCODE -ne 0) { throw 'Could not read cgroup version.' }
"CgroupsVersion=$($CgroupVersion.Trim())"
if ("$CgroupVersion".Trim() -ne 'v2') { throw 'cgroup v2 is not active; stop.' }

$Controllers = podman info --format '{{.Host.CgroupControllers}}'
if ($LASTEXITCODE -ne 0) { throw 'Could not read cgroup controller list.' }
"CgroupControllers=$($Controllers -join ' ')"
$ControllerText = $Controllers | Out-String
if ($ControllerText -notmatch '\bcpu\b') { throw 'CPU cgroup controller is unavailable; stop.' }
if ($ControllerText -notmatch '\bmemory\b') { throw 'Memory cgroup controller is unavailable; stop.' }

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
    Write-Output "Available: $Name"
}

'PREFLIGHT_COMPLETE'
Get-Date -Format o
```

**Preflight acceptance:** Podman Client/Server are reachable and match the approved 6.2.1 target; the existing machine is running and rootless; cgroup v2 is active; `cpu` and `memory` controllers are listed; the local image exists and reports ARM64; neither reserved container name is occupied. If a required controller is absent or any requirement is unmet, classify the test as BLOCKED and do not create containers.

## 7. Controlled Test Procedure

### 7.1 Create an unmodified baseline control

```powershell
'===== RT-15 BASELINE CREATION TIMESTAMP ====='
Get-Date -Format o

podman run -d --pull=never --arch arm64 --name tos-rt15-baseline quay.io/fedora/fedora:latest sleep 900
if ($LASTEXITCODE -ne 0) { throw 'Baseline container creation failed. Stop and report.' }

podman inspect --format 'ID={{.Id}}; State={{.State.Status}}' tos-rt15-baseline
if ($LASTEXITCODE -ne 0) { throw 'Baseline container inspection failed.' }

Get-Date -Format o
```

### 7.2 Create the resource-limited test container

The requested CPU quota is explicitly defined as 50,000 microseconds per 100,000-microsecond period. The requested memory ceiling is 256 MiB.

```powershell
'===== RT-15 LIMITED CONTAINER CREATION TIMESTAMP ====='
Get-Date -Format o

podman run -d --pull=never --arch arm64 --name tos-rt15-limited --cpu-period=100000 --cpu-quota=50000 --memory=256m quay.io/fedora/fedora:latest sleep 900
if ($LASTEXITCODE -ne 0) {
    Write-Output 'LIMITED CONTAINER CREATION FAILED'
    podman rm -f tos-rt15-baseline
    throw 'Resource-limited container creation failed. Baseline cleanup attempted. Stop and report.'
}

podman inspect --format 'ID={{.Id}}; State={{.State.Status}}' tos-rt15-limited
if ($LASTEXITCODE -ne 0) { throw 'Limited container inspection failed. Stop and report.' }

podman ps --filter name=tos-rt15
if ($LASTEXITCODE -ne 0) { throw 'RT-15 container status check failed.' }

Get-Date -Format o
```

Both containers must report `running`. A permission/unsupported-resource error is evidence about the tested rootless environment; do not retry using rootful mode or alter system settings.

### 7.3 Capture Podman resource configuration

```powershell
'===== RT-15 PODMAN RESOURCE INSPECT TIMESTAMP ====='
Get-Date -Format o

'===== BASELINE RESOURCE CONFIGURATION ====='
podman inspect --format 'Name={{.Name}}; Memory={{.HostConfig.Memory}}; CpuQuota={{.HostConfig.CpuQuota}}; CpuPeriod={{.HostConfig.CpuPeriod}}' tos-rt15-baseline
if ($LASTEXITCODE -ne 0) { throw 'Baseline resource inspection failed.' }

'===== LIMITED RESOURCE CONFIGURATION ====='
podman inspect --format 'Name={{.Name}}; Memory={{.HostConfig.Memory}}; CpuQuota={{.HostConfig.CpuQuota}}; CpuPeriod={{.HostConfig.CpuPeriod}}' tos-rt15-limited
if ($LASTEXITCODE -ne 0) { throw 'Limited resource inspection failed.' }

Get-Date -Format o
```

Expected limited-container metadata: `Memory=268435456`, `CpuQuota=50000`, and `CpuPeriod=100000`. The baseline should show no explicit memory limit (`Memory=0`) and no CPU quota (`CpuQuota=0`) in its HostConfig. `CpuPeriod` alone may contain a default period and does not indicate an active CPU quota without a non-zero quota. Preserve actual output; do not infer missing values.

### 7.4 Verify effective cgroup v2 limits inside the limited container

```powershell
'===== RT-15 CGROUP LIMIT READBACK TIMESTAMP ====='
Get-Date -Format o

'===== LIMITED CPU MAX ====='
podman exec tos-rt15-limited cat /sys/fs/cgroup/cpu.max
if ($LASTEXITCODE -ne 0) { throw 'Could not read limited-container cpu.max.' }

'===== LIMITED MEMORY MAX ====='
podman exec tos-rt15-limited cat /sys/fs/cgroup/memory.max
if ($LASTEXITCODE -ne 0) { throw 'Could not read limited-container memory.max.' }

'===== LIMITED MEMORY CURRENT (INFORMATIONAL) ====='
podman exec tos-rt15-limited cat /sys/fs/cgroup/memory.current
if ($LASTEXITCODE -ne 0) { Write-Output 'memory.current unavailable; record as informational evidence limitation.' }

'===== LIMITED CPU STAT (INFORMATIONAL) ====='
podman exec tos-rt15-limited cat /sys/fs/cgroup/cpu.stat
if ($LASTEXITCODE -ne 0) { Write-Output 'cpu.stat unavailable; record as informational evidence limitation.' }

Get-Date -Format o
```

Expected effective values:

- `cpu.max`: `50000 100000`.
- `memory.max`: `268435456`.

`memory.current` and `cpu.stat` are context only. No increase in throttling or memory usage is expected from the idle `sleep` process, and absence of such activity is not a failure.

### 7.5 Capture current runtime statistics (informational)

```powershell
'===== RT-15 PODMAN STATS TIMESTAMP ====='
Get-Date -Format o

'===== BASELINE STATS (INFORMATIONAL) ====='
podman stats --no-stream tos-rt15-baseline
if ($LASTEXITCODE -ne 0) { Write-Output 'Baseline stats unavailable; retain prior inspect and cgroup evidence.' }

'===== LIMITED CONTAINER STATS (INFORMATIONAL) ====='
podman stats --no-stream tos-rt15-limited
if ($LASTEXITCODE -ne 0) { Write-Output 'Limited-container stats unavailable; retain prior inspect and cgroup evidence.' }

Get-Date -Format o
```

Stats output is supporting evidence only. It does not prove a limit by itself and must not replace Podman inspection or cgroup read-back.

## 8. Cleanup and Post-Test Verification

Run after evidence collection, including when a test fails:

```powershell
'===== RT-15 CLEANUP TIMESTAMP ====='
Get-Date -Format o

foreach ($Name in @('tos-rt15-baseline', 'tos-rt15-limited')) {
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

'===== VERIFY RT-15 CONTAINERS ABSENT ====='
podman ps -a --filter name=tos-rt15
if ($LASTEXITCODE -ne 0) { throw 'Could not list containers for cleanup verification.' }

foreach ($Name in @('tos-rt15-baseline', 'tos-rt15-limited')) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "RT-15 container still exists after cleanup: $Name" }
    if ($CheckExit -ne 1) { throw "Could not verify RT-15 container absence: $Name (exit $CheckExit)" }
    Write-Output "Verified absent: $Name"
}

'===== VERIFY PODMAN MACHINE ====='
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Could not verify Podman machine status.' }

'===== VERIFY PODMAN ROOTLESS / REACHABILITY ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman did not remain reachable after cleanup.' }

'RT15_CLEANUP_AND_POSTCHECK_COMPLETE'
Get-Date -Format o
```

**Cleanup acceptance:** Both RT-15 containers are absent; Podman remains reachable; the existing machine remains running and rootless; no unrelated containers were touched.

## 9. Evidence Register

| Evidence ID | Required evidence |
|---|---|
| RT15-E01 | Timestamped preflight: Podman version, machine status, rootless/cgroup v2/controllers, local image ID/architecture, name-collision checks |
| RT15-E02 | Timestamped baseline container creation output and ID |
| RT15-E03 | Timestamped resource-limited container creation output and ID |
| RT15-E04 | Podman HostConfig resource values for baseline and limited containers |
| RT15-E05 | Limited-container `cpu.max` and `memory.max` values |
| RT15-E06 | `memory.current`, `cpu.stat`, and Podman stats, if available (informational only) |
| RT15-E07 | Timestamped cleanup output, container-absence checks, machine status, and post-test Podman info |

For every evidence item, preserve the complete command output and the timestamp(s) emitted by its block. Preserve error output and exit codes. Do not mark missing evidence as PASS.

## 10. Acceptance Criteria and Result Classification

### PASS

A PASS requires all applicable checks to be evidenced:

- Preflight confirms Podman 6.2.1, rootless operation, cgroup v2, CPU and memory controllers, local ARM64 image, and free test names.
- Baseline and limited containers start successfully using the local image only.
- Podman inspection for the limited container reports `CpuQuota=50000`, `CpuPeriod=100000`, and `Memory=268435456`.
- The limited container's visible cgroup values report `cpu.max = 50000 100000` and `memory.max = 268435456`.
- Cleanup succeeds, both test containers are absent, and the existing machine remains running and reachable in rootless mode.
- Required evidence items and timestamps are preserved.

A PASS here means the requested per-container limits were accepted and the exact configured values were read back from Podman and cgroup v2. It does not mean throttling or OOM enforcement was dynamically exercised; those actions are explicitly prohibited by this plan.

### PARTIAL PASS

Use PARTIAL PASS when Podman accepts the requested limits and configuration evidence is present, but one effective cgroup file cannot be read due to a demonstrated visibility/tooling limitation, while no contradictory value is observed. State exactly which effective control remains unverified. Missing both Podman and cgroup evidence is not a PASS.

### FAIL

Use FAIL if a requested CPU or memory limit is explicitly rejected by Podman despite the preflight prerequisites being satisfied, if inspect reports values different from those requested, or if a readable cgroup file reports a contradictory value (for example `cpu.max` is `max 100000` or a different quota/period, or `memory.max` is `max` or another value).

### BLOCKED

Use BLOCKED if preflight cannot establish the approved target environment, cgroup v2, required CPU/memory controllers, the local image, or clear test-name availability. Do not proceed to container creation in that case.

## 11. Stop Conditions

Stop execution immediately if:

- Podman reports a different client/server or machine state than the approved target without an explained, previously authorized change.
- cgroup v2 or the CPU/memory controllers are not available.
- The local image is missing or would require pulling.
- Either reserved test name is already occupied.
- Rootless resource-limit configuration is denied or requires privilege escalation, permission changes, or system configuration changes.
- A command unexpectedly requests privileged execution, broad host access, or an external mount.
- A container or process outside the RT-15 names appears to be affected.
- The test requires memory pressure, OOM, benchmark, stress, or unapproved workload execution to continue.

Collect diagnostic evidence and perform only scoped RT-15 cleanup. Do not improvise alternate commands that expand scope.

## 12. Limitations

This test validates per-container CPU and memory cgroup limit configuration and read-back in the current Podman rootless WSL2 environment. Because cgroup v2 is hierarchical, stricter limits inherited from parent cgroups may further restrict a container; this test does not inventory or quantify all ancestor limits. It does not measure workload performance, prove throttling under CPU contention, deliberately exercise the memory hard limit, or prove behavior under memory pressure. It does not validate global WSL/Podman-machine resource allocation, production readiness, or simultaneous TOS Agent capacity. Those questions require separate, approved tests.

## 13. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — ACCEPTED |
| Test execution | NOT STARTED |
| Test result | NOT YET ASSESSED |
| GitHub upload | UPLOADED — VERIFIED ON MAIN |
| User acceptance | ACCEPTED — USER CONFIRMED |
| Scope | RT-15 per-container CPU and memory enforcement only |
| Next stage |RT-15 execution — preflight|

---

## References

- Podman 6.2.0 `podman-run` documentation (versioned reference for the 6.2.x runtime): https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
- Podman 6.2.0 `podman-stats` documentation: https://docs.podman.io/en/v6.2.0/markdown/podman-stats.1.html
- Linux kernel cgroup v2 documentation (`cpu.max`, `memory.max`): https://docs.kernel.org/admin-guide/cgroup-v2.html

---

**End of document**
