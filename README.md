# LynxOS Ubuntu

**LynxOS Linux Edition 001** is a planned, independent Linux distribution built on an Ubuntu LTS foundation.

**Engineering ambition:** exceptionally secure, resource-efficient, responsive, compact, modern and visually distinctive. These are measurable engineering objectives, not unsupported marketing claims.

> Project status: architecture and implementation planning. No bootable LynxOS image or production-ready system is claimed yet.

## Initial product targets

| Area | Target |
|---|---|
| Linux foundation | Ubuntu 26.04 LTS |
| Initial architecture | x86-64 |
| Future architecture | ARM64, subject to validation |
| Minimum memory | 4 GB RAM |
| Minimum storage | 64 GB |
| Desktop | Full, first-party LynxOS desktop experience |
| Display stack | Wayland-first |
| Package compatibility | Ubuntu APT repositories; Snap support planned |
| Security | Secure Boot, AppArmor, least privilege, encrypted installation option |
| Task Engine | Adaptive userspace workload coordinator; Linux scheduler remains authoritative |
| Long-term direction | Gradual replacement of selected Linux userspace components; independent kernel remains a separate research track |

## Engineering principles

1. Security and correctness are hard constraints, not performance trade-offs.
2. Every performance or efficiency claim requires reproducible measurements.
3. Preserve Ubuntu driver, firmware and package compatibility wherever possible.
4. Remove components only after dependency, security and hardware analysis.
5. Every service runs with the least privilege practical.
6. The Task Engine must be bounded, explainable, safe and cheaper than the work it optimizes.
7. The desktop must be beautiful, accessible, consistent and responsive on minimum-target hardware.
8. Distinguish plans, prototypes, tested behavior, measured results and production validation.

## Documentation

- [Engineering Vision 001](docs/engineering/vision-001.md)
- [Generation 001 architecture and implementation plan](docs/architecture/lynxos-linux-edition-001.md)
- [Task Engine architecture proposal](docs/architecture/task-engine.md)
- [Desktop design system requirements](docs/desktop/design-system.md)
- [Architecture decision: Ubuntu foundation](docs/decisions/0001-ubuntu-foundation.md)
- [Security baseline](docs/security/baseline.md)
- [Performance budgets and measurement plan](docs/performance/budgets.md)
- [Development, build, and test workflow](docs/development/build-and-test.md)
- [Initial package manifest](distribution/packages/manifest.yaml)
- [Project roadmap](docs/roadmap.md)

## Repository layout

```text
distribution/   Ubuntu integration, packages, image and installer configuration
desktop/        LynxOS desktop shell, design system and user experience
system/         Task Engine, services, security, updates, hardware and recovery
applications/   First-party desktop and system applications
language/       Future LynxOS language specification, compiler and runtime
tests/          Boot, installation, security, performance, engine and desktop tests
docs/           Architecture, decisions, security and development documentation
```

Directories are introduced as implementation work begins; the documentation does not imply that these components already exist.

## Current milestone

**P001 — Foundation & Build System**

- Pin the Ubuntu 26.04 LTS base and repository snapshot.
- Define and validate a minimal package set.
- Select reproducible image-build tooling.
- Produce the first bootable UEFI live image.
- Boot it in QEMU/OVMF and capture logs.
- Record real RAM, CPU, boot-time and image-size measurements.

Task Engine research and desktop design-system prototyping can proceed as parallel tracks, without delaying the first bootable image.

## Status and contribution

This is an actively planned engineering project. Proposed technologies and numerical targets remain subject to validation and documented architecture decisions. Do not treat an unimplemented feature as available.

Security issues should be reported privately to the repository maintainers rather than opened as public exploit details.
