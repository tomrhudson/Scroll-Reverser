# Scroll Reverser (Fork)

**Reverses the direction of scrolling on macOS, with independent settings for trackpads and mice.**

> **This fork is no longer maintained.** The [final release (v1.0.1)](https://github.com/tomrhudson/Scroll-Reverser/releases/tag/v1.0.1) remains available.

> **This is a fork of [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser)** by Nicholas Moore / Pilotmoon Software. The original project is licensed under Apache License 2.0. This fork contains bug fixes, deprecated API replacements, and modernization changes that diverge from the upstream codebase.

---

## Prerequisites

- **macOS 13.5** (Ventura) or later
- **Xcode** (with macOS SDK and command-line tools)
- A valid **Developer ID** code signing certificate (or replace with your own in Xcode's Signing & Capabilities)

## How to Build and Run

1. Clone the repo:
   ```bash
   git clone <this-repo-url>
   cd Scroll-Reverser
   ```
2. Clone the BuildScripts dependency (gitignored, not a submodule):
   ```bash
   git clone https://github.com/pilotmoon/BuildScripts.git BuildScripts
   ```
3. Open `ScrollReverser.xcodeproj` in Xcode
4. Replace the code signing identity with your own Developer ID certificate in the **Signing & Capabilities** tab of the Scroll Reverser target
5. Build and run (Cmd+R)

**Note:** Debug builds produce an app named "Scroll Reverser (Dev)" with version "99999" and no app icon. This is expected — the build is fully functional.

## Folder Structure

```
Scroll-Reverser/
├── *.h / *.m             # Objective-C source (18 header/implementation pairs)
├── ScrollReverser.xcodeproj/  # Xcode project
├── Sparkle.framework/    # Embedded auto-update framework
├── BuildScripts/         # Cloned manually (gitignored) — version injection, signing, language filtering
├── .github/workflows/    # GitHub Actions release pipeline
├── Graphics/             # App icon assets and status bar icons
├── resources/            # Localization files (20+ languages)
├── script/               # Post-build scripts
├── *.xcconfig            # Build configuration files
├── ScrollReverser.sdef   # AppleScript dictionary
├── LICENSE               # Apache License 2.0
└── NOTICE                # Original copyright attribution
```

### Key Source Files

| File | Purpose |
|------|---------|
| `MouseTap.m` | Core engine — event tap interception, trackpad/mouse detection, scroll reversal |
| `AppDelegate.m` | Application lifecycle, Sparkle updates, menu, preferences, permissions |
| `PermissionsManager.m` | Accessibility and Input Monitoring permission management |
| `StatusItemController.m` | Menu bar status icon |
| `PrefsWindowController.m` | Preferences window UI |
| `LauncherController.m` | "Start at Login" via ServiceManagement |
| `CoreGraphicsSPI.h` / `IOKitSPI.h` | Private SPI type definitions for IOHID event access |

## How It Works

Scroll Reverser installs two system event taps:

1. **Passive tap** — Monitors gesture events to detect trackpad finger touches (used to distinguish trackpad from mouse)
2. **Active tap** — Intercepts scroll wheel events and reverses direction based on user preferences

Source detection uses a 222ms touch window and continuous/discrete scroll state to determine whether events come from a trackpad or mouse. Vertical and horizontal reversal are handled independently, with special step-size adjustment for discrete mouse wheel scrolling.

The app depends on private IOKit/CoreGraphics SPI (`CGEventCopyIOHIDEvent`, `IOHIDEventGetFloatValue`, `IOHIDEventSetFloatValue`) for fine-grained scroll delta access. These are resolved at runtime via `dlsym` with graceful degradation if unavailable.

## Fork Changes

Changes made in this fork from the upstream [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser):

- **Bug fix:** Corrected horizontal IOHID scroll reversal using wrong multiplier (`vmul` instead of `hmul`)
- **Deprecated API replacement:** `lockFocus`/`unlockFocus` replaced with block-based `NSImage` drawing
- **Deprecated API replacement:** `activateIgnoringOtherApps:` replaced with `[NSApp activate]`
- **Correctness fix:** `NSTouchPhase` constants replaced with semantically correct `NSEventPhase` equivalents
- **Dead code removal:** Stripped `@available` guards that are always true given the macOS 13.5 deployment target; removed unused `LSSharedFileListRef` property
- **Performance:** Removed per-event `refreshPermissions` call from the hot event tap callback
- **Resilience:** Private SPI functions now resolved via `dlsym` at runtime with graceful fallback
- **CI/CD:** GitHub Actions workflow for automated builds, notarization, and releases on version tags
- **Signing:** Configured for Developer ID Application (Thomas Hudson, JZPZ8PQR46) with ad-hoc signing for local dev builds
- **Updates:** Sparkle feed redirected to this fork's GitHub repo with new EdDSA signing key

## Known Issues / Limitations

- **Private SPI dependency** — Core functionality relies on undocumented Apple APIs that could break in any macOS release. Runtime `dlsym` resolution provides a safety net but degraded behavior if removed.
- **NOTICE file trademark restriction** — The name "Scroll Reverser" and the application icon are trademarks of the original author and may not be used by derivative works (except to describe origin).
- **No automated tests** — The app requires system-level Accessibility and Input Monitoring permissions, making automated testing impractical without manual permission grants.
- **Sparkle framework** — Embedded directly in the repo rather than managed via SPM/CocoaPods. Update feed points to `raw.githubusercontent.com/tomrhudson/Scroll-Reverser/main/appcast.xml`.

## License

Apache License 2.0 — see [LICENSE](LICENSE) and [NOTICE](NOTICE).

Original work copyright 2011 Nicholas Moore / Pilotmoon Software.

---

**Last updated:** 2026-04-12
