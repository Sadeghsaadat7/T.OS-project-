# TOS RT-15 — Resource Enforcement Test Results Report

**Document ID:** TOS-RT-15-RE-TR-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — READY FOR USER ACCEPTANCE  
**Approved Test Plan:** `TOS_RT-15_Resource_Enforcement_Test_Plan_v1.0_FINAL.md`  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, rootless  
**Test Image:** Existing local `quay.io/fedora/fedora:latest` (`linux/arm64`); no pull performed  
**Test Class:** Controlled, non-production cgroup resource-limit validation

---

## 1. Executive Result

**Overall result: PASS**

RT-15 verified that Podman accepted the requested per-container CPU and memory limits and that the corresponding cgroup v2 values inside the limited container matched the approved target values exactly:

- CPU: `cpu.max = 50000 100000`, representing a quota of 50,000 microseconds per 100,000-microsecond period (0.5 CPU equivalent).
- Memory: `memory.max = 268435456` bytes (256 MiB).

Podman inspection independently reported `CpuQuota=50000`, `CpuPeriod=100000`, and `Memory=268435456` for the limited container. The unmodified baseline container reported no explicit memory or CPU quota (`Memory=0`, `CpuQuota=0`). Both containers started successfully using the existing local ARM64 image. Cleanup removed both RT-15 containers, verified their absence, and confirmed that the Podman machine remained running, reachable, and rootless.

All required evidence blocks included timestamps, and the supplied outputs preserve the timestamps for preflight, container creation, configuration inspection, cgroup read-back, runtime statistics, and cleanup. The acceptance criteria for PASS in the approved test plan were met.

This PASS is limited to per-container resource-limit configuration and cgroup v2 read-back under the tested Podman rootless WSL2 environment. It does not claim that CPU throttling was dynamically exercised or that the memory hard limit/OOM behavior was tested; those activities were explicitly excluded from the approved test.

## 2. Scope and Method

The approved RT-15 scope was limited to CPU and memory resource enforcement at the per-container cgroup level. The test used two temporary Fedora ARM64 containers:

- Baseline control: `tos-rt15-baseline`
- Resource-limited test: `tos-rt15-limited`

The baseline container was created without explicit CPU quota or memory limit options. The limited container was created with:

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt15-limited --cpu-period=100000 --cpu-quota=50000 --memory=256m quay.io/fedora/fedora:latest sleep 900
```

No image pull, privileged mode, host mount, published port, host/WSL/Podman-machine configuration change, cgroup-parent change, or global resource-setting change was performed. No stress, benchmark, CPU saturation, memory-pressure, or OOM test was performed.

## 3. Preflight Results

**Preflight interval:** `2026-11-26T12:46:18.5157984+03:30` to `2026-11-26T12:46:24.5649331+03:30`

| Check | Observed result | Assessment |
|---|---|---|
| Podman Client | 6.2.1, `windows/arm64` | PASS |
| Podman Server | 6.2.1, `linux/arm64` | PASS |
| Podman machine | `podman-machine-default`, WSL, currently running | PASS |
| Rootless operation | `true` | PASS |
| cgroup version | `v2` | PASS |
| CPU controller | Present in controller list | PASS |
| Memory controller | Present in controller list | PASS |
| Local image | `quay.io/fedora/fedora:latest`, Linux ARM64 | PASS |
| Local image ID | `e838c6ec3829f921dd0345e04e301df6b3fa8017fad4d7e7c889697bb5800a58` | Recorded |
| Reserved test names | Both available before creation | PASS |
| Preflight completion marker | `PREFLIGHT_COMPLETE` | PASS |

The Podman server reported Fedora 45 on ARM64, kernel `6.18.58.1-microsoft-standard-WSL2`, cgroup v2, and the `cpu`, `memory`, and `pids` controllers. No preflight blocker was observed.

## 4. Container Creation Results

### 4.1 Baseline container

- Creation timestamp: `2026-11-26T12:47:22.4391300+03:30`
- Container ID: `b9deebefea1641b468fad181f716c42caaf0f2a7be7b49f3bf0194a35f6db645`
- Observed state: `running`
- Inspection timestamp: `2026-11-26T12:47:24.1472077+03:30`
- Creation assessment: PASS

### 4.2 Resource-limited container

- Creation timestamp: `2026-11-26T12:47:24.1482978+03:30`
- Container ID: `7ecc8c78d7d99596cfe45bcde19c9019a56109efa8fcf571e0f50f9fabc6e83e`
- Observed state: `running`
- Completion timestamp: `2026-11-26T12:47:26.1587810+03:30`
- Creation assessment: PASS

Both containers appeared in the RT-15 container listing with status `Up`. The limited container was created successfully using rootless Podman and without privilege escalation or system configuration changes.

## 5. Podman Resource Configuration Results

**Inspection interval:** `2026-11-26T12:48:06.6604142+03:30` to `2026-11-26T12:48:07.6674292+03:30`

| Parameter | Baseline observed | Limited observed | Limited target | Assessment |
|---|---:|---:|---:|---|
| `HostConfig.Memory` | `0` | `268435456` | `268435456` bytes | PASS |
| `HostConfig.CpuQuota` | `0` | `50000` | `50000` | PASS |
| `HostConfig.CpuPeriod` | `0` | `100000` | `100000` | PASS |

The inspection results confirm that the baseline had no explicit memory or CPU quota configured, while the limited container contained the exact approved values. The CPU quota is an aggregate bandwidth ceiling equivalent to 0.5 CPU; it is not CPU pinning or a reservation of a dedicated processor.

## 6. Effective cgroup v2 Read-back

**Read-back interval:** `2026-11-26T12:48:48.7387402+03:30` to `2026-11-26T12:48:52.5664215+03:30`

| cgroup file | Expected value | Observed value | Command exit code | Assessment |
|---|---|---|---:|---|
| `/sys/fs/cgroup/cpu.max` | `50000 100000` | `50000 100000` | 0 | PASS |
| `/sys/fs/cgroup/memory.max` | `268435456` | `268435456` | 0 | PASS |

Both effective cgroup v2 limit files were readable inside the limited container and returned the exact requested values. This provides the required read-back evidence in addition to Podman's HostConfig inspection.

### 6.1 Informational cgroup observations

| Item | Observed output | Interpretation |
|---|---|---|
| `memory.current` | `3297280` bytes | Informational current usage only; not a limit test |
| `cpu.stat` `usage_usec` | `87946` | Informational usage counter |
| `cpu.stat` `nr_periods` | `9` | Informational scheduler-period counter |
| `cpu.stat` `nr_throttled` | `0` | No throttling observed during this idle, non-stress test |
| `cpu.stat` `throttled_usec` | `0` | No throttling time observed during this idle, non-stress test |

The absence of throttling is expected for the low-activity `sleep` workload and does not indicate that the configured quota failed. No CPU contention was generated.

## 7. Podman Runtime Statistics

**Statistics interval:** `2026-11-26T12:49:18.9098126+03:30` to `2026-11-26T12:49:20.0981868+03:30`

Both `podman stats --no-stream` commands completed with exit code 0.

| Metric | Baseline | Limited |
|---|---:|---:|
| CPU | `0.05%` | `0.08%` |
| Memory usage | `831.5 kB` | `794.6 kB` |
| Memory denominator shown in stats output | `8.035 GB` | `268.4 MB` |
| PIDs | `1` | `1` |
| Stats command exit code | `0` | `0` |

These statistics are supplemental, point-in-time observations only. In particular, the `8.035 GB` denominator displayed for the baseline is not treated as a per-container limit: Podman inspection reported `HostConfig.Memory=0`, which is the recorded evidence that no explicit per-container memory limit was configured. The `memory.current` cgroup value and Podman `MEM USAGE` figures are retained as separate informational metrics; they are not assumed to be identical accounting measures and are not compared as an acceptance criterion. Stats output is not used in place of Podman inspection or cgroup v2 read-back as proof of the configured limits.

## 8. Cleanup and Post-Test Verification

**Cleanup interval:** `2026-11-26T12:49:51.8658180+03:30` to `2026-11-26T12:50:18.3153421+03:30`

| Check | Observed result | Assessment |
|---|---|---|
| Pre-cleanup listing | Only the two named RT-15 test containers shown in the filtered listing | Recorded |
| Remove `tos-rt15-baseline` | Removal command returned the container name | PASS |
| Remove `tos-rt15-limited` | Removal command returned the container name | PASS |
| Post-cleanup listing | No RT-15 containers listed | PASS |
| Baseline absence check | `Verified absent: tos-rt15-baseline` | PASS |
| Limited absence check | `Verified absent: tos-rt15-limited` | PASS |
| Podman machine | `podman-machine-default`, WSL, currently running | PASS |
| Rootless status | `Rootless=true` | PASS |
| Podman reachability | `podman info` completed successfully | PASS |
| Completion marker | `RT15_CLEANUP_AND_POSTCHECK_COMPLETE` | PASS |

Only the two reserved RT-15 test container names were removed. The local image and Podman machine were retained. No unrelated container was targeted.

## 9. Evidence Register

| Evidence ID | Evidence item | Timestamp coverage | Result |
|---|---|---|---|
| RT15-E01 | Preflight: version, machine, rootless/cgroup/controller status, image identity, name-collision checks | `2026-11-26T12:46:18.5157984+03:30`–`2026-11-26T12:46:24.5649331+03:30` | Complete |
| RT15-E02 | Baseline container creation and inspection | `2026-11-26T12:47:22.4391300+03:30`–`2026-11-26T12:47:24.1472077+03:30` | Complete |
| RT15-E03 | Limited container creation and running-state confirmation | `2026-11-26T12:47:24.1482978+03:30`–`2026-11-26T12:47:26.1587810+03:30` | Complete |
| RT15-E04 | Podman HostConfig resource values | `2026-11-26T12:48:06.6604142+03:30`–`2026-11-26T12:48:07.6674292+03:30` | Complete |
| RT15-E05 | Effective `cpu.max` and `memory.max` read-back | `2026-11-26T12:48:48.7387402+03:30`–`2026-11-26T12:48:52.5664215+03:30` | Complete |
| RT15-E06 | `memory.current`, `cpu.stat`, and Podman stats | `2026-11-26T12:48:48.7387402+03:30`–`2026-11-26T12:48:52.5664215+03:30`; `2026-11-26T12:49:18.9098126+03:30`–`2026-11-26T12:49:20.0981868+03:30` | Complete; informational |
| RT15-E07 | Cleanup, container-absence checks, machine status, and post-test Podman info | `2026-11-26T12:49:51.8658180+03:30`–`2026-11-26T12:50:18.3153421+03:30` | Complete |

All required evidence items were supplied with timestamp coverage. No missing timestamp was reconstructed or inferred.

## 10. Acceptance Criteria and Result Classification

The approved plan's PASS criteria are met:

- Preflight confirmed the expected Podman 6.2.1 client/server, rootless operation, cgroup v2, CPU and memory controllers, local ARM64 image, and available test names.
- Both baseline and resource-limited containers started successfully using the existing local image.
- Podman inspection reported the exact requested CPU quota, period, and memory limit for the limited container.
- The limited container's cgroup v2 `cpu.max` and `memory.max` files returned the exact requested values.
- Cleanup removed both test containers and verified their absence; the machine remained running and reachable in rootless mode.
- Required evidence items and timestamps were preserved.

### Overall result: PASS

This PASS means the requested per-container CPU and memory limits were accepted and the exact configured values were read back from Podman and cgroup v2. It does not mean CPU throttling or the memory hard-limit/OOM path was dynamically exercised.

## 11. Limitations

- No CPU saturation, benchmark, stress, contention, or throttling-generation test was performed.
- No memory allocation near or beyond 256 MiB was attempted; OOM and OOM-kill behavior were not tested.
- `memory.swap.max` and total memory-plus-swap behavior were not configured or verified.
- cgroup v2 is hierarchical. Stricter ancestor limits may further constrain the container; this test did not inventory or quantify all ancestor limits.
- Podman stats and current usage values are point-in-time informational observations, not proof of enforcement.
- This test does not validate global WSL/Podman-machine resource allocation, production readiness, workload performance under contention, simultaneous TOS Agent capacity, or final runtime selection.

## 12. References

- Approved RT-15 Test Plan: `TOS_RT-15_Resource_Enforcement_Test_Plan_v1.0_FINAL.md`
- Podman 6.2.0 `podman-run` documentation (versioned reference for the 6.2.x runtime): https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
- Podman 6.2.0 `podman-stats` documentation: https://docs.podman.io/en/v6.2.0/markdown/podman-stats.1.html
- Linux kernel cgroup v2 documentation (`cpu.max`, `memory.max`): https://docs.kernel.org/admin-guide/cgroup-v2.html

## 13. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — READY FOR USER ACCEPTANCE |
| Test execution | COMPLETED — cleanup and post-test checks passed |
| Test result | PASS — within approved RT-15 scope |
| GitHub upload | NOT UPLOADED — READY FOR UPLOAD |
| User acceptance | PENDING USER CONFIRMATION |
| Scope | RT-15 per-container CPU and memory enforcement only |
| Next stage | User acceptance → GitHub upload → verify on `main` → user confirms upload → RT-15 closure |

---

**End of document**
