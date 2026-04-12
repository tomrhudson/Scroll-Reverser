# CHANGELOG

All notable changes to this fork are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com).

## [Unreleased]

### Fixed
- **Bug:** Horizontal IOHID scroll reversal was using vertical multiplier (`vmul`) instead of horizontal multiplier (`hmul`) in `MouseTap.m`
- **Correctness:** Replaced `NSTouchPhase` constants with semantically correct `NSEventPhase` equivalents for momentum phase detection

### Changed
- Replaced deprecated `lockFocus`/`unlockFocus` with block-based `[NSImage imageWithSize:flipped:drawingHandler:]` in `StatusItemController.m`
- Replaced deprecated `activateIgnoringOtherApps:` with `[NSApp activate]` across all call sites in `AppDelegate.m`
- Private SPI functions (`CGEventCopyIOHIDEvent`, `IOHIDEventGetFloatValue`, `IOHIDEventSetFloatValue`) now resolved at runtime via `dlsym` with graceful fallback — replaces direct linking in `MouseTap.m`, `CoreGraphicsSPI.h`, `IOKitSPI.h`
- Removed per-scroll-event `refreshPermissions` call from event tap callback for better performance

### Removed
- Dead `@available(macOS 10.14/10.15/13.0)` guards and their unreachable `else` branches in `PermissionsManager.m` and `LauncherController.m` (always true given macOS 13.5 deployment target)
- Unused `LSSharedFileListRef` property from `LauncherController.m`
- Stale comment referencing macOS 10.9 in `PermissionsManager.m`

### Added
- GitHub Actions release workflow (`.github/workflows/release.yml`) — automated build, notarization, Sparkle signing, GitHub Release creation, and appcast.xml updates on `v*` tags
- Developer ID Application signing (Thomas Hudson, JZPZ8PQR46) for production releases
- `ScrollReverser-Dev.entitlements` with `disable-library-validation` for local dev builds
- `appcast.xml` Sparkle update feed pointing to this fork's GitHub Releases
- New EdDSA keypair for Sparkle update signing (public key in Info.plist, private key in GitHub secrets)
- Repo hygiene scaffolding: `CLAUDE.md`, `CHANGELOG.md`, `SOP.md`, `CONTEXT.md`, `DEPENDENCIES.md`, `PROMPTS/`, `TESTS/`, `ARCHIVE/` per `REPO_HYGIENE.md` Customization Fork standard
- Updated `README.md` with fork attribution, build instructions, folder structure, and change log
- Updated `.gitignore` with secrets patterns, BuildScripts exclusion, and expanded coverage

### Changed
- Sparkle update feed URL redirected from `softwareupdate.pilotmoon.com` to `raw.githubusercontent.com/tomrhudson/Scroll-Reverser`
- SUPublicEDKey in Info.plist replaced with new fork-specific EdDSA public key
- Copyright in Info.plist updated to Tom Hudson with original author attribution
- About screen credits updated to show both Tom Hudson and Nick Moore
- Xcode project signing changed to ad-hoc with empty team for local development
- BuildScripts changed from git submodule to manually cloned (gitignored)
- `script/build` updated: Debug builds use ad-hoc re-signing, Release builds use Developer ID with secure timestamps

## [0.0.0] — 2026-04-11

### Added
- Initial fork from [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser)
