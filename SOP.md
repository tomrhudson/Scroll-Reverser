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

## How to Build for Release

1. Switch to the **Production** build configuration in Xcode
2. Ensure the Developer ID code signing identity is configured
3. Build (Cmd+B)
4. The post-build scripts in `script/` handle Sparkle framework processing and language filtering

## How to Publish an Update

1. Archive the Release build in Xcode (Product > Archive)
2. Export the archive as a `.app`, then zip it:
   ```bash
   cd /path/to/export
   zip -r "ScrollReverser-vX.Y.Z.zip" "Scroll Reverser.app"
   ```
3. Sign the zip with the EdDSA private key:
   ```bash
   # Private key is stored securely (not in repo). See /tmp/sparkle_ed25519_private_key.base64
   # Use Sparkle's sign_update or a CryptoKit-based signing script
   ```
4. Create a GitHub Release at `tomrhudson/Scroll-Reverser` with the zip attached
5. Update `appcast.xml` in the repo with a new `<item>` entry containing:
   - Version number (`sparkle:version` and `sparkle:shortVersionString`)
   - Download URL (GitHub Release asset URL)
   - EdDSA signature (`sparkle:edSignature`)
   - File length
6. Commit and push `appcast.xml`
7. Sparkle will pick up the update on the next check cycle

**EdDSA signing key**: The private key (`sparkle_ed25519_private_key.base64`) must be stored securely outside the repo. The public key is in `Info.plist` as `SUPublicEDKey`.

## How to Roll Back

- `git revert <commit>` for individual changes
- For broader rollback: the upstream commit `c518ca8` is the unmodified fork point

## Escalation Path

Single-maintainer repo. If the app stops working after a macOS update, the most likely cause is Apple removing or changing the private SPI functions. Check `dlsym` resolution in `MouseTap.m` — the `_spiAvailable` flag and startup log will indicate if the functions are no longer found.

## Maintenance Cadence

- Review after each major macOS release (WWDC / fall release) — check for deprecated API removals and SPI breakage
- Check upstream [pilotmoon/Scroll-Reverser](https://github.com/pilotmoon/Scroll-Reverser) quarterly for significant changes worth cherry-picking
