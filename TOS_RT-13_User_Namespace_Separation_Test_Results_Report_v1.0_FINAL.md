# TOS RT-13 — User Namespace Separation Test Results Report

**Document ID:** TOS-RT-13-UNS-TR-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — PENDING USER ACCEPTANCE  
**Test Plan:** `TOS_RT-13_User_Namespace_Separation_Test_Plan_v1.0_FINAL.md`  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, Rootless  
**Test Class:** Controlled, non-production runtime validation

---

## 1. Executive Result

**Overall result: PARTIAL PASS**

The test demonstrated that two concurrently running rootless Podman containers had distinct Linux User Namespace identifiers and non-overlapping UID/GID mappings. Both containers were non-privileged and were successfully removed.

The test does **not** qualify for a full PASS against the approved test plan because `podman inspect` reported `UsernsMode=private` for both containers rather than explicitly reporting the requested `auto` mode. The observed namespace IDs and disjoint mappings demonstrate effective separation, but the inspect field discrepancy was not independently resolved. In addition, the planned pre-cleanup `podman ps` evidence was not captured.

This result is limited to the evidence collected. It is not a production security certification or a TOS runtime-selection decision.

## 2. Scope and Method

Two temporary Fedora ARM64 containers were created using:

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt13-a --userns=auto:size=65536 quay.io/fedora/fedora:latest sleep 600
podman run -d --pull=never --arch arm64 --name tos-rt13-b --userns=auto:size=65536 quay.io/fedora/fedora:latest sleep 600
```

The test used the pre-existing local image. No image pull, host-path mount, port publication, privileged mode, WSL configuration change, Podman machine change, or host security-policy change was performed.

## 3. Preflight Results

| Check | Observed result | Assessment |
|---|---|---|
| Podman Client | 6.2.1, `windows/arm64` | PASS |
| Podman Server | 6.2.1, `linux/arm64` | PASS |
| Podman machine | `podman-machine-default`, WSL, running | PASS |
| Default connection | `podman-machine-default`, rootless user connection | PASS |
| Rootless and architecture | `Rootless=true; Arch=arm64` | PASS |
| Local Fedora image | `podman image exists` exit code 0 | PASS |
| Test container names before creation | Neither name existed | PASS |

## 4. Container Creation Results

Both `podman run` commands completed successfully and returned container IDs.

| Container | Container ID | Creation result |
|---|---|---|
| `tos-rt13-a` | `41535198fa896e1a448bb7752a9cd689f419fb174a963c0bf84fdfbcaf8cc6cd` | PASS |
| `tos-rt13-b` | `e1213c0129e10c8542e9bd3f6a245caa3674a67e96bbc7393c1f0ed4cd09d4c9` | PASS |

Both commands explicitly included `--userns=auto:size=65536`.

## 5. Identity and Namespace Evidence

### Container A

| Field | Observed value |
|---|---|
| Identity | `uid=0(root) gid=0(root) groups=0(root)` |
| User Namespace | `user:[4026532230]` |
| UID map | `0 1 65536` |
| GID map | `0 1 65536` |

### Container B

| Field | Observed value |
|---|---|
| Identity | `uid=0(root) gid=0(root) groups=0(root)` |
| User Namespace | `user:[4026532309]` |
| UID map | `0 65537 65536` |
| GID map | `0 65537 65536` |

### Mapping comparison

The parent-namespace ranges implied by the captured maps are:

| Mapping | Container A | Container B | Overlap |
|---|---:|---:|---|
| UID | 1–65536 | 65537–131072 | No |
| GID | 1–65536 | 65537–131072 | No |

The namespace identifiers are distinct. Both UID and GID maps were readable, equal in requested length, and non-overlapping.

The `uid=0(root)` and `gid=0(root)` identities are container-local identities. The maps show that container ID 0 maps to parent-namespace ID 1 for A and 65537 for B; this output does not indicate host-root identity.

**Assessment:** Effective User Namespace separation and disjoint UID/GID allocations are demonstrated by the collected kernel namespace and mapping data.

## 6. Podman Inspect Results

The following output was captured for both containers:

```text
Name=tos-rt13-a; UsernsMode=private; Privileged=false
Name=tos-rt13-b; UsernsMode=private; Privileged=false
```

| Inspect field | A | B | Assessment |
|---|---|---|---|
| `UsernsMode` | `private` | `private` | Does not explicitly confirm `auto` |
| `Privileged` | `false` | `false` | PASS |

The test creation commands explicitly requested `--userns=auto:size=65536`, and both containers received distinct namespace IDs and disjoint UID/GID ranges. However, the inspect field reports `private`, not `auto`.

The available evidence does not establish why Podman exposes `private` in this inspect field for these containers. This discrepancy must not be interpreted as proof that the `auto` request was ignored, nor may it be silently treated as confirmation that the inspect criterion passed.

**Assessment:** Non-privileged status is confirmed. The inspect-mode criterion in the approved test plan is not fully verified.

## 7. Cleanup

The following cleanup command completed successfully:

```powershell
podman rm -f tos-rt13-a tos-rt13-b
```

The command returned both names:

```text
tos-rt13-a
tos-rt13-b
```

The subsequent command:

```powershell
podman ps -a --filter name=tos-rt13
```

returned only the table header and no containers.

**Cleanup assessment: PASS.** Both RT-13 test containers were removed, and no container matching the RT-13 name filter remained.

## 8. Acceptance Criteria Assessment

| Approved-plan criterion | Evidence | Result |
|---|---|---|
| Both containers start in Rootless mode | Rootless preflight; both containers created | PASS |
| Inspect confirms requested `auto` mode and `Privileged=False` | Inspect reports `private` and `false` for both | PARTIAL — `Privileged=False` confirmed; `auto` not explicitly confirmed |
| User Namespace identifiers differ | A `4026532230`; B `4026532309` | PASS |
| Both UID/GID maps are readable | All four maps captured | PASS |
| Parent-namespace UID/GID ranges do not overlap | A 1–65536; B 65537–131072 | PASS |
| Both containers are removed | Removal command and empty post-cleanup listing | PASS |

### Evidence completeness limitation

The approved plan also called for a `podman ps --filter name=tos-rt13` capture before cleanup. That command was not run before the containers were removed. Creation success and subsequent exec/inspect evidence establish that both containers were available during the test, but the specific pre-cleanup listing is absent.

### Outcome-category interpretation

The approved plan's PARTIAL PASS definition focuses on incomplete or overlapping UID/GID mapping evidence. The observed mapping evidence in this run is complete and non-overlapping. However, the PASS definition separately requires `podman inspect` to confirm the requested `auto` mode, and that condition was not met by the captured output (`UsernsMode=private`). The plan does not explicitly define this exact combination of results.

To avoid upgrading an unmet PASS criterion or asserting an unproven FAIL, this report uses **PARTIAL PASS as a conservative, explicitly documented classification pending resolution of the inspect-mode discrepancy**. This is a classification limitation in the test plan and should be considered when interpreting the result.

## 9. Final Assessment and Limitations

**Final result: PARTIAL PASS**

What this test establishes:

- Podman successfully created two rootless Fedora ARM64 test containers using commands that requested `--userns=auto:size=65536`.
- The containers had different User Namespace identifiers.
- Their UID and GID mappings were independently allocated and non-overlapping.
- Both containers were non-privileged.
- Both test containers were successfully removed.

What this test does not establish:

- It does not resolve why `podman inspect` reported `UsernsMode=private` rather than `auto`.
- It does not satisfy every evidence item in the approved plan, because the pre-cleanup `podman ps` capture is missing.
- It does not prove complete isolation across all kernel resources, resistance to container escape, production suitability, or capacity for five simultaneous TOS Agents.
- It does not select or approve a final TOS runtime.

No host, WSL, Podman machine, subordinate-ID, or security-policy configuration was changed during this test.

## 10. Recommended Follow-up

Before treating the `auto` mode inspect criterion as resolved, conduct a narrowly scoped follow-up review of Podman's effective user-namespace configuration and inspect/API semantics for this exact Podman 6.2.1 remote WSL environment. Do not modify `/etc/subuid`, `/etc/subgid`, machine configuration, or WSL settings as part of that review.

Any additional test should have its own approved scope and evidence requirements. The current RT-13 result should remain PARTIAL PASS unless the discrepancy is resolved with reproducible evidence.

## 11. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — PENDING USER ACCEPTANCE |
| Test execution | COMPLETED |
| Test result | PARTIAL PASS |
| GitHub upload | PENDING |
| User acceptance | PENDING |
| Scope | RT-13 User Namespace Separation only |
| Next stage | User review and acceptance; then GitHub upload and verification |

---

**End of document**
