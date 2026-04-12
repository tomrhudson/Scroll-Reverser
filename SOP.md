# SOP.md — Scroll Reverser

## How to Make Changes

1. Work directly on `main` for bug fixes and small changes
2. Use a feature branch for anything that touches `MouseTap.m`'s event tap logic — this is the critical path and needs isolation
3. Commit messages: imperative mood, concise (`Fix horizontal IOHID multiplier bug`, not `Fixed the bug with the horizontal thing`)
4. No PR process — single-maintainer repo

## How to Test

Automated testing is impractical — the app requires system-level Accessibility and Input Monitoring permissions that can't be granted programmatically.

**Manual smoke test:**
1. Build and run in Xcode (Development configuration)
2. Grant Accessibility and Input Monitoring permissions when prompted
3. Enable Scroll Reverser via the menu bar icon
4. Verify trackpad scrolling is reversed (vertical)
5. Verify mouse scrolling is reversed (vertical)
6. Verify horizontal scrolling works correctly for both devices
7. Option-click the menu bar icon to open the debug console — confirm events are being logged without errors
8. Toggle the enable/disable switch and verify it responds immediately
9. Test "Start at Login" toggle in preferences

**After touching MouseTap.m:**
- Verify all six delta fields log correctly in the debug console
- Test with both continuous (trackpad) and discrete (mouse wheel) scrolling
- Confirm source detection correctly identifies trackpad vs. mouse
- Check that momentum scrolling (trackpad fling) continues in the reversed direction

## How to Build Locally

1. Clone BuildScripts if not already present: `git clone https://github.com/pilotmoon/BuildScripts.git BuildScripts`
2. Open `ScrollReverser.xcodeproj` in Xcode
3. Build and run (Cmd+R) — Debug configuration uses ad-hoc signing, no Developer ID needed
4. The `ScrollReverser-Dev.entitlements` file disables library validation for local dev builds

## How to Publish a Release (Automated)

Releases are fully automated via GitHub Actions (`.github/workflows/release.yml`). To publish:

```bash
git tag -a v1.0.2 -m "Description of release"
git push origin v1.0.2
```

The workflow automatically:
1. Builds the Release configuration with Developer ID signing (Thomas Hudson, JZPZ8PQR46)
2. Re-signs Sparkle framework with secure timestamps
3. Submits to Apple notarization and staples the ticket
4. Signs the zip with the Sparkle EdDSA private key
5. Creates a GitHub Release with the zip attached
6. Updates `appcast.xml` and pushes it to main

**Required GitHub Secrets** (already configured):
- `DEVELOPER_ID_CERTIFICATE_P12` — Base64-encoded Developer ID cert
- `DEVELOPER_ID_CERTIFICATE_PASSWORD` — P12 export password
- `APPLE_ID` — Apple ID for notarization
- `APPLE_ID_PASSWORD` — App-specific password for notarization
- `APPLE_TEAM_ID` — `JZPZ8PQR46`
- `SPARKLE_PRIVATE_KEY` — EdDSA private key (also in 1Password as "Scroll Reverser - Sparkle EdDSA Signing Key")

## How to Publish a Release (Manual)

If GitHub Actions is unavailable:

1. Build the Release configuration locally (requires Developer ID cert in Keychain)
2. Re-sign Sparkle with `--timestamp` flag
3. Zip the .app, sign with EdDSA key, create GitHub Release, update appcast.xml manually
4. See the workflow file for the exact steps

## How to Roll Back

- `git revert <commit>` for individual changes
- For broader rollback: the upstream commit `c518ca8` is the unmodified fork point

## Escalation Path

Single-maintainer repo. If the app stops working after a macOS update, the most likely cause is Apple removing or changing the private SPI functions. Check `dlsym` resolution in `MouseTap.m` — the `_spiAvailable` flag and startup log will indicate if the functions are no longer found.

## Maintenance Cadence

- Review after each major macOS release (WWDC / fall release) — check for deprecated API removals and SPI breakage
- Check upstream [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser) quarterly for significant changes worth cherry-picking
