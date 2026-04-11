# CONTEXT.md — Scroll Reverser

## Problem Statement

macOS allows reversing scroll direction system-wide, but the setting applies to both trackpad and mouse simultaneously. Many users want natural scrolling on the trackpad but classic scrolling on a mouse (or vice versa). Scroll Reverser solves this by intercepting scroll events at the system level and selectively reversing them based on input device.

## Why This Fork Exists

The upstream [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser) contained:
- A bug where horizontal IOHID scroll reversal used the wrong multiplier
- Deprecated macOS APIs that will eventually be removed (`lockFocus`, `activateIgnoringOtherApps:`)
- Direct calls to private SPI functions with no runtime safety check
- Dead code paths from old macOS version guards that no longer apply

Rather than contribute back upstream (unclear maintenance status), this fork applies fixes directly and evolves independently.

## Decision Log

| Date | Decision | Rationale | Alternatives Considered |
|------|----------|-----------|------------------------|
| 2026-04-11 | Classified as Customization Fork | Code changes diverge from upstream; not PRing back | Contribution Fork — rejected because upstream maintenance cadence is unclear |
| 2026-04-11 | Runtime `dlsym` for private SPI | Graceful degradation if Apple removes these functions | Direct linking (original approach) — rejected because it crashes if symbols disappear; Removing SPI usage entirely — rejected because there's no public API alternative |
| 2026-04-11 | Replaced `NSTouchPhase` with `NSEventPhase` | Semantically correct constants for momentum phase detection | Keeping `NSTouchPhase` — rejected because it relies on coincidental integer value overlap |
| 2026-04-11 | Removed `refreshPermissions` from event tap callback | Called on every scroll event; unnecessary overhead | Throttling instead of removing — rejected because PermissionsManager already has timer-based polling |

## Dependencies and Integrations

- **Sparkle.framework** — Embedded auto-update framework. Feed URLs point to `softwareupdate.pilotmoon.com`. If this fork is ever distributed independently, the update feed would need to be changed or disabled.
- **BuildScripts** — Git submodule from `github.com/pilotmoon/BuildScripts`. Handles version injection and post-build processing. Must be initialized before building.
- **macOS Accessibility + Input Monitoring** — The app requires both system permissions to function. Without them, the event tap cannot be created.

## Future State

- Monitor each macOS release for private SPI breakage
- If Apple provides a public API for per-device scroll direction (unlikely but possible), migrate away from event tap approach
- Consider removing or replacing the Sparkle update mechanism if this fork is never distributed via the original update channel

## Owner

Tom Hudson
