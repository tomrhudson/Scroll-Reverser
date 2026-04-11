# DEPENDENCIES.md — Scroll Reverser

## Runtime

| Dependency | Version | Notes |
|---|---|---|
| macOS | 13.5+ (Ventura) | Minimum deployment target |

## Libraries / Frameworks

| Dependency | Version | Notes |
|---|---|---|
| Sparkle.framework | Embedded (v2.x) | Auto-update framework; pre-built binary included in repo |
| Cocoa.framework | System | AppKit / Foundation — provided by macOS SDK |
| IOKit.framework | System | Hardware I/O access — permission checks |
| ServiceManagement.framework | System | `SMAppService` for "Start at Login" |
| CoreGraphics.framework | System | Event tap API (`CGEventTapCreate`, scroll event fields) |

## Build Tools

| Tool | Version | Notes |
|---|---|---|
| Xcode | Latest supporting macOS 13.5 SDK | Build system, code signing, Interface Builder nibs |
| BuildScripts | Git submodule | `github.com/pilotmoon/BuildScripts` — version injection, post-build language filtering, Sparkle processing. Must `git submodule update --init` after clone |

## Private / Undocumented APIs

| Symbol | Framework | Resolved Via | Notes |
|---|---|---|---|
| `CGEventCopyIOHIDEvent` | CoreGraphics (SPI) | `dlsym` at runtime | Returns IOHIDEvent from a CGEvent; no public alternative |
| `IOHIDEventGetFloatValue` | IOKit (SPI) | `dlsym` at runtime | Reads float scroll delta from IOHIDEvent |
| `IOHIDEventSetFloatValue` | IOKit (SPI) | `dlsym` at runtime | Writes float scroll delta to IOHIDEvent |

These are undocumented and unsupported. They may be removed or changed in any macOS release. The app degrades gracefully if they are unavailable (scroll reversal still works via public CGEvent fields, but smooth scrolling quality may be reduced).

## External Services / APIs

| Service | Purpose | Notes |
|---|---|---|
| softwareupdate.pilotmoon.com | Sparkle appcast feed | Points to upstream author's update server. Not relevant for this fork unless distributed |

## Infrastructure

None. This is a standalone macOS desktop application with no server-side dependencies.

## Update Policy

- Check for Xcode SDK compatibility after each macOS release
- Monitor private SPI availability after each macOS release (check `dlsym` resolution logs)
- BuildScripts submodule: pin to current commit; update only if build process requires it
- Sparkle.framework: no planned updates unless a security issue is identified
