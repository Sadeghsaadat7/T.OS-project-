# TOS RT-13 — User Namespace Separation Test Plan

**Document ID:** TOS-RT-13-UNS-TP-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:** FINAL — READY FOR USER REVIEW / GITHUB UPLOAD  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, Rootless  
**Target Image:** `quay.io/fedora/fedora:latest` (ARM64 image previously pulled and executed successfully)  
**Test Class:** Controlled, non-production runtime validation

---

## 1. Purpose

Determine whether two concurrently running rootless Podman containers can receive distinct Linux User Namespace instances and non-overlapping UID/GID mappings in the current Podman-on-WSL2 environment.

This test follows the earlier observation that the inspected containers had separate PID, Mount, and Network namespaces, but shared the same User Namespace ID and UID/GID mapping.

## 2. Scope

### In scope

- Confirm the Podman machine is running and the active connection remains Rootless.
- Confirm the previously used Fedora ARM64 image is already available locally.
- Start two short-lived containers using `--userns=auto:size=65536`.
- Capture each container's User Namespace identifier, UID map, GID map, effective identity, and Podman-inspected UserNS mode.
- Compare namespace identifiers and mapping ranges.
- Remove both test containers and verify cleanup.

### Out of scope

- Container-escape testing or kernel exploit testing.
- Rootful mode, privileged containers, or changes to Podman machine configuration.
- Changes to WSL, `.wslconfig`, Windows features, Registry, security policy, or host configuration.
- Mounting host paths or accessing user files from test containers.
- Production security certification or selection of a final TOS runtime.

## 3. Baseline Evidence

The following baseline facts were established in the preceding ID-15N runtime investigation:

- Podman Client and Server: 6.2.1.
- Windows client architecture and Linux guest architecture: ARM64.
- Podman machine provider: WSL2.
- Podman execution mode: Rootless.
- Fedora 45 ARM64 container execution succeeded previously.
- In the earlier two-container test, PID, Mount, and Network namespace identifiers differed, while User Namespace identifiers matched.
- The earlier rootless UID/GID mapping showed the invoking Linux user mapped to container UID/GID 0 and subordinate ID ranges mapped into the container.

These are baseline observations, not the result of RT-13.

## 4. Technical Basis

Podman's `--userns=auto` mode is documented to create a unique User Namespace and allocate subordinate UID/GID ranges. In rootless mode, Podman uses the ranges configured for the invoking user; a container using the full subordinate range without `auto` can prevent further subdivision. An explicit `size` can bound the requested range.

The test therefore uses `--userns=auto:size=65536` for both containers and verifies the effective namespace and mappings from inside each container. The namespace ID alone is not sufficient: UID and GID mapping ranges must also be captured and compared.

Reference: Podman `podman run` documentation, `--userns` and UID/GID mapping sections:  
https://docs.podman.io/en/latest/markdown/podman-run.1.html

## 5. Preconditions and Stop Conditions

Proceed only when all preconditions are satisfied:

1. `podman version` reports Client and Server 6.2.1.
2. `podman machine list` shows `podman-machine-default` running with WSL provider.
3. `podman info` confirms `rootless=true` and ARM64 architecture.
4. No container named `tos-rt13-a` or `tos-rt13-b` already exists.
5. The local image `quay.io/fedora/fedora:latest` exists. The test uses `--pull=never` and must not initiate a registry download.
6. No unrelated Podman containers are stopped, removed, or modified by this test.

Stop without changing system configuration if:

- the machine is not running or the active connection is not Rootless;
- the local image is missing;
- either test container name is already in use;
- Podman reports insufficient subordinate IDs, mapping allocation failure, or an unexpected privilege requirement;
- either container fails to start or the collected mapping data is incomplete.

Do not attempt to fix a failed precondition by changing `/etc/subuid`, `/etc/subgid`, Podman machine configuration, or WSL settings during this test.

## 6. Test Procedure

Commands are intended for **Windows PowerShell 5.1**. Run them in the normal PowerShell session used for the preceding Podman tests. Do not switch to the rootful Podman connection.

### RT-13.0 — Preflight

```powershell
podman version
podman machine list
podman system connection list
podman info --format 'Rootless={{.Host.Security.Rootless}}; Arch={{.Host.Arch}}'
podman image exists quay.io/fedora/fedora:latest
podman ps -a --filter name=tos-rt13-a
podman ps -a --filter name=tos-rt13-b
```

Expected preflight evidence:

- Client and Server version are 6.2.1.
- The default machine is running and uses WSL.
- Rootless is `true`; architecture is `arm64`.
- The image-exists command exits successfully.
- Neither test name is already present.

If the image-exists command fails, stop. Do not pull an image as part of this test plan.

### RT-13.1 — Create Container A

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt13-a --userns=auto:size=65536 quay.io/fedora/fedora:latest sleep 600
```

### RT-13.2 — Create Container B

```powershell
podman run -d --pull=never --arch arm64 --name tos-rt13-b --userns=auto:size=65536 quay.io/fedora/fedora:latest sleep 600
```

Both containers are deliberately idle. No host directories, ports, devices, or elevated privileges are requested.

### RT-13.3 — Capture Effective Identity and Namespace Data

Run the following separately for each container:

```powershell
podman exec tos-rt13-a sh -c 'echo "IDENTITY:"; id; echo "USER_NAMESPACE:"; readlink /proc/self/ns/user; echo "UID_MAP:"; cat /proc/self/uid_map; echo "GID_MAP:"; cat /proc/self/gid_map'
podman exec tos-rt13-b sh -c 'echo "IDENTITY:"; id; echo "USER_NAMESPACE:"; readlink /proc/self/ns/user; echo "UID_MAP:"; cat /proc/self/uid_map; echo "GID_MAP:"; cat /proc/self/gid_map'
```

Capture Podman's recorded mode for both:

```powershell
podman inspect --format 'Name={{.Name}}; UsernsMode={{.HostConfig.UsernsMode}}; Privileged={{.HostConfig.Privileged}}' tos-rt13-a
podman inspect --format 'Name={{.Name}}; UsernsMode={{.HostConfig.UsernsMode}}; Privileged={{.HostConfig.Privileged}}' tos-rt13-b
```

Capture current container state:

```powershell
podman ps --filter name=tos-rt13
```

### RT-13.4 — Cleanup

```powershell
podman rm -f tos-rt13-a tos-rt13-b
podman ps -a --filter name=tos-rt13
```

Expected cleanup evidence: neither test container remains. Do not remove any other container or image.

## 7. Evaluation Rules

### PASS

All of the following must be demonstrated:

1. Both containers start successfully in Rootless mode.
2. Both container inspect results show the requested `auto` UserNS mode and `Privileged=False`.
3. `/proc/self/ns/user` identifiers differ between Container A and Container B.
4. Both UID maps and both GID maps are present and readable.
5. The mapped parent-namespace UID ranges are non-overlapping between the two containers; the same is true for GID ranges.
6. Both containers are removed successfully and no RT-13 test container remains.

### PARTIAL PASS

Use PARTIAL PASS if the containers run and their User Namespace identifiers differ, but the mapping data is incomplete, overlaps unexpectedly, or cannot be interpreted sufficiently to establish independent UID/GID allocations.

### FAIL

Use FAIL if the requested `auto` mode cannot be created, both containers use the same User Namespace, the mappings demonstrate shared allocation, or the containers cannot run without changing out-of-scope host/runtime settings.

### BLOCKED

Use BLOCKED when a precondition is not satisfied or an environmental/tooling issue prevents the test from reaching a valid result. A blocked test is not evidence that the runtime itself failed.

## 8. Evidence Requirements

Retain the unedited PowerShell output for:

- preflight checks;
- both `podman run` commands and returned container IDs;
- identity, namespace ID, UID map, and GID map for each container;
- both `podman inspect` results;
- `podman ps` before and after cleanup.

Do not include private SSH keys, credentials, access tokens, or unrelated personal data in the evidence package.

## 9. Risks and Limitations

- This is a basic namespace-allocation test, not a container escape or hostile multi-tenant security test.
- Distinct User Namespace IDs and disjoint UID/GID mappings are evidence of namespace separation, not proof of complete isolation across all kernel resources.
- Successful RT-13 does not prove security suitability for Production or simultaneous hosting of five TOS Agents.
- Failure may reflect subordinate ID configuration, Podman remote behavior, WSL kernel/runtime limitations, or another environmental condition. Root cause must be recorded rather than assumed.
- The test is designed to avoid system-level changes; it does create and remove two temporary containers.

## 10. Decision Output

After execution, record:

- Result: PASS / PARTIAL PASS / FAIL / BLOCKED.
- User Namespace IDs for A and B.
- UID/GID map comparison result.
- Podman inspect result for each container.
- Cleanup result.
- Any error text and the point at which execution stopped.
- Conclusion limited strictly to the evidence collected.

No runtime-selection decision is made by this test.

## 11. Document Control

| Field | Value |
|---|---|
| Document status | FINAL — READY FOR USER REVIEW / GITHUB UPLOAD |
| Test execution | NOT STARTED |
| GitHub upload | NOT PERFORMED |
| User acceptance | PENDING |
| Scope | RT-13 User Namespace Separation only |
| Next stage | Execute RT-13 after document upload and user confirmation |

---

**End of document**
