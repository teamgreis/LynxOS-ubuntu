# LynxOS Performance and Resource Budgets

**Status:** Provisional engineering targets; no values in this document are measured results.

## Hardware floor

- RAM: 4 GB.
- Storage: 64 GB.
- Initial target architecture: x86-64.
- Reference measurements must specify CPU, RAM, storage model, GPU, firmware, display resolution and driver versions.

## Initial targets

| Metric | Provisional target | Measurement notes |
|---|---:|---|
| Idle desktop memory | <= 1.2 GiB | PSS and RSS; fixed settling period; no user applications |
| Idle CPU | <= 2% average | Defined reference machine and 5-minute sample |
| Cold boot to usable desktop | <= 20 s | SSD reference system; split firmware, bootloader, kernel and userspace |
| Login to ready desktop | <= 5 s | Same hardware and session profile |
| Installed system footprint | <= 15 GiB | Fresh install, before user data; report reserved and occupied space |

Targets may be revised only through a documented decision supported by measurements and product trade-offs. They are not marketing claims.

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

CPU:
- idle utilization, wakeups, scheduler activity and workload utilization.
- startup CPU bursts and background maintenance.

Storage:
- ISO size and installed footprint.
- boot read volume, update write volume and write amplification where measurable.

Responsiveness:
- login-to-ready time.
- application launch time.
- input-to-visible-response latency.
- compositor frame pacing and missed frames.

Energy:
- idle and representative workload power on supported laptops.
- thermal throttling and fan behavior.

## Optimization policy

1. Establish a reproducible baseline before tuning.
2. Profile before changing code or package selection.
3. Preserve security controls and correctness.
4. Evaluate regressions across representative hardware.
5. Avoid optimizing one benchmark at the cost of interactive latency, reliability or compatibility.
6. Record the change, expected effect and measured outcome.

## Minimum test workloads

- idle desktop after login.
- browser with a defined tab set.
- office/document workload.
- video playback.
- file copy and archive extraction.
- software installation/update.
- memory pressure and low-storage behavior.
- suspend/resume on supported laptop hardware.
