# LynxOS Linux Edition 001 — Architecture and Implementation Plan

**Status:** Proposed engineering baseline  
**Revision:** 1.1  
**Date:** 2026-11-14

## 1. Product definition

LynxOS Linux Edition 001 is a complete, independently branded Linux distribution built on Ubuntu 26.04 LTS. Its initial product goal is a usable desktop operating system with a LynxOS-owned desktop experience, system integration, installer experience, security defaults, update experience, recovery workflow and an adaptive workload coordination layer.

The engineering ambition is exceptional security, low resource consumption, high responsiveness, compactness, modern capabilities and outstanding visual quality. These goals are governed by measurable budgets and release gates; they are not claims about an unbuilt system.

It is not a new kernel in this generation. The Ubuntu Linux kernel, driver ecosystem, firmware integration and package ecosystem are intentionally retained to maximize hardware and application compatibility. Independent-kernel research remains a separate long-term track.

## 2. Fixed product constraints

| Requirement | Initial target |
|---|---|
| Base | Ubuntu 26.04 LTS |
| First architecture | x86-64 |
| Future architecture | ARM64, after validation |
| Minimum RAM | 4 GB |
| Minimum storage | 64 GB |
| Desktop | Full LynxOS desktop experience |
| Display protocol | Wayland-first |
| Package compatibility | Ubuntu APT; Snap compatibility planned |
| Security posture | Secure-by-default, least privilege |
| Performance posture | Measured resource budgets; no unsupported speed claims |
| Task Engine | Bounded userspace workload coordinator; no replacement kernel scheduler |

The exact Ubuntu point-release snapshot, kernel flavour, firmware set and repository components must be pinned in the build configuration after compatibility testing. Avoid silently tracking moving package versions in release builds.

## 3. Architectural layers

### 3.1 Ubuntu Foundation

Responsibilities:
- Ubuntu LTS base packages and supported repositories.
- Linux kernel, modules, firmware, graphics and device drivers.
- systemd, D-Bus, NetworkManager, udev and core userspace.
- APT compatibility and supported Snap integration.
- Ubuntu security updates and package signing infrastructure.

The distribution must not remove a package solely because it appears unused in one test image. Dependency, hardware and recovery impact must be evaluated first.

### 3.2 LynxOS Platform

First-party integration and services:
- system configuration and policy management.
- adaptive Task Engine and resource policy.
- update orchestration and update status.
- security posture reporting.
- hardware and driver status.
- first-boot experience.
- recovery entry points and diagnostics.
- consistent application integration and branding.

Each service must have a documented owner, interface, privilege set, lifecycle, logging policy and failure behavior.

### 3.3 LynxOS Experience

A complete desktop environment:
- Wayland session and compositor.
- desktop shell, panel and launcher.
- notifications and status surfaces.
- settings and control center.
- application discovery and launch.
- file management and terminal integration.
- accessibility, keyboard navigation and multi-monitor support.
- consistent LynxOS design tokens and reusable components.

A full desktop is a product requirement. Reusing a mature compositor and protocol infrastructure is acceptable; the LynxOS shell and integrated user experience remain first-party work.

## 4. Desktop technology strategy

Initial candidates:
- Wayland as the primary display protocol.
- labwc as a lightweight compositor prototype candidate.
- XWayland for legacy X11 application compatibility.
- PipeWire for audio and modern media routing.
- XDG Desktop Portals for sandbox-aware desktop integration.
- greetd or another reviewed display-manager option for session startup.

The compositor and toolkit are not irrevocably selected. Compare at least two viable toolkit approaches (for example Qt 6 and GTK 4) using a small representative prototype. Evaluate memory use, startup time, accessibility, localization, maintainability, packaging, Wayland behavior and developer ergonomics.

The desktop must use a cohesive first-party design system. Visual effects must be profiled on minimum-target hardware, honor reduced-motion settings and never block input. See [Desktop Design System](../desktop/design-system.md).

Do not build a compositor, graphics driver, audio server or display protocol from scratch in Generation 001.

## 5. Installer and boot strategy

Initial boot path:
1. UEFI firmware.
2. Ubuntu-compatible signed shim/GRUB chain where Secure Boot is enabled.
3. Live environment and kernel.
4. LynxOS first-boot/live session.
5. Installer workflow.
6. Installed system boot and LynxOS session.

Use mature Ubuntu installer and image infrastructure as the starting point. The exact image-build path must be selected by a short technical spike comparing Ubuntu-supported image tooling, live filesystem integration, installer support and reproducibility. Do not claim an installer or ISO exists until an artifact has been built and tested.

Installer requirements:
- UEFI/GPT support.
- hardware and storage discovery.
- clear destructive-operation confirmation.
- optional full-disk encryption using LUKS2.
- user creation and administrator policy.
- network configuration.
- install failure diagnostics.
- recovery and repair entry points.
- accessible keyboard-driven operation.

## 6. Storage layout

Initial default:
- GPT partition table.
- EFI System Partition sized according to tested bootloader and firmware requirements.
- LUKS2-encrypted system volume when encryption is selected.
- ext4 as the first supported root filesystem.
- swap policy selected from measured memory-pressure behavior.
- recovery capability that does not depend on a successfully booting desktop.

A 1 GiB ESP is a provisional layout candidate, not a universal requirement. Installer sizing must be tested against firmware, bootloader, Secure Boot and future update requirements.

## 7. Security architecture

Security is a system property, not a single application. The baseline combines:
- Secure Boot where supported.
- signed packages and trusted repository metadata.
- AppArmor profiles in enforce mode for first-party services and suitable applications.
- least-privilege service accounts and Linux capabilities.
- systemd sandboxing.
- Seccomp where a stable syscall profile is practical.
- nftables with a documented default policy.
- LUKS2 as an installation option.
- automatic security update policy with clear user control and recovery.
- SBOM generation and vulnerability scanning.
- signed release artifacts and provenance.
- a narrow, authenticated interface for any privileged Task Engine resource controller.

Security mechanisms must be tested for enforcement, not merely shown as enabled. Exceptions require an owner, rationale, scope, expiry/review date and compensating control. The Task Engine must never grant untrusted applications privileges or bypass existing security boundaries.

## 8. Performance and resource engineering

The 4 GB RAM / 64 GB storage requirement is a minimum compatibility target, not a guarantee that every workload will be comfortable.

Initial provisional targets:
- idle desktop memory: at or below 1.2 GiB, measured after a defined settling period.
- idle CPU: at or below 2% average on a documented reference machine.
- cold boot to usable desktop: at or below 20 seconds on a documented SSD reference machine.
- login completion after credentials: at or below 5 seconds on the same reference machine.
- installed system footprint: target at or below 15 GiB before user data.
- LynxOS Task Engine core service: target at or below 20 MiB resident memory and 0.2% average idle CPU, pending prototype validation.

These are engineering targets, not measured results or release promises. Record both median and tail behavior where meaningful. Define the hardware, firmware, display mode, services, test duration and measurement tools for every result.

Measure:
- memory: RSS/PSS, cache, swap and peak usage.
- CPU: idle and workload utilization, wakeups and scheduler activity.
- storage: ISO size, installed footprint, boot I/O and write volume.
- startup: firmware, bootloader, kernel, userspace and session-ready timings.
- responsiveness: application launch and input-to-visible-response latency.
- energy and thermal behavior on supported laptops.
- Task Engine overhead, event rate, decision latency and policy oscillation.

Do not trade correctness or security for a benchmark improvement. See [Performance Budgets](../performance/budgets.md) and the [Task Engine proposal](task-engine.md).

## 9. First-party applications

P0 for the first complete desktop:
- LynxOS Welcome.
- LynxOS Settings.
- LynxOS Update.
- LynxOS Security / Security Status.
- LynxOS Control Center.

P1 for the first stable product generation:
- LynxOS Software.
- LynxOS Hardware.
- LynxOS Monitor.
- LynxOS Recovery.
- LynxOS File Manager.

Existing mature applications may be integrated temporarily when that is safer and more maintainable. Clearly label temporary integrations; do not claim them as first-party implementations.

## 10. Repository and module boundaries

- distribution/: Ubuntu image, package, installer and repository integration.
- desktop/: shell, panel, launcher, notifications, design tokens and settings UI.
- system/: Task Engine, privileged services, security, updates, networking and recovery.
- applications/: user-facing first-party applications.
- language/: future LynxOS language, compiler and runtime.
- tests/: automated test suites.
- docs/: architecture, decisions, security and development records.

Prefer narrow, documented interfaces between desktop components and privileged system services. Desktop processes must not run as root to perform routine tasks.

## 11. Development phases

### P001 — Foundation and build system
- pin the Ubuntu base and repository snapshot.
- create a minimal package manifest.
- choose image-build tooling through a documented spike.
- build a UEFI-bootable live image.
- boot in QEMU/OVMF and capture logs.
- create a repeatable build and artifact manifest.

Exit criteria: reproducible live image boots in a VM; build inputs and artifact hashes are recorded; no claim of hardware validation yet.

### P002 — Installation and system foundation
- installer integration.
- UEFI and Secure Boot testing.
- storage layout and LUKS2.
- user creation, networking, updates and recovery.

Exit criteria: clean installation, reboot, update and recovery tests pass in defined VM profiles.

### P003 — LynxOS desktop and design system
- compositor/session integration.
- shell, panel, launcher and notifications.
- settings, accessibility and multi-monitor support.
- XWayland and desktop portals.
- reusable design tokens and component library.

Exit criteria: a complete desktop session works with documented compatibility, accessibility and performance tests.

### P004 — Security foundation
- service identities and privilege boundaries.
- AppArmor profiles and systemd hardening.
- firewall and update policy.
- signed artifacts, SBOM and vulnerability scans.

Exit criteria: automated security tests pass; all exceptions are documented; no unresolved release-blocking findings.

### P005 — Performance and Task Engine
- establish reference hardware and baseline workload traces.
- prototype Task Engine in observe-only mode.
- validate deterministic policies in simulation.
- test bounded cgroup v2 controls on isolated workloads.
- automate boot, memory, CPU, storage, energy and responsiveness tests.
- optimize only from measured profiles.

Exit criteria: published measurements show bounded engine overhead, no starvation, safe fallback and no unacceptable interactive-latency regression.

### P006 — Platform applications
- implement first-party settings, update, security and control-center experiences.
- add software, hardware, monitoring and recovery applications.
- integrate Task Engine mode/status controls with clear explanations.

Exit criteria: core administration tasks are available through supported LynxOS interfaces.

### P007 — Release engineering
- package and image signing.
- SBOM and provenance.
- upgrade, rollback and recovery testing.
- hardware compatibility matrix and release documentation.

Exit criteria: release candidate is reproducible, signed and reviewable.

### P008 — LynxOS 001 Stable
- beta feedback and issue triage.
- security and performance review.
- installation, upgrade and hardware validation.
- release publication.

Exit criteria: all release gates are met and known limitations are published.

## 12. Release gates

A release must not be called stable unless:
- supported installation and upgrade paths pass.
- Secure Boot behavior is documented and tested on supported configurations.
- encryption and recovery workflows pass.
- security review has no unresolved critical/high release blockers.
- resource and performance reports use real measurements.
- core desktop workflows pass automated and manual tests.
- package sources, licenses and SBOM are recorded.
- artifacts are signed and checksums published.
- known hardware limitations are documented.
- Task Engine can be disabled safely and has no unresolved starvation, privilege or stability issue if enabled by default.

## 13. Explicit non-goals for Generation 001

- replacing the Linux kernel.
- writing a new bootloader.
- writing graphics, storage or network drivers from scratch.
- replacing the Linux kernel scheduler with a LynxOS scheduler.
- claiming universal compatibility with every Ubuntu package.
- promising performance values before measurement.
- requiring the future LynxOS programming language for initial system components.

The native LynxOS kernel and language remain separate long-term research programs. Their design must not destabilize the first usable Linux-based distribution.
