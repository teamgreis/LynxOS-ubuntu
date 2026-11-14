# LynxOS Security Baseline — Generation 001

**Status:** Proposed baseline; implementation and enforcement are not yet verified.

## Security principles

1. Secure by default; no routine root desktop session.
2. Least privilege for users, services, applications and build automation.
3. Defense in depth; no single mechanism is treated as sufficient.
4. Updates and release artifacts must be authenticated.
5. Security controls must be tested in their effective configuration.
6. Exceptions must be documented, scoped and reviewed.
7. Recovery must remain possible after failed updates or configuration errors.

## Boot and platform integrity

- Enable UEFI Secure Boot on supported systems.
- Use the supported Ubuntu shim/GRUB signed chain where applicable.
- Document unsupported firmware and third-party module cases.
- Do not disable Secure Boot silently to make installation succeed.
- Evaluate measured boot and TPM-backed features separately; do not imply they are available on all hardware.

## Identity and privilege

- Create a standard user by default.
- Use controlled privilege elevation for administrative tasks.
- Give each LynxOS daemon a dedicated unprivileged service identity where practical.
- Avoid ambient Linux capabilities; grant only the specific capabilities required.
- Do not run desktop shell components or ordinary applications as root.
- Authenticate privileged operations through narrow, auditable interfaces.

## Process and application isolation

- Use AppArmor profiles in enforce mode for first-party services and suitable applications.
- Apply systemd sandboxing to services, including filesystem, device, namespace and privilege restrictions where compatible.
- Use Seccomp for services with stable and testable syscall requirements.
- Prefer Wayland-native applications and XDG Desktop Portals.
- Treat XWayland and legacy applications as compatibility boundaries, not as automatically sandboxed.

## Network

- Use nftables with an explicitly documented inbound policy.
- Expose no network service unless required and documented.
- Minimize listening sockets.
- Apply per-service network restrictions where practical.
- Test IPv4, IPv6, DNS, VPN and local-network behavior.

## Storage and secrets

- Offer full-system encryption using LUKS2 during installation.
- Explain recovery-key handling and the consequences of losing credentials.
- Protect secrets with appropriate OS facilities; never store credentials in logs or plaintext configuration.
- Define swap and hibernation behavior for encrypted systems before enabling them by default.
- Securely handle temporary files and crash reports.

## Updates and software supply chain

- Use authenticated Ubuntu repositories and package metadata.
- Keep security updates enabled by default, with transparent user-facing status.
- Validate package and image signatures.
- Generate an SBOM for release artifacts.
- Scan dependencies and images for known vulnerabilities.
- Pin GitHub Actions to full commit SHAs.
- Use least-privilege CI permissions.
- Do not embed signing keys, tokens or credentials in the repository or build logs.
- Sign LynxOS release artifacts and publish checksums and provenance.

## Logging and privacy

- Log security-relevant events without collecting unnecessary personal data.
- Never log passwords, recovery keys, access tokens or private key material.
- Document log retention and user controls.
- Avoid remote telemetry by default unless a separately reviewed opt-in design is approved.

## Required security tests

- Verify Secure Boot enabled/disabled and unsupported-hardware behavior.
- Verify AppArmor profiles are loaded and enforcing.
- Verify service users, capabilities and systemd sandbox settings.
- Verify no unexpected listening sockets.
- Verify firewall behavior for IPv4 and IPv6.
- Verify encrypted install, boot, recovery and key-rotation procedures.
- Verify package signature failure is rejected.
- Test interrupted update and recovery paths.
- Run static analysis, dependency scanning and SBOM generation.
- Test privilege-boundary failures and malformed IPC input.

## Release blockers

Any unresolved critical or high-severity vulnerability affecting the release, any bypass of a required security boundary, any exposed signing secret, or any unverified destructive installer behavior blocks release until resolved or formally assessed with an approved exception.

This document is a target baseline. A control is not considered implemented merely because it appears in this document or is enabled by an upstream default.
