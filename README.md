# LynxOS Ubuntu

**LynxOS Linux Edition 001** is a planned, independent Linux distribution built on an Ubuntu LTS foundation.

The first generation prioritizes security, performance, low resource consumption, broad Ubuntu hardware/software compatibility, and a complete LynxOS desktop experience.

> Project status: architecture and implementation planning. No bootable LynxOS image or production-ready system is claimed yet.

## Initial product targets

| Area | Target |
|---|---|
| Linux foundation | Ubuntu 26.04 LTS |
| Initial architecture | x86-64 |
| Future architecture | ARM64, subject to validation |
| Minimum memory | 4 GB RAM |
| Minimum storage | 64 GB |
| Desktop | Full LynxOS desktop |
| Display stack | Wayland |
| Package compatibility | Ubuntu APT repositories; Snap support planned |
| Security | Secure Boot, AppArmor, least privilege, encrypted installation option |
| Long-term direction | Gradual replacement of selected Linux userspace components; independent kernel remains a separate research track |

## Project principles

1. Security and correctness are hard constraints, not performance trade-offs.
2. Performance claims require reproducible measurements on documented hardware.
3. The system must preserve Ubuntu driver, firmware, and package compatibility wherever possible.
4. Remove unnecessary components only after dependency and compatibility analysis.
5. Every LynxOS service should run with the least privilege practical.
6. Keep the Ubuntu foundation, LynxOS platform, and LynxOS desktop independently maintainable.
7. Distinguish plans, prototypes, tested behavior, measured results, and production validation.

## Documentation

- [Generation 001 architecture and implementation plan](docs/architecture/lynxos-linux-edition-001.md)
- [Architecture decision: Ubuntu foundation](docs/decisions/0001-ubuntu-foundation.md)
- [Security baseline](docs/security/baseline.md)
- [Performance budgets and measurement plan](docs/performance/budgets.md)
- [Development, build, and test workflow](docs/development/build-and-test.md)
- [Initial package manifest](distribution/packages/manifest.yaml)
- [Project roadmap](docs/roadmap.md)

## Repository layout

```text
distribution/   Ubuntu integration, packages, image and installer configuration
desktop/        LynxOS desktop shell and user experience
system/         LynxOS services, security, updates, hardware and recovery
applications/   First-party desktop and system applications
language/       Future LynxOS language specification, compiler and runtime
tests/          Boot, installation, security, performance and desktop tests
docs/           Architecture, decisions, security and development documentation
```

Directories are introduced as implementation work begins; the documentation does not imply that these components already exist.

## Current milestone

**P001 — Foundation & Build System**

- Define a minimal Ubuntu-based package set.
- Establish reproducible image-build tooling.
- Produce the first bootable UEFI live image.
- Boot it in QEMU/OVMF and capture serial/test logs.
- Record the first real RAM, CPU, boot-time and image-size measurements.

See [the roadmap](docs/roadmap.md) for acceptance criteria.

## Status and contribution

This is an actively planned engineering project. Proposed technologies and numerical targets remain subject to validation and documented architecture decisions. Do not treat an unimplemented feature as available.

Security issues should be reported privately to the repository maintainers rather than opened as public exploit details.
