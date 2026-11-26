# TOS RT-16 — Filesystem Access Isolation Test Plan

**Document ID:** TOS-RT-16-FAI-TP-001  
**Version:** 1.0  
**Date:** 2026-11-26  
**Status:**	FINAL — ACCEPTED  
**Assessment Track:** ID-15 — Independent Execution Environment Feasibility Assessment  
**Target Runtime:** Podman 6.2.1, WSL2 provider, ARM64, rootless  
**Target Image:** Existing local `quay.io/fedora/fedora:latest` (`linux/arm64`); pull prohibited  
**Test Class:** Controlled, non-production filesystem and mount-isolation validation  
**Authority:** TOS Human Authority / System Owner

---

## 1. Purpose

Determine whether the current rootless Podman environment provides demonstrable per-container filesystem-layer separation, separate named-volume attachment, and effective read-only enforcement for a deliberately read-only volume mount.

RT-16 addresses the filesystem and mount-access gap left open by the initial Podman + WSL2 feasibility assessment. It is a bounded runtime-configuration and effective-access test using two subject containers and synthetic data only.

The test does not attempt to bypass Podman's control plane or the Linux kernel. In particular, a private volume that is not mounted in the other container is expected to be absent from that container's mount namespace. This demonstrates mount-attachment isolation; it is not an OS discretionary-access-control denial test against a volume that has deliberately been mounted into the other container.

## 2. Scope

### 2.1 In scope

1. Reconfirm the active Podman client/server, WSL machine, rootless state, ARM64 architecture, and availability of the existing local Fedora ARM64 image.
2. Create two subject containers, `tos-rt16-a` and `tos-rt16-b`, using the existing image with `--pull=never`.
3. Verify that each container's writable root filesystem layer is independent by creating a unique synthetic marker in one container and confirming it is not present in the other.
4. Create two distinct Podman-managed named volumes:
   - `tos-rt16-private-a`, mounted only in Container A at `/tos-private` as read-write.
   - `tos-rt16-private-b`, mounted only in Container B at `/tos-private` as read-write.
5. Verify positive access: each subject container can create and read its own private-volume canary.
6. Verify cross-volume non-visibility: Container A cannot see Container B's private-volume canary at the corresponding private mount path, and vice versa. Confirm the mount inspection shows that the other container's private volume was not attached.
7. Create one synthetic shared fixture in a third named volume and mount that volume read-only into both subject containers. Verify reading succeeds and write attempts are denied by the effective read-only mount.
8. Compare Podman inspection data with observed filesystem behavior.
9. Remove only RT-16 test containers and named volumes, then verify their absence and confirm the Podman machine remains running and rootless.

A short-lived, disposable seed container is used only to write the synthetic shared-volume fixture. It is not a third subject container and is created with `--rm`.

### 2.2 Out of scope

- Host-directory or personal-directory bind mounts.
- Access to the Podman socket, runtime socket, credentials, host devices, or unrelated containers/volumes.
- Privileged containers, rootful Podman, host PID/network namespaces, or added capabilities.
- User Namespace retesting (RT-13), container hardening retesting (RT-14), or CPU/memory enforcement retesting (RT-15).
- Host filesystem ACL testing, cross-user Windows ACL testing, or adversarial access against the Podman machine's internal storage paths.
- Container escape, kernel exploit, penetration, or hostile multi-tenant testing.
- Network-egress validation, resource stress, five-Agent capacity, independent TOS Agent execution, Production readiness, or final Runtime selection.
- Changes to Windows, WSL, `.wslconfig`, Podman Machine settings, global Podman configuration, Linux security policy, or the local image.

## 3. Baseline and Technical Basis

The preceding ID-15N and RT-13–RT-15 evidence establishes the following baseline, which must still be rechecked during RT-16 preflight:

- Podman Client/Server 6.2.1.
- Windows ARM64 client and Linux ARM64 server.
- WSL2 provider and `podman-machine-default`.
- Rootless execution.
- cgroup v2 and a previously successful Fedora ARM64 container execution.
- The local image `quay.io/fedora/fedora:latest` was used successfully in RT-13, RT-14, and RT-15.

The versioned Podman 6.2.0 `podman-run` documentation describes that a named volume is mounted from the Podman server-side environment, supports explicit `rw` and `ro` mount options, and permits multiple volume mounts per container. It also documents that a read-only volume mount is write-protected and that named volumes persist independently of container removal. The installed runtime is 6.2.1; the 6.2.0 versioned manual is used as the nearby versioned reference. Local `podman run --help` must be consulted if behavior or accepted syntax differs; do not improvise a different test method during execution.

The plan deliberately uses named volumes managed by Podman, not Windows host paths. This avoids exposing user data and keeps the test within the remote Linux runtime environment used by the Windows client. The mount specifications include `nocopy` so image content at a mount destination is not copied into a newly created named volume; this keeps the synthetic volume fixtures deterministic and independent of any pre-existing image-path content.

## 4. Test Objects and Expected Mount Layout

| Object | Type | Intended attachment | Mode |
|---|---|---|---|
| `tos-rt16-a` | Subject container | Container A | Rootless; non-privileged; no network |
| `tos-rt16-b` | Subject container | Container B | Rootless; non-privileged; no network |
| `tos-rt16-seed` | Temporary seed container | Shared fixture volume only | Rootless; `--rm`; no network |
| `tos-rt16-private-a` | Named volume | Container A only, `/tos-private` | Read-write |
| `tos-rt16-private-b` | Named volume | Container B only, `/tos-private` | Read-write |
| `tos-rt16-shared-ro` | Named volume | Seed helper read-write; both subject containers read-only at `/tos-shared` | RW only during fixture creation, then RO in subjects |

Expected subject mount layout:

| Container | `/tos-private` | `/tos-shared` | Other private volume |
|---|---|---|---|
| A | `tos-rt16-private-a`, RW | `tos-rt16-shared-ro`, RO | Not attached |
| B | `tos-rt16-private-b`, RW | `tos-rt16-shared-ro`, RO | Not attached |

No host path is included in any `-v` / `--volume` argument. No mount targets `/`, `/run`, `/etc`, the Podman socket, or a host-sensitive directory.

## 5. Safety, Change-Control, and Evidence Rules

1. Execute only after the Human Authority explicitly accepts this plan and separately authorizes RT-16 execution.
2. Use Windows PowerShell 5.1 in the existing rootless Podman connection. Do not switch connections or elevate the Podman runtime.
3. Use only the already-present local image and `--pull=never`. Do not pull, build, tag, or modify images.
4. Use only the exact reserved RT-16 container and volume names in this plan. If any name already exists, stop; do not remove or reuse it.
5. All containers must be non-privileged, rootless, and run with `--network=none`. No host path, port, device, or socket is mounted or published.
6. Do not use `--volumes-from`, `--privileged`, `--userns=host`, custom UID/GID maps, `:U`, `:z`, `:Z`, mount propagation options, or other mount changes not specified in this plan.
7. Do not change host, WSL, Podman Machine, cgroup, SELinux/AppArmor, or global Podman configuration. If a command proposes or requires an out-of-scope change, stop.
8. Use synthetic canary content only. Do not copy real files or secrets into the test volumes.
9. Capture complete command output, exit codes, and timestamps. Keep raw execution output separate from interpretation. Preserve the transcript until the results report has been completed and its evidence references checked.
10. If any private-volume marker becomes visible in the other subject container, or a read-only write probe succeeds, stop further access testing immediately. Preserve the result; do not overwrite or delete the unexpected data as part of the test.
11. Cleanup is limited to the three exact RT-16 container names and three exact RT-16 volume names. Do not run broad prune commands.
12. If cleanup cannot be completed safely, stop and report the exact residual state. Do not improvise system-level cleanup.

## 6. Preconditions and Preflight

The commands below are intended for Windows PowerShell 5.1. Run each evidence block separately and preserve its full output. The preflight must pass before creating any volumes or containers.

### 6.1 Establish variables and record host/time context

```powershell
$ErrorActionPreference = 'Stop'
$Image = 'quay.io/fedora/fedora:latest'
$ContainerNames = @('tos-rt16-a', 'tos-rt16-b', 'tos-rt16-seed', 'tos-rt16-readcheck')
$VolumeNames = @('tos-rt16-private-a', 'tos-rt16-private-b', 'tos-rt16-shared-ro')

'===== RT-16 PREFLIGHT START ====='
Get-Date -Format o
'HOST:'
$env:COMPUTERNAME
'POWERSHELL:'
$PSVersionTable.PSVersion.ToString()
```

### 6.2 Verify runtime and local image

```powershell
'===== PODMAN VERSION ====='
Get-Date -Format o
podman version
if ($LASTEXITCODE -ne 0) { throw 'Podman version check failed.' }

'===== PODMAN MACHINE ====='
Get-Date -Format o
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Podman machine query failed.' }

'===== PODMAN CONNECTIONS ====='
Get-Date -Format o
podman system connection list
if ($LASTEXITCODE -ne 0) { throw 'Podman connection query failed.' }

'===== PODMAN ROOTLESS / ARCHITECTURE ====='
Get-Date -Format o
podman info --format 'Rootless={{.Host.Security.Rootless}}; Arch={{.Host.Arch}}'
if ($LASTEXITCODE -ne 0) { throw 'Podman info query failed.' }

'===== LOCAL IMAGE ====='
Get-Date -Format o
podman image exists $Image
if ($LASTEXITCODE -ne 0) { throw 'Required local image is missing. Stop; do not pull it.' }
podman image inspect --format 'ID={{.Id}}; OS={{.Os}}; Architecture={{.Architecture}}' $Image
if ($LASTEXITCODE -ne 0) { throw 'Local image inspection failed.' }
```

Preflight expectations:

- Client and Server are both Podman 6.2.1.
- `podman-machine-default` is running and uses the WSL provider.
- Rootless is `true`; server architecture is `arm64`.
- The local Fedora image exists and reports Linux ARM64.
- No image pull is performed.

### 6.3 Verify reserved-name availability

```powershell
'===== CONTAINER NAME COLLISION CHECK ====='
Get-Date -Format o
foreach ($Name in $ContainerNames) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "Reserved RT-16 container name already exists: $Name. Stop; do not remove it." }
    if ($CheckExit -ne 1) { throw "Could not reliably check container name $Name (exit $CheckExit). Stop." }
}

'===== VOLUME NAME COLLISION CHECK ====='
Get-Date -Format o
foreach ($Name in $VolumeNames) {
    podman volume exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "Reserved RT-16 volume name already exists: $Name. Stop; do not remove it." }
    if ($CheckExit -ne 1) { throw "Could not reliably check volume name $Name (exit $CheckExit). Stop." }
}
'RT16_PREFLIGHT_COMPLETE'
Get-Date -Format o
```

If any precondition fails, classify the execution as **BLOCKED**. Do not create resources or attempt to repair the environment under RT-16.

## 7. Controlled Test Procedure

### 7.1 Create the three empty named volumes

```powershell
'===== CREATE PRIVATE VOLUME A ====='
Get-Date -Format o
podman volume create tos-rt16-private-a
if ($LASTEXITCODE -ne 0) { throw 'Creation of private volume A failed.' }
Get-Date -Format o

'===== CREATE PRIVATE VOLUME B ====='
Get-Date -Format o
podman volume create tos-rt16-private-b
if ($LASTEXITCODE -ne 0) { throw 'Creation of private volume B failed.' }
Get-Date -Format o

'===== CREATE SHARED READ-ONLY FIXTURE VOLUME ====='
Get-Date -Format o
podman volume create tos-rt16-shared-ro
if ($LASTEXITCODE -ne 0) { throw 'Creation of the shared fixture volume failed.' }
Get-Date -Format o

'===== INSPECT CREATED VOLUMES ====='
Get-Date -Format o
podman volume inspect tos-rt16-private-a tos-rt16-private-b tos-rt16-shared-ro
if ($LASTEXITCODE -ne 0) { throw 'Named volume inspection failed.' }
```

Record the names, driver, labels (if present), and mountpoint metadata returned by Podman. The mountpoint is runtime-side metadata; do not access or alter it directly from Windows or WSL.

### 7.2 Seed the synthetic shared fixture

The helper container exists only to place one harmless marker in the shared volume before it is mounted read-only in both subject containers.

```powershell
'===== SEED SHARED READ-ONLY FIXTURE ====='
Get-Date -Format o
podman run --rm --pull=never --arch arm64 --network=none --name tos-rt16-seed --volume tos-rt16-shared-ro:/seed:rw,nocopy quay.io/fedora/fedora:latest sh -c 'printf "%s\n" "RT16_SHARED_RO_FIXTURE" > /seed/shared.txt && cat /seed/shared.txt'
$SeedExit = $LASTEXITCODE
if ($SeedExit -ne 0) { throw "Shared-volume seed helper failed (exit $SeedExit). Stop and preserve output." }
Get-Date -Format o

'===== VERIFY SEED HELPER REMOVED ====='
podman container exists tos-rt16-seed
$SeedExistsExit = $LASTEXITCODE
if ($SeedExistsExit -eq 0) { throw 'Seed helper still exists; stop and inspect before proceeding.' }
if ($SeedExistsExit -ne 1) { throw "Could not verify seed-helper absence (exit $SeedExistsExit)." }

'===== VERIFY SHARED FIXTURE VOLUME ====='
podman run --rm --pull=never --arch arm64 --network=none --name tos-rt16-readcheck --volume tos-rt16-shared-ro:/seed:ro,nocopy quay.io/fedora/fedora:latest sh -c 'test "$(cat /seed/shared.txt)" = "RT16_SHARED_RO_FIXTURE" && echo SHARED_FIXTURE_READ_PASS'
if ($LASTEXITCODE -ne 0) { throw 'Shared fixture could not be read back. Do not proceed to subject-container creation.' }
podman container exists tos-rt16-readcheck
if ($LASTEXITCODE -ne 1) { throw 'Read-check helper was not automatically removed or its absence could not be verified.' }
Get-Date -Format o
```

The verification command is a second short-lived read-only helper. It must also be removed automatically by `--rm`. It may use the reserved helper name again only after the previous helper's absence has been verified.

### 7.3 Start the two subject containers

```powershell
'===== CREATE CONTAINER A ====='
Get-Date -Format o
podman run -d --pull=never --arch arm64 --network=none --name tos-rt16-a --label tos.test=RT-16 --volume tos-rt16-private-a:/tos-private:rw,nocopy --volume tos-rt16-shared-ro:/tos-shared:ro,nocopy quay.io/fedora/fedora:latest sleep 900
if ($LASTEXITCODE -ne 0) { throw 'Container A creation failed. Stop, preserve output, and perform scoped cleanup.' }
Get-Date -Format o

'===== CREATE CONTAINER B ====='
Get-Date -Format o
podman run -d --pull=never --arch arm64 --network=none --name tos-rt16-b --label tos.test=RT-16 --volume tos-rt16-private-b:/tos-private:rw,nocopy --volume tos-rt16-shared-ro:/tos-shared:ro,nocopy quay.io/fedora/fedora:latest sleep 900
if ($LASTEXITCODE -ne 0) { throw 'Container B creation failed. Stop, preserve output, and perform scoped cleanup.' }
Get-Date -Format o

'===== SUBJECT CONTAINER STATE ====='
Get-Date -Format o
podman ps --filter name=tos-rt16
if ($LASTEXITCODE -ne 0) { throw 'Subject container state query failed.' }
```

Expected: both subject containers are running. The shared fixture volume is intentionally mounted read-only in both. Each private volume is attached only to its designated subject container.

### 7.4 Inspect effective mount configuration

```powershell
'===== CONTAINER A MOUNT INSPECTION ====='
Get-Date -Format o
podman inspect --format 'Name={{.Name}}; Privileged={{.HostConfig.Privileged}}; NetworkMode={{.HostConfig.NetworkMode}}{{range .Mounts}}{{printf "\nType=%s; Name=%s; Destination=%s; RW=%v" .Type .Name .Destination .RW}}{{end}}' tos-rt16-a
if ($LASTEXITCODE -ne 0) { throw 'Container A inspection failed.' }
Get-Date -Format o

'===== CONTAINER B MOUNT INSPECTION ====='
Get-Date -Format o
podman inspect --format 'Name={{.Name}}; Privileged={{.HostConfig.Privileged}}; NetworkMode={{.HostConfig.NetworkMode}}{{range .Mounts}}{{printf "\nType=%s; Name=%s; Destination=%s; RW=%v" .Type .Name .Destination .RW}}{{end}}' tos-rt16-b
if ($LASTEXITCODE -ne 0) { throw 'Container B inspection failed.' }
Get-Date -Format o
```

Required configuration observations:

- Both containers are `Privileged=false` and report `NetworkMode=none`.
- Container A has `tos-rt16-private-a` at `/tos-private` with `RW=true`; it does not have `tos-rt16-private-b` attached.
- Container B has `tos-rt16-private-b` at `/tos-private` with `RW=true`; it does not have `tos-rt16-private-a` attached.
- Both containers have `tos-rt16-shared-ro` at `/tos-shared` with `RW=false`.
- All inspected mounts are named volumes (`Type=volume`), not host bind mounts.

If Podman inspection does not establish this exact mount layout, stop the behavioral access tests and preserve the output. Do not attempt to correct a live container by changing its mounts.

### 7.5 Verify root filesystem-layer separation

First confirm that neither marker path already exists in either base container. This prevents pre-existing image content from being mistaken for cross-container visibility.

```powershell
'===== ROOTFS MARKER BASELINE ABSENCE ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'test ! -e /rt16-rootfs-a.txt && test ! -e /rt16-rootfs-b.txt && echo A_ROOTFS_MARKER_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Container A rootfs marker path was not initially absent.' }
podman exec tos-rt16-b sh -c 'test ! -e /rt16-rootfs-a.txt && test ! -e /rt16-rootfs-b.txt && echo B_ROOTFS_MARKER_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Container B rootfs marker path was not initially absent.' }
Get-Date -Format o

'===== WRITE ROOTFS MARKER IN A ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'printf "%s\n" "RT16_ROOTFS_A" > /rt16-rootfs-a.txt && test "$(cat /rt16-rootfs-a.txt)" = "RT16_ROOTFS_A" && echo A_ROOTFS_WRITE_READ_PASS'
if ($LASTEXITCODE -ne 0) { throw 'Container A rootfs positive control failed.' }
Get-Date -Format o

'===== VERIFY A ROOTFS MARKER ABSENT IN B ====='
podman exec tos-rt16-b sh -c 'if test -e /rt16-rootfs-a.txt; then echo A_ROOTFS_MARKER_VISIBLE_IN_B; exit 41; else echo A_ROOTFS_MARKER_NOT_VISIBLE_IN_B; fi'
if ($LASTEXITCODE -ne 0) { throw 'Container rootfs separation check failed or returned an unexpected result.' }
Get-Date -Format o

'===== WRITE ROOTFS MARKER IN B ====='
podman exec tos-rt16-b sh -c 'printf "%s\n" "RT16_ROOTFS_B" > /rt16-rootfs-b.txt && test "$(cat /rt16-rootfs-b.txt)" = "RT16_ROOTFS_B" && echo B_ROOTFS_WRITE_READ_PASS'
if ($LASTEXITCODE -ne 0) { throw 'Container B rootfs positive control failed.' }
Get-Date -Format o

'===== VERIFY B ROOTFS MARKER ABSENT IN A ====='
podman exec tos-rt16-a sh -c 'if test -e /rt16-rootfs-b.txt; then echo B_ROOTFS_MARKER_VISIBLE_IN_A; exit 42; else echo B_ROOTFS_MARKER_NOT_VISIBLE_IN_A; fi'
if ($LASTEXITCODE -ne 0) { throw 'Reciprocal rootfs separation check failed or returned an unexpected result.' }
Get-Date -Format o
```

### 7.6 Verify positive access to each private named volume

Confirm that both new volume canary paths are initially absent. This ensures the positive and negative checks are based on this Run's data.

```powershell
'===== PRIVATE VOLUME CANARY BASELINE ABSENCE ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'test ! -e /tos-private/private-a.txt && test ! -e /tos-private/private-b.txt && echo A_PRIVATE_CANARY_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Container A private-volume canary paths were not initially absent.' }
podman exec tos-rt16-b sh -c 'test ! -e /tos-private/private-a.txt && test ! -e /tos-private/private-b.txt && echo B_PRIVATE_CANARY_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Container B private-volume canary paths were not initially absent.' }
Get-Date -Format o

'===== CONTAINER A PRIVATE VOLUME POSITIVE CONTROL ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'printf "%s\n" "RT16_PRIVATE_A_CANARY" > /tos-private/private-a.txt && test "$(cat /tos-private/private-a.txt)" = "RT16_PRIVATE_A_CANARY" && echo A_PRIVATE_VOLUME_READ_WRITE_PASS'
if ($LASTEXITCODE -ne 0) { throw 'Container A own-volume positive control failed.' }
Get-Date -Format o

'===== CONTAINER B PRIVATE VOLUME POSITIVE CONTROL ====='
Get-Date -Format o
podman exec tos-rt16-b sh -c 'printf "%s\n" "RT16_PRIVATE_B_CANARY" > /tos-private/private-b.txt && test "$(cat /tos-private/private-b.txt)" = "RT16_PRIVATE_B_CANARY" && echo B_PRIVATE_VOLUME_READ_WRITE_PASS'
if ($LASTEXITCODE -ne 0) { throw 'Container B own-volume positive control failed.' }
Get-Date -Format o
```

Both positive controls must pass. A failure here means a negative visibility result cannot be accepted as proof of isolation.

### 7.7 Verify reciprocal private-volume non-visibility

```powershell
'===== A MUST NOT SEE B PRIVATE CANARY ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'if test -e /tos-private/private-b.txt; then echo B_PRIVATE_CANARY_VISIBLE_IN_A; exit 51; else echo B_PRIVATE_CANARY_NOT_VISIBLE_IN_A; fi'
if ($LASTEXITCODE -ne 0) { throw 'Cross-volume non-visibility check A-to-B failed.' }
Get-Date -Format o

'===== B MUST NOT SEE A PRIVATE CANARY ====='
Get-Date -Format o
podman exec tos-rt16-b sh -c 'if test -e /tos-private/private-a.txt; then echo A_PRIVATE_CANARY_VISIBLE_IN_B; exit 52; else echo A_PRIVATE_CANARY_NOT_VISIBLE_IN_B; fi'
if ($LASTEXITCODE -ne 0) { throw 'Cross-volume non-visibility check B-to-A failed.' }
Get-Date -Format o

'===== RECONFIRM PRIVATE VOLUME ATTACHMENTS — A ====='
Get-Date -Format o
podman inspect --format 'A: {{range .Mounts}}{{.Name}} -> {{.Destination}} (RW={{.RW}}) {{end}}' tos-rt16-a
if ($LASTEXITCODE -ne 0) { throw 'Final private-volume mount inspection for A failed.' }
Get-Date -Format o

'===== RECONFIRM PRIVATE VOLUME ATTACHMENTS — B ====='
Get-Date -Format o
podman inspect --format 'B: {{range .Mounts}}{{.Name}} -> {{.Destination}} (RW={{.RW}}) {{end}}' tos-rt16-b
if ($LASTEXITCODE -ne 0) { throw 'Final private-volume mount inspection for B failed.' }
Get-Date -Format o
```

Interpretation is deliberately precise: the private canary must be absent because the other private volume is not attached to that container. Record this as **NOT VISIBLE / NOT MOUNTED**, not as a POSIX permission-denied result. RT-16 does not test direct access to a volume after deliberately mounting that volume in both containers.

If a marker is visible in the other container, stop further access tests immediately, preserve all outputs, and classify the result as FAIL unless investigation shows that the approved mount layout was not actually established; in that case classify it as INCONCLUSIVE and document the evidence.

### 7.8 Verify shared read-only mount behavior

```powershell
'===== READ-ONLY WRITE-PROBE BASELINE ABSENCE ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'test ! -e /tos-shared/rt16-write-probe-a.txt && test ! -e /tos-shared/rt16-write-probe-b.txt && echo A_READ_ONLY_PROBE_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Read-only probe paths were not initially absent in Container A.' }
podman exec tos-rt16-b sh -c 'test ! -e /tos-shared/rt16-write-probe-a.txt && test ! -e /tos-shared/rt16-write-probe-b.txt && echo B_READ_ONLY_PROBE_PATHS_ABSENT'
if ($LASTEXITCODE -ne 0) { throw 'Read-only probe paths were not initially absent in Container B.' }
Get-Date -Format o

'===== CONTAINER A READ-ONLY VOLUME CHECK ====='
Get-Date -Format o
podman exec tos-rt16-a sh -c 'test "$(cat /tos-shared/shared.txt)" = "RT16_SHARED_RO_FIXTURE" || exit 61; echo A_SHARED_VOLUME_READ_PASS; if printf "%s\n" "UNEXPECTED_A_WRITE" > /tos-shared/rt16-write-probe-a.txt; then echo A_READ_ONLY_WRITE_UNEXPECTEDLY_ALLOWED; exit 62; else echo A_READ_ONLY_WRITE_DENIED; fi'
if ($LASTEXITCODE -ne 0) { throw 'Container A read-only mount check failed or write was unexpectedly allowed. Stop.' }
Get-Date -Format o

'===== CONTAINER B READ-ONLY VOLUME CHECK ====='
Get-Date -Format o
podman exec tos-rt16-b sh -c 'test "$(cat /tos-shared/shared.txt)" = "RT16_SHARED_RO_FIXTURE" || exit 63; echo B_SHARED_VOLUME_READ_PASS; if printf "%s\n" "UNEXPECTED_B_WRITE" > /tos-shared/rt16-write-probe-b.txt; then echo B_READ_ONLY_WRITE_UNEXPECTEDLY_ALLOWED; exit 64; else echo B_READ_ONLY_WRITE_DENIED; fi'
if ($LASTEXITCODE -ne 0) { throw 'Container B read-only mount check failed or write was unexpectedly allowed. Stop.' }
Get-Date -Format o

'===== FINAL READ-ONLY MOUNT CONFIGURATION — A ====='
Get-Date -Format o
podman inspect --format 'A: {{range .Mounts}}{{if eq .Destination "/tos-shared"}}{{.Name}} -> {{.Destination}} (RW={{.RW}}){{end}}{{end}}' tos-rt16-a
if ($LASTEXITCODE -ne 0) { throw 'Final read-only mount inspection for A failed.' }
Get-Date -Format o

'===== FINAL READ-ONLY MOUNT CONFIGURATION — B ====='
Get-Date -Format o
podman inspect --format 'B: {{range .Mounts}}{{if eq .Destination "/tos-shared"}}{{.Name}} -> {{.Destination}} (RW={{.RW}}){{end}}{{end}}' tos-rt16-b
if ($LASTEXITCODE -ne 0) { throw 'Final read-only mount inspection for B failed.' }
Get-Date -Format o
```

Expected: both containers can read the known fixture, both write probes fail with an OS diagnostic identifying the read-only filesystem condition (normally `Read-only file system`), and Podman inspection reports the shared volume mounted with `RW=false` in both containers. The operator must preserve the exact stderr text. A generic denial such as `Permission denied` without evidence tying it to the read-only mount is INCONCLUSIVE, not a PASS. A write probe that succeeds is an immediate stop condition and a FAIL for the read-only control.

## 8. Cleanup and Post-Test Verification

Cleanup begins only after the evidence above has been preserved, or immediately after a stop condition once the relevant failure output has been captured.

### 8.1 Record pre-cleanup state

```powershell
'===== RT-16 PRE-CLEANUP STATE ====='
Get-Date -Format o
podman ps -a --filter name=tos-rt16
if ($LASTEXITCODE -ne 0) { throw 'Pre-cleanup RT-16 container listing failed.' }
podman volume ls --filter name=^tos-rt16-
if ($LASTEXITCODE -ne 0) { throw 'Pre-cleanup RT-16 volume listing failed.' }
Get-Date -Format o
```

### 8.2 Remove only the exact RT-16 containers that exist

The per-name check supports safe cleanup even if execution stopped before every planned container was created. It never targets an unlisted container.

```powershell
'===== REMOVE ONLY EXISTING RT-16 CONTAINERS ====='
Get-Date -Format o
foreach ($Name in $ContainerNames) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) {
        podman rm -f $Name
        if ($LASTEXITCODE -ne 0) { throw "Could not remove RT-16 container $Name. Stop and inspect exact residual state." }
    }
    elseif ($CheckExit -ne 1) {
        throw "Could not reliably determine whether RT-16 container $Name exists (exit $CheckExit). Stop."
    }
}
Get-Date -Format o
```

The seed and read-back helper containers use `--rm`; this loop also checks the reserved seed name so an unexpected residual can be reported and removed only by its exact reserved name.

### 8.3 Verify container absence, then remove only existing RT-16 volumes

```powershell
'===== VERIFY RT-16 CONTAINER ABSENCE ====='
Get-Date -Format o
podman ps -a --filter name=tos-rt16
if ($LASTEXITCODE -ne 0) { throw 'RT-16 post-removal container listing failed.' }
foreach ($Name in $ContainerNames) {
    podman container exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "RT-16 container remains: $Name. Do not proceed to volume removal." }
    if ($CheckExit -ne 1) { throw "Could not verify container absence for $Name (exit $CheckExit)." }
}

'===== REMOVE ONLY EXISTING RT-16 NAMED VOLUMES ====='
Get-Date -Format o
foreach ($Name in $VolumeNames) {
    podman volume exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) {
        podman volume rm $Name
        if ($LASTEXITCODE -ne 0) { throw "Could not remove RT-16 volume $Name. Stop; do not run volume prune." }
    }
    elseif ($CheckExit -ne 1) {
        throw "Could not reliably determine whether RT-16 volume $Name exists (exit $CheckExit). Stop."
    }
}
Get-Date -Format o

'===== VERIFY RT-16 VOLUME ABSENCE ====='
foreach ($Name in $VolumeNames) {
    podman volume exists $Name
    $CheckExit = $LASTEXITCODE
    if ($CheckExit -eq 0) { throw "RT-16 volume remains: $Name. Do not run volume prune." }
    if ($CheckExit -ne 1) { throw "Could not verify volume absence for $Name (exit $CheckExit)." }
}
'RT16_CLEANUP_COMPLETE'
Get-Date -Format o
```

### 8.4 Verify Podman machine and rootless state remain available

```powershell
'===== POST-CLEANUP PODMAN MACHINE ====='
Get-Date -Format o
podman machine list
if ($LASTEXITCODE -ne 0) { throw 'Post-cleanup machine query failed.' }

'===== POST-CLEANUP PODMAN INFO ====='
podman info --format 'Rootless={{.Host.Security.Rootless}}; Arch={{.Host.Arch}}'
if ($LASTEXITCODE -ne 0) { throw 'Post-cleanup Podman info failed.' }
'RT16_POSTCHECK_COMPLETE'
Get-Date -Format o
```

Expected: `podman-machine-default` remains running; `Rootless=true`; architecture remains ARM64; all named RT-16 containers and volumes are absent. Do not stop or remove the Podman machine or local image.

## 9. Acceptance Criteria and Result Classification

### 9.1 PASS

PASS requires all of the following:

1. Preflight confirms the expected Podman runtime, rootless state, ARM64 architecture, running WSL machine, existing local image, and no reserved-name collisions.
2. Both subject containers start successfully; inspection reports `Privileged=false` and `NetworkMode=none` for each.
3. Container A's rootfs marker is not visible in B, and B's rootfs marker is not visible in A.
4. Both private named volumes are distinct and mounted only in their designated subject containers at `/tos-private` with `RW=true`.
5. Each subject container can create and read its own private-volume canary.
6. Each private canary is not visible at the corresponding private mount path in the other container; the mount inspection confirms the other private volume is not attached.
7. The shared fixture is readable in both containers.
8. Both shared-volume write probes are denied with an OS diagnostic identifying the read-only filesystem condition, and Podman inspection reports `RW=false` for the shared mount in both subject containers. A generic or unattributed denial does not satisfy this criterion.
9. All required evidence and timestamps are preserved.
10. Only RT-16 test resources are removed; their absence is verified; the Podman machine remains running and rootless.

### 9.2 PARTIAL PASS

Use PARTIAL PASS only when all functional controls pass (rootfs-layer separation, private-volume positive and reciprocal non-visibility controls, and attributable read-only write denial), but a non-critical evidence item or supplementary read-back is incomplete. State exactly which evidence item is incomplete. If any functional control cannot be attributed or verified, use INCONCLUSIVE; do not use PARTIAL PASS if an unauthorized private canary is visible or a read-only write succeeds.

### 9.3 FAIL

Use FAIL if any of the following is established:

- A private canary is visible in the other subject container despite the approved distinct-volume layout being reported as mounted.
- A shared volume configured as read-only is writable from either subject container.
- Podman reports an unintended cross-attachment of the private volumes and the associated private data becomes accessible.
- A container unexpectedly runs privileged or the required rootless/non-privileged boundary is not maintained during execution.

Stop immediately after preserving the failing evidence. Do not continue with further write probes.

### 9.4 BLOCKED

Use BLOCKED when a precondition fails before valid test execution begins, such as the runtime being unavailable, the local image being missing, or a reserved name already existing. A blocked test is not evidence that filesystem isolation failed.

### 9.5 INCONCLUSIVE

Use INCONCLUSIVE when the test cannot reliably establish the expected mount layout or attribute observed behavior to the configured Podman mounts—for example, inspection output is incomplete, an operation fails for an unrelated reason, or an unexpected runtime error prevents valid positive and negative controls.

## 10. Evidence Requirements

Retain the following raw evidence with a timestamp for each evidence block:

- Host name, PowerShell version, date/time, Podman client/server versions, machine status, rootless state, architecture, local image ID and architecture.
- Reserved-name checks for all four reserved container names (two subject containers and two disposable helpers) and all three volume names.
- Volume creation and inspection outputs.
- Seed-helper command, output, exit code, and automatic-removal confirmation.
- Both subject-container creation commands and returned IDs.
- `podman ps` state before tests.
- Complete Podman inspection mount details for A and B.
- Rootfs positive controls and reciprocal non-visibility outputs.
- Private-volume positive controls and reciprocal non-visibility outputs.
- Shared-volume read controls, denied write-probe outputs, and final `RW=false` inspections.
- Pre-cleanup listing, removal outputs, container/volume absence checks, and post-cleanup machine/rootless checks.
- Any stop condition, error, unexpected output, or deviation.

Evidence must distinguish raw observation from interpretation. Do not reconstruct missing timestamps. Do not include credentials, tokens, user files, or other non-test data.

## 11. Risks and Interpretation Limits

1. **Mount-attachment boundary only:** A private volume that is not mounted in a container is not visible through that container's mount namespace. The test does not prove that a malicious process with access to the Podman API, host account, Podman machine, or privileged runtime control plane cannot attach or inspect that volume.
2. **Not a DAC/ACL test:** Cross-volume non-visibility is not the same as an OS permission-denied result on a file deliberately mounted into both containers. RT-16 makes no such claim.
3. **Read-only scope:** Successful read plus a write denial attributable to the read-only filesystem state, together with `RW=false` inspection, demonstrates the tested read-only mount behavior for the tested paths and operations. A generic permission failure is insufficient. This does not prove every possible kernel or filesystem write path is blocked in all circumstances.
4. **Rootless scope:** Rootless Podman reduces host privilege exposure but is not proof against host-user compromise, kernel vulnerabilities, or container escape.
5. **Volume lifecycle:** Named volumes persist independently of containers; therefore cleanup must explicitly remove the three named test volumes. Container removal alone is insufficient.
6. **No production inference:** This test does not establish production security certification, complete Agent isolation, evidence integrity, safe concurrent workloads, five-Agent capacity, or final Runtime suitability.
7. **SELinux/AppArmor:** RT-16 does not enable, configure, or claim enforcement from either Linux security module. No volume relabeling option is used.

## 12. Decision Output

The results report must record:

- Overall result: PASS / PARTIAL PASS / FAIL / BLOCKED / INCONCLUSIVE.
- Actual runtime and image identity.
- Effective mount table for each subject container.
- Rootfs separation result in both directions.
- Private-volume positive-control and reciprocal non-visibility results.
- Shared read-only positive-read and denied-write results.
- Cleanup and post-test runtime-state results.
- Evidence IDs, timestamps, exit codes, deviations, and limitations.
- A conclusion restricted to the exact filesystem layers, volumes, containers, and operations tested.

No broader ID-15 gap is closed automatically by RT-16. Any later decision to map the result into Agent-level authorization or final Runtime selection requires separate architectural review.

## 13. References

The installed runtime is Podman 6.2.1. The following versioned Podman 6.2.0 manuals are used as the nearest versioned documentation references for this 6.2.x patch release. They document the command behavior and options used by this plan; local preflight still records the actual installed version.

1. Podman 6.2.0 `podman-run` manual — `--pull=never`, `--network=none`, named-volume mounts, `rw` / `ro`, and `nocopy` options:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-run.1.html
2. Podman 6.2.0 `podman-volume-create` manual — creation of Podman-managed named volumes:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-volume-create.1.html
3. Podman 6.2.0 `podman-volume-inspect` manual — inspection of one or more volume metadata records:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-volume-inspect.1.html
4. Podman 6.2.0 `podman-volume-exists` manual — exit status `0` when a volume exists and `1` when it does not:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-volume-exists.1.html
5. Podman 6.2.0 `podman-volume-ls` manual — name filtering for the pre-cleanup RT-16 volume inventory:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-volume-ls.1.html
6. Podman 6.2.0 `podman-volume-rm` manual — removal of explicitly named volumes and non-forced behavior when a volume is in use:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-volume-rm.1.html
7. Podman 6.2.0 `podman-inspect` manual — container configuration inspection using Go templates:  
   https://docs.podman.io/en/v6.2.0/markdown/podman-inspect.1.html

The references document supported Podman mechanisms. They do not establish that the RT-16 test has passed on the target device.

## 14. Document Control

| Field | Value |
|---|---|
| Document ID | TOS-RT-16-FAI-TP-001 |
| Version | 1.0 |
| Document Status | FINAL — ACCEPTED |
| Plan Acceptance | ACCEPTED |
| Test Execution | NOT STARTED |
| Execution Authorization | NOT GRANTED |
| GitHub Upload | UPLOADED — VERIFIED ON MAIN |
| User Acceptance | ACCEPTED — USER CONFIRMED |
| Scope | RT-16 filesystem-layer separation, private named-volume attachment, and read-only named-volume mount behavior only |
| Runtime Selection | NOT FINALIZED |
| Production Authorization | NOT GRANTED |
| Next Stage | RT-16 execution only after plan acceptance, GitHub verification, and separate execution authorization |

---

**End of document**
