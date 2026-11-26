# TOS RT-14 — Container Security Hardening Test Results Report

**Document ID:** TOS-RT-14-CSH-TR-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — ACCEPTED  
**Approved Test Plan:** `TOS_RT-14_Container_Security_Hardening_Test_Plan_v1.0_FINAL.md`  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, rootless  
**Test Image:** Existing local `quay.io/fedora/fedora:latest` (`linux/arm64`)  
**Test Class:** Controlled, non-production runtime validation

---

## 1. Executive Result

**Overall result: PARTIAL PASS — all executed hardening controls passed; evidence timestamp metadata is incomplete.**

The RT-14 execution demonstrated that the hardened container ran with all five observed Linux capability sets equal to zero, `NoNewPrivs=1`, Seccomp active (`Seccomp=2`, one filter), a read-only root filesystem, and a PID limit of 64 confirmed both by Podman inspection and the container's cgroup `pids.max` file. The container was non-privileged, had no capabilities added, and used the existing rootless Podman environment.

The `/etc` write probe was denied with `Read-only file system`; the `/tmp` probe succeeded, which is consistent with a read-only container root filesystem when a temporary writable location is provided. Both test containers were removed, their absence was verified, and the Podman machine remained running and reachable in rootless mode.

The overall classification is conservatively **PARTIAL PASS**, rather than full PASS, because the evidence was not accompanied by a separately captured timestamp for every individual command/evidence item as required by the approved plan. The test session date and the preflight timestamp are known, and outputs were preserved in the conversation, but missing per-step timestamps have not been reconstructed or invented. This is an evidence-record limitation; no tested hardening control was observed to fail.

This report is not a penetration-test result, production security certification, proof of container-escape resistance, or a TOS runtime-selection decision.

## 2. Scope and Method

The test used two temporary Fedora ARM64 containers:

- Baseline control: `tos-rt14-baseline`
- Hardened test: `tos-rt14-hardened`

The hardened container was launched with:

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt14-hardened --cap-drop=all --security-opt=no-new-privileges --read-only --pids-limit=64 quay.io/fedora/fedora:latest sleep 900
```

The baseline container was launched from the same existing local image without the RT-14 hardening options. No image pull was performed. No host mounts, published ports, privileged mode, added capabilities, global configuration changes, WSL changes, Podman-machine changes, or host LSM policy changes were made.

## 3. Environment and Preflight

### 3.1 Preflight evidence

Preflight timestamp supplied by the user: `2026-11-26T12:16:43.4337608+03:30`.

| Check | Observed value | Assessment |
|---|---|---|
| Host | `DESKTOP-HNHQ2K7` | Recorded |
| Podman Client | `6.2.1`, `windows/arm64` | PASS |
| Podman Server | `6.2.1`, `linux/arm64` | PASS |
| Podman Git commit | `3471162efb28760529d852706c493c29245def6d` | Recorded |
| Provider / machine | WSL; `podman-machine-default` running | PASS |
| Rootless | `true` | PASS |
| cgroup | Version 2; `pids` controller available | PASS |
| OCI runtime | `crun 1.31` | Recorded |
| Kernel | `6.18.58.1-microsoft-standard-WSL2` | Recorded |
| Local image | `quay.io/fedora/fedora:latest`, `linux/arm64` | PASS |
| Local image ID | `e838c6ec3829f921dd0345e04e301df6b3fa8017fad4d7e7c889697bb5800a58` | Recorded |
| Reserved names | Both test names available before creation | PASS |
| Preflight terminal marker | `PREFLIGHT_COMPLETE` | PASS |

No registry pull was needed or performed. The preflight timestamp is available as recorded above. Subsequent execution outputs were supplied in the conversation in sequence, but separate timestamps were not captured for each command/evidence item. This limitation is retained explicitly in the result classification; no missing times have been inferred.

### 3.2 Runtime security context

The preflight and final `podman info` both reported rootless operation and Seccomp enabled. Final security fields were:

- `rootless=true`
- `seccompEnabled=true`
- `apparmorEnabled=false`
- `selinuxEnabled=false`

The runtime feature description advertised AppArmor and SELinux support, but the effective host security fields reported both as disabled. These are distinct facts. AppArmor and SELinux activation/configuration were explicitly outside RT-14 scope; neither was enabled or modified.

## 4. Container Creation

Both containers were created successfully and observed in the `running` state.

| Container | Container ID | Creation / runtime result |
|---|---|---|
| `tos-rt14-baseline` | `db90bd69ab802306c84173e529640be6ab53318f7bd7f2b1cacefa65c79c4e29` | Created; running |
| `tos-rt14-hardened` | `731612b7912a1b0c4cfd91017beead86a7610ee8112e3db86e20e534c6831670` | Created; running |

The subsequent `podman ps --filter name=tos-rt14` output listed both containers as running.

## 5. Runtime Configuration Inspection

The following values were obtained from `podman inspect`:

| Setting | Baseline | Hardened | Assessment |
|---|---|---|---|
| `Privileged` | `false` | `false` | PASS |
| `CapAdd` | `[]` | `[]` | PASS — no capabilities added |
| `CapDrop` | `[]` | Listed 11 capability names | Effective state checked separately in Section 6 |
| `ReadonlyRootfs` | `false` | `true` | PASS — hardened configuration set |
| `PidsLimit` | `2048` | `64` | PASS — effective cgroup value checked separately |
| `SecurityOpt` | `[]` | `[no-new-privileges]` | PASS — process state checked separately |

The hardened `CapDrop` inspection value listed:

`CAP_CHOWN`, `CAP_DAC_OVERRIDE`, `CAP_FOWNER`, `CAP_FSETID`, `CAP_KILL`, `CAP_NET_BIND_SERVICE`, `CAP_SETFCAP`, `CAP_SETGID`, `CAP_SETPCAP`, `CAP_SETUID`, and `CAP_SYS_CHROOT`.

The inspection list is retained exactly as observed. The effective capability result is based on the process status values below, not on treating the list alone as proof.

## 6. Process Capability, NoNewPrivs, and Seccomp Evidence

The following values were read from `/proc/1/status` in both containers.

| Field | Baseline | Hardened | Hardened acceptance |
|---|---|---|---|
| `CapInh` | `0000000000000000` | `0000000000000000` | PASS |
| `CapPrm` | `00000000800405fb` | `0000000000000000` | PASS |
| `CapEff` | `00000000800405fb` | `0000000000000000` | PASS |
| `CapBnd` | `00000000800405fb` | `0000000000000000` | PASS |
| `CapAmb` | `0000000000000000` | `0000000000000000` | PASS |
| `NoNewPrivs` | `0` | `1` | PASS |
| `Seccomp` | `2` | `2` | PASS |
| `Seccomp_filters` | `1` | `1` | PASS |

**Assessment:** All five capability sets in the hardened container were zero. `NoNewPrivs=1` was observed. Seccomp remained active (`Seccomp=2`) with one filter. These are direct process-level observations and satisfy the corresponding RT-14 acceptance criteria.

## 7. Read-Only Root Filesystem Evidence

The hardened container's `/etc` write probe returned exit code `1` and the error:

`touch: cannot touch '/etc/rt14-root-write-probe': Read-only file system`

The result was classified as `ROOTFS_WRITE_BLOCKED — PASS`.

The `/tmp` write probe returned exit code `0` and was classified as `TMP_WRITE_ALLOWED`. The temporary probe file was subsequently removed successfully by the execution script. This does not contradict a read-only container root filesystem; `/tmp` may be a writable temporary filesystem location.

A subsequent Podman inspection reported:

`ReadonlyRootfs=true`

**Assessment:** PASS.

## 8. PID Limit Evidence

The two independent observations were:

| Evidence source | Observed value | Command exit | Assessment |
|---|---:|---:|---|
| `podman inspect` / `HostConfig.PidsLimit` | `64` | `0` | PASS |
| Container cgroup `/sys/fs/cgroup/pids.max` | `64` | `0` | PASS |

No process-exhaustion or stress test was performed, consistent with the approved scope.

**Assessment:** PASS — configured and effective cgroup limit both reported `64`.

## 9. Privilege and Host LSM Context

Final hardened-container inspection reported:

- `Privileged=false`
- `CapAdd=[]`
- `CapDrop` as listed in Section 5

Final `podman info` reported:

- `rootless=true`
- `seccompEnabled=true`
- `apparmorEnabled=false`
- `selinuxEnabled=false`

No AppArmor, SELinux, host LSM, seccomp profile, or global runtime configuration was modified. The disabled AppArmor/SELinux host status is a recorded limitation, not a failed RT-14 criterion under the approved plan.

## 10. Cleanup and Post-Test Verification

The pre-cleanup listing showed exactly the two RT-14 test containers. Both were removed:

- `tos-rt14-baseline`
- `tos-rt14-hardened`

The post-cleanup `podman ps -a --filter name=tos-rt14` listing contained no containers. Separate `podman container exists` checks reported:

- `Verified absent: tos-rt14-baseline`
- `Verified absent: tos-rt14-hardened`

The Podman machine remained `Currently running`. The final `podman info` command succeeded and reported `rootless=true`. Its container store reported `number: 0`, `running: 0`, and `stopped: 0`.

The final terminal marker was:

`RT14_CLEANUP_AND_POSTCHECK_COMPLETE`

**Assessment:** PASS — scoped cleanup complete; Podman machine and rootless service remained operational.

## 11. Acceptance Matrix

| Acceptance item | Result | Evidence section |
|---|---|---|
| Approved runtime and local image available; no pull required | PASS | 3 |
| Baseline and hardened containers created | PASS | 4 |
| Hardened container non-privileged; no capabilities added | PASS | 5, 9 |
| All five hardened capability sets equal zero | PASS | 6 |
| `NoNewPrivs=1` | PASS | 6 |
| Root filesystem read-only; `/etc` write blocked | PASS | 7 |
| PID limit 64 in Podman and cgroup | PASS | 8 |
| Seccomp active with at least one filter | PASS | 6, 9 |
| Cleanup and post-test runtime health | PASS | 10 |
| Per-evidence-item timestamps captured | PARTIAL / NOT COMPLETE | 1, 12 |
| AppArmor/SELinux active | Not tested / out of scope | 3, 9 |

## 12. Result Classification and Limitations

### Overall result: PARTIAL PASS

All functional hardening controls executed under RT-14 passed. There was no observed failure of the requested capability-drop, `no-new-privileges`, read-only root filesystem, PID-limit, or default Seccomp checks.

The approved plan required a timestamp to be preserved for every command/evidence item. The preflight timestamp was supplied, and the overall execution date is known, but individual timestamps for the subsequent execution steps were not captured in the presented evidence. This documentation gap is not repaired by assigning assumed times. Therefore the report uses PARTIAL PASS as the conservative overall result, even though all tested functional controls passed.

Other scope limitations:

- AppArmor and SELinux were not active in the effective host security context and were not enabled or configured.
- Seccomp was verified as active, but the completeness of the default profile was not audited.
- No container-escape, exploit, privilege-escalation, or destructive testing was performed.
- No CPU/memory enforcement or load/capacity test was performed; those belong to RT-15.
- The test does not establish production suitability, complete kernel isolation, TOS multi-agent capacity, or final runtime selection.

## 13. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — ACCEPTED |
| Test execution | COMPLETED — cleanup and post-test checks passed |
| Test result | PARTIAL PASS |
| GitHub upload | UPLOADED — VERIFIED ON MAIN |
| User acceptance | ACCEPTED — USER CONFIRMED |
| Next stage | RT-14 CLOSED; proceed to RT-15 planning |
| Scope | RT-14 Container Security Hardening only|

---

**End of document**
