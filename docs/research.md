# Research — 2026-09-18

This is source inspection and read-only network probing, not a reproduced build or runtime certification. Required reading order was upstream plan, arm-utm README/stages, community package repository, Armarchy, then discussion #7960; follow-up source checks and the Guy James gist resolved contradictions.

## Pinned sources

| Source | Revision | Read / relevant files |
| --- | --- | --- |
| omacom/omarchy-iso, quattro | 7cfb7111a06873d61c45d37034577d4ba08d3f4f | plan; bin/omarchy-iso-make; builder/build-iso.sh; build-omarchy-packages.sh; profiledef; pacman config; configurator; orchestrator adapter and boot phases |
| ggalancs/omarchy-arm-utm | bf5ca3e9d2631220e5093d84728dc415685872ae | README; provision/src/stage1.sh, stage2.sh, stage3.sh, repair.sh, sanitize.sh |
| omarchy-mac/omarchy-pkgs-aarch64 | c18489bcad76dfa83884da41e8bdbb91bcad536d | README; SIGNING.md; manifest/workflow references |
| jondkinney/omarchy, amarchy-3-x | e40abf0c270c8a7955c10def536769c2c1cfa96e | ARM file inventory; install/login/limine-arm64.sh; install/arm_install_scripts/omarchy-nvim.sh |
| omacom/omarchy, quattro | d174d4aa279ea7393d4fad4a80fed147866106b9 | install tree; post-install/pacman.sh; hardware/pacman.sh; bin/omarchy-update-restart |
| omacom/omarchy-pkgs | 7a3f00b924a2627fd23d45deee4b73fb5e14694a | pkgbuilds/omarchy/PKGBUILD; pkgbuilds/omarchy-settings/PKGBUILD |
| Guy James gist | 3bf0692d35545c960341f8792c1ff3388f108a0e | omarchy-parallels-arm64.md |
| archiso gitlink in ISO | 424e78130db2af6c1ceb55b442d7914b1109ff2b | preserve pin; ARM mkarchiso support remains to qualify |

Old omacom-io/basecamp URLs redirect to **omacom**. jondkinney/armarchy redirects to **jondkinney/omarchy**; its default master is not the intended amarchy-3-x reference. Discussion [#7960](https://github.com/omacom/omarchy/discussions/7960) requests first-class ARM support and contains interest, not implementation evidence.

## Four upstream hard prerequisites

| Prerequisite | Evidence / community contribution | Status for this project |
| --- | --- | --- |
| 1. Pacman-accessible aarch64 base | ALARM HTTP core.db returns 200; arm-utm stage1/2 extracts ALARM rootfs and uses linux-aarch64 | Base exists. Mirror/keyring/container and package closure must be qualified (#2, #4, #6). HTTPS hostname failures remain recorded. |
| 2. Real aarch64 Omarchy repo | Official stable and edge DBs now return 200 and parse as tar archives; edge has desktop pair; community feed also returns 200 | Existence satisfied, **complete signed compatible closure not yet proven** (#6–#8). Stable lacks desktop. |
| 3. Architecture guards allow ARM | Quattro has no install/preflight tree; old guard is gone. ISO entrypoint still has no --arch and profile arch=x86_64 | Desktop guard removed; complete ISO/live/target execution audit remains (#4, #10, #15). |
| 4. archinstall + Limine end-to-end | Armarchy has BOOTAA64.EFI and ARM kernel hooks. arm-utm uses systemd-boot instead | **Open** for this exact Quattro + ALARM + generic UEFI + LUKS/Btrfs/Snapper path (#15, #16). |

## Direct network observations

Commands: `curl -L -sS --max-time 30 -o /dev/null -w '%{http_code}\\n' URL`; for official DB inventory, `curl -fsSL --max-time 30 URL | tar -tzf -`. Re-run with pipeline failure handling in #4. Redirect query tokens are intentionally not retained.

| URL | Result |
| --- | --- |
| https://pkgs.omarchy.org/stable/aarch64/omarchy.db | 200; five entries: claude-code, claude-desktop, github-copilot-cli, mise-bin, openai-codex-desktop. No omarchy/settings. |
| https://pkgs.omarchy.org/edge/aarch64/omarchy.db | 200; includes omarchy/settings 4.0.2-1, matching dev pair, keyring, nvim, Hyprland, toolkit, quickshell-git, Limine hooks and many tools. |
| https://pkgs.omarchy.org/stable/x86_64/omarchy.db | 200 control |
| https://stable-mirror.omarchy.org/core/os/aarch64/core.db | 404; base mirror problem persists |
| https://mirror.archlinuxarm.org/aarch64/core/core.db | curl exit 60, certificate hostname mismatch; not a 404 |
| https://de.mirror.archlinuxarm.org/aarch64/core/core.db | same TLS failure |
| http://mirror.archlinuxarm.org/aarch64/core/core.db | 200; ALARM package signatures still required |
| https://github.com/omarchy-mac/omarchy-pkgs-aarch64/releases/download/edge/omarchy-aarch64.db | 200 after redirect; no trust or full coverage claim |

No pacman transaction was executed. Database membership alone does not prove download integrity, signature validity, dependencies or ABI compatibility. The exact gap analysis remains #6.

## Already solved / reusable

- ARM base and a working Omarchy 4 UTM desktop are demonstrated by arm-utm, with published run evidence. Stage1 covers rootfs extraction before mounting the ESP; stage2 handles virtio modules, ALARM keyring, kernel paths, service parity and conditional Hyprland ABI rebuilds; stage3 covers the 18-tool contract, /usr/bin layout, user units, migrations and update/kernel handling.
- Community package recipes/workflows cover far more than the brief's approximate 25 packages. Reuse reviewed binaries and build only gaps. `herdr` uses checksum-pinned Zig 0.15.2. `omarchy-nvim` can carry native Mason tools despite an `any` declaration: inspect payloads.
- Armarchy's non-Asahi Limine script copies BOOTAA64.EFI and provides ARM update-hook prior art. Its nvim script still contains the obsolete `pkgbuilds/edge/omarchy-nvim` path; the corrected fallback is in the Guy James guide and current arm-utm uses the root pkgbuilds path. Do not claim the fix is already in that Armarchy file.
- UTM Wayland clipboard has an existing replacement session agent; keep spice-vdagentd and avoid competing agents. Port the device/session conditions and verify real text round trips.
- GitHub confirms free public ARM runners are GA: [official announcement](https://github.blog/changelog/2025-08-07-arm64-hosted-runners-for-public-repositories-are-now-generally-available/).

## What still needs building

Shared architecture selection and profile overlay; audited builder/container and archiso execution; ALARM mirror/keyring routing through build, live, target and updates; a qualified project-signed package snapshot; generic ARM Limine/UKI/kernel/Snapper integration; architecture-aware release/smoke scripts and CI; current-package desktop/guest fixes; regression and boot/install/update/recovery evidence.

## Deltas from the brief and frozen plan

1. Official ARM repo DBs are no longer 404. Stable is insufficient, edge is substantial. Reframe the long pole as qualification/signing plus missing runtime contracts.
2. Runtime/settings recipes are `arch=('x86_64' 'aarch64')`, not `any`. ARM dependencies/payload omissions encode Asahi assumptions, so publication alone is insufficient for generic UEFI.
3. ISO profile already uses zstd and no BCJ. Preserve it; do not add an ARM BCJ filter.
4. New Python orchestrator owns Limine directly and hardcodes BOOTX64.EFI/limine_x64.efi, in addition to Configurator JSON. Refactoring only the plan's old file list would miss the installed bootloader.
5. ALARM prior art uses linux-aarch64 and /boot/Image; verify presets, headers and UKIs instead of assuming the spec's linux package paths.
6. arm-utm now has an 18-tool contract and working herdr; GPU acceleration is reported usable on UTM 5 with software fallback, and resolution can change at runtime. Treat these as version-specific claims to retest, not permanent limitations.
7. Community desktop pair comes from Omarchy Mac and rejects Limine; its edge signing transition is pending per pinned docs. Neither is a drop-in answer to this project's UEFI + project GPG requirement.
8. Current ISO requires the runtime's shared setup-form and consistent runtime/settings packages. Existing published ARM 4.0.2 cannot be assumed compatible without payload inspection.
9. x86 regression should compare package/config/boot content under pinned inputs, accounting for timestamps and metadata. “Byte-similar” is not an unqualified bit-identical ISO claim.
10. `omarchy-update-restart` still uses modules/*/vmlinuz; post-install/pacman.sh still restores channel-specific configs. Both can undo an otherwise working ARM install.

## Evidence boundaries

No image, chroot, package build, signing key, installer execution or hardware test was created in this PM engagement. Links to prior-art results are attributed to their authors. Read the immutable sources linked in README and the work queue before reusing a patch.
