# LynxOS Performance and Resource Budgets

**Status:** Provisional engineering targets; no values in this document are measured results.

## Hardware floor

- RAM: 4 GB.
- Storage: 64 GB.
- Initial target architecture: x86-64.
- Reference measurements must specify CPU, RAM, storage model, GPU, firmware, display resolution and driver versions.

## Initial system targets

| Metric | Provisional target | Measurement notes |
|---|---:|---|
| Idle desktop memory | <= 1.2 GiB | PSS and RSS; fixed settling period; no user applications |
| Idle CPU | <= 2% average | Defined reference machine and 5-minute sample |
| Cold boot to usable desktop | <= 20 s | SSD reference system; split firmware, bootloader, kernel and userspace |
| Login to ready desktop | <= 5 s | Same hardware and session profile |
| Installed system footprint | <= 15 GiB | Fresh install, before user data; report reserved and occupied space |
| Task Engine core RSS | <= 20 MiB | Idle service, no optional prediction module |
| Task Engine idle CPU | <= 0.2% average | Documented 10-minute interval |

All values are targets, not measurements or release promises. Targets may be revised only through a documented decision supported by measurements and product trade-offs.

## Measurement rules

Every benchmark report must include:
- exact LynxOS commit and image hash.
- Ubuntu package versions and repository snapshot.
- hardware and firmware configuration.
- test procedure and tool versions.
- warm/cold state and cache policy.
- number of runs, median and tail percentile where applicable.
- background services and desktop session state.
- raw results or machine-readable artifacts.

## Metrics

Memory:
- RSS, PSS, cache, swap-in/out and peak memory.
- memory pressure under browser, office and video workloads.
- per-component resident memory for shell, compositor, platform services and Task Engine.

CPU:
- idle utilization, wakeups, scheduler activity and workload utilization.
- startup CPU bursts and background maintenance.
- Task Engine event rate, policy execution time and controller overhead.

Storage:
- ISO size and installed footprint.
- boot read volume, update write volume and write amplification where measurable.
- update download size and retained package/cache footprint.

Responsiveness:
- login-to-ready time.
- application launch time.
- input-to-visible-response latency.
- compositor frame pacing and missed frames.
- policy change latency under Task Engine operation.

Energy:
- idle and representative workload power on supported laptops.
- thermal throttling and fan behavior.
- effect of background work and display effects on energy.

## Optimization policy

1. Establish a reproducible baseline before tuning.
2. Profile before changing code or package selection.
3. Preserve security controls and correctness.
4. Evaluate regressions across representative hardware.
5. Avoid optimizing one benchmark at the cost of interactive latency, reliability or compatibility.
6. Record the change, expected effect and measured outcome.
7. Include the Task Engine's own overhead in every claimed efficiency improvement.
8. Keep expensive predictive or adaptive features optional until their net benefit is demonstrated.

## Minimum test workloads

- idle desktop after login.
- browser with a defined tab set.
- office/document workload.
- video playback.
- file copy and archive extraction.
- software installation/update.
- memory pressure and low-storage behavior.
- suspend/resume on supported laptop hardware.
- simultaneous foreground interaction, background indexing, file I/O and update activity.
- Task Engine disabled versus observe-only versus active policy, with identical workload traces.
