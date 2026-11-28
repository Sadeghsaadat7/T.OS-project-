# TOS RT-17 — Shared-Volume DAC Isolation Under Distinct User Namespaces

**Document ID:** TOS-RT-17-SVDAI-TP-001  
**Version:** 1.0  
**Date:** 2026-11-28  
**Status:** FINAL — READY FOR USER REVIEW / GITHUB UPLOAD  
**Assessment Track:** Local Execution Environment Feasibility — Agent Identity & Permission Isolation  
**Target Runtime:** Podman 6.2.1 / WSL2 / Linux ARM64 / rootless  
**Target Image:** Existing local `quay.io/fedora/fedora:latest` (`linux/arm64`); image pull prohibited  
**Test Class:** Controlled, synthetic, non-production runtime validation  
**Authority:** TOS Human Authority / System Owner

---

## 1. Purpose

RT-17 evaluates whether Linux discretionary access control (DAC) mode bits on a single Podman-managed named volume prevent two container workload identities from reading, writing, or renaming each other's private workspaces when:

- Both workload containers attach the same named volume read/write.
- Both workload processes use the same numeric in-container UID/GID (`1000:1000`).
- Each workload container runs in a distinct rootless Podman User Namespace with a non-overlapping UID/GID mapping.
- A separate, non-workload initializer owns the shared volume root directory. Its mapping is distinct from both workload mappings.
- Each workload's private workspace uses POSIX mode `0700`, with its canary file set to `0600`.
- Both workload processes have all Linux capability sets dropped and `NoNewPrivs=1`.

The central question is whether the kernel enforces reciprocal file and directory access restrictions even though both workload containers mount the same read/write volume.

RT-17 is a narrowly scoped follow-up to RT-16. RT-16 demonstrated non-visibility because each private volume was attached to only one container; it did not test DAC denial when the same volume is mounted into both containers.

## 2. Scope

### 2.1 In scope

1. Verify three distinct User Namespaces: one volume-initializer namespace and two workload namespaces.
2. Verify all three UID maps and GID maps are readable and their parent-namespace ranges are pairwise non-overlapping.
3. Run both workload processes under the same numeric in-container identity, UID/GID `1000:1000`.
4. Verify the same named volume is attached read/write to both workload containers.
5. Initialize the shared root directory as mode `1777`, owned by the separate initializer identity, so the sticky bit applies to both workload identities.
6. Create one private workspace per workload on that shared volume, with directory mode `0700` and canary-file mode `0600`.
7. Verify each workload can read its own canary (positive controls).
8. Verify reciprocal cross-workspace reads and writes are denied by `0700` directory permissions, and verify reciprocal reads/appends to standalone `0600` files are denied independently of private-directory traversal.
9. Verify neither workload can rename the other workload's top-level workspace entry in the sticky shared root.
10. Confirm both workload processes have zero values in all five capability sets and `NoNewPrivs=1`.
11. Perform controlled cleanup and verify all RT-17 resources are absent.

### 2.2 Explicitly out of scope

- Extended POSIX ACL entries (`setfacl` / `getfacl`); this test covers owner/group/other mode-bit DAC only.
- SELinux, AppArmor, custom seccomp profiles, or host LSM configuration.
- Host bind mounts, shared host directories, Podman API access, host-user compromise, or administrative bypass.
- Container escape, kernel exploit, hostile penetration testing, or denial-of-service.
- Network access, tool/API authorization, Agent orchestration, cross-Run lifecycle, Evidence Package security, or production secrets.
- Production readiness, final runtime selection, or authorization to execute TOS Agents.
- Changes to `/etc/subuid`, `/etc/subgid`, WSL, Podman machine, host security policy, or global Podman configuration.

A successful RT-17 result establishes only the tested named-volume DAC behavior under the exact runtime, mapping, ownership, and permission configuration. It does not prove complete Agent isolation or a comprehensive security boundary.

## 3. Basis and Known Findings

### 3.1 Prior test results

- **RT-13:** Distinct User Namespace identifiers and non-overlapping UID/GID mappings were demonstrated for two rootless containers. The overall result remains **PARTIAL PASS** because `podman inspect` reported `UsernsMode=private` rather than explicitly confirming `auto`, and a planned pre-cleanup listing was not captured.
- **RT-14:** Selected container hardening controls were demonstrated, including zero capability sets, `NoNewPrivs=1`, a read-only root filesystem, a PID limit, and active seccomp. The overall result remains **PARTIAL PASS** because per-evidence timestamps were incomplete.
- **RT-15:** Configured per-container CPU and memory limits were verified through Podman inspection and cgroup v2 read-back. Result: **PASS within its defined scope**.
- **RT-16:** Root filesystem separation, private-volume attachment non-visibility, and shared read-only-volume behavior were demonstrated. Test and cleanup results: **PASS**. The user accepted the final report and RT-16 is CLOSED.

These results provide context only. RT-17 must independently recheck the runtime and identity preconditions; prior results are not substitutes for current evidence.

### 3.2 Technical basis

Podman 6.2.0 documentation (the versioned reference for this 6.2.x runtime) describes `--userns=auto` as allocating unique User Namespaces with distinct subordinate UID/GID ranges. It also documents that, when an eligible empty named volume is first mounted, Podman adjusts the volume mount-point ownership to match the container process UID/GID as mapped through that container's User Namespace. The `:U` option performs a recursive ownership change and is excluded from this test.

RT-17 uses a dedicated initializer container as the first volume user. The initializer's root identity owns the volume root; its User Namespace range remains allocated while both workload tests run. Because the initializer, workload A, and workload B have pairwise non-overlapping mappings, neither workload identity owns the shared root directory. The shared root's sticky bit can therefore restrict both workloads from renaming or removing the other workload's top-level workspace entry.

Linux `user_namespaces(7)` explains that file permission checks compare credentials after mapping them into the initial User Namespace, and identifies file-related capabilities that can bypass permission checks. RT-17 drops all capabilities from the workload containers and records the effective process capability state rather than relying only on command-line options.

## 4. Test Resources and Reserved Names

| Resource type | Reserved name / value | Purpose |
|---|---|---|
| Initializer container | `tos-rt17-init` | First mount and neutral ownership of the shared root |
| Workload container A | `tos-rt17-a` | First test identity and volume attachment |
| Workload container B | `tos-rt17-b` | Second test identity and volume attachment |
| Named volume | `tos-rt17-shared` | Same read/write volume attached to initializer and both workloads |
| Image | `quay.io/fedora/fedora:latest` | Existing local Linux ARM64 image |
| Mount path | `/tos-shared` | Shared volume path inside all three containers |
| Workload UID/GID | `1000:1000` | Same numeric identity in A and B |
| Initializer UID/GID | `0:0` | Separate root identity in its own User Namespace; not a workload identity |
| Initializer-owned shared root | `/tos-shared`, mode `1777` | World-writable shared parent with sticky-bit enforcement |
| Agent A workspace | `/tos-shared/agent-a` | Private directory, mode `0700` |
| Agent B workspace | `/tos-shared/agent-b` | Private directory, mode `0700` |
| Agent A canary | `RT17_PRIVATE_A` | Synthetic file, mode `0600` |
| Agent B canary | `RT17_PRIVATE_B` | Synthetic file, mode `0600` inside B workspace |
| Agent A file-mode probe | `/tos-shared/agent-a-file-0600` | Shared-root canary, mode `0600`; independently tests file read/write bits |
| Agent B file-mode probe | `/tos-shared/agent-b-file-0600` | Shared-root canary, mode `0600`; independently tests file read/write bits |

No other container, volume, image, host path, account, or persistent resource may be created or modified for RT-17.

## 5. Safety and Change-Control Rules

1. Execution is **NOT AUTHORIZED by this plan**. The user must separately accept the final plan, verify its upload to `main`, and explicitly authorize RT-17 execution.
2. Use Windows PowerShell 5.1 in a non-elevated session.
3. Use only the default rootless Podman connection and the existing `podman-machine-default` WSL machine.
4. Use only the existing local Fedora ARM64 image with `--pull=never`; do not pull or build images.
5. Use only the four reserved RT-17 resource names: three containers and one volume.
6. Do not use rootful Podman, `sudo`, `--privileged`, host networking, published ports, host bind mounts, devices, or external volumes.
7. All three containers must use `--network=none`.
8. All three containers must use `--userns=auto:size=65536`, `--cap-drop=all`, `--security-opt=no-new-privileges`, and `--read-only`. The initializer runs as `--user=0:0`; workload A and B run as `--user=1000:1000`.
9. The volume must be a Podman-managed named volume. Do not use `:U`, `idmap`, `:z`, `:Z`, or any ownership-remapping/relabeling option. Do not manually `chown` the volume or change host filesystem ownership.
10. The initializer must be the first container to mount the new empty volume and must remain running throughout the test. This keeps its automatically allocated User Namespace range reserved and prevents either workload mapping from becoming the volume-root owner.
11. Create workload A and B only after the initializer's ownership and `1777` shared-root mode have been read back and recorded.
12. Do not modify `/etc/subuid`, `/etc/subgid`, WSL settings, Podman machine settings, host configuration, or global Podman configuration.
13. Capture each command, complete output, exit code, and timestamp. Never reconstruct missing timestamps.
14. If an unauthorized cross-workspace read, write, or rename succeeds, stop all remaining access probes immediately, preserve evidence, and perform only scoped cleanup.
15. Do not use broad `podman prune` commands or remove unrelated resources.

## 6. Preconditions and Preflight

Run in Windows PowerShell 5.1 only after execution is separately authorized.

```powershell
$ErrorActionPreference = 'Stop'
$Image = 'quay.io/fedora/fedora:latest'
$ContainerNames = @('tos-rt17-init', 'tos-rt17-a', 'tos-rt17-b')
$VolumeName = 'tos-rt17-shared'

'===== RT-17 DATE / HOST CONTEXT ====='
Get-Date -Format o
$env:COMPUTERNAME
$PSVersionTable.PSVersion

'===== ELEVATION CHECK ====='
$Identity = [Security.Principal.WindowsIdentity]::GetCurrent()
$Principal = New-Object -TypeName System.Security.Principal.WindowsPrincipal -ArgumentList $Identity
$IsElevated = $Principal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
"ELEVATED=$IsElevated"
if ($IsElevated) { throw 'PowerShell is elevated. Close this shell and use a non-elevated PowerShell 5.1 session.' }

'===== PODMAN VERSION ====='
podman version
if ($LASTEXITCODE -ne 0) { throw 'Podman version check failed.' }

'===== PODMAN MACHINE ====='
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Podman machine check failed.' }

'===== PODMAN INFO ====='
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman info check failed.' }

'===== LOCAL IMAGE CHECK ====='
podman image exists $Image
if ($LASTEXITCODE -ne 0) { throw 'Required local image is missing. Do not pull it; stop.' }
podman image inspect --format 'ID={{.Id}}; OS={{.Os}}; Architecture={{.Architecture}}' $Image
if ($LASTEXITCODE -ne 0) { throw 'Local image inspection failed.' }

'===== CONTAINER NAME COLLISION CHECK ====='
foreach ($Name in $ContainerNames) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "Reserved container name already exists: $Name. Do not remove it; stop." }
    if ($CheckExit -ne 1) { throw "Could not reliably check container name: $Name (exit $CheckExit). Stop." }
}

'===== VOLUME NAME COLLISION CHECK ====='
podman volume exists $VolumeName
$VolumeCheckExit = $LASTEXITCODE
if ($VolumeCheckExit -eq 0) { throw "Reserved volume already exists: $VolumeName. Do not remove it; stop." }
if ($VolumeCheckExit -ne 1) { throw "Could not reliably check volume name (exit $VolumeCheckExit). Stop." }

'RT17_PREFLIGHT_COMPLETE'
```

**Preflight acceptance:** PowerShell is 5.1 and not elevated; Podman client/server are reachable; the existing WSL machine is running; rootless mode is confirmed; server architecture and local image architecture are ARM64; the exact image is present locally; all three reserved container names and the reserved volume name are absent. If any condition fails, classify the run as BLOCKED and create no RT-17 resources.

## 7. Controlled Test Procedure

### 7.1 Create the named volume

```powershell
podman volume create tos-rt17-shared
if ($LASTEXITCODE -ne 0) { throw 'RT-17 named-volume creation failed.' }
podman volume inspect tos-rt17-shared
if ($LASTEXITCODE -ne 0) { throw 'RT-17 named-volume inspection failed.' }
```

Record the full volume inspection output. It must identify the newly created Podman-managed named volume. Do not mount it manually from a host path.

### 7.2 Create the volume initializer — first volume attachment

```powershell
podman run -d --pull=never --arch arm64 --userns=auto:size=65536 --user=0:0 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --name tos-rt17-init --volume tos-rt17-shared:/tos-shared:rw,nocopy quay.io/fedora/fedora:latest sleep 1800
if ($LASTEXITCODE -ne 0) { throw 'Initializer creation failed. Stop and perform scoped cleanup.' }
```

Capture the returned container ID and running state. The initializer must remain running until the end of the test; do not remove or restart it during access testing.

Capture its namespace, maps, process security state, and initial volume ownership:

```powershell
'===== INITIALIZER IDENTITY / USER NAMESPACE / MAPS ====='
podman exec --user=0:0 tos-rt17-init sh -c 'id; readlink /proc/self/ns/user; cat /proc/self/uid_map; cat /proc/self/gid_map'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect initializer identity or mappings.' }

'===== INITIALIZER CAPABILITIES / NO-NEW-PRIVS ====='
podman exec --user=0:0 tos-rt17-init sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs):" /proc/self/status'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect initializer process security state.' }

'===== INITIAL VOLUME OWNERSHIP ====='
podman exec --user=0:0 tos-rt17-init sh -c 'stat -c "%u:%g %a %n" /tos-shared'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect initial volume ownership.' }
```

The first-mount ownership result must show `/tos-shared` owned by initializer UID/GID `0:0` in the initializer's namespace. All five initializer capability sets must be zero and `NoNewPrivs=1`. If ownership, identity, or security-state evidence differs or is unavailable, stop; do not repair it with `chown`, `:U`, or another privileged helper.

### 7.3 Set and verify the neutral shared-root mode

```powershell
podman exec --user=0:0 tos-rt17-init sh -c 'chmod 1777 /tos-shared && stat -c "%u:%g %a %n" /tos-shared'
if ($LASTEXITCODE -ne 0) { throw 'Could not set and verify shared-root mode 1777.' }
```

The shared root is owned by the initializer's mapped root identity and has mode `1777`. The initializer's User Namespace range must remain allocated while the workloads run. Because the workload mappings are required to be disjoint from the initializer mapping, neither workload process is the shared-root directory owner. The sticky bit therefore applies to both workload identities when they attempt to rename or remove each other's top-level workspace entries.

If the mode read-back is not `1777`, stop and clean up. Do not use an alternate owner, mapping, or mount option.

### 7.4 Create workload container A

```powershell
podman run -d --pull=never --arch arm64 --userns=auto:size=65536 --user=1000:1000 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --name tos-rt17-a --volume tos-rt17-shared:/tos-shared:rw,nocopy quay.io/fedora/fedora:latest sleep 1800
if ($LASTEXITCODE -ne 0) { throw 'Workload A creation failed. Stop and perform scoped cleanup.' }
```

Capture the returned container ID and running state.

### 7.5 Create workload container B

```powershell
podman run -d --pull=never --arch arm64 --userns=auto:size=65536 --user=1000:1000 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --name tos-rt17-b --volume tos-rt17-shared:/tos-shared:rw,nocopy quay.io/fedora/fedora:latest sleep 1800
if ($LASTEXITCODE -ne 0) { throw 'Workload B creation failed. Stop and perform scoped cleanup.' }
```

Capture the returned container ID and running state. Do not recreate any container under alternative User Namespace options if creation fails.

### 7.6 Verify workload identities, namespace mappings, mount configuration, and process security

```powershell
'===== WORKLOAD A IDENTITY / USER NAMESPACE / MAPS ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'id; readlink /proc/self/ns/user; cat /proc/self/uid_map; cat /proc/self/gid_map'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect workload A identity or mappings.' }

'===== WORKLOAD A PROCESS SECURITY STATE ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs):" /proc/self/status'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect workload A process security state.' }

'===== WORKLOAD B IDENTITY / USER NAMESPACE / MAPS ====='
podman exec --user=1000:1000 tos-rt17-b sh -c 'id; readlink /proc/self/ns/user; cat /proc/self/uid_map; cat /proc/self/gid_map'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect workload B identity or mappings.' }

'===== WORKLOAD B PROCESS SECURITY STATE ====='
podman exec --user=1000:1000 tos-rt17-b sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs):" /proc/self/status'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect workload B process security state.' }

'===== SHARED ROOT VIEW FROM A ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'stat -c "%u:%g %a %n" /tos-shared'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect shared root from A.' }

'===== SHARED ROOT VIEW FROM B ====='
podman exec --user=1000:1000 tos-rt17-b sh -c 'stat -c "%u:%g %a %n" /tos-shared'
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect shared root from B.' }

'===== ALL THREE CONTAINER INSPECTIONS ====='
podman inspect tos-rt17-init
if ($LASTEXITCODE -ne 0) { throw 'Initializer inspection failed.' }
podman inspect tos-rt17-a
if ($LASTEXITCODE -ne 0) { throw 'Workload A inspection failed.' }
podman inspect tos-rt17-b
if ($LASTEXITCODE -ne 0) { throw 'Workload B inspection failed.' }

'===== ALL THREE CONTAINERS RUNNING ====='
podman ps --filter name=tos-rt17
if ($LASTEXITCODE -ne 0) { throw 'Could not list RT-17 containers.' }
```

Before filesystem probes, verify and record:

- Initializer, A, and B have three distinct User Namespace identifiers.
- All three UID maps and GID maps are readable. For each map, compare the ranges in the parent namespace; the ranges must be pairwise non-overlapping across initializer, A, and B. For each line, the mapped interval is `parent_start` through `parent_start + length - 1`.
- A and B both report numeric in-container UID/GID `1000:1000`.
- A and B mount the same `tos-rt17-shared` named volume read/write at `/tos-shared` (`RW=true`). The initializer has that same named volume attached read/write.
- The shared-root mode is `1777` and its owner is the initializer's mapped root identity, not either workload's `1000:1000` identity. Record the numeric view from A and B; do not assume a fixed overflow UID/GID value.
- All five capability sets are zero in both workload processes and both report `NoNewPrivs=1`. The initializer process must also report zero in all five capability sets and `NoNewPrivs=1`.
- The containers are non-privileged and have no unintended host or other volume mounts.

The `UsernsMode` string from `podman inspect` must be recorded but is not, by itself, the authoritative namespace-separation criterion. Kernel namespace identifiers and actual UID/GID maps are the primary evidence. The three mappings must be comparable in the common parent namespace used by the same rootless Podman allocator; if that cannot be established, classify the mapping comparison as INCONCLUSIVE rather than assuming the numeric intervals are directly comparable. If any mapping overlaps, any namespace identifier is shared, the workload identity differs, or the volume/mount configuration is not as specified, do not continue to filesystem probes. Preserve evidence and clean up.

### 7.7 Create private workspaces and synthetic canaries

Workload A creates its private workspace:

```powershell
podman exec --user=1000:1000 tos-rt17-a sh -c 'umask 077 && mkdir /tos-shared/agent-a && printf RT17_PRIVATE_A > /tos-shared/agent-a/private.txt && chmod 700 /tos-shared/agent-a && chmod 600 /tos-shared/agent-a/private.txt && printf RT17_FILE_A > /tos-shared/agent-a-file-0600 && chmod 600 /tos-shared/agent-a-file-0600 && stat -c "%u:%g %a %n" /tos-shared/agent-a /tos-shared/agent-a/private.txt /tos-shared/agent-a-file-0600 && cat /tos-shared/agent-a/private.txt && cat /tos-shared/agent-a-file-0600'
if ($LASTEXITCODE -ne 0) { throw 'Workload A could not create and read its own private canary.' }
```

Workload B creates its private workspace:

```powershell
podman exec --user=1000:1000 tos-rt17-b sh -c 'umask 077 && mkdir /tos-shared/agent-b && printf RT17_PRIVATE_B > /tos-shared/agent-b/private.txt && chmod 700 /tos-shared/agent-b && chmod 600 /tos-shared/agent-b/private.txt && printf RT17_FILE_B > /tos-shared/agent-b-file-0600 && chmod 600 /tos-shared/agent-b-file-0600 && stat -c "%u:%g %a %n" /tos-shared/agent-b /tos-shared/agent-b/private.txt /tos-shared/agent-b-file-0600 && cat /tos-shared/agent-b/private.txt && cat /tos-shared/agent-b-file-0600'
if ($LASTEXITCODE -ne 0) { throw 'Workload B could not create and read its own private canary.' }
```

Capture outputs and timestamps. The owner of each workspace and canary must correspond to its creating workload's mapped `1000:1000` identity. The directory mode must be `700`; the canary-file mode must be `600`. Both canaries must be readable by their creating workload before any cross-access attempt.

### 7.8 Positive controls — each workload reads its own canary

```powershell
'===== A READS OWN CANARY ====='
podman exec --user=1000:1000 tos-rt17-a cat /tos-shared/agent-a/private.txt
if ($LASTEXITCODE -ne 0) { throw 'A could not read its own canary.' }

'===== B READS OWN CANARY ====='
podman exec --user=1000:1000 tos-rt17-b cat /tos-shared/agent-b/private.txt
if ($LASTEXITCODE -ne 0) { throw 'B could not read its own canary.' }

'===== A READS OWN FILE-MODE PROBE ====='
podman exec --user=1000:1000 tos-rt17-a cat /tos-shared/agent-a-file-0600
if ($LASTEXITCODE -ne 0) { throw 'A could not read its own file-mode probe.' }

'===== B READS OWN FILE-MODE PROBE ====='
podman exec --user=1000:1000 tos-rt17-b cat /tos-shared/agent-b-file-0600
if ($LASTEXITCODE -ne 0) { throw 'B could not read its own file-mode probe.' }
```

Expected exact contents: `RT17_PRIVATE_A` / `RT17_FILE_A` for A and `RT17_PRIVATE_B` / `RT17_FILE_B` for B. The two workspace canaries test directory traversal protection (`0700`); the additional canaries in the shared root test file read/write protection (`0600`) without the private-directory mode blocking access first.

### 7.9 Negative controls — reciprocal unauthorized reads

Run A's attempt to read B's private file and preserve the error and exit code:

```powershell
'===== A ATTEMPTS TO READ B PRIVATE FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$AReadBOutput = podman exec --user=1000:1000 tos-rt17-a sh -c 'cat /tos-shared/agent-b/private.txt' 2>&1
$AReadBExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$AReadBOutput
"A_READ_B_EXIT=$AReadBExit"
if ($AReadBExit -eq 0) { throw 'UNAUTHORIZED READ SUCCEEDED. Stop all remaining access probes and clean up.' }
$AReadBText = ($AReadBOutput | Out-String)
if (($AReadBExit -ne 1) -or ($AReadBText -notmatch 'Permission denied')) { throw 'A read failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

Only after A-to-B is confirmed denied, run B's attempt to read A's private file:

```powershell
'===== B ATTEMPTS TO READ A PRIVATE FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$BReadAOutput = podman exec --user=1000:1000 tos-rt17-b sh -c 'cat /tos-shared/agent-a/private.txt' 2>&1
$BReadAExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$BReadAOutput
"B_READ_A_EXIT=$BReadAExit"
if ($BReadAExit -eq 0) { throw 'UNAUTHORIZED READ SUCCEEDED. Stop all remaining access probes and clean up.' }
$BReadAText = ($BReadAOutput | Out-String)
if (($BReadAExit -ne 1) -or ($BReadAText -notmatch 'Permission denied')) { throw 'B read failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

A denial attributed to `Permission denied` is required. A missing-file error, container-exec error, or generic failure is not sufficient evidence of DAC enforcement.

### 7.10 Negative controls — reciprocal unauthorized reads of standalone mode-0600 files

These probes use files located directly in the shared `1777` root. Therefore the workload can traverse the parent directory, and the expected denial is attributable to the file's `0600` mode and ownership rather than the workspace directory's `0700` mode.

A attempts to read B's standalone file:

```powershell
'===== A ATTEMPTS TO READ B MODE-0600 FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$AReadFileBOutput = podman exec --user=1000:1000 tos-rt17-a sh -c 'cat /tos-shared/agent-b-file-0600' 2>&1
$AReadFileBExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$AReadFileBOutput
"A_READ_FILE_B_EXIT=$AReadFileBExit"
if ($AReadFileBExit -eq 0) { throw 'UNAUTHORIZED FILE READ SUCCEEDED. Stop all remaining access probes and clean up.' }
$AReadFileBText = ($AReadFileBOutput | Out-String)
if (($AReadFileBExit -ne 1) -or ($AReadFileBText -notmatch 'Permission denied')) { throw 'A file read failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

Only after A-to-B is confirmed denied, run B's reciprocal attempt:

```powershell
'===== B ATTEMPTS TO READ A MODE-0600 FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$BReadFileAOutput = podman exec --user=1000:1000 tos-rt17-b sh -c 'cat /tos-shared/agent-a-file-0600' 2>&1
$BReadFileAExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$BReadFileAOutput
"B_READ_FILE_A_EXIT=$BReadFileAExit"
if ($BReadFileAExit -eq 0) { throw 'UNAUTHORIZED FILE READ SUCCEEDED. Stop all remaining access probes and clean up.' }
$BReadFileAText = ($BReadFileAOutput | Out-String)
if (($BReadFileAExit -ne 1) -or ($BReadFileAText -notmatch 'Permission denied')) { throw 'B file read failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

### 7.11 Negative controls — reciprocal unauthorized writes to private workspaces

Run A's attempt to create a file inside B's private directory:

```powershell
'===== A ATTEMPTS TO WRITE IN B PRIVATE DIRECTORY ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$AWriteBOutput = podman exec --user=1000:1000 tos-rt17-a sh -c 'touch /tos-shared/agent-b/unauthorized-by-a.txt' 2>&1
$AWriteBExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$AWriteBOutput
"A_WRITE_B_EXIT=$AWriteBExit"
if ($AWriteBExit -eq 0) { throw 'UNAUTHORIZED WRITE SUCCEEDED. Stop all remaining access probes and clean up.' }
$AWriteBText = ($AWriteBOutput | Out-String)
if (($AWriteBExit -ne 1) -or ($AWriteBText -notmatch 'Permission denied')) { throw 'A write failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

Only after A-to-B is confirmed denied, run B's attempt to create a file inside A's private directory:

```powershell
'===== B ATTEMPTS TO WRITE IN A PRIVATE DIRECTORY ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$BWriteAOutput = podman exec --user=1000:1000 tos-rt17-b sh -c 'touch /tos-shared/agent-a/unauthorized-by-b.txt' 2>&1
$BWriteAExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$BWriteAOutput
"B_WRITE_A_EXIT=$BWriteAExit"
if ($BWriteAExit -eq 0) { throw 'UNAUTHORIZED WRITE SUCCEEDED. Stop all remaining access probes and clean up.' }
$BWriteAText = ($BWriteAOutput | Out-String)
if (($BWriteAExit -ne 1) -or ($BWriteAText -notmatch 'Permission denied')) { throw 'B write failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

### 7.12 Negative controls — reciprocal unauthorized writes to standalone mode-0600 files

These probes append to the other workload's file located directly in the shared `1777` root. The parent directory allows traversal and creation; the expected denial is attributable to the file's `0600` mode and ownership.

```powershell
'===== A ATTEMPTS TO APPEND TO B MODE-0600 FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$AWriteFileBOutput = podman exec --user=1000:1000 tos-rt17-a sh -c 'printf X >> /tos-shared/agent-b-file-0600' 2>&1
$AWriteFileBExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$AWriteFileBOutput
"A_WRITE_FILE_B_EXIT=$AWriteFileBExit"
if ($AWriteFileBExit -eq 0) { throw 'UNAUTHORIZED FILE WRITE SUCCEEDED. Stop all remaining access probes and clean up.' }
$AWriteFileBText = ($AWriteFileBOutput | Out-String)
if (($AWriteFileBExit -ne 1) -or ($AWriteFileBText -notmatch 'Permission denied')) { throw 'A file write failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }

'===== B ATTEMPTS TO APPEND TO A MODE-0600 FILE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$BWriteFileAOutput = podman exec --user=1000:1000 tos-rt17-b sh -c 'printf X >> /tos-shared/agent-a-file-0600' 2>&1
$BWriteFileAExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$BWriteFileAOutput
"B_WRITE_FILE_A_EXIT=$BWriteFileAExit"
if ($BWriteFileAExit -eq 0) { throw 'UNAUTHORIZED FILE WRITE SUCCEEDED. Stop all remaining access probes and clean up.' }
$BWriteFileAText = ($BWriteFileAOutput | Out-String)
if (($BWriteFileAExit -ne 1) -or ($BWriteFileAText -notmatch 'Permission denied')) { throw 'B file write failed for a reason other than a verified permission denial. Stop and classify as INCONCLUSIVE.' }
```

### 7.13 Negative controls — sticky-bit protection against cross-workspace rename

The shared root is owned by the initializer identity, not A or B. With mode `1777`, neither workload is the shared-root owner; each workload may rename its own entry but not the other workload's entry. Test the reciprocal denial:

```powershell
'===== A ATTEMPTS TO RENAME B WORKSPACE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$ARenameBOutput = podman exec --user=1000:1000 tos-rt17-a sh -c 'mv /tos-shared/agent-b /tos-shared/agent-b-renamed' 2>&1
$ARenameBExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$ARenameBOutput
"A_RENAME_B_EXIT=$ARenameBExit"
if ($ARenameBExit -eq 0) { throw 'UNAUTHORIZED RENAME SUCCEEDED. Stop all remaining access probes and clean up.' }
$ARenameBText = ($ARenameBOutput | Out-String)
if (($ARenameBExit -ne 1) -or ($ARenameBText -notmatch 'Operation not permitted|Permission denied')) { throw 'A rename failed for a reason other than a verified sticky-bit denial. Stop and classify as INCONCLUSIVE.' }
```

Only after A-to-B is confirmed denied, run B's reciprocal attempt:

```powershell
'===== B ATTEMPTS TO RENAME A WORKSPACE ====='
$PreviousErrorActionPreference = $ErrorActionPreference
$ErrorActionPreference = 'Continue'
$BRenameAOutput = podman exec --user=1000:1000 tos-rt17-b sh -c 'mv /tos-shared/agent-a /tos-shared/agent-a-renamed' 2>&1
$BRenameAExit = $LASTEXITCODE
$ErrorActionPreference = $PreviousErrorActionPreference
$BRenameAOutput
"B_RENAME_A_EXIT=$BRenameAExit"
if ($BRenameAExit -eq 0) { throw 'UNAUTHORIZED RENAME SUCCEEDED. Stop all remaining access probes and clean up.' }
$BRenameAText = ($BRenameAOutput | Out-String)
if (($BRenameAExit -ne 1) -or ($BRenameAText -notmatch 'Operation not permitted|Permission denied')) { throw 'B rename failed for a reason other than a verified sticky-bit denial. Stop and classify as INCONCLUSIVE.' }
```

If either rename succeeds, stop immediately. Do not attempt the reciprocal rename after a successful unauthorized rename.

### 7.14 Final data-integrity, workspace, and mount checks

```powershell
'===== A FINAL OWN CANARY READ ====='
podman exec --user=1000:1000 tos-rt17-a cat /tos-shared/agent-a/private.txt
if ($LASTEXITCODE -ne 0) { throw 'A final canary read failed.' }

'===== B FINAL OWN CANARY READ ====='
podman exec --user=1000:1000 tos-rt17-b cat /tos-shared/agent-b/private.txt
if ($LASTEXITCODE -ne 0) { throw 'B final canary read failed.' }

'===== A FINAL OWN MODE-0600 FILE READ ====='
podman exec --user=1000:1000 tos-rt17-a cat /tos-shared/agent-a-file-0600
if ($LASTEXITCODE -ne 0) { throw 'A final file-mode canary read failed.' }

'===== B FINAL OWN MODE-0600 FILE READ ====='
podman exec --user=1000:1000 tos-rt17-b cat /tos-shared/agent-b-file-0600
if ($LASTEXITCODE -ne 0) { throw 'B final file-mode canary read failed.' }

'===== A WORKSPACE / FILE METADATA ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'stat -c "%u:%g %a %n" /tos-shared /tos-shared/agent-a /tos-shared/agent-a/private.txt /tos-shared/agent-a-file-0600 /tos-shared/agent-b'
if ($LASTEXITCODE -ne 0) { throw 'A final metadata inspection failed.' }

'===== B WORKSPACE / FILE METADATA ====='
podman exec --user=1000:1000 tos-rt17-b sh -c 'stat -c "%u:%g %a %n" /tos-shared /tos-shared/agent-a /tos-shared/agent-b /tos-shared/agent-b/private.txt /tos-shared/agent-b-file-0600'
if ($LASTEXITCODE -ne 0) { throw 'B final metadata inspection failed.' }

'===== A DESTINATION PATH ABSENCE ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'test ! -e /tos-shared/agent-b-renamed && echo A_RENAME_DESTINATION_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'A rename destination exists or absence could not be verified.' }

'===== B DESTINATION PATH ABSENCE ====='
podman exec --user=1000:1000 tos-rt17-b sh -c 'test ! -e /tos-shared/agent-a-renamed && echo B_RENAME_DESTINATION_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'B rename destination exists or absence could not be verified.' }

'===== UNAUTHORIZED WRITE-PROBE ABSENCE ====='
podman exec --user=1000:1000 tos-rt17-a sh -c 'test ! -e /tos-shared/agent-a/unauthorized-by-b.txt'
if ($LASTEXITCODE -ne 0) { throw 'A unauthorized write-probe artifact exists or absence could not be verified.' }
podman exec --user=1000:1000 tos-rt17-b sh -c 'test ! -e /tos-shared/agent-b/unauthorized-by-a.txt'
if ($LASTEXITCODE -ne 0) { throw 'B unauthorized write-probe artifact exists or absence could not be verified.' }

'===== RECHECK SHARED VOLUME MOUNTS ====='
podman inspect tos-rt17-a
if ($LASTEXITCODE -ne 0) { throw 'Final A inspection failed.' }
podman inspect tos-rt17-b
if ($LASTEXITCODE -ne 0) { throw 'Final B inspection failed.' }
```

Confirm that the original canary contents remain unchanged; both original workspace paths still exist; both rename destinations are absent; both unauthorized write-probe files are absent; and the same named volume remains mounted read/write at `/tos-shared` in both workload containers. The final metadata output must retain the expected directory/file modes and ownership relationship.

## 8. Stop Conditions

Stop access testing immediately if:

- The local image is missing or any command would pull/build an image.
- Any reserved container or volume name is already in use.
- The runtime is not the approved rootless Podman configuration.
- Any of the three containers cannot be created in a unique User Namespace without changing out-of-scope settings.
- A or B does not run the test process as numeric UID/GID `1000:1000`.
- The three User Namespace identifiers are not distinct or the UID/GID parent ranges are not pairwise non-overlapping.
- The same named volume is not mounted read/write in both A and B.
- The first-mount owner is not the initializer's `0:0` identity, or the shared root cannot be verified as initializer-owned mode `1777`.
- Either workload process has a non-zero capability set or `NoNewPrivs` is not `1`.
- Any unauthorized read, write, or rename succeeds.
- A negative probe fails for a reason other than an attributable DAC/sticky-bit denial.
- Unexpected files, mounts, resources, or configuration changes appear.
- Any command would require a host/WSL/Podman/global configuration change, privilege elevation, image pull, host bind mount, or broader cleanup.

Preserve exact evidence and stop. If unauthorized access or rename succeeds, do not run the remaining reciprocal probes; capture the observed result and perform only scoped cleanup.

## 9. Result Classification

### PASS

A PASS requires all of the following:

- Initializer, A, and B run in distinct User Namespaces with readable, pairwise non-overlapping UID/GID parent mappings.
- Workload A and B use the same in-container UID/GID `1000:1000`.
- The initializer owns the shared root directory; its mapping is distinct from A and B; shared-root mode is `1777`.
- Both workload containers mount the same named volume read/write at `/tos-shared`.
- Both private workspace directories are owned by their respective workload identity and have mode `0700`; both canary files have mode `0600`.
- Both workloads can read their own canary.
- Both reciprocal reads of `0700` workspaces are explicitly denied with `Permission denied`.
- Both reciprocal reads of standalone `0600` files in the shared root are explicitly denied with `Permission denied`.
- Both reciprocal writes into `0700` workspaces and appends to standalone `0600` files are explicitly denied with `Permission denied`.
- Both reciprocal workspace-renaming attempts are denied with the expected sticky-bit error (`Operation not permitted` or `Permission denied`).
- No unauthorized write/rename artifacts are created and both original canaries remain intact.
- All five capability sets are zero in both workload processes and both report `NoNewPrivs=1`; the initializer security preconditions also pass.
- Cleanup and post-test runtime checks pass.

### PARTIAL PASS

Use PARTIAL PASS only when the workload identity separation and at least some DAC controls are positively demonstrated, but a required evidence item cannot be verified because of a documented tooling or environment limitation, with no evidence that an unauthorized operation succeeded.

### FAIL

Use FAIL if any unauthorized cross-workspace read, write, or rename succeeds; if the two workload User Namespaces are identical; if workload UID/GID mappings overlap; or if a required workload security setting is demonstrably ineffective.

### BLOCKED

Use BLOCKED when preflight or setup cannot establish the approved runtime, local image, resource availability, or three distinct auto User Namespace allocations before valid access testing begins. A blocked test is not evidence that DAC isolation failed.

### INCONCLUSIVE

Use INCONCLUSIVE when an operation does not yield an attributable permission result—for example, a missing path, unavailable command, unrelated runtime error, incomplete output, ambiguous mapping, or unverifiable ownership prevents determining whether DAC caused the denial.

## 10. Evidence Requirements

Retain raw, timestamped output for:

- Host, PowerShell, elevation state, Podman client/server, Podman machine, rootless state, server architecture, and local image identity.
- Reserved-name collision checks for all three containers and the volume.
- Named-volume creation and inspection.
- Initializer, A, and B creation commands, returned IDs, and running-state output.
- `id`, User Namespace identifiers, UID maps, and GID maps for all three containers.
- Pairwise comparison of all mapped parent UID/GID ranges.
- Capability and `NoNewPrivs` fields for initializer and both workload processes.
- First-mount volume ownership from the initializer and shared-root mode `1777` read-back.
- Shared-root owner/mode observations from A and B.
- Both workspace creation commands and ownership/mode output.
- Positive own-canary reads.
- All reciprocal denied workspace/file reads, workspace/file writes, and workspace-renaming attempts, including complete stderr and exit codes.
- Final canary integrity, workspace metadata, rename-destination absence, write-probe absence, and mount inspection.
- Pre-cleanup inventory, removal outputs, exact resource-absence checks, and post-cleanup runtime state.
- Any error, deviation, stop condition, or unexpected result.

Every evidence block must distinguish raw output from interpretation. Do not include host credentials, API tokens, production secrets, real trading data, or unrelated user files.

## 11. Controlled Cleanup and Post-Test Verification

Cleanup must target only `tos-rt17-a`, `tos-rt17-b`, `tos-rt17-init`, and `tos-rt17-shared`. Remove all three containers before removing the named volume.

```powershell
'===== PRE-CLEANUP RT-17 INVENTORY ====='
podman ps -a --filter name=tos-rt17
if ($LASTEXITCODE -ne 0) { throw 'Could not capture pre-cleanup RT-17 container inventory.' }
podman volume ls --filter name=tos-rt17
if ($LASTEXITCODE -ne 0) { throw 'Could not capture pre-cleanup RT-17 volume inventory.' }

'===== REMOVE ONLY RT-17 CONTAINERS ====='
foreach ($Name in @('tos-rt17-a', 'tos-rt17-b', 'tos-rt17-init')) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) {
        podman rm -f $Name
        if ($LASTEXITCODE -ne 0) { throw "Could not remove RT-17 container: $Name. Stop and report." }
    }
    elseif ($CheckExit -ne 1) {
        throw "Could not reliably determine whether RT-17 container exists: $Name. Stop."
    }
}

'===== REMOVE ONLY RT-17 NAMED VOLUME ====='
podman volume exists tos-rt17-shared
$VolumeExit = $LASTEXITCODE
if ($VolumeExit -eq 0) {
    podman volume rm tos-rt17-shared
    if ($LASTEXITCODE -ne 0) { throw 'Could not remove RT-17 named volume. Stop and report.' }
}
elseif ($VolumeExit -ne 1) {
    throw 'Could not reliably determine whether RT-17 named volume exists. Stop.'
}

'===== VERIFY RT-17 CONTAINERS ABSENT ====='
podman ps -a --filter name=tos-rt17
if ($LASTEXITCODE -ne 0) { throw 'RT-17 post-cleanup container listing failed.' }
foreach ($Name in @('tos-rt17-a', 'tos-rt17-b', 'tos-rt17-init')) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "RT-17 container remains: $Name" }
    if ($CheckExit -ne 1) { throw "Could not verify container absence: $Name" }
    "Verified absent: $Name"
}

'===== VERIFY RT-17 VOLUME ABSENT ====='
podman volume exists tos-rt17-shared
$VolumeExit = $LASTEXITCODE
if ($VolumeExit -eq 0) { throw 'RT-17 named volume remains after cleanup.' }
if ($VolumeExit -ne 1) { throw 'Could not verify RT-17 named-volume absence.' }
'Verified absent: tos-rt17-shared'

'===== VERIFY PODMAN MACHINE AND ROOTLESS RUNTIME ====='
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Could not verify Podman machine after cleanup.' }
podman info
if ($LASTEXITCODE -ne 0) { throw 'Podman is not reachable after RT-17 cleanup.' }
'RT17_CLEANUP_AND_POSTCHECK_COMPLETE'
```

Cleanup is complete only after all four exact RT-17 resource-absence checks pass and Podman remains reachable in the existing rootless configuration. Do not remove the local image or stop/delete the Podman machine. If a removal or absence check is inconclusive, stop and report the residual state; do not broaden cleanup.

## 12. Risks and Interpretation Limits

1. **DAC scope only:** The test evaluates POSIX owner/group/other mode-bit checks and the sticky-bit rule on one Podman-managed named volume. It does not evaluate extended POSIX ACL entries.
2. **Shared-volume configuration:** Both workload containers deliberately receive the same read/write volume. The result applies only to the tested ownership, mappings, modes, mount options, and operations.
3. **Initializer/control-plane trust:** The initializer owns the shared root. It is infrastructure, not a workload identity. The test does not protect against the initializer, Podman API, host account, WSL machine, or an administrator deliberately changing ownership/modes or attaching the volume elsewhere.
4. **User Namespace scope:** Distinct namespace IDs and disjoint mappings are identity evidence, not proof of complete kernel isolation or resistance to container escape.
5. **Capabilities:** Dropping all capabilities removes relevant capability-based DAC bypass paths for the tested workload processes. The test does not claim that all TOS workloads can operate with no capabilities.
6. **Volume initialization:** Podman's first-mount ownership adjustment is part of this test's setup. The observed initializer ownership must be captured; no manual ownership repair or `:U` option is permitted.
7. **Operations tested:** Directory traversal through `0700` workspaces, reads/writes of standalone `0600` files in the shared root, creation attempts within private workspaces, and top-level workspace rename denial are tested. Other filesystem operations, hard links, symbolic links, special files, race conditions, and all possible filesystem paths are not exhaustively tested.
8. **Tool permissions:** No MCP/tool/API authorization boundary is tested.
9. **Production:** No production credentials, trading data, TOS prompts, live operations, or production Agents are used. A PASS is not production authorization or runtime selection.

## 13. References

1. Podman 6.2.0 `podman-run` manual — `--userns=auto`, unique subordinate UID/GID range allocation, and `--user`:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
2. Podman 6.2.0 `podman-run` manual — named volumes, read/write mounts, first-mount ownership adjustment, `:U`, and `:nocopy`:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
3. Linux man-pages — `user_namespaces(7)`, including UID/GID mapping, filesystem permission checks, and file-related capabilities:  
   https://man7.org/linux/man-pages/man7/user_namespaces.7.html
4. Approved RT-13, RT-14, RT-15, and RT-16 plans/results in this repository.

These references explain the mechanisms and command semantics. They do not establish that RT-17 has passed on the target device.

## 14. Document Control

| Field | Value |
|---|---|
| Document ID | TOS-RT-17-SVDAI-TP-001 |
| Version | 1.0 |
| Document Status | FINAL — READY FOR USER REVIEW / GITHUB UPLOAD |
| Plan Acceptance | PENDING USER REVIEW |
| GitHub Upload | NOT UPLOADED |
| Test Execution | NOT STARTED |
| Execution Authorization | NOT GRANTED |
| Test Result | NOT YET ASSESSED |
| Scope | Shared named-volume DAC isolation between two distinct rootless workload User Namespaces, with a separate volume initializer |
| Runtime Selection | NOT FINALIZED |
| Production Authorization | NOT GRANTED |
| Next Stage | User review and acceptance; GitHub upload and verification; separate execution authorization required before RT-17 begins |

---

**End of document**
