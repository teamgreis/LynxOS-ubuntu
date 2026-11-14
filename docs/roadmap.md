# LynxOS Linux Edition 001 Roadmap

This roadmap tracks the first Ubuntu-based LynxOS generation. Dates are intentionally not estimated until the build pipeline and staffing capacity are known.

| Phase | Name | Main deliverable | Exit condition |
|---|---|---|---|
| P001 | Foundation & Build System | Reproducible UEFI live image | Boots in QEMU/OVMF; inputs and hashes recorded |
| P002 | Installation & System Foundation | Installable base system | Fresh install, reboot, update and recovery tests pass |
| P003 | LynxOS Desktop & Design System | Complete first-party desktop experience | Desktop, accessibility and performance tests pass |
| P004 | Security Foundation | Enforced security baseline | Automated security tests pass; exceptions documented |
| P005 | Performance & Task Engine | Measured optimization and adaptive workload prototype | Bounded overhead, no starvation, safe fallback and measured benefit |
| P006 | Platform Applications | First-party administration tools | Core administration workflows usable |
| P007 | Release Engineering | Signed release candidate | Reproducible, signed, scanned and reviewable artifacts |
| P008 | LynxOS 001 Stable | First stable distribution | All release gates met; limitations published |

## P001 detailed work items

- [ ] Pin Ubuntu 26.04 LTS base and repository snapshot.
- [ ] Define initial x86-64 package manifest.
- [x] Compare image-build tooling and record an ADR. — [ADR 0003](decisions/0003-image-build-tooling.md): ubuntu-image classic selected; validation spike with prototype and artifact hash still outstanding.
- [ ] Create minimal live image configuration.
- [ ] Add UEFI QEMU/OVMF boot test.
- [ ] Capture boot logs and artifact hashes.
- [ ] Record first baseline image size and VM resource usage.
- [ ] Publish limitations and known gaps.

## Parallel engineering tracks

### Task Engine

- [ ] TE-001: observe-only collection of cgroup v2 and PSI signals.
- [ ] TE-002: deterministic policy simulation using captured workloads.
- [ ] TE-003: reversible cgroup controls in isolated test workloads.
- [ ] TE-004: desktop mode/status integration.
- [ ] TE-005: pressure, starvation, crash, stale-signal and permission tests.
- [ ] TE-006: controlled enablement only after security and performance review.

See [Task Engine architecture](architecture/task-engine.md).

### Desktop design system

- [ ] Define semantic color, typography, spacing, shape and motion tokens.
- [ ] Prototype representative shell/settings components with at least two toolkit candidates.
- [ ] Review accessibility, keyboard navigation, scaling and reduced motion.
- [ ] Measure compositor, shell and UI component memory/GPU usage.
- [ ] Build reusable components before implementing every application independently.

See [Desktop Design System](desktop/design-system.md).

## Dependencies

P002 depends on P001 producing a repeatable image. P003 may prototype desktop components in parallel, but integration must target the pinned foundation. P004 security requirements apply throughout all phases; P005 measurement begins as soon as a bootable image exists. Task Engine policy must be tested in observe-only mode before it can modify resource controls.

## Release gates

See [the architecture plan](architecture/lynxos-linux-edition-001.md#12-release-gates), [security baseline](security/baseline.md) and [engineering vision](engineering/vision-001.md).
