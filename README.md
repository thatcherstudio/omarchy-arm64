# Omarchy ARM64

An unofficial project to produce a native aarch64 Omarchy 4 ISO for UTM on Apple Silicon and generic UEFI ARM64 machines. The intended entrypoint is `bin/omarchy-iso-make --arch aarch64`, with one shared profile and build-time architecture overlays.

**Status: planning and work queue created; no port or ISO has been implemented or validated here.** Bare-metal Apple Silicon/Asahi and SBC/U-Boot installs are outside this project's scope.

## Project dashboard

| Milestone | Tracker | Status / exit gate |
| --- | --- | --- |
| 1 — Foundation | #1 | Research complete; baseline import and prerequisite automation queued |
| 2 — Package repo | #5 | Existing ARM feeds found; coverage, ABI compatibility and project signing unqualified |
| 3 — Installer/scripts refactor | #9 | Queued; target bootloader integration is a separate gate |
| 4 — ISO build | #21 | Blocked on package qualification and installer integration |
| 5 — Validation & boot | #25 | Queued; requires UTM install/reboot/update evidence |

[All issues](https://github.com/thatcherstudio/omarchy-arm64/issues) · [Milestones](https://github.com/thatcherstudio/omarchy-arm64/milestones) · [Implementation plan](docs/implementation-plan.md) · [Research](docs/research.md) · [Base/reuse ADR](docs/adr/001-base-distro.md)

## Important corrections from research

On 2026-09-18, both upstream `stable/aarch64/omarchy.db` and `edge/aarch64/omarchy.db` returned 200. Stable contained five packages without the desktop; edge included Omarchy 4.0.2 and settings. Repo existence is no longer the blocker: a complete compatible, trusted package closure is. Current ARM recipes assume Asahi in their boot dependencies; they are not proof of generic UEFI support.

The ISO still has no `--arch` switch. Its squashfs compression already uses zstd without BCJ, and its newer Python orchestrator still copies `BOOTX64.EFI`. See the research report for source pins and all brief deltas.

## Sources and prior art

- [Upstream spec](https://github.com/omacom/omarchy-iso/blob/7cfb7111a06873d61c45d37034577d4ba08d3f4f/plans/aarch64-support.md) ([original branch link](https://github.com/omacom-io/omarchy-iso/blob/quattro/plans/aarch64-support.md)); preserved in [docs/upstream-plan.md](docs/upstream-plan.md).
- [UTM Omarchy 4 implementation](https://github.com/ggalancs/omarchy-arm-utm/tree/bf5ca3e9d2631220e5093d84728dc415685872ae) — stages 1–3, kernel paths, packaging contracts and Wayland integration.
- [Community ARM package repository](https://github.com/omarchy-mac/omarchy-pkgs-aarch64/tree/c18489bcad76dfa83884da41e8bdbb91bcad536d) — reuse audited artifacts/recipes, not its Asahi desktop pair wholesale.
- [Armarchy 3.x branch](https://github.com/jondkinney/omarchy/tree/e40abf0c270c8a7955c10def536769c2c1cfa96e) — ARM Limine and legacy application fixes.
- [Guy James Parallels guide](https://gist.github.com/guyjames/c8d0fd23ba029f4f7a624e7a7170c40d/3bf0692d35545c960341f8792c1ff3388f108a0e) — historical package/workflow fixes, not a Quattro acceptance test.
- [Community feature request #7960](https://github.com/omacom/omarchy/discussions/7960).

## Working conventions

`main` plus `task/<issue>-<slug>` feature branches. Every substantive change uses a PR with `Closes #N`, source pins, and verification evidence. Branch protection is intentionally absent during foundation. Initial project content is bootstrapped through a planning PR.

This repository currently contains PM artifacts only. Issue #3 imports the pinned upstream source at the repository root and preserves its license and archiso gitlink; it must not create a second profile. Package/desktop changes outside the ISO are tracked as pinned patches or linked source PRs. See [CONTRIBUTING.md](CONTRIBUTING.md).

Issue bodies and the dependency manifest are versioned under [docs/issues](docs/issues). Epic checklists are membership, not child prerequisites: children never wait for their parent epic to close.
