# TOS RT-17 — Shared-Volume DAC Isolation Under Distinct User Namespaces

**Document ID:** TOS-RT-17-SVDAI-TP-001  
**Version:** 1.1  
**Date:** 2026-11-28  
**Status:** FINAL — ACCEPTED BY USER; UPLOADED AND VERIFIED ON MAIN; EXECUTION NOT AUTHORIZED  
**Assessment Track:** Local Execution Environment Feasibility — Agent Identity & Permission Isolation  
**Target Runtime:** Podman 6.2.1 / WSL2 / Linux ARM64 / rootless  
**Target Image:** Existing local `quay.io/fedora/fedora:latest` (`linux/arm64`); image pull prohibited  
**Test Class:** Controlled, synthetic, non-production runtime validation  
**Authority:** TOS Human Authority / System Owner

---

## 1. Purpose

RT-17 evaluates whether Linux discretionary access control (DAC) mode bits protect files created by two container workloads when both workloads attach the same Podman-managed named volume read/write, use the same numeric in-container UID/GID (`1000:1000`), and run in distinct rootless Podman User Namespaces with non-overlapping UID/GID mappings.

The revised v1.1 test has a deliberately narrow target:

1. A separate initializer owns a shared directory with mode `1777`.
2. Workload A and workload B each create one file directly inside that shared directory with mode `0600`.
3. Each workload can read and append to its own file.
4. Neither workload can read, append to, delete, or rename the other workload's file.
5. Each workload can rename and restore its own file.

This test is a follow-up to RT-16. RT-16 demonstrated non-visibility when private volumes were attached to separate containers; RT-17 v1.1 tests DAC denial while the same read/write named volume is attached to both workloads.

## 2. Scope and Explicit Limitations

### 2.1 In scope

- One Podman-managed named volume attached read/write to an initializer, workload A, and workload B.
- A separate initializer User Namespace and two workload User Namespaces.
- Pairwise non-overlapping parent-namespace UID and GID ranges.
- The same numeric in-container workload identity, UID/GID `1000:1000`, in A and B.
- An initializer-owned shared directory, mode `1777`.
- Two workload canary files, each owned by its creator and set to mode `0600`.
- Reciprocal positive controls (own-file read and append; own-file rename and restore).
- Reciprocal negative controls (cross-file read, append, delete, and rename must be denied).
- Verification of workload capability sets and `NoNewPrivs`.
- Exact, scoped cleanup of v1.1 resources only.

### 2.2 Explicitly out of scope

- Private per-workload subdirectories with mode `0700`. **v1.1 does not test or establish `0700` directory isolation.**
- POSIX ACL entries (`setfacl` / `getfacl`), SELinux, AppArmor, custom seccomp profiles, or host LSM configuration.
- Host bind mounts, shared host directories, Podman API access, host-user compromise, administrative bypass, container escape, or kernel exploits.
- Network access, Agent orchestration, cross-Run lifecycle, Evidence Package security, production secrets, or production workload authorization.
- Production readiness, final runtime selection, or authorization to execute TOS Agents.
- Changes to `/etc/subuid`, `/etc/subgid`, WSL, Podman machine settings, host configuration, or global Podman configuration.

A PASS establishes only the tested named-volume DAC behavior under the exact runtime, image, mapping, ownership, and permission configuration recorded in the results. It does not prove complete Agent isolation or a comprehensive security boundary.

## 3. Why v1.1 Replaces the v1.0 Execution Design

### 3.1 v1.0 design issue

The v1.0 execution attempt mounted the new named volume using `nocopy`. In the observed Podman 6.2.1 run, the volume retained `NeedsCopyUp=true` and `NeedsChown=true`. When workload A first attached it, Podman changed the volume-root ownership to A's mapped host UID/GID. The intended initializer-owned shared root was therefore not preserved. The run was stopped before the formal DAC probes began.

This was a test-setup/design defect, not evidence that DAC isolation passed or failed. The v1.0 execution authorization must not be interpreted as authorization for the materially revised v1.1 procedure.

### 3.2 v1.1 design correction

The v1.1 procedure uses a fresh named volume and the following order:

1. The initializer is the first container to mount the volume at `/usr/share`, without `nocopy`.
2. Podman's initial copy-up behavior is allowed to complete.
3. The initializer creates `/usr/share/rt17-shared`, mode `1777`, while running as UID/GID `0:0` in its own `--userns=auto:size=65536` namespace.
4. Workload A and then workload B attach the already-initialized volume at `/usr/share`, also without `nocopy`.
5. The initializer remains running so its User Namespace allocation remains in use while both workloads execute.
6. The test verifies ownership views and mappings before any access-control probes.

Do not infer success from the absence of `NeedsCopyUp` or `NeedsChown` fields in an inspect display. The decisive checks are the recorded volume metadata, directory ownership/mode as seen by the initializer and both workloads, actual mount records, and the DAC positive/negative controls.

### 3.3 v1.1 design-probe evidence (not formal RT-17 results)

Independent probes P1–P10 established the feasibility of the corrected setup:

- P1–P3: an initial mount at an image path without `nocopy` performed copy-up; the first-mount ownership behavior was observed, and a later namespace saw unmapped ownership as `65534:65534`.
- P4: independent probe resource names were checked for collisions.
- P5/P5A: a fresh volume was first mounted by an initializer at `/usr/share`; a dedicated directory was created with owner `0:0`, mode `1777` in the initializer's view.
- P6: workloads A2 and B2 attached that same volume with distinct, non-overlapping mappings; both saw the shared test directory as `65534:65534`, mode `1777`.
- P7/P7A: each workload's `0600` canary was readable by its owner and unreadable by the other workload.
- P8/P8A: each workload was denied deletion and rename of the peer's canary; each could rename and restore its own canary.
- P9: each workload could append to its own canary and was denied append access to the peer's canary.
- P10: final ownership, modes, mappings, capability state, and file structure were read back.

Selected recorded probe timestamps (host local time, `+03:30`): P4 `2026-11-28T11:02:21.7416355`; P5 volume/initializer setup `2026-11-28T11:06:17.283950208`; P7A `2026-11-28T11:10:20.5555473`; P8 `2026-11-28T11:11:07.9521701`; P8A `2026-11-28T11:11:58.8705723`; P9 `2026-11-28T11:13:06.9652231`; P10 `2026-11-28T11:13:47.1258091`; probe cleanup `2026-11-28T11:14:44.2037686`.

The probes used independent resources and were cleaned. They are design/feasibility evidence only. They are not the formal RT-17 v1.1 execution and must not be reported as formal test results.

## 4. Formal v1.1 Resource Names

To preserve the original v1.0 resources and prevent accidental reuse, v1.1 uses a distinct resource namespace.

| Resource type | v1.1 reserved name/value | Purpose |
|---|---|---|
| Initializer container | `tos-rt17v11-init` | First mount and ownership of the shared test directory |
| Workload A container | `tos-rt17v11-a` | First workload identity |
| Workload B container | `tos-rt17v11-b` | Second workload identity |
| Named volume | `tos-rt17v11-shared` | Shared read/write volume |
| Image | `quay.io/fedora/fedora:latest` | Existing local Linux ARM64 image |
| Volume mount path | `/usr/share` | Mount destination in all three containers |
| Shared test directory | `/usr/share/rt17-shared` | Initializer-owned directory, mode `1777` |
| A canary file | `/usr/share/rt17-shared/a-private.txt` | A-owned file, mode `0600` |
| B canary file | `/usr/share/rt17-shared/b-private.txt` | B-owned file, mode `0600` |
| Workload UID/GID | `1000:1000` | Same numeric identity in A and B |
| Initializer UID/GID | `0:0` | Separate identity in the initializer namespace |

### 4.1 Protected v1.0 resources

The following existing v1.0 resources are intentionally preserved and must not be modified, mounted, removed, renamed, or reused by v1.1:

- Container: `tos-rt17-init`
- Volume: `tos-rt17-shared`

The v1.0 volume was observed with UID/GID `1:1`, `NeedsCopyUp=true`, `NeedsChown=true`, and `MountCount=0` after the v1.0 attempt. Its initializer remained running. The v1.1 preflight must record and preserve their current state. If either protected resource is missing or unexpectedly changed, stop and report the discrepancy; do not repair it as part of RT-17 v1.1.

All independent probe resources were confirmed absent after probe cleanup. Do not recreate or reuse probe names.

## 5. Safety and Change-Control Rules

1. **This document is a draft and does not authorize execution.** Formal v1.1 execution requires: user acceptance of this plan; upload by the user to GitHub; verification of the uploaded file on `main`; and a separate, explicit user authorization for formal v1.1 execution.
2. Use Windows PowerShell 5.1 in a non-elevated session.
3. Use only the default rootless Podman connection and the existing `podman-machine-default` WSL machine.
4. Use only the already-cached local Fedora ARM64 image and `--pull=never`. Do not pull or build images.
5. Use only the four v1.1 resource names in Section 4. Do not create any other persistent container or volume.
6. Do not alter the protected v1.0 resources listed in Section 4.1.
7. Do not use rootful Podman, `sudo`, `--privileged`, host networking, published ports, host bind mounts, devices, external volumes, or host filesystem paths.
8. All three containers must use `--network=none`, `--cap-drop=all`, `--security-opt=no-new-privileges`, and `--read-only`.
9. The initializer must use `--userns=auto:size=65536` and `--user=0:0`. Workloads A and B must each use `--userns=auto:size=65536` and `--user=1000:1000`.
10. The initializer must be the first container to mount the newly created v1.1 volume and must remain running until both workloads are removed.
11. Mount the named volume at `/usr/share` without `nocopy`, `:U`, `idmap`, `:z`, or `:Z`. Do not manually `chown` the volume or change host filesystem ownership.
12. Do not modify `/etc/subuid`, `/etc/subgid`, WSL settings, Podman machine settings, host configuration, or global Podman configuration.
13. Capture each command, complete output, exit code, and timestamp. Never reconstruct missing timestamps.
14. If any unauthorized cross-file operation succeeds, stop all remaining access probes immediately. Preserve the evidence and proceed only with scoped cleanup.
15. Do not use broad `podman prune` commands or remove unrelated resources.

## 6. Preconditions and Preflight

Run this section only after formal v1.1 execution has been separately authorized. Use a fresh, non-elevated Windows PowerShell 5.1 session.

```powershell
$ErrorActionPreference = 'Stop'
$Image = 'quay.io/fedora/fedora:latest'
$V11Containers = @('tos-rt17v11-init', 'tos-rt17v11-a', 'tos-rt17v11-b')
$V11Volume = 'tos-rt17v11-shared'
$ProtectedV10Container = 'tos-rt17-init'
$ProtectedV10Volume = 'tos-rt17-shared'
$ProbeContainers = @('tos-rt17-probe-a', 'tos-rt17-probe-b', 'tos-rt17-probe-init', 'tos-rt17-probe-a2', 'tos-rt17-probe-b2')
$ProbeVolumes = @('tos-rt17-probe', 'tos-rt17-probe-initfirst')

'===== RT-17 v1.1 PREFLIGHT TIMESTAMP / HOST ====='
Get-Date -Format o
$env:COMPUTERNAME
$PSVersionTable.PSVersion

'===== ELEVATION CHECK ====='
$Identity = [Security.Principal.WindowsIdentity]::GetCurrent()
$Principal = New-Object -TypeName System.Security.Principal.WindowsPrincipal -ArgumentList $Identity
$IsElevated = $Principal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
"ELEVATED=$IsElevated"
if ($IsElevated) { throw 'Stop: use a non-elevated PowerShell 5.1 session.' }

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
if ($LASTEXITCODE -ne 0) { throw 'Stop: required local image is missing. Do not pull it.' }
$ImageMeta = podman image inspect --format '{{.Id}}|{{.Os}}|{{.Architecture}}' $Image
if ($LASTEXITCODE -ne 0) { throw 'Local image inspection failed.' }
$ImageMeta
if ($ImageMeta -notmatch '\|linux\|arm64$') { throw 'Stop: the cached image is not linux/arm64.' }

'===== V1.1 CONTAINER NAME COLLISION CHECK ====='
$ExistingContainers = @(podman ps -a --format '{{.Names}}')
if ($LASTEXITCODE -ne 0) { throw 'Container listing failed.' }
$ContainerCollisions = @($ExistingContainers | Where-Object { $V11Containers -contains $_ })
if ($ContainerCollisions.Count -gt 0) { throw ('Stop: v1.1 container name collision: ' + ($ContainerCollisions -join ', ')) }
'No v1.1 container-name collisions.'

'===== V1.1 VOLUME NAME COLLISION CHECK ====='
$ExistingVolumes = @(podman volume ls --format '{{.Name}}')
if ($LASTEXITCODE -ne 0) { throw 'Volume listing failed.' }
if ($ExistingVolumes -contains $V11Volume) { throw 'Stop: v1.1 volume-name collision.' }
'No v1.1 volume-name collision.'

'===== PROBE RESOURCE ABSENCE CHECK ====='
$ProbeCollisions = @($ExistingContainers | Where-Object { $ProbeContainers -contains $_ })
$ProbeVolumeCollisions = @($ExistingVolumes | Where-Object { $ProbeVolumes -contains $_ })
if ($ProbeCollisions.Count -gt 0) { throw ('Stop: probe containers unexpectedly exist: ' + ($ProbeCollisions -join ', ')) }
if ($ProbeVolumeCollisions.Count -gt 0) { throw ('Stop: probe volumes unexpectedly exist: ' + ($ProbeVolumeCollisions -join ', ')) }
'All previously cleaned probe names remain absent.'

'===== PROTECTED V1.0 CONTAINER CHECK ====='
$V10ContainerStatus = podman inspect --format '{{.State.Status}}' $ProtectedV10Container
if ($LASTEXITCODE -ne 0) { throw 'Stop: protected v1.0 initializer is missing or cannot be inspected.' }
"V1.0_INITIALIZER_STATUS=$V10ContainerStatus"
if ($V10ContainerStatus -ne 'running') { throw 'Stop: protected v1.0 initializer state differs from the accepted baseline.' }

'===== PROTECTED V1.0 VOLUME CHECK ====='
podman volume inspect $ProtectedV10Volume
if ($LASTEXITCODE -ne 0) { throw 'Stop: protected v1.0 volume is missing or cannot be inspected.' }
'Compare this complete inspection with the preserved baseline: UID/GID 1:1, NeedsCopyUp=true, NeedsChown=true, MountCount=0. Do not modify it.'

'===== PREFLIGHT COMPLETE ====='
'RT17_V11_PREFLIGHT_COMPLETE'
```

### 6.1 Preflight acceptance criteria

Proceed only when all of the following are true:

- PowerShell is 5.1 and not elevated.
- Podman client/server are the expected 6.2.1 installation; the active machine is the existing WSL machine; Podman reports rootless mode and Linux ARM64.
- The cached image is present and reports `linux/arm64`; no image pull is required.
- All four v1.1 names are absent.
- All old probe names are absent.
- The original v1.0 initializer remains running and the v1.0 volume is present with its preserved baseline state.

Any mismatch is a STOP condition. Do not adapt resource names or change the protected v1.0 resources ad hoc.

## 7. Controlled v1.1 Test Procedure

### 7.1 Create a fresh v1.1 named volume

```powershell
Get-Date -Format o
podman volume create tos-rt17v11-shared
if ($LASTEXITCODE -ne 0) { throw 'Could not create the v1.1 volume.' }
podman volume inspect tos-rt17v11-shared
if ($LASTEXITCODE -ne 0) { throw 'Could not inspect the new v1.1 volume.' }
```

Record the complete initial volume inspection. Do not mount it into any container other than the initializer first.

### 7.2 Start the initializer as the first volume user

The initializer mounts the named volume at `/usr/share` without `nocopy`. Do not change the destination path or add a volume option.

```powershell
Get-Date -Format o
podman run -d --name tos-rt17v11-init --pull=never --userns=auto:size=65536 --user=0:0 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --volume tos-rt17v11-shared:/usr/share:rw quay.io/fedora/fedora:latest sh -c 'set -eu; test -d /usr/share; test ! -e /usr/share/rt17-shared; mkdir -m 1777 /usr/share/rt17-shared; chmod 1777 /usr/share/rt17-shared; stat -c "INITIALIZER_DIR uid=%u gid=%g mode=%a path=%n" /usr/share/rt17-shared; exec sleep infinity'
if ($LASTEXITCODE -ne 0) { throw 'Initializer creation/start failed.' }

'===== INITIALIZER STATUS ====='
podman ps --filter name=tos-rt17v11-init
if ($LASTEXITCODE -ne 0) { throw 'Initializer status check failed.' }

'===== INITIALIZER IDENTITY / MAP / DIRECTORY ====='
podman exec --user 0:0 tos-rt17v11-init sh -c 'id; echo UID_MAP; cat /proc/self/uid_map; echo GID_MAP; cat /proc/self/gid_map; stat -c "DIR uid=%u gid=%g mode=%a path=%n" /usr/share/rt17-shared'
if ($LASTEXITCODE -ne 0) { throw 'Initializer identity or directory read-back failed.' }

'===== VOLUME STATE AFTER INITIALIZER FIRST MOUNT ====='
podman volume inspect tos-rt17v11-shared
if ($LASTEXITCODE -ne 0) { throw 'Volume inspection after initializer mount failed.' }
```

**Initializer gate:** The directory must be visible to the initializer as owner `0:0`, mode `1777`; the initializer must be running; its UID/GID maps must be recorded. If this gate fails, do not start either workload.

### 7.3 Start workload A and then workload B

Start A first, verify its mount and mapping, then start B. The initializer must remain running throughout.

```powershell
Get-Date -Format o
podman run -d --name tos-rt17v11-a --pull=never --userns=auto:size=65536 --user=1000:1000 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --volume tos-rt17v11-shared:/usr/share:rw quay.io/fedora/fedora:latest sleep infinity
if ($LASTEXITCODE -ne 0) { throw 'Workload A creation/start failed.' }

'===== WORKLOAD A IDENTITY / MAP / DIRECTORY ====='
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'id; echo UID_MAP; cat /proc/self/uid_map; echo GID_MAP; cat /proc/self/gid_map; stat -c "DIR uid=%u gid=%g mode=%a path=%n" /usr/share/rt17-shared'
if ($LASTEXITCODE -ne 0) { throw 'Workload A identity or directory read-back failed.' }

'===== WORKLOAD A MOUNT INSPECTION ====='
podman inspect tos-rt17v11-a
if ($LASTEXITCODE -ne 0) { throw 'Workload A inspection failed.' }

Get-Date -Format o
podman run -d --name tos-rt17v11-b --pull=never --userns=auto:size=65536 --user=1000:1000 --cap-drop=all --security-opt=no-new-privileges --read-only --network=none --volume tos-rt17v11-shared:/usr/share:rw quay.io/fedora/fedora:latest sleep infinity
if ($LASTEXITCODE -ne 0) { throw 'Workload B creation/start failed.' }

'===== WORKLOAD B IDENTITY / MAP / DIRECTORY ====='
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'id; echo UID_MAP; cat /proc/self/uid_map; echo GID_MAP; cat /proc/self/gid_map; stat -c "DIR uid=%u gid=%g mode=%a path=%n" /usr/share/rt17-shared'
if ($LASTEXITCODE -ne 0) { throw 'Workload B identity or directory read-back failed.' }

'===== WORKLOAD B MOUNT INSPECTION ====='
podman inspect tos-rt17v11-b
if ($LASTEXITCODE -ne 0) { throw 'Workload B inspection failed.' }
```

**Identity and mount gate:**

- All three containers must show distinct User Namespace mappings.
- The parent-namespace UID ranges and GID ranges must be pairwise non-overlapping across initializer, A, and B. Compare every mapping line, not just the first line.
- A and B must both run as in-container `1000:1000`.
- All three inspect records must show the same named volume mounted read/write at `/usr/share`.
- Initializer must see `/usr/share/rt17-shared` as `0:0`, mode `1777`.
- A and B must each see that directory as `65534:65534`, mode `1777` (unmapped owner view), consistent with the probe evidence.

If a mapping overlaps, a mount differs, or the ownership/mode view differs, STOP before creating canaries. Do not proceed by changing permissions or ownership.

### 7.4 Verify workload hardening state

```powershell
'===== A CAPABILITIES / NO_NEW_PRIVS ====='
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs):" /proc/self/status'
if ($LASTEXITCODE -ne 0) { throw 'A hardening-state read-back failed.' }

'===== B CAPABILITIES / NO_NEW_PRIVS ====='
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'grep -E "^(CapInh|CapPrm|CapEff|CapBnd|CapAmb|NoNewPrivs):" /proc/self/status'
if ($LASTEXITCODE -ne 0) { throw 'B hardening-state read-back failed.' }
```

For both workloads, all five capability values (`CapInh`, `CapPrm`, `CapEff`, `CapBnd`, `CapAmb`) must be zero and `NoNewPrivs` must be `1`. A mismatch is a STOP condition.

### 7.5 Create A and B canary files

Run A's creation first, then B's. Both files are created directly inside the initializer-owned `1777` directory.

```powershell
# Windows PowerShell 5.1 does not pass ordinary pipeline input to a native
# executable's stdin. Encode each short shell script and decode it inside the
# container so this procedure does not depend on PowerShell pipeline-to-stdin.
function Invoke-RT17ContainerScript {
    param(
        [Parameter(Mandatory=$true)][string]$Container,
        [Parameter(Mandatory=$true)][string]$ScriptText
    )
    $EncodedScript = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($ScriptText))
    $ShellCommand = "printf '%s' '$EncodedScript' | base64 -d | sh"
    podman exec --user 1000:1000 $Container sh -c $ShellCommand
    $ScriptExitCode = $LASTEXITCODE
    if ($ScriptExitCode -ne 0) {
        throw "Canary creation failed in $Container (exit code $ScriptExitCode)."
    }
}

$ASeed = @'
set -eu
umask 077
D=/usr/share/rt17-shared
test -d "$D"
test ! -e "$D/a-private.txt"
printf 'RT17_A_INITIAL\n' > "$D/a-private.txt"
chmod 600 "$D/a-private.txt"
stat -c 'A_CANARY uid=%u gid=%g mode=%a size=%s path=%n' "$D/a-private.txt"
'@
Get-Date -Format o
Invoke-RT17ContainerScript -Container 'tos-rt17v11-a' -ScriptText $ASeed

$BSeed = @'
set -eu
umask 077
D=/usr/share/rt17-shared
test -d "$D"
test ! -e "$D/b-private.txt"
printf 'RT17_B_INITIAL\n' > "$D/b-private.txt"
chmod 600 "$D/b-private.txt"
stat -c 'B_CANARY uid=%u gid=%g mode=%a size=%s path=%n' "$D/b-private.txt"
'@
Get-Date -Format o
Invoke-RT17ContainerScript -Container 'tos-rt17v11-b' -ScriptText $BSeed
```

Read back both files from each workload and from the initializer. Each owner must see its own file as `1000:1000`, mode `600`; the peer must see the other workload's file as unmapped (`65534:65534`), mode `600`.

```powershell
'===== A VIEW OF BOTH CANARIES ====='
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'ls -ln /usr/share/rt17-shared; stat -c "%u:%g %a %n" /usr/share/rt17-shared/a-private.txt /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'A canary metadata read-back failed.' }

'===== B VIEW OF BOTH CANARIES ====='
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'ls -ln /usr/share/rt17-shared; stat -c "%u:%g %a %n" /usr/share/rt17-shared/a-private.txt /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'B canary metadata read-back failed.' }

'===== INITIALIZER VIEW OF BOTH CANARIES ====='
podman exec --user 0:0 tos-rt17v11-init sh -c 'ls -ln /usr/share/rt17-shared; stat -c "%u:%g %a %n" /usr/share/rt17-shared/a-private.txt /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'Initializer canary metadata read-back failed.' }
```

### 7.6 Positive read controls

Each owner must be able to read its own canary.

```powershell
Get-Date -Format o
podman exec --user 1000:1000 tos-rt17v11-a cat /usr/share/rt17-shared/a-private.txt
if ($LASTEXITCODE -ne 0) { throw 'A could not read its own canary.' }
podman exec --user 1000:1000 tos-rt17v11-b cat /usr/share/rt17-shared/b-private.txt
if ($LASTEXITCODE -ne 0) { throw 'B could not read its own canary.' }
```

### 7.7 Expected-denial helper for negative controls

Use this helper for operations that must fail. It requires both a nonzero exit code and the expected Linux denial message; an unrelated failure (for example, a missing path) is not accepted as a DAC denial.

```powershell
function Invoke-RT17ExpectedDenial {
    param(
        [Parameter(Mandatory=$true)][string]$Label,
        [Parameter(Mandatory=$true)][string]$Container,
        [Parameter(Mandatory=$true)][string]$CommandLine,
        [Parameter(Mandatory=$true)][string]$ExpectedPattern
    )
    $PodmanArguments = @('exec', '--user', '1000:1000', $Container, 'sh', '-c', $CommandLine)
    $Output = & podman @PodmanArguments 2>&1
    $ExitCode = $LASTEXITCODE
    $Text = ($Output | Out-String)
    "===== $Label ====="
    $Text
    "EXIT_CODE=$ExitCode"
    if ($ExitCode -eq 0) { throw "FAIL: $Label unexpectedly succeeded. Stop all remaining access probes." }
    if ($Text -notmatch $ExpectedPattern) { throw "INCONCLUSIVE/FAIL: $Label did not return the expected denial text. Stop and preserve evidence." }
    "EXPECTED_DENIAL_CONFIRMED=$Label"
}
```

### 7.8 Cross-file read denial

```powershell
Get-Date -Format o
Invoke-RT17ExpectedDenial -Label 'A_READS_B_FILE' -Container 'tos-rt17v11-a' -CommandLine 'cat /usr/share/rt17-shared/b-private.txt' -ExpectedPattern 'Permission denied'
Invoke-RT17ExpectedDenial -Label 'B_READS_A_FILE' -Container 'tos-rt17v11-b' -CommandLine 'cat /usr/share/rt17-shared/a-private.txt' -ExpectedPattern 'Permission denied'
```

### 7.9 Positive and negative append controls

First append to each workload's own file; then test reciprocal append denial.

```powershell
Get-Date -Format o
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'printf "RT17_A_OWN_APPEND\n" >> /usr/share/rt17-shared/a-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'A could not append to its own canary.' }
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'printf "RT17_B_OWN_APPEND\n" >> /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'B could not append to its own canary.' }

Invoke-RT17ExpectedDenial -Label 'A_APPENDS_TO_B_FILE' -Container 'tos-rt17v11-a' -CommandLine 'printf "RT17_A_UNAUTHORIZED_APPEND\\n" >> /usr/share/rt17-shared/b-private.txt' -ExpectedPattern 'Permission denied'
Invoke-RT17ExpectedDenial -Label 'B_APPENDS_TO_A_FILE' -Container 'tos-rt17v11-b' -CommandLine 'printf "RT17_B_UNAUTHORIZED_APPEND\\n" >> /usr/share/rt17-shared/a-private.txt' -ExpectedPattern 'Permission denied'

'===== FINAL CANARY CONTENTS ====='
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'cat /usr/share/rt17-shared/a-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'A canary content verification failed.' }
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'cat /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'B canary content verification failed.' }
```

The final content must include each file's initial marker and its owner's append marker. It must not include the peer's unauthorized append marker.

### 7.10 Sticky-bit delete and rename denial

The shared directory must remain `1777`; the files must remain `0600`. Each cross-owner delete and rename must be denied by the sticky-bit rule. Linux documents either `EPERM` (`Operation not permitted`) or `EACCES` (`Permission denied`) for this condition. The helper accepts either message; because the files are verified to exist and same-owner rename controls are run, unrelated failures must not be treated as a successful denial.

```powershell
Get-Date -Format o
Invoke-RT17ExpectedDenial -Label 'A_DELETES_B_FILE' -Container 'tos-rt17v11-a' -CommandLine 'rm /usr/share/rt17-shared/b-private.txt' -ExpectedPattern 'Operation not permitted|Permission denied'
Invoke-RT17ExpectedDenial -Label 'B_DELETES_A_FILE' -Container 'tos-rt17v11-b' -CommandLine 'rm /usr/share/rt17-shared/a-private.txt' -ExpectedPattern 'Operation not permitted|Permission denied'
Invoke-RT17ExpectedDenial -Label 'A_RENAMES_B_FILE' -Container 'tos-rt17v11-a' -CommandLine 'mv /usr/share/rt17-shared/b-private.txt /usr/share/rt17-shared/b-private-renamed-by-a.txt' -ExpectedPattern 'Operation not permitted|Permission denied'
Invoke-RT17ExpectedDenial -Label 'B_RENAMES_A_FILE' -Container 'tos-rt17v11-b' -CommandLine 'mv /usr/share/rt17-shared/a-private.txt /usr/share/rt17-shared/a-private-renamed-by-b.txt' -ExpectedPattern 'Operation not permitted|Permission denied'
```

If any operation succeeds, stop immediately. Do not continue with remaining probes. Capture directory contents, ownership, modes, command output, and timestamp.

### 7.11 Positive own-file rename controls

Each workload must be able to rename and restore its own file.

```powershell
Get-Date -Format o
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'set -eu; D=/usr/share/rt17-shared; mv "$D/a-private.txt" "$D/a-private-renamed.txt"; test -f "$D/a-private-renamed.txt"; mv "$D/a-private-renamed.txt" "$D/a-private.txt"; test -f "$D/a-private.txt"'
if ($LASTEXITCODE -ne 0) { throw 'A could not rename and restore its own file.' }

podman exec --user 1000:1000 tos-rt17v11-b sh -c 'set -eu; D=/usr/share/rt17-shared; mv "$D/b-private.txt" "$D/b-private-renamed.txt"; test -f "$D/b-private-renamed.txt"; mv "$D/b-private-renamed.txt" "$D/b-private.txt"; test -f "$D/b-private.txt"'
if ($LASTEXITCODE -ne 0) { throw 'B could not rename and restore its own file.' }
```

### 7.12 Final integrity and evidence read-back

```powershell
Get-Date -Format o

'===== INITIALIZER FINAL DIRECTORY / FILE VIEW ====='
podman exec --user 0:0 tos-rt17v11-init sh -c 'stat -c "%u:%g %a %n" /usr/share/rt17-shared; ls -ln /usr/share/rt17-shared; stat -c "%u:%g %a %n" /usr/share/rt17-shared/a-private.txt /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'Final initializer read-back failed.' }

'===== A FINAL DIRECTORY / FILE VIEW ====='
podman exec --user 1000:1000 tos-rt17v11-a sh -c 'stat -c "%u:%g %a %n" /usr/share/rt17-shared; ls -ln /usr/share/rt17-shared; grep -Fx RT17_A_INITIAL /usr/share/rt17-shared/a-private.txt; grep -Fx RT17_A_OWN_APPEND /usr/share/rt17-shared/a-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'Final A integrity check failed.' }

'===== B FINAL DIRECTORY / FILE VIEW ====='
podman exec --user 1000:1000 tos-rt17v11-b sh -c 'stat -c "%u:%g %a %n" /usr/share/rt17-shared; ls -ln /usr/share/rt17-shared; grep -Fx RT17_B_INITIAL /usr/share/rt17-shared/b-private.txt; grep -Fx RT17_B_OWN_APPEND /usr/share/rt17-shared/b-private.txt'
if ($LASTEXITCODE -ne 0) { throw 'Final B integrity check failed.' }

'===== ALL V1.1 CONTAINER STATUS ====='
podman ps -a --filter name=tos-rt17v11
if ($LASTEXITCODE -ne 0) { throw 'Final container listing failed.' }

'===== ALL V1.1 CONTAINER INSPECTIONS ====='
podman inspect tos-rt17v11-init tos-rt17v11-a tos-rt17v11-b
if ($LASTEXITCODE -ne 0) { throw 'Final container inspection failed.' }

'===== V1.1 VOLUME INSPECTION ====='
podman volume inspect tos-rt17v11-shared
if ($LASTEXITCODE -ne 0) { throw 'Final volume inspection failed.' }

'===== PROTECTED V1.0 RESOURCES STILL PRESENT ====='
podman inspect --format '{{.State.Status}}' tos-rt17-init
if ($LASTEXITCODE -ne 0) { throw 'Protected v1.0 initializer was unexpectedly changed or removed.' }
podman volume inspect tos-rt17-shared
if ($LASTEXITCODE -ne 0) { throw 'Protected v1.0 volume was unexpectedly changed or removed.' }
```

Final integrity requires both files to exist under their original names, both to remain mode `600`, the shared directory to remain mode `1777`, and all ownership/mapping relationships to remain consistent with the earlier read-backs.

## 8. Stop Conditions

Stop the current test immediately if any of the following occurs:

- Preflight fails or any v1.1 resource name is already present.
- The protected v1.0 initializer or volume is missing or differs unexpectedly from its preserved baseline.
- The cached image is unavailable or is not Linux ARM64.
- Podman is not rootless, the active machine is not the expected WSL machine, or the runtime version differs from the approved baseline.
- The initializer is not the first v1.1 volume user, or the initializer does not see the test directory as `0:0` / `1777`.
- The initializer, A, and B mappings are not pairwise non-overlapping.
- A or B sees the shared directory with an unexpected owner or mode.
- The three containers do not mount the same named volume read/write at `/usr/share`.
- Any workload has a nonzero capability value or `NoNewPrivs` is not `1`.
- A workload cannot perform its own positive-control operation.
- Any cross-file operation succeeds.
- An expected negative-control operation fails for a reason other than the expected DAC/sticky-bit denial.
- Any command produces incomplete, ambiguous, or contradictory evidence.

After a stop condition, do not improvise permission changes, remount the volume with alternate options, change mappings, or continue with later probes. Record the evidence and classify the outcome.

## 9. Result Classification

| Classification | Rule |
|---|---|
| **PASS** | All preconditions and identity/mount gates pass; all own-file positive controls succeed; all reciprocal cross-file read/append/delete/rename controls are denied for the expected reasons; final integrity checks pass; and cleanup is verified. |
| **PARTIAL PASS** | The core isolation behavior is demonstrated for a subset of planned controls, no unauthorized cross-file operation succeeds, but one or more required controls or evidence items are missing or inconclusive. |
| **FAIL** | Any unauthorized cross-file read, append, delete, or rename succeeds, or a required security condition (for example, non-overlapping mappings or zero workload capabilities) is demonstrably violated. |
| **BLOCKED** | Execution cannot begin or continue because a precondition, resource, image, runtime, or platform dependency is unavailable. |
| **INCONCLUSIVE** | Evidence is insufficient or contradictory to determine whether a required control passed or failed. |

A result classification is not production authorization. Production Authorization remains **NOT GRANTED** regardless of the RT-17 outcome.

## 10. Required Evidence Package

The formal v1.1 result report must include:

1. Execution date/time, host, PowerShell version, Podman client/server versions, active Podman machine, rootless status, server OS/architecture, and exact local image ID.
2. Preflight output and all resource-collision checks.
3. Initial and post-initializer v1.1 volume inspection.
4. Initializer, A, and B container IDs, states, inspect output, and mount records.
5. UID/GID maps for all three containers, with a recorded pairwise range-overlap analysis.
6. Directory ownership/mode views from the initializer, A, and B.
7. Canary ownership/mode/size views from all three containers.
8. A/B capability and `NoNewPrivs` read-backs.
9. Each positive-control command, output, exit code, and timestamp.
10. Each negative-control command, denial output, exit code, and timestamp.
11. Final file contents, directory listing, ownership/mode checks, and final inspections.
12. Exact cleanup commands and post-cleanup proof that all v1.1 containers and the v1.1 volume are absent.
13. Proof that the protected v1.0 initializer and volume remain present and unchanged.
14. Result classification, scope limitations, deviations, and unresolved issues.

Do not report probe P1–P10 as formal RT-17 v1.1 test results. Preserve missing evidence as missing; do not reconstruct it.

## 11. Scoped Cleanup

Cleanup is performed only after the evidence required for classification has been captured. Remove only the three v1.1 containers and the v1.1 volume. Do not use `podman rm --all`, `podman system prune`, `podman volume prune`, or any broad cleanup command.

```powershell
Get-Date -Format o

'===== REMOVE ONLY V1.1 CONTAINERS ====='
podman rm -f tos-rt17v11-a tos-rt17v11-b tos-rt17v11-init
if ($LASTEXITCODE -ne 0) { throw 'One or more v1.1 containers could not be removed; inspect manually and do not broaden cleanup.' }

'===== VERIFY V1.1 CONTAINERS ABSENT ====='
$RemainingContainers = @(podman ps -a --format '{{.Names}}')
if ($LASTEXITCODE -ne 0) { throw 'Container listing after cleanup failed.' }
$RemainingV11Containers = @($RemainingContainers | Where-Object { $V11Containers -contains $_ })
if ($RemainingV11Containers.Count -gt 0) { throw ('V1.1 containers remain: ' + ($RemainingV11Containers -join ', ')) }

'===== REMOVE ONLY V1.1 VOLUME ====='
podman volume rm tos-rt17v11-shared
if ($LASTEXITCODE -ne 0) { throw 'V1.1 volume could not be removed; inspect manually and do not broaden cleanup.' }

'===== VERIFY V1.1 VOLUME ABSENT ====='
$RemainingVolumes = @(podman volume ls --format '{{.Name}}')
if ($LASTEXITCODE -ne 0) { throw 'Volume listing after cleanup failed.' }
if ($RemainingVolumes -contains $V11Volume) { throw 'V1.1 volume remains after cleanup.' }

'===== VERIFY PROTECTED V1.0 RESOURCES REMAIN ====='
podman inspect --format '{{.State.Status}}' tos-rt17-init
if ($LASTEXITCODE -ne 0) { throw 'Protected v1.0 initializer is missing after v1.1 cleanup.' }
podman volume inspect tos-rt17-shared
if ($LASTEXITCODE -ne 0) { throw 'Protected v1.0 volume is missing after v1.1 cleanup.' }

'===== V1.1 CLEANUP COMPLETE ====='
'RT17_V11_CLEANUP_COMPLETE'
```

If a container removal fails, do not attempt volume removal until all v1.1 containers are confirmed absent. If the v1.1 volume removal fails, preserve the error and investigate only that named volume. Never touch the v1.0 resources.

## 12. Interpretation and Risk Limits

- This is a Linux DAC behavior test on a local rootless Podman 6.2.1 runtime hosted through WSL2 on ARM64.
- `65534:65534` is the observed unmapped-owner presentation in this environment; it must be confirmed by the formal execution rather than assumed.
- The initializer-first copy-up sequence is necessary to avoid the v1.0 first-workload ownership change observed with the earlier `nocopy` setup.
- The shared directory remains intentionally world-writable (`1777`) to let both workloads create files. The sticky bit restricts removal/rename of another user's file; it does not prevent directory listing or file-name discovery.
- File mode `0600` is what is expected to deny peer reads and writes. The sticky bit is what is expected to deny peer deletion and rename.
- The test does not evaluate `0700` per-agent subdirectories, ACLs, host administrators, a compromised Podman service, kernel vulnerabilities, or all possible bypass paths.
- No production workload, trading data, secrets, or real Agent process may be used.

## 13. References

1. Podman 6.2.0 `podman-run(1)` documentation — volume copy-up behavior, first-mount ownership adjustment, `--user`, and `--userns=auto`:  
   <https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html>
2. Linux `user_namespaces(7)` — UID/GID mapping and namespace permission semantics:  
   <https://man7.org/linux/man-pages/man7/user_namespaces.7.html>
3. Linux `unlink(2)` — sticky-directory deletion restrictions and `EPERM`/`EACCES` behavior:  
   <https://man7.org/linux/man-pages/man2/unlink.2.html>
4. Linux `rename(2)` — sticky-directory rename restrictions and `EPERM`/`EACCES` behavior:  
   <https://man7.org/linux/man-pages/man2/rename.2.html>
5. Microsoft PowerShell 5.1 `about_Pipelines` — documents native command pipeline behavior and that standard PowerShell pipeline input is not directly connected to native-process stdin:  
   <https://github.com/MicrosoftDocs/PowerShell-Docs/blob/main/reference/5.1/Microsoft.PowerShell.Core/About/about_Pipelines.md>
6. Local test environment baseline: Podman 6.2.1, rootless Linux ARM64 server through `podman-machine-default` (WSL), verified in the RT-17 preflight evidence on 2026-11-28.
7. RT-17 v1.0 accepted plan and its halted initial setup:  
   <https://github.com/Sadeghsaadat7/T.OS-project-/blob/main/TOS_RT-17_Shared_Volume_DAC_Isolation_Test_Plan_v1.0_FINAL.md>
8. RT-17 v1.1 design-probe evidence: conversation execution records P1–P10 and scoped cleanup on 2026-11-28. These are design evidence only, not formal test results.

## 14. Document Control
| Field | Value |
|---|---|
| Document ID | `TOS-RT-17-SVDAI-TP-001` |
| Version | `1.1` |
| Status | `FINAL — ACCEPTED BY USER; UPLOADED AND VERIFIED ON MAIN; EXECUTION NOT AUTHORIZED` |
| Supersedes | v1.0 execution procedure for any future RT-17 run; v1.0 remains preserved as historical record |
| Change Summary | Replaces the v1.0 volume-initialization design; applies initializer-first copy-up; narrows the test to `0600` files directly inside an initializer-owned `1777` directory; reserves independent v1.1 resource names; corrects PowerShell 5.1 script delivery and sticky-bit denial matching |
| Plan Review | `REVIEWED AND ACCEPTED BY USER — 2026-11-28` |
| GitHub Upload | `UPLOADED — VERIFIED ON MAIN` |
| GitHub Verification | `PASS — EXACT FILE PRESENT ON MAIN; CONTENT AND BLOB SHA VERIFIED` |
| GitHub Path | `TOS_RT-17_Shared_Volume_DAC_Isolation_Test_Plan_v1.1_FINAL.md` |
| Verified Main Commit | ba898e439c6240090410572da5f21f04ddabe5bc |
| Verified Blob SHA | 5e0378dce63b03cb2c116a0324831f6cc912f32a |
| Execution Authorization | `NOT AUTHORIZED — REQUIRES SEPARATE EXPLICIT USER APPROVAL` |
| Test Execution | `NOT STARTED` |
| Test Result | `NOT ASSESSED` |
| Production Authorization | `NOT GRANTED` |
| Next Stage | User separately authorizes formal RT-17 v1.1 execution; no execution may begin before that authorization |

**End of Document**
