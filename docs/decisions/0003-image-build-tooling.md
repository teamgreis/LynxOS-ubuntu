# ADR 0003: Image-Build Tooling for Generation 001

- **Status:** Provisional decision — comparison complete, mandatory validation spike outstanding
- **Date:** 2026-11-14
- **Decision owners:** LynxOS project maintainers

## Context

The P001 milestone requires a reproducible, UEFI-bootable live image of LynxOS
on an Ubuntu 26.04 LTS foundation. [ADR 0001](0001-ubuntu-foundation.md) fixed
Ubuntu 26.04 LTS ("Resolute Raccoon", released 2026-04-23; 26.04.1 point release
2026-08-27) as the base. [The build-and-test workflow](../development/build-and-test.md)
demands a short technical spike comparing Ubuntu-supported image tooling against
the criteria below, recorded as an ADR **with a minimal prototype and artifact
hash**, before a permanent pipeline is selected.

## Evaluation criteria

Derived from [the architecture plan §5](../architecture/lynxos-linux-edition-001.md)
and [the build-and-test workflow](../development/build-and-test.md):

1. **Ubuntu 26.04 LTS compatibility** — must build against the pinned release.
2. **UEFI and Secure Boot support** — signed shim/GRUB boot chain without
   inventing new bootloaders.
3. **Live filesystem and installer integration** — a live environment and a
   path to the Ubuntu subiquity installer.
4. **Package pinning and reproducibility** — pinned base snapshot, recorded
   inputs, repeatable outputs.
5. **Customization without forking upstream** — configure, do not fork.
6. **Signed artifacts and SBOM support** — release-signing path and SBOM
   generation are P004/P007 requirements.
7. **Build duration, caching, maintainability** — sustainable on a small team.

## Candidates considered

| Criterion | ubuntu-image classic | live-build (direct) | debos | Cubic |
|---|---|---|---|---|
| Ubuntu 26.04 support | Yes — classic mode runs live-build with the host's livecd-rootfs config; suite passthrough (`--suite`) | Yes — Ubuntu ships a patched build (3.0~a57-1u56); Debian upstream actively maintained (1:20250814) | Yes — debootstrap/mmdebstrap based; mainline (1.1.8-1, 2026-11) | Untested for 26.04 — latest release 2025.06.93 documents support through 25.10 (Questing) |
| UEFI / Secure Boot | Yes via Ubuntu's signed `shim-signed` / `grub-efi-amd64-signed` packages in the gadget/boot chain | Yes — Ubuntu's live images use the same signed packages | Not built-in — boot assets must be assembled manually | Yes by construction — repacks an already-signed Ubuntu ISO |
| Live environment + subiquity | Yes — livecd-rootfs produces the live rootfs; subiquity integration is the Ubuntu-native path | Partially — live environment is its core competence, but subiquity-inside-live-ISO plumbing is Ubuntu-specific and must be replicated | No — it builds preinstalled disk images, not live ISOs | Yes — starts from the official ISO including subiquity |
| Pinning / reproducibility | Manifest generation built in (seed/manifest + disk info); snapshot pinning must be pinned in configuration | Config-driven debootstrap + apt snapshot mirror; Debian live reproducibility features (SOURCE_DATE_EPOCH) partially applicable | High host-independence (fakemachine VM, official CI container) but outputs are not bit-reproducible without a local mirror | Interactive, no meaningful reproducibility |
| Customization depth | Full, via image-definition YAML + gadget tree + live-build hooks | Very high — this is what ubuntu-image classic uses underneath | High, but low-level: every boot/install step is manual work | Shallow — install/remove packages and add files inside the existing ISO |
| SBOM / signed artifacts | Manifest output usable as SBOM seed; signing path is project work | No built-in SBOM | No built-in SBOM | None |
| Duration / maintainability | Matches Canonical's own tooling — fewest upstream deltas | Heavier: ubuntu-image classic adds the disk assembly and manifest layers on top | Excellent for disk images; costliest to reach a live-ISO + installer product | Fastest for one-off prototypes; unusable for CI |

## Decision

**Use `ubuntu-image classic` as the primary image-build tool for Generation 001**
(`ubuntu-image classic <image-definition.yaml>`, with a first-party gadget tree
and an image-definition YAML pinned to the Ubuntu 26.04 LTS repository
snapshot). Rationale:

- It is Canonical's own tooling for precisely this product class, so LynxOS
  tracks the Ubuntu 26.04 base with the smallest possible upstream delta
  (criterion 5) — the rootfs is produced by live-build with the host's
  livecd-rootfs configuration, which keeps Ubuntu live-environment behavior.
- The classic path requires no Snap Store account or signed model assertion
  (the snap-based Ubuntu Core mode does), avoiding an unnecessary dependency.
- It produces a GPT disk image with the Ubuntu-signed boot chain
  (`shim-signed`, `grub-efi-amd64-signed`), satisfying the UEFI/Secure Boot
  requirement without forking boot components.
- It generates build manifests that seed the SBOM work required in P004/P007.

**Supporting assignments:**

- **live-build stays the fallback**: if the Ubuntu-packaged live-build version
  (3.0~a57-1u56) or livecd-rootfs proves too limiting during the spike, building
  with live-build directly (upstream 1:20250814) is the natural fallback, because
  ubuntu-image classic delegates to it anyway.
- **debos is reserved for the preinstalled/ARM track**: raw disk images and
  fast prototyping, not for the P001 live image. revisit when ARM64 work starts.
- **Cubic is permitted only for manual, one-off prototypes and explicitly
  excluded from the release pipeline**: GUI-driven, interactive, no
  reproducibility, no CI support, and 26.04 compatibility unverified. Its two
  known failure modes (Ubuntu Server two-part squashfs extraction requiring
  patched forks) make it unsuitable as infrastructure.

## Provisional constraints

This decision is **not final until the validation spike on an Ubuntu 26.04 x86-64
build host** (per [build-and-test](../development/build-and-test.md)) records:

1. a minimal image-definition YAML + gadget tree in the repository;
2. an artifact hash of the produced image and its build manifest;
3. the pinned Ubuntu repository snapshot (suite/date/components) actually used;
4. an UEFI QEMU/OVMF boot result with boot logs (P001 boot test);
5. measured ISO/image size and boot-time values against
   [performance budgets](../performance/budgets.md).

Until these exist, the evidence label for the tooling choice is **"Documented"**,
not **"Tested"**. The validation spike may only investigate tooling — it must not
diverge from the pinned base or weaken the boot-chain security posture.

## Consequences

Positive:

- Smallest maintained delta to Ubuntu's own image tooling.
- Signed UEFI/GRUB boot chain inherited from Ubuntu packages.
- Live-environment behavior follows Ubuntu's supported path.
- Clear roles: ubuntu-image classic for release images, live-build as fallback,
  debos for preinstalled/ARM, manual experiments only outside the pipeline.

Trade-offs:

- Requires root and a Linux build host (snap-based tooling on the build host).
- ubuntu-image classic is distributed as a classic snap; the build environment
  must permit snapd, or a pip/container-hosted build of it must be investigated.
- Snapshot pinning and reproducibility discipline are our responsibility
  (apt snapshot mirror, recorded inputs, artifact hashes).
- The classic path emits a raw GPT image; a hybrid ISO step for the live image
  remains project work to verify during the spike.

## Revisit when

- The validation spike contradicts any positive above (tooling defect, missing
  Secure Boot step, unacceptable build time).
- Deviates materially from Ubuntu 26.04's own packaging (a new major
  livecd-rootfs/ubuntu-image release, or deprecation of the classic mode).
- Or a first-party preinstalled-image track (ARM64) makes debos primary.