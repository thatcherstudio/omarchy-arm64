# Phased implementation plan

As of 2026-09-18. Scope: PM setup is complete; all implementation and runtime validation is queued. Immutable research sources are in [research.md](research.md); accepted planning decisions are in [ADR 001](adr/001-base-distro.md).

## Delivery strategy

Import the pinned upstream ISO source through #3. Keep one root profile and build-time overlays. Use ALARM base packages and native ARM CI. Audit and reuse compatible official-edge/community artifacts; build only gaps and generic UEFI packaging changes. Publish an immutable project-signed package snapshot before selecting it in the installer. Verify target Limine separately from live ISO GRUB.

Package work is still the likely long pole, but its size is evidence-dependent: repositories now exist. Current upstream ARM packaging is Asahi-oriented, and the newer orchestrator has additional x86 assumptions. These make the early target boot proof a second critical gate.

## Milestones and ordered queue

| Milestone | Tracker | Work / phase exit |
| --- | --- | --- |
| Foundation | #1 | #2 ALARM/kernel qualification; #3 upstream import; #4 four-prerequisite probe |
| Package repo | #5 | #6 closure/ABI/payload audit → #7 only missing builds → #8 signed snapshot and clean chroot |
| Installer/scripts refactor | #9 | #10–#20 shared architecture plumbing, repositories, boot, desktop, VM integration and CI |
| ISO build | #21 | #22 x86 regression → #23 ARM ISO → #24 live Configurator |
| Validation & boot | #25 | #26 UTM install → #27 updates/recovery; #28 guest UX; #29 additional UEFI target; #30 docs; #31 upstream handoff |

Every task is sized around a reviewable output. The package-build task may split into package-specific children **after** #6 reports actual gaps; do not manufacture 18 rebuild tasks for packages already published. Such additions must retain topological ordering in the manifest and one milestone/one-to-two labels.

## Critical path and parallel work

Primary gate chain: **#2 + #3 → #4 → #6 → #7 → #8 → #14 → #15 → #16 → #20 → #22 → #23 → #24 → #26 → #27**.

Converging build dependencies: #3 → #10 → #11 → #12/#13; #14 depends on #12 and #8; #15 also needs #13 and #7. #17 → #18 and #19 converge at #20. #11 waits for #6 so package-name/kernel changes reflect the audit.

After #26, #28 guest UX can proceed alongside #27 update qualification. #29 adds a non-UTM target after lifecycle proof. #30 depends on #27/#28/#29 and #31 depends on #22/#27/#30. If hardware is unavailable, #29 remains open; narrow supported scope only through an explicit recorded decision, never by claiming a test happened.

#2 and #3 can proceed independently. Package reuse/audit and argument-plumbing work can run concurrently. Changes touching builder/configurator files must be rebased/serialized during integration. Numbers order prerequisites, not a requirement that every lower-numbered unrelated issue close first.

Epics are membership trackers. They can reference future children in checklists, but children have no “wait for parent epic closure” dependency. Their forward membership links are deliberately excluded from the dependency DAG.

## Upstream spec coverage

| Plan area | Issues |
| --- | --- |
| 1 Build entrypoint | #10 |
| 2 Profile | #11 (zstd/BCJ already resolved upstream) |
| 3 Build script | #11, #12 |
| 4 archinstall packages | #12 |
| 5 Live bootloader | #13 |
| 6 Preset/kernel | #11, #15 |
| 7 Configurator | #14, #15 |
| 8 pacman configs | #8, #12, #14 |
| 9 Smoke scripts | #19, #24 |
| 10 Release script | #19 |
| 11 CI | #20 |
| New orchestrator/Asahi package delta | #7, #15, #16 |
| Application/guest lifecycle | #17, #18, #26–#28 |

## Scope and correctness decisions

- The frozen plan is preserved, not silently rewritten. Current-source deltas appear in research and affected tasks.
- ALARM rootfs vs container is a bootstrap decision inside the same base distribution; no invented “vanilla Arch ARM” mirror.
- Generic UEFI ARM is distinct from bare-metal Asahi. Packages that exclude Limine for all aarch64 require a qualified integration contract.
- Live media GRUB, installed Limine and UTM firmware fallback are separate test surfaces.
- Preserve current zstd profile; no obsolete BCJ work.
- Published package availability, complete dependency/ABI closure, correct target payload and trusted signatures are separate gates.
- Correct all installer phases, including post-install pacman config restore and update behavior.
- Use semantic/payload x86 regression with controlled inputs; report nondeterministic ISO bytes.
- Prior-art manual copies/wrappers become source patches or packages; never hand-patch a delivered VM/ISO.
- Project-signing key setup/publication, actual ISO builds and upstream submission are future tasks, not PM setup actions.

## Evidence and completion

Each issue lists files, steps, source of truth, acceptance criteria, dependency IDs and a final exact Verification command/expected result. New verification script names are explicit implementation deliverables, not existing tooling claims. Record actual command output, source/package/image hashes and runner/UTM versions. Keep large evidence artifacts in CI/releases and commit their index.

Close tasks only after their PR merges and evidence is attached; update the epic checklist and README. The milestone titles have no invented due dates. Estimates such as 30–60 minutes for an ARM ISO build are provisional and replaced with measurements in #23.

## Full issue index

| Issue | Milestone | Dependencies |
| --- | --- | --- |
| [#1 [Epic] Foundation: repository, decisions and executable prerequisite checks](https://github.com/thatcherstudio/omarchy-arm64/issues/1) | 1 | None |
| [#2 [Task] Qualify ALARM base, kernel and package-reuse decisions](https://github.com/thatcherstudio/omarchy-arm64/issues/2) | 1 | None |
| [#3 [Task] Import the pinned upstream ISO baseline through a PR](https://github.com/thatcherstudio/omarchy-arm64/issues/3) | 1 | None |
| [#4 [Task] Implement a four-prerequisite probe with honest unknown results](https://github.com/thatcherstudio/omarchy-arm64/issues/4) | 1 | #2, #3 |
| [#5 [Epic] Qualify and publish a signed aarch64 package repository](https://github.com/thatcherstudio/omarchy-arm64/issues/5) | 2 | #2, #4 |
| [#6 [Task] Audit full package closure across ALARM, official edge and community feeds](https://github.com/thatcherstudio/omarchy-arm64/issues/6) | 2 | #2, #4 |
| [#7 [Task] Package only confirmed ARM gaps and generic UEFI dependencies](https://github.com/thatcherstudio/omarchy-arm64/issues/7) | 2 | #6 |
| [#8 [Task] Publish and verify a project-signed immutable ARM package snapshot](https://github.com/thatcherstudio/omarchy-arm64/issues/8) | 2 | #7 |
| [#9 [Epic] Refactor the shared installer and builder for dual architecture](https://github.com/thatcherstudio/omarchy-arm64/issues/9) | 3 | #2, #3 |
| [#10 [Task] Add --arch selection, container plumbing and isolated build state](https://github.com/thatcherstudio/omarchy-arm64/issues/10) | 3 | #3 |
| [#11 [Task] Overlay profile, builder packages and kernel paths at build time](https://github.com/thatcherstudio/omarchy-arm64/issues/11) | 3 | #2, #6, #10 |
| [#12 [Task] Route all pacman channels through ARM-aware package and trust overlays](https://github.com/thatcherstudio/omarchy-arm64/issues/12) | 3 | #6, #11 |
| [#13 [Task] Generate ARM UEFI live-ISO boot entries and EFI binaries](https://github.com/thatcherstudio/omarchy-arm64/issues/13) | 3 | #11 |
| [#14 [Task] Preserve ALARM mirrors and kernel choice through configurator and finalization](https://github.com/thatcherstudio/omarchy-arm64/issues/14) | 3 | #8, #12 |
| [#15 [Task] Port the target Limine orchestrator and generic ARM boot package contract](https://github.com/thatcherstudio/omarchy-arm64/issues/15) | 3 | #7, #13, #14 |
| [#16 [Task] Prove archinstall + Limine + LUKS/Btrfs/Snapper on ARM before ISO release](https://github.com/thatcherstudio/omarchy-arm64/issues/16) | 3 | #15 |
| [#17 [Task] Port remaining ARM desktop/package integration fixes](https://github.com/thatcherstudio/omarchy-arm64/issues/17) | 3 | #6, #14 |
| [#18 [Task] Package optional UTM Wayland integration and graphics controls](https://github.com/thatcherstudio/omarchy-arm64/issues/18) | 3 | #17 |
| [#19 [Task] Parameterize QEMU smoke tools and architecture-specific release selection](https://github.com/thatcherstudio/omarchy-arm64/issues/19) | 3 | #10, #13 |
| [#20 [Task] Configure native dual-arch CI and evidence artifacts](https://github.com/thatcherstudio/omarchy-arm64/issues/20) | 3 | #8, #12, #13, #14, #15, #16, #17, #18, #19 |
| [#21 [Epic] Produce and qualify the first dual-architecture ISO artifacts](https://github.com/thatcherstudio/omarchy-arm64/issues/21) | 4 | #16, #20 |
| [#22 [Task] Build x86_64 and verify semantic parity against the pinned upstream baseline](https://github.com/thatcherstudio/omarchy-arm64/issues/22) | 4 | #20 |
| [#23 [Task] Build the first aarch64 ISO from the qualified signed snapshot](https://github.com/thatcherstudio/omarchy-arm64/issues/23) | 4 | #8, #16, #20, #22 |
| [#24 [Task] QEMU ARM live-ISO smoke: reach and exercise the Configurator](https://github.com/thatcherstudio/omarchy-arm64/issues/24) | 4 | #23 |
| [#25 [Epic] Validate native UTM install, updates, recovery and generic UEFI scope](https://github.com/thatcherstudio/omarchy-arm64/issues/25) | 5 | #24 |
| [#26 [Task] Install and reboot into Omarchy 4 in native Apple Silicon UTM](https://github.com/thatcherstudio/omarchy-arm64/issues/26) | 5 | #18, #24 |
| [#27 [Task] Verify updates, kernel reboot detection and snapshot recovery on ARM](https://github.com/thatcherstudio/omarchy-arm64/issues/27) | 5 | #26 |
| [#28 [Task] Validate UTM clipboard, display changes and graphics fallback](https://github.com/thatcherstudio/omarchy-arm64/issues/28) | 5 | #26 |
| [#29 [Task] Qualify one additional generic UEFI ARM target and document limits](https://github.com/thatcherstudio/omarchy-arm64/issues/29) | 5 | #27 |
| [#30 [Task] Publish tested known issues and operator installation/recovery docs](https://github.com/thatcherstudio/omarchy-arm64/issues/30) | 5 | #27, #28, #29 |
| [#31 [Task] Submit the verified dual-arch changes upstream and link the project in discussion 7960](https://github.com/thatcherstudio/omarchy-arm64/issues/31) | 5 | #22, #27, #30 |
