# TOS ID-15N — Runtime Feasibility Results Report

**Document ID:** TOS-ID-15N-RFR-001  
**Version:** 1.0  
**Status:** FINAL — READY FOR USER REVIEW / GITHUB UPLOAD  
**Assessment Track:** ID-15 — Independent Execution Environment Feasibility Assessment  
**Assessment Date:** 2026-11-26  
**Host Platform:** Microsoft Surface Pro X — Windows 11 Pro ARM64  
**Runtime Candidate:** Podman 6.2.1 with WSL2  
**Authority:** TOS Human Authority / System Owner

---

## 1. Purpose

This report records the results of the initial Podman + WSL2 runtime feasibility investigation performed on the Surface Pro X ARM64 host.

It consolidates the observed installation, runtime, container-execution, namespace, and security-configuration results. It distinguishes demonstrated capabilities from partial findings, untested controls, and remaining limitations relevant to the ID-15 environment assessment.

This report is a companion results record to the existing **TOS ID-15N — Minimum Runtime Isolation Test Plan**. It does not replace that plan or claim that its Windows-specific RT-10, RT-11, or RT-12 tests have passed.

This report does not:

- Select a final TOS execution environment.
- Demonstrate independent execution of the five TOS Agents.
- Establish Production readiness or a Production security boundary.
- Authorize execution of real Agents, access to production credentials, or live trading operations.
- Modify or reopen the frozen TOS architecture.

## 2. Assessment Context and Scope

The Windows Sandbox investigation under ID-15M did not establish an operational Sandbox runtime. The recorded disposition was **NOT OPERATIONAL — HOST ENVIRONMENT BLOCKER / ROOT CAUSE UNRESOLVED**. The Sandbox investigation was not repeated as part of this Podman assessment.

Following that result, WSL2 and Podman were approved as an alternative runtime-feasibility path. Windows 11 Repair Reinstall had been completed before the WSL2/Podman tests. Post-repair checks supplied during the assessment reported:

- DISM ScanHealth: no component-store corruption detected.
- SFC VerifyOnly: no integrity violations.
- Windows build: 26300.10075, ARM64.

WSL2 was installed and configured. Podman was installed in User Scope with the WSL provider. The tests in this report used synthetic container images and test workloads only. No TOS Agent prompt, real trading data, production credential, or live trading operation was used.

### 2.1 Included

- Podman installation and WSL machine initialization.
- Client/server connectivity and runtime state.
- ARM64 container execution.
- A limited concurrent-container test.
- Basic PID, Mount, Network, and User Namespace observations.
- Rootless UID mapping and selected effective container security settings.
- Cleanup and final runtime-state verification.

### 2.2 Excluded

- Independent execution of any TOS Educational Agent.
- Execution or capacity validation of all five Agents.
- Production deployment or security approval.
- Container escape, kernel exploit, or adversarial penetration testing.
- Cross-container filesystem-denial testing.
- Network-egress policy testing.
- Sustained resource, stress, or host-exhaustion testing.
- Per-container CPU/memory enforcement testing.
- Full per-Run Evidence Package generation and independent audit for every test.

## 3. Host and Runtime Configuration

### 3.1 Host Environment

| Property | Observed value |
|---|---|
| Device | Microsoft Surface Pro X |
| Operating system | Windows 11 Pro |
| Windows build | 10.0.26300.10075 |
| Architecture | ARM64 |
| Processor | Qualcomm SQ1 |
| Logical processors | 8 |
| Installed RAM | Approximately 16 GB |
| WSL version | 3.0.5.0 |
| WSL kernel | 6.18.58.1-1 |
| WSL default version | 2 |

### 3.2 Podman Configuration

| Property | Observed value |
|---|---|
| Podman Client | 6.2.1 |
| Podman Server | 6.2.1 |
| Client OS/architecture | Windows ARM64 |
| Server OS/architecture | Linux ARM64 |
| Provider | WSL |
| Machine | `podman-machine-default` |
| Machine state at final check | Running |
| Machine configuration metadata | 2 CPUs / 4 GiB memory / 50 GiB disk |
| Runtime mode | Rootless |
| Container engine | crun 1.31 |
| Network backend | netavark |
| Cgroup mode/version | cgroup v2 observed |
| System manager | systemd |

**WSL resource interpretation:** The Podman machine listing and machine configuration contained values of 2 CPUs and 4 GiB. Under the WSL provider, the Linux environment shares WSL's virtualized kernel and resource environment; those displayed/configured values must not be interpreted as independently enforced per-container limits or as evidence that the WSL guest is restricted to those values. In the supplied checks, Linux reported 8 CPUs and approximately 7.5 GiB RAM. `/sys/fs/cgroup/cpu.max` and `/sys/fs/cgroup/memory.max` both reported `max`. No global WSL resource cap or per-container CPU/memory limit was configured during this assessment.

## 4. Installation and Runtime Verification

### 4.1 Installation and Initialization

**Result: PASS**

The supplied evidence showed:

- The Windows ARM64 installer was used.
- The installer Authenticode signature was valid; signer: Red Hat LLC.
- Podman 6.2.1 was installed in User Scope.
- The Podman WSL provider configuration was present.
- `podman machine init` completed successfully.
- `podman machine start` completed successfully.

### 4.2 Client–Server Connectivity

**Result: PASS**

The native Windows ARM64 Podman client successfully communicated with the Linux ARM64 Podman server. `podman version` reported version 6.2.1 for both Client and Server. `podman info` reported the ARM64 host architecture, rootless operation, cgroup v2, crun, and an active remote connection.

### 4.3 Final Runtime State and Cleanup

**Result: PASS**

The final supplied state check reported:

- `podman ps -a`: no remaining containers.
- `podman machine list`: `podman-machine-default` was running.
- `podman version`: Client and Server were both 6.2.1.
- `podman info`: `Rootless=true` and `Arch=arm64`.

The attempted template expression for `CgroupVersion` returned a template-field error because that field was not available at the referenced location in the returned data structure. This was a reporting/template error, not evidence of runtime failure. cgroup v2 had been observed in the earlier Linux/cgroup diagnostics and `podman info` inspection.

## 5. Container Execution Tests

### 5.1 ARM64 Container Smoke Test

**Result: PASS**

A Fedora ARM64 container was pulled from Quay.io and executed with an explicit ARM64 architecture selection. The supplied output included:

```text
CONTAINER_SMOKE_TEST_OK
 aarch64
Fedora Linux 45
```

This demonstrates actual Linux ARM64 container execution through Podman on the WSL2 environment.

### 5.2 Registry Access Observation

An earlier image pull from Docker Hub failed with:

```text
403 RBAC: access denied
```

A separate pull from Quay.io succeeded, including execution of the Fedora ARM64 smoke test.

**Interpretation:** Docker Hub access failed in the tested attempt, while Quay.io access succeeded. The Docker Hub response is treated as a registry-specific access failure in that attempt; it is not evidence that the Podman runtime itself failed. Registry availability remains dependent on the registry and network conditions.

### 5.3 Concurrent Container Execution

**Result: PASS — LIMITED TEST**

Three Fedora containers were started concurrently. `podman ps` showed all three running, and `podman stats --no-stream` returned resource observations for each container. The workloads were lightweight sleep processes.

This demonstrates basic concurrent container operation only. It does not establish sustained workload capacity, five-Agent concurrency, resource isolation under contention, or Production resource sufficiency.

## 6. Namespace Isolation Findings

**Result: PARTIAL PASS — BASIC NAMESPACE SEPARATION OBSERVED**

Two containers were inspected. The supplied namespace identifiers were:

| Namespace | Container 01 | Container 02 | Observation |
|---|---:|---:|---|
| PID | `4026532303` | `4026532378` | Different |
| Mount | `4026532305` | `4026532380` | Different |
| Network | `4026532229` | `4026532306` | Different |
| User | `4026532295` | `4026532295` | Shared |

Both containers reported UID 0 inside the container. The UID mapping observed in both was:

```text
         0       1000          1
         1     524288    1000000
```

The mapping shows that container UID 0 mapped to Linux host-side UID 1000, rather than host root. The same User Namespace identifier was observed for both containers.

**Interpretation:**

- Different PID, Mount, and Network Namespace identifiers were demonstrated for the tested containers.
- The User Namespace was shared between the two inspected containers; independent User Namespace separation was not demonstrated.
- Rootless UID mapping was observed.
- These observations are not a container-escape test and do not establish a complete security boundary.

## 7. Effective Container Security Configuration

**Result: PARTIAL PASS**

The inspected container configuration and runtime security information reported:

| Setting | Observed value |
|---|---|
| `Privileged` | `false` |
| Host Rootless mode | `true` |
| Seccomp | Enabled |
| Seccomp profile path | `/usr/share/containers/seccomp.json` |
| Process Seccomp mode | `2` |
| PID mode | `private` |
| Cgroup mode | `private` |
| Network mode | `pasta` |
| Read-only root filesystem | `false` |
| AppArmor | Disabled / not active in reported host security data |
| SELinux | Disabled / not active in reported host security data |
| Explicit `CapDrop` | None configured in inspected HostConfig |
| Per-container CPU limit | None configured (`0` in inspected HostConfig) |
| Per-container memory limit | None configured (`0` in inspected HostConfig) |
| PidsLimit | `2048` |

Positive observations:

- Rootless mode was active.
- The inspected container was not privileged.
- Seccomp filtering was active.
- PID and cgroup modes were private.
- A PIDs limit was present in the inspected configuration.

Limitations:

- The root filesystem was writable.
- AppArmor and SELinux were not active in the reported security configuration.
- No explicit capability-drop configuration was present in the inspected HostConfig.
- No per-container CPU or memory limit was configured.
- The PidsLimit setting alone does not establish comprehensive process or resource isolation.

These observations require policy-specific review before any production use. They do not, on their own, establish a vulnerability or prove that the runtime is unsafe; they identify controls not demonstrated or not configured in the inspected test container.

## 8. Capabilities and Privilege Boundary Findings

**Result: PARTIAL PASS**

The inspected container process reported:

- `uid=0(root)` inside the container.
- `Privileged=false` in the container configuration.
- Rootless host operation.
- UID mapping to Linux host-side UID 1000.
- `Seccomp=2` and one active seccomp filter.
- `NoNewPrivs=0`.

The effective capability mask was:

```text
00000000800405fb
```

The decoded capabilities included:

- `CAP_CHOWN`
- `CAP_DAC_OVERRIDE`
- `CAP_FOWNER`
- `CAP_FSETID`
- `CAP_KILL`
- `CAP_SETGID`
- `CAP_SETUID`
- `CAP_SETPCAP`
- `CAP_NET_BIND_SERVICE`
- `CAP_SYS_CHROOT`
- `CAP_SETFCAP`

The `podman unshare` inspection attempts failed because the native Windows remote client does not support that command. This is a limitation of that inspection method; it is not evidence of a Podman runtime failure.

**Interpretation:** The available evidence supports rootless execution, UID mapping, non-privileged container configuration, and active seccomp filtering. It does not establish a complete privilege boundary or resistance to container escape. `NoNewPrivs=0` is recorded as a hardening limitation for the inspected process; it is not, by itself, proof of an exploitable vulnerability.

## 9. Resource and Architecture Limitations

The Linux environment reported 8 logical CPUs and approximately 7.5 GiB RAM. The WSL cgroup checks reported no global CPU or memory ceiling (`max`). The inspected container HostConfig also showed no per-container CPU or memory limit.

No `.wslconfig` file was present during the supplied inspection. No global WSL resource cap was applied as part of this assessment.

No resource-stress, sustained workload, or five-Agent capacity test was performed. The lightweight three-container test must not be used to infer capacity for real Agent workloads.

The observed ARM64 success establishes feasibility on this specific host and software combination. It must not be generalized into a guarantee of full automated ARM64 support across all Podman/WSL configurations.

## 10. Relationship to the Existing ID-15N Test Plan

The Podman investigation is an alternative runtime-feasibility path. It does not replace the Windows identity and ACL tests defined in the existing ID-15N Minimum Runtime Isolation Test Plan.

| Planned test | Status in this assessment | Interpretation |
|---|---|---|
| RT-09 — Windows Sandbox readiness | HOLD / not operational; no retest | Previously recorded Sandbox findings remain; Podman testing did not change that status |
| RT-10 — Windows OS identity enforcement | NOT EXECUTED | Temporary Windows test identities and independent process-token inspection were not performed |
| RT-11 — Direct filesystem access denial | NOT EXECUTED | Reciprocal Windows account ACL tests were not performed |
| RT-12 — Windows Job Object resource controls | DEFERRED | No Job Object test was performed |

The Podman namespace and security observations provide additional evidence about a Linux container candidate. They must not be represented as PASS results for RT-10, RT-11, or RT-12.

## 11. Gap Mapping

| Gap area | Evidence from this assessment | Status |
|---|---|---|
| GAP-01 — Independent execution identity | Rootless UID mapping observed, but the inspected containers shared the same User Namespace; independent per-container identity enforcement not demonstrated | OPEN — partial supporting evidence only |
| GAP-02 — Direct filesystem isolation | Different Mount Namespace identifiers observed; cross-container file-access denial and volume/bind-mount access controls not tested | OPEN |
| GAP-03 — Runtime/resource isolation | cgroup v2 and private cgroup mode observed; no per-container CPU/memory enforcement test | OPEN |
| Container privilege boundary | Rootless, non-privileged configuration and seccomp observed; hardening limitations remain | PARTIAL |
| Network isolation | Different Network Namespace identifiers observed; egress policy and traffic-control enforcement not tested | PARTIAL |
| Evidence integrity and run-level controls | Not tested by this Podman investigation | OPEN |
| Independent Agent execution | Not performed | OPEN |
| Five-Agent concurrency and capacity | Not tested | OPEN |
| Container escape resistance | Not tested | OPEN |

No broader ID-15 gap is closed solely by the results in this report.

## 12. Outstanding Limitations and Risks

The following remain material to any future runtime decision:

1. Independent User Namespace separation was not demonstrated; the inspected containers shared a User Namespace.
2. AppArmor and SELinux were not active in the reported runtime security configuration.
3. The inspected container root filesystem was writable.
4. Explicit capability dropping was not configured in the inspected container.
5. `NoNewPrivs` was not active for the inspected process.
6. Per-container CPU and memory limits were not configured or tested.
7. Cross-container filesystem access denial was not tested.
8. Network egress restrictions and policy enforcement were not tested.
9. Container escape resistance was not tested.
10. No independent execution of the TOS Agents was performed.
11. No five-Agent concurrency or sustained workload test was performed.
12. A complete per-Run Evidence Package was not independently produced and reviewed for every test in this investigation.
13. The ARM64 result is host-specific feasibility evidence, not a universal compatibility guarantee.

These limitations prevent a Production security or readiness conclusion.

## 13. Overall Feasibility Assessment

**Finding: BASIC RUNTIME FEASIBILITY DEMONSTRATED — Podman 6.2.1 + WSL2 on the tested Surface Pro X ARM64 host.**

The investigation demonstrated:

- Successful installation and initialization.
- Successful WSL2 machine operation.
- Successful Windows ARM64 client to Linux ARM64 server communication.
- Actual ARM64 container execution.
- Basic concurrent container operation.
- PID, Mount, and Network Namespace separation in the tested case.
- Rootless operation and UID mapping.
- Active seccomp filtering and non-privileged configuration in the inspected container.

The investigation did not demonstrate:

- Complete per-Agent or per-container identity separation.
- Direct cross-container filesystem access denial.
- Comprehensive container hardening or a strong security boundary.
- Per-container CPU and memory enforcement.
- Network-egress policy enforcement.
- Container escape resistance.
- Independent execution of the five TOS Agents.
- Five-Agent concurrency or Production resource sufficiency.

**Decision:** Retain Podman + WSL2 as a viable candidate for further controlled feasibility work. Do not select it as the final TOS Runtime or authorize Production use on the basis of these results.

## 14. Recommended Next Actions

1. Preserve this report as the results record for the initial Podman/WSL2 feasibility investigation.
2. Review the open security and evidence gaps before designing additional tests.
3. If further testing is justified, define a narrowly scoped test plan for independent identity, filesystem access control, and resource enforcement, with explicit authorization and cleanup criteria.
4. Keep independent Agent execution and five-Agent capacity validation separate from basic container-runtime feasibility.
5. Avoid further host configuration changes until a specific test objective, expected decision value, and change-control boundary have been reviewed.

No additional PowerShell operation or system change is required to review this report.

## 15. Evidence Basis and Verification Boundaries

This report was prepared from the execution outputs supplied during the assessment and the existing ID-15N Minimum Runtime Isolation Test Plan.

The report does not claim that:

- A complete independently archived Run Evidence Package exists for every test.
- All raw outputs have been independently re-collected from the host.
- A separate reviewer reproduced the tests.
- Container escape, penetration, or adversarial testing was performed.

The findings are limited to the specific observations and test scope recorded in this report. Where an output was not supplied or a behavior was not tested, the corresponding claim remains unverified.

## 16. Document Control

| Field | Status |
|---|---|
| Document ID | TOS-ID-15N-RFR-001 |
| Version | 1.0 |
| Document status | FINAL — READY FOR USER REVIEW / GITHUB UPLOAD |
| Technical assessment | Initial Podman + WSL2 feasibility completed |
| RT-09 | HOLD / NOT OPERATIONAL — ROOT CAUSE UNRESOLVED |
| RT-10 | NOT EXECUTED |
| RT-11 | NOT EXECUTED |
| RT-12 | DEFERRED |
| Runtime selection | NOT FINALIZED |
| Production authorization | NOT GRANTED |
| GitHub upload | NOT PERFORMED |
| User acceptance | PENDING |

---

**End of Document**
