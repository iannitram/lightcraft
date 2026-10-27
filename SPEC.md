# LightCraft (our fork): spec

**Approval:** 2026-10-26, the owner: "The specs/roadmaps were developed by someone else. Nomalize them to our system" (spec = upstream roadmap + our delta, R18).

## The spec is upstream's roadmap

LightCraft is storytold's pure-Rust photo library and raw developer aiming at parity with Adobe Lightroom Classic. We follow upstream's plan:

- One-page roadmap: https://github.com/storytold/lightcraft/blob/main/ROADMAP.md
- Ranked gaps (beta blockers marked B): https://github.com/storytold/lightcraft/blob/main/docs/gaps.md
- Milestones and alpha/beta gates: https://github.com/storytold/lightcraft/blob/main/docs/roadmap.md
- Parity assessment and raw coverage: https://github.com/storytold/lightcraft/blob/main/docs/target-app-parity.md, https://github.com/storytold/lightcraft/blob/main/docs/raw-parity.md

**1.0 for us** is upstream's beta bar on our build: the beta-blocking gaps (B in docs/gaps.md) closed and the six core workflows (import, cull, develop, save and reopen, batch looks, export) working on Mac, Windows and Linux.

## Our delta

| Item | Where | Disposition |
|---|---|---|
| Use it for the owner's day-to-day photo work: the 95% feature list and a golden task per platform | #6, #7 | keep on our fork (needs the owner) |
| Tests on macOS and Windows in our CI (upstream gap 9) | build issue | send upstream (to confirm with the owner) |
| Bugs found by our sibling sweep | #4 | send upstream when reproduced and fixed |
| Anything else | none yet: we run upstream's code unchanged | |

Upstream PRs need the owner's yes, one by one; no credit beyond the username (pinned issue #8).
