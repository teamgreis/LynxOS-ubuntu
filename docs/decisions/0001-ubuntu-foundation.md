# ADR 0001: Ubuntu LTS as the Generation 001 Foundation

- **Status:** Accepted for Generation 001
- **Date:** 2026-11-14
- **Decision owners:** LynxOS project maintainers

## Context

LynxOS requires a complete desktop operating system with strong hardware and software compatibility, a 4 GB RAM minimum target, a 64 GB storage minimum target, and a security- and performance-first engineering approach.

Developing a new kernel, driver ecosystem, installer and desktop simultaneously would delay a usable system and multiply early compatibility and security risks.

## Decision

Generation 001 will be an independent Linux distribution based on Ubuntu 26.04 LTS.

The Ubuntu foundation supplies the Linux kernel, drivers, firmware integration, core userspace, package ecosystem and security-update infrastructure. LynxOS will own its distribution integration, branding, desktop experience, first-party platform services, security policy, installer experience and release engineering.

The initial architecture target is x86-64. ARM64 is a future target that requires separate validation.

## Consequences

Positive:
- Reuses a mature kernel and broad hardware ecosystem.
- Retains APT and Ubuntu package compatibility as a primary goal.
- Allows engineering effort to focus on desktop experience, security integration and resource efficiency.
- Enables a bootable product milestone before native-kernel research is complete.

Trade-offs:
- LynxOS inherits upstream kernel and userspace release constraints.
- Ubuntu package compatibility limits how aggressively base components can be replaced.
- Security depends partly on upstream maintenance and timely integration.
- A custom desktop and distribution still require substantial testing and maintenance.

## Constraints

- Do not describe Generation 001 as an independent-kernel operating system.
- Do not remove drivers, firmware or core packages without dependency and hardware testing.
- Pin build inputs for release artifacts.
- Treat performance budgets as provisional until measured.
- Keep native-kernel and own-language research separate from the initial Linux release path.

## Revisit when

Revisit this decision if Ubuntu's maintenance, licensing, package, hardware or security constraints materially prevent LynxOS product goals.
