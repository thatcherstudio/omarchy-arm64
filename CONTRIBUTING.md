# Contributing

Start with an open issue whose explicit dependencies are closed with evidence. Claim it, create `task/<number>-<slug>` from main, and open a PR. Name upstream/prior-art commits, files changed, acceptance commands and actual results. The PM engagement does not implement the port.

Do not edit generated ISO images, VM disks or installed guests as the deliverable. Reproducible source changes, fixtures and runbooks must be in Git. Store large ISOs, logs and screenshots as release/CI artifacts with checksums; commit an evidence index under docs/evidence. Scrub credentials from installation logs.

All source imports and patches preserve licenses. Upstream ISO code lands at root via #3, not a parallel aarch64 profile. Desktop/recipe changes use pinned source patches and explicit source-repository PR links, so an ISO build can reproduce them without relying on a mutable developer checkout.

Each issue has one milestone, one or two labels, Objective / Files / Steps / Acceptance criteria / Source of truth / Depends on / final Verification. Commands mentioning future scripts describe deliverables to implement in that issue; they are not claims those scripts exist today.

Epics close only after their child checklist is complete. A child belongs to an epic but does not depend on its closure. Dependencies always point to lower-numbered issues; membership links may point forward. Update the GitHub checklist, README dashboard and versioned issue snapshot in the same completion PR. Serialize changes to shared build/configurator files or rebase the later PR.

Signing private keys never enter Git. The package publication task establishes a project keyring and signed snapshot; no keys, package publication or builds are performed during project setup.
