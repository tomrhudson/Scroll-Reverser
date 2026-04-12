# CLAUDE.md — Scroll Reverser

## Fork Classification

**Customization Fork**

## Upstream Source

[https://github.com/pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser)

Original author: Nicholas Moore / Pilotmoon Software
License: Apache License 2.0

## Why It Was Forked

The upstream repo contained deprecated API usage, a bug in horizontal scroll reversal, and direct calls to private SPI without runtime safety checks. This fork modernizes the codebase for current macOS versions and fixes issues rather than contributing back upstream (the original project's maintenance cadence is unclear).

## Sync Strategy

No regular upstream sync. This fork has intentionally diverged. Upstream changes may be cherry-picked on a case-by-case basis if the original project introduces significant new features or compatibility fixes, but the codebases are expected to drift apart.

## Project Identity

A macOS menu bar utility that intercepts system scroll events and reverses their direction. Supports independent configuration for trackpad vs. mouse, vertical vs. horizontal axes, and discrete mouse wheel step size adjustment. Written in Objective-C targeting macOS 13.5+.

## Role Definition

Claude's role in this repo: **Implementer and Reviewer**

- Fix bugs and modernize deprecated APIs
- Review code for correctness, especially around the event tap callback and private SPI usage
- Maintain repo hygiene per REPO_HYGIENE.md (Customization Fork standard)
- Do not introduce new features without explicit direction

## Constraints

- **Do not remove or rename** `LICENSE`, `NOTICE`, or any upstream attribution — required by Apache 2.0
- **Do not modify** the Sparkle.framework directory — it's a pre-built binary dependency
- **Do not modify** the BuildScripts directory — it's cloned from an external repo (gitignored, not a submodule)
- **Do not change** the bundle identifier (`com.pilotmoon.scroll-reverser`) without explicit direction — it affects permissions, login items, and user preferences
- **Private SPI usage is intentional** — `CGEventCopyIOHIDEvent`, `IOHIDEventGetFloatValue`, `IOHIDEventSetFloatValue` are required for core functionality. Always access via runtime `dlsym` resolution, never direct linking
- **Localization files in `resources/`** — Do not modify existing translations. New strings should be added to English only; translation is a separate concern

## Conventions

- **Language:** Objective-C with ARC enabled
- **Style:** Follow existing code style — no spaces around `=` in property assignments, K&R braces, `self.property` access pattern
- **Commit messages:** Imperative mood, concise first line, body if needed
- **Build configurations:** Development (debug), Production (release), Beta (pre-release channel)
- **Logging:** Use `NSLog` for app-level events; use the custom `TapLogger` for event tap diagnostics (never `NSLog` in the hot callback path)

## Active Context

- **Status:** `[ACTIVE]`
- **Deployment target:** macOS 13.5
- **Current state:** Bug fixes, API modernization, CI/CD pipeline, and signing infrastructure applied. Core architecture unchanged from upstream.
- **Signing:** Developer ID Application (Thomas Hudson, JZPZ8PQR46) for releases; ad-hoc for local dev builds
- **CI:** GitHub Actions workflow (`.github/workflows/release.yml`) handles build, notarization, and release on `v*` tags

## Linked Documents

- `CONTEXT.md` — Decision log and architectural reasoning
- `SOP.md` — Build, test, and release procedures
- `DEPENDENCIES.md` — External dependencies and version pins
- `REPO_HYGIENE.md` (parent `REPOS/` directory) — Standards this repo follows

## Prompt Patterns

- When reviewing MouseTap.m, always verify that all six scroll delta fields (axis1, axis2, point_axis1, point_axis2, fixedpt_axis1, fixedpt_axis2) plus the two IOHID fields are handled consistently
- Before suggesting any API replacement, verify the replacement is available at the deployment target (macOS 13.5)
- When touching event tap code, consider performance — the callback runs on every scroll event
