# ADR 001 — ALARM base, shared ISO profile, reuse qualified ARM packages

Date: 2026-09-18. Status: accepted planning direction; release qualification remains open in #2, #6, #8 and #16.

## Decision

Use **Arch Linux ARM (ALARM)** repositories and its keyring for aarch64. ALARM is the base distribution; its rootfs tarball is a bootstrap mechanism, not a competing distribution. Use an audited ARM container/rootfs snapshot to run the shared archiso build. Keep native x86_64 Arch unchanged.

Start with `linux-aarch64`, its matching headers, `/boot/Image` and package-owned initramfs paths, as demonstrated by arm-utm stage2. Validate the exact package paths rather than implementing the spec's assumed `linux` / `vmlinuz-linux` mapping. Select the container by verified architecture, freshness and digest, not by trusting the example image name in the upstream plan.

Reuse existing compatible ARM binaries and packaging workflows first. Upstream edge now exists and includes the upstream desktop pair; stable is incomplete. The community repo supplies many remaining packages, but its `omarchy`/`omarchy-settings` pair targets Omarchy Mac/Asahi. Do not install that pair wholesale into a generic UEFI VM. Audit versions, transitive dependencies, ELF architecture, embedded binaries in `any` packages, licenses and ABI consistency. Build only actual gaps or necessary generic-UEFI packaging deltas. An audited empty gap set is a valid result for #7.

Publish a **project-signed immutable snapshot** of the qualified package closure using GitHub Releases in this repository, with public keyring/bootstrap instructions and signed database/package artifacts. This preserves the requested project GPG trust policy without requiring a separate Pages repository. Reuse upstream bytes where compatible and redistribution permits; sign the selected artifacts after provenance verification. Never confuse HTTP 200 or a configured `SigLevel` with verified trust.

Keep Limine as the intended installed-system bootloader; the live ISO uses ARM UEFI GRUB. An early ALARM + archinstall + Limine + LUKS/Btrfs/Snapper qualification is mandatory. Arm-utm's systemd-boot install is a useful diagnostic/bootstrap reference, but switching the product to systemd-boot requires a new ADR and explicit feature/rollback implications.

## Why

The working arm-utm path proves the base and desktop are feasible. Existing package work sharply reduces the build queue. Native public `ubuntu-24.04-arm` runners avoid QEMU compilation cost. One profile prevents architecture drift.

## Consequences / qualification

- Upstream aarch64 settings omit Limine/mkinitcpio configuration for Asahi. Generic UEFI needs a package-owned, tested boot integration contract.
- ALARM mirrors use `$arch/$repo`, not Arch's `$repo/os/$arch`. There is no assumed multilib tree.
- Our HTTPS probes of the ALARM geo and German mirror failed certificate hostname checks. The documented HTTP geo URL returned 200. Resolve transport in #2/#4; preserve ALARM package signature verification and never use `curl -k`.
- Keep repository selection through post-install and future updates, not just in the Configurator.
- Runtime/settings versions and source commits must match; current published 4.0.2 packages may lag the current ISO's setup-form contract.
- UTM-specific graphics/clipboard settings are opt-in or VM-scoped, not generic ARM defaults.
- Hardware qualification is distinct from QEMU/UTM proof. Do not claim all UEFI machines work.

## Sources

[Spec prerequisites and approach](https://github.com/omacom/omarchy-iso/blob/7cfb7111a06873d61c45d37034577d4ba08d3f4f/plans/aarch64-support.md), [arm-utm stage2/3](https://github.com/ggalancs/omarchy-arm-utm/tree/bf5ca3e9d2631220e5093d84728dc415685872ae/provision/src), [community recipes and signing](https://github.com/omarchy-mac/omarchy-pkgs-aarch64/tree/c18489bcad76dfa83884da41e8bdbb91bcad536d), [upstream desktop recipes](https://github.com/omacom/omarchy-pkgs/tree/7a3f00b924a2627fd23d45deee4b73fb5e14694a/pkgbuilds/omarchy).
