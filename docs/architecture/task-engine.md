# LynxOS Task Engine — Architecture Proposal

**Status:** Proposed architecture for research and prototyping; not yet implemented or selected as a kernel scheduler replacement.  
**Generation:** LynxOS Linux Edition 001

## 1. Purpose

The LynxOS Task Engine is a system-level workload coordinator intended to improve responsiveness, resource efficiency, battery life and predictable behavior under load.

It must make the system feel intelligent without becoming a permanently expensive background workload. The engine coordinates policy and resource controls; it does not replace the Linux kernel scheduler.

## 2. Critical boundary

Linux remains responsible for actual CPU scheduling, memory management, I/O scheduling and process execution.

The Task Engine operates in userspace and uses supported Linux interfaces such as:
- cgroup v2 for resource accounting and limits.
- systemd scopes/slices for workload grouping and lifecycle.
- PSI (Pressure Stall Information) for memory, CPU and I/O pressure signals.
- process metadata and desktop session state, subject to permission and privacy rules.
- carefully scoped nice, I/O priority and cgroup weight controls where justified.

eBPF may be evaluated later for observability, but it is not a required dependency for the first implementation. Do not introduce custom kernel modules for the initial Task Engine.

## 3. Workload classes

Initial policy classes:

| Class | Examples | Policy intent |
|---|---|---|
| Interactive foreground | Active editor, current browser interaction | Protect input and visible response latency |
| Interactive background | Other open desktop applications | Preserve responsiveness without starving foreground work |
| Latency-sensitive service | Audio, input, compositor-critical work | Respect explicit latency requirements and safe limits |
| Normal | Ordinary user applications | Fair resource sharing |
| Background | Indexing, thumbnails, sync | Yield under pressure and avoid disruptive bursts |
| Maintenance | Updates, scans, cleanup | Run in bounded windows and pause/defer when appropriate |
| Batch | Compression, large exports | Maximize throughput when system is otherwise available |

Classification must be conservative. Unknown applications receive normal policy, not privileged priority.

## 4. Core components

1. **Task Registry** — tracks workload identity, owner, lifecycle and declared intent.
2. **Signal Collector** — gathers cgroup counters, PSI, session state, battery/power state and system pressure.
3. **Policy Engine** — deterministic, bounded decision logic mapping signals to allowed resource profiles.
4. **Resource Controller** — applies validated cgroup/systemd controls through a narrow privileged interface.
5. **Budget Manager** — enforces per-class CPU, memory and I/O ceilings/floors.
6. **Explainability API** — exposes current policy, reason codes and user-visible status.
7. **Safety Supervisor** — detects stale signals, controller failure, oscillation and unsafe policy; reverts to neutral defaults.

## 5. Control loop

Prefer event-driven updates using system state changes and pressure notifications. Use a conservative low-frequency reconciliation loop as a safety net.

Each decision cycle:
1. Collect bounded, low-cost metrics.
2. Validate freshness and source permissions.
3. Estimate pressure and user-visible urgency.
4. Select a pre-approved resource profile.
5. Apply only the minimum necessary adjustment.
6. Verify that the change succeeded.
7. Record a compact reason code and outcome.
8. Revert or fall back if the controller or signal source is unhealthy.

The engine must not continuously poll every process at high frequency.

## 6. Policy constraints

- Never allow an untrusted application to grant itself elevated priority.
- Never let the Task Engine bypass Unix permissions, AppArmor or other security controls.
- Avoid starvation: background work must make bounded progress.
- Avoid priority inversion and uncontrolled feedback loops.
- Use hysteresis and minimum dwell times to prevent rapid policy oscillation.
- Respect explicit user choices and accessibility needs.
- Do not kill applications solely because they are backgrounded.
- Do not silently discard unsaved user work.
- Keep memory protection and OOM behavior under tested Linux mechanisms.
- If the engine crashes, is disabled or loses telemetry, the system must remain usable with neutral kernel/systemd defaults.

## 7. Adaptive intelligence

The first version should be deterministic and explainable, using measured pressure, declared workload class, foreground state and user-selected power mode.

Later research may evaluate predictive models for workload patterns. Any learned/adaptive behavior must:
- be optional and privacy-preserving.
- avoid cloud dependency.
- have bounded CPU, RAM and storage budgets.
- expose understandable reasons for resource changes.
- be reproducible in tests.
- never override security policy or explicit user constraints.
- fall back to deterministic policy when confidence is low.

No always-on large AI model is appropriate for the baseline Task Engine. Intelligence means good policy and efficient feedback, not expensive inference for every process.

## 8. User modes

Initial user-facing modes:
- **Balanced** — default, responsiveness and efficiency combined.
- **Performance** — prioritizes latency and throughput within thermal and safety limits.
- **Efficiency** — reduces background work and energy use.
- **Quiet** — minimizes disruptive maintenance and notifications, without disabling essential security updates.
- **Custom** — advanced user-controlled limits with safe bounds.

Modes are policy presets, not magic CPU-frequency switches. Hardware-specific controls must be capability-detected.

## 9. Security model

- Run the policy service unprivileged where possible.
- Isolate the resource-controller helper and expose a narrow authenticated IPC API.
- Validate all requests and reject arbitrary cgroup paths or process IDs from untrusted clients.
- Derive process ownership from trusted kernel/session information, not client-supplied claims.
- Restrict capabilities and filesystem access.
- Apply AppArmor and systemd hardening.
- Audit policy changes without logging sensitive application content.
- Ensure a compromised desktop shell cannot grant itself system-wide resource privileges.

## 10. Performance budgets for the engine

Provisional targets, to be validated on the 4 GB reference system:
- idle resident memory target: <= 20 MiB for the core service.
- idle CPU average target: <= 0.2% over a documented 10-minute interval.
- no high-frequency full-process scans.
- no synchronous disk writes in the critical decision path.
- bounded decision execution time and bounded queue sizes.
- no measurable input-latency regression outside the agreed test tolerance.

These are targets, not measured results. Report RSS/PSS, wakeups, CPU time, event rates and decision latency.

## 11. Prototype sequence

### TE-001 — Observe only
Collect cgroup v2 and PSI signals; classify workloads; make no resource changes.

### TE-002 — Deterministic policy simulation
Replay captured workloads and compare policy decisions against baseline behavior.

### TE-003 — Safe cgroup controls
Apply reversible CPU/I/O weights to controlled test workloads only.

### TE-004 — Desktop integration
Expose mode selection, active policy and reason codes in LynxOS Settings.

### TE-005 — Stress and failure testing
Test memory pressure, CPU saturation, I/O contention, stale signals, service crash, permission failures and rapid foreground changes.

### TE-006 — Controlled release
Enable by default only after measurable improvement, no starvation, security review and rollback validation.

## 12. Success criteria

The Task Engine is successful only if it demonstrates:
- improved or unchanged interactive latency under representative mixed workloads.
- lower or equal unnecessary background CPU and I/O.
- bounded memory and CPU overhead.
- no starvation or loss of user work.
- deterministic fallback on failure.
- no security boundary bypass.
- reproducible tests and documented trade-offs.

A faster benchmark in isolation is insufficient if user experience, security, fairness or reliability regresses.
