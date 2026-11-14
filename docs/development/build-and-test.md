# Development, Build and Test Workflow

**Status:** Initial workflow specification. Tool choices that require a prototype remain open.

## Development host

Recommended starting environment:
- Ubuntu 26.04 LTS x86-64.
- Git.
- Python 3.
- QEMU and OVMF for UEFI virtual-machine tests.
- shellcheck for shell scripts.
- pytest for Python test harnesses.
- GCC/Clang and CMake/Ninja for native components when required.

No Rust implementation is planned for the initial system. Existing upstream components may of course have their own implementation languages.

## Image-build tooling spike

Before selecting the permanent image pipeline, compare suitable Ubuntu image and live-system tooling. Evaluate:
- compatibility with Ubuntu 26.04 LTS.
- support for UEFI and Secure Boot.
- live filesystem and installer integration.
- package pinning and reproducibility.
- customization without forking large upstream components.
- support for signed artifacts and SBOM generation.
- build duration, caching and maintainability.

The outcome must be recorded as an ADR with a minimal prototype and artifact hash. Do not assume a tool is compatible until tested on the selected Ubuntu release.

## Build principles

- Pin the Ubuntu point-release snapshot and package versions for release builds.
- Keep package manifests declarative and reviewed.
- Separate build-time tools from runtime packages.
- Build in a clean, documented environment.
- Record source revision, package inputs, build logs and output hashes.
- Never store signing keys or credentials in Git.
- Make CI permissions read-only unless a job explicitly requires more.
- Pin third-party GitHub Actions to full commit SHAs.

## First boot test

The first image milestone must:
1. boot through UEFI in QEMU/OVMF.
2. reach the live environment.
3. expose serial or structured boot logs.
4. reach a defined ready marker.
5. terminate on timeout and preserve logs.
6. report image hash and build revision.

A VM boot test does not prove physical hardware compatibility or Secure Boot compatibility on every machine.

## Test layers

- Manifest validation: package names, duplicates, prohibited packages and categories.
- Build validation: clean build and artifact integrity.
- Boot: UEFI VM, failure/timeout and recovery behavior.
- Installer: fresh install, encryption, reboot and failed-install paths.
- Security: AppArmor, service hardening, firewall, package signature and privilege tests.
- Desktop: Wayland session, XWayland compatibility, accessibility and multi-monitor.
- Performance: boot, memory, CPU, storage and responsiveness.
- Hardware: reference Intel, AMD and NVIDIA systems plus Wi-Fi, audio, Bluetooth and suspend/resume.

## CI policy

- Run formatting, linting and manifest validation on every relevant change.
- Run fast unit tests on every pull request.
- Run VM image/boot tests when the build pipeline is available.
- Publish logs and hashes as CI artifacts.
- Never label a workflow green unless the actual run completed successfully.
- Separate non-blocking hardware tests from release-blocking VM and security tests.

## Evidence labels

Use explicit labels in documentation:
- Planned
- Prototyped
- Tested
- Measured
- Reviewed
- Verified
- Release-validated

A passing toy test or VM boot must not be described as production validation.
