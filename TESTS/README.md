# TESTS/

Validation scripts, smoke tests, and test checklists for this repo.

## Constraints

Scroll Reverser requires system-level Accessibility and Input Monitoring permissions to function. These permissions cannot be granted programmatically, which makes fully automated testing impractical. Testing is manual.

## How to Run

Follow the manual smoke test procedure in `SOP.md`.

## What's Here

| File | Type | What It Validates |
|---|---|---|
| (none yet) | — | See SOP.md for manual test procedures |

## What "Passing" Looks Like

- App builds without warnings or errors in Development configuration
- Menu bar icon appears and responds to clicks
- Scroll reversal works correctly for both trackpad and mouse
- Debug console (Option-click icon) shows event logs without errors
- Source detection correctly distinguishes trackpad from mouse
- Enable/disable toggle responds immediately
- Preferences window opens and all controls function
