# LynxOS Linux Edition 001 Roadmap

This roadmap tracks the first Ubuntu-based LynxOS generation. Dates are intentionally not estimated until the build pipeline and staffing capacity are known.

| Phase | Name | Main deliverable | Exit condition |
|---|---|---|---|
| P001 | Foundation & Build System | Reproducible UEFI live image | Boots in QEMU/OVMF; inputs and hashes recorded |
| P002 | Installation & System Foundation | Installable base system | Fresh install, reboot, update and recovery tests pass |
| P003 | LynxOS Desktop | Complete Wayland desktop | Core desktop workflows and compatibility tests pass |
| P004 | Security Foundation | Enforced security baseline | Automated security tests pass; exceptions documented |
| P005 | Performance Engineering | Measured optimization report | Budgets evaluated on documented reference hardware |
| P006 | Platform Applications | First-party administration tools | Core administration workflows usable |
| P007 | Release Engineering | Signed release candidate | Reproducible, signed, scanned and reviewable artifacts |
| P008 | LynxOS 001 Stable | First stable distribution | All release gates met; limitations published |

## P001 detailed work items

- [ ] Pin Ubuntu 26.04 LTS base and repository snapshot.
- [ ] Define initial x86-64 package manifest.
- [ ] Compare image-build tooling and record an ADR.
- [ ] Create minimal live image configuration.
- [ ] Add UEFI QEMU/OVMF boot test.
- [ ] Capture boot logs and artifact hashes.
- [ ] Record first baseline image size and VM resource usage.
- [ ] Publish limitations and known gaps.

## Dependencies

P002 depends on P001 producing a repeatable image. P003 may prototype desktop components in parallel, but integration must target the pinned foundation. P004 security requirements apply throughout all phases; P005 measurement begins as soon as a bootable image exists.

## Release gates

See [the architecture plan](architecture/lynxos-linux-edition-001.md#12-release-gates) and [security baseline](security/baseline.md).
