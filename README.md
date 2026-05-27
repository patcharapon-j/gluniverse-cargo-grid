# GLUniverse Cargo Grid

A Foundry VTT v14 module for mission-scoped cargo packing.

## Installation

Use this manifest URL in Foundry VTT:

```text
https://github.com/patcharapon-j/gluniverse-cargo-grid/releases/latest/download/module.json
```

## Current Implementation

- Floating Cargo button with GM-controlled global player visibility.
- Dedicated resizable Cargo Grid window with persisted per-user position and size.
- World-scoped mission data with one active mission at a time.
- Shared rectangular containers and irregular polyomino cargo shapes.
- GM Quick Cargo creator with shape presets, random valid shape generation by cell count, click-to-draw mask editor, color/category selection, quantity creation, and PF2e item drag/drop snapshot support.
- Player cargo selection, placement, rotation, rearrangement, return-to-floor, and multiplayer lock timeout.
- GM board lock for pausing player cargo movement without hiding the board.
- Mission manager with create, activate, duplicate, reopen, delete, and extraction report.
- Extraction chat report with secured/abandoned cargo summary.
- Premium GLUniverse-styled CSS inspired by the sibling initiative module while using separate `glucargo` variables.

## V1 Limits

- No automatic PF2e item grants.
- No train/base status mutation.
- No weight system.
- No sound or touch/mobile optimization.
- No mission/template import-export.

See [docs/PRD.md](docs/PRD.md) for the product specification.
