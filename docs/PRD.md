# GLUniverse Cargo Grid PRD

## Overview

GLUniverse Cargo Grid is a Foundry VTT v13 module for mission cargo management. It provides a shared RE4-inspired packing board where the GM awards mission cargo and players collaboratively place irregular cargo shapes into rectangular extraction containers.

The module should feel clean, modern, premium, and polished, taking visual inspiration from Arknights: Endfield and the existing `gluniverse-initiative` module without directly coupling to its CSS. The cargo module uses its own `glucargo` design system.

## Goals

- Give the GM a fast way to create cargo during play or prepare cargo before a session.
- Let players see available mission cargo, inspect it based on GM visibility settings, and pack it into extraction containers.
- Support multiplayer synchronized movement, rotation, rearrangement, locks, and extraction reporting.
- Support PF2e item linking when PF2e is available, while keeping the core module system-agnostic.
- Preserve completed mission boards and generate player-facing extraction reports.

## Non-Goals For V1

- Automatic PF2e item granting.
- Automatic train/base status mutation.
- Weight or non-grid capacity rules.
- Touch/mobile optimization.
- Sound effects.
- Template or mission import/export.
- Per-player container ownership.
- Category-driven mechanical rules.

## Core Concepts

### Mission

A mission is a module-owned cargo board that can span multiple Foundry scenes. The module supports many saved missions, but only one active mission at a time.

Mission state is not scene-scoped. Scene integration may later indicate which scenes belong to a mission, but cargo state lives in module world data.

Missions can be:

- Draft
- Active
- Extracted

Extracted missions are read-only by default. The GM can reopen, duplicate, or delete them.

### Global Player Visibility

Player visibility is global, not per mission or per scene.

When disabled:

- Players see no floating cargo button.
- Any open player cargo window closes.
- GM still sees and can manage the board.

When enabled:

- Players see the cargo button if there is an active visible mission.
- Players can open the shared Cargo Board window.

### Containers

Containers are party/shared rectangular grids. They are simple named containers in v1.

Container fields:

- `id`
- `name`
- `width`
- `height`
- `locked`
- `notes`
- `createdAt`
- `updatedAt`

Container sidebar displays:

- used cells / total cells
- percentage full
- cargo piece count
- lock/read-only indicators

### Cargo

Cargo is a mission cargo instance. Every awarded cargo item is a separate tile instance, even when created via quantity.

Cargo shape is a polyomino mask, not a rectangle. Containers remain rectangular.

Cargo fields:

- `id`
- `templateId`
- `name`
- `subtitle`
- `category`
- `customCategory`
- `shape`
- `rotation`
- `location`
- `position`
- `visibility`
- `priority`
- `value`
- `benefit`
- `gmNotes`
- `linkedItem`
- `styleOverride`
- `state`
- `lock`
- `createdAt`
- `updatedAt`

Shape rules:

- Shape is a small occupied-cell mask.
- Default max shape editor size is `8x8`.
- GM can change max shape size in module configuration.
- Occupied cells must be orthogonally contiguous.
- Holes are allowed.
- Holes are usable space for other cargo.
- Rotation is allowed.
- Flipping/mirroring is not allowed in v1.

Location states:

- `floor`
- `container`
- `held`

Extraction states:

- `available`
- `extracted`
- `abandoned`
- `unresolved`

### Cargo Templates

Templates are reusable world-level cargo blueprints. Adding a template to a mission copies it into cargo instances.

Template fields:

- category defaults
- name
- subtitle
- shape
- color/icon/pattern overrides
- optional linked item
- notes

No template import/export in v1.

## Default Categories

Categories are visual/reporting metadata in v1. They do not automatically apply mechanics.

Default categories:

- Supplies: food, medicine, tools, filters, consumable expedition necessities.
- Parts: train/base components, upgrades, replacement machinery.
- Comforts: luxury or morale goods.
- Objective: mission-critical cargo.
- Intel: drives, maps, documents, samples, encrypted data.
- Loot: PF2e item rewards, including weapons, armor, equipment, treasure.
- Specimens: biological, arcane, anomalous, or contaminated samples.
- Salvage: raw scrap, trade goods, mundane valuables.
- Hazardous: unstable, cursed, radioactive, explosive, or quarantined cargo.

Each category has:

- accent color
- icon
- pattern/texture treatment

Suggested defaults:

- Supplies: green/teal, medical cross or crate, utility grid.
- Parts: amber/yellow, gear, industrial diagonal striping.
- Comforts: pink/gold, sparkle/heart/cup, refined shimmer.
- Objective: white/red, target/flag, high-priority brackets.
- Intel: blue/cyan, chip/file, circuit traces.
- Loot: violet, sword/shield/gem, inventory plate lines.
- Specimens: acid green, flask/dna, bio-cell pattern.
- Salvage: steel/orange, recycle/scrap, scratched metal lines.
- Hazardous: red/orange, warning/radiation, hazard stripes.

Custom categories support:

- name
- accent color
- icon
- generic pattern

## Visibility Model

Cargo visibility has three states:

- `revealed`: players can see full available details.
- `scanned`: players can see item type/category, level, traits, rarity, icon, and limited details.
- `unknown`: players see only safe cargo category or generic labeling.

GM can always see full cargo data.

Linked PF2e cargo defaults to `scanned`.

## PF2e Integration

The core module is system-agnostic. PF2e enhancements activate when available.

PF2e item linking supports drag-and-drop from item sheets or compendiums onto the Quick Cargo drawer or Advanced Editor.

When an item is linked, defaults derive from it:

- icon from item image
- name from item name
- category `Loot`
- subtitle from item type, level, rarity
- visibility `scanned`

Store both:

- `itemUuid`
- snapshot data: name, image, type, level, rarity, traits, and description excerpt

Use the snapshot for preview/reporting. Use the UUID to open the source item when available.

V1 does not grant items automatically on extraction.

## Permissions

GM can:

- create/edit/delete cargo
- create/edit/delete missions
- create/edit/delete containers
- manage templates
- lock/unlock the board
- toggle global player visibility
- run extraction
- undo the last GM board operation

Players can:

- open/close the Cargo Board window
- inspect cargo according to visibility
- pick up floor cargo
- place cargo into containers
- rotate cargo
- rearrange cargo in containers

Players cannot:

- create/edit/delete cargo
- edit mission setup
- edit containers
- run extraction
- see hidden GM details

## Multiplayer Synchronization

All users see shared mission cargo data.

Each user has independent view state:

- selected container
- zoom
- scroll position
- window position
- window size

Cargo interactions use per-cargo locks.

Lock rules:

- When a user starts dragging cargo, it is locked to that user.
- Others can inspect but cannot move it.
- Lock timeout is 30 seconds.
- Cancel, drop, disconnect, or timeout releases the lock.
- If a player closes/disconnects while holding floor cargo, it returns to the floor.

Pickup from floor is a soft lock. The cargo is not permanently removed from the floor until placed.

## Cargo Board UI

The Cargo Board is a dedicated resizable Foundry window.

Layout:

- Left sidebar: containers.
- Center: active container grid.
- Right panel: floor cargo manifest.

Narrow layouts may collapse the floor manifest to a bottom tray later, but desktop is the v1 target.

Players can resize the window. Position and size persist per user.

If there is no active mission:

- GM sees an empty state prompting mission creation/activation.
- Players see no button/window.

### Floating Button

Use a small floating button, not a Scene Controls button in v1.

GM always sees the button. Players see it only when global player visibility is enabled and an active mission exists.

### Floor Manifest

The floor is a loose/staged manifest, not a strict grid.

Cargo cards show:

- miniature shape
- name
- subtitle/benefit
- category visual
- priority marker
- visibility state
- lock/held status

Controls:

- search by name/subtitle
- filter by category
- filter by visibility
- sort by priority, category, name, size, newest

### Container Grid

The container grid is the packing surface.

Rules:

- Only valid drops commit.
- Invalid hover previews are allowed.
- Occupied cells collide.
- Empty holes inside cargo shapes remain usable.

Preview states:

- valid: cyan/green outline
- blocked/out of bounds: red outline
- locked/permission issue: amber outline

Coordinates are hidden by default. GM can toggle a coordinate overlay.

Grid cell sizing:

- default base cell size: 44px
- auto-scale down for large containers
- readable minimum: approximately 28px
- scroll when needed

### Cargo Tile Display

Grid cargo tiles show:

- icon or category mark
- short name if it fits
- category styling
- priority marker

Full details are available via hover/click panel and manifest card.

### Drag/Rotate Behavior

Rotation:

- `R` rotates selected/dragged cargo.
- Rotation preserves the same occupied-cell anchor where possible.
- If anchor cannot be preserved, ghost preview shifts to nearest valid anchor near cursor.
- If no valid placement exists, show blocked preview.

Keyboard:

- `R`: rotate selected/dragged cargo
- `Esc`: cancel drag or close detail panel
- `Delete`/`Backspace`: GM delete selected cargo after confirmation
- Arrow keys: nudge selected cargo one grid cell when focused
- `Enter`: confirm focused valid placement

## GM Workflows

### Quick Cargo

GM gets a compact Quick Cargo drawer in the floor manifest.

Fields:

- name
- subtitle/benefit
- category
- shape preset
- quantity
- visibility
- linked PF2e item drop target
- create button

Quantity creates separate cargo instances.

### Advanced Editor

Advanced editor supports:

- custom shape drawing
- category/style overrides
- linked item review
- extraction notes
- GM notes
- value/priority metadata

### Shape Editor

Shape editor supports:

- quick presets
- click-to-toggle cells
- drag-paint cells
- clear
- trim empty rows/columns
- rotate preview
- configurable max dimensions

Presets should include common rectangles and Tetris-like shapes.

### Mission Manager

Mission Manager supports:

- create mission
- rename mission
- delete mission
- activate mission
- duplicate mission
- configure containers
- prebuild floor cargo
- add cargo from templates
- clear extracted/abandoned cargo where appropriate
- reopen extracted mission

Mission Manager is separate from the play-focused Cargo Board.

## Extraction

Extraction is GM-only.

V1 extraction is report-first and does not automatically grant PF2e items or apply train/base boosts.

On extraction:

- placed cargo becomes `extracted`
- floor cargo becomes `abandoned`
- locked/held cargo becomes `unresolved` if it cannot be resolved automatically
- mission becomes `extracted`
- board remains visually intact
- mission becomes read-only by default

Post a polished player-facing chat report showing:

- extracted cargo
- abandoned cargo
- unresolved cargo, if any
- grouped categories
- visible benefits

GM-only details should include:

- linked item UUIDs
- hidden item details
- internal state warnings

Cargo-awarded chat announcements are optional via GM checkbox. Item inspection should not spam chat.

## Data Storage

V1 persistence uses world-scoped Foundry settings with versioned JSON.

Settings:

- `activeMissionId`
- `missions`
- `templates`
- `playerVisible`
- `config`
- `lastUndo`

Use sockets to broadcast refresh/update events.

Data should be versioned for future migration.

## Undo

V1 includes simple GM-only last-action undo for board operations such as accidental delete, extraction, or clear action.

No full multiplayer undo stack in v1.

## Visual Direction

Use a separate cargo design system with `--glucargo-*` variables.

Design language:

- dark ink base
- cyan/white/violet GLUniverse accents
- crisp clipped corners
- thin luminous borders
- frosted panels
- technical typography
- premium sci-fi control surfaces
- distinct category colors, icons, and patterns

Avoid directly depending on `gluniverse-initiative` CSS classes.

## Animation

Use one premium animation profile. No module intensity setting.

Animation should be satisfying but not imprecise:

- cargo lift with depth/shadow and holographic edge
- valid placement snap-charge with cyan scanline
- invalid placement red edge distortion/pushback
- crisp mechanical 90-degree rotation
- subtle category pattern motion on hover/selection
- staged extraction report reveal

Respect `prefers-reduced-motion` automatically.

## Accessibility

- Desktop-first for v1.
- Use keyboard controls listed above.
- Do not rely on color alone; category icon and pattern must also communicate type.
- Maintain readable minimum cell size.
- Provide visible focus states.
- Keep text compact but not clipped.

## Open Implementation Notes

- Validate shape masks before saving.
- Normalize masks by trimming empty outer rows/columns.
- Use occupied-cell coordinates for collision checks.
- Keep per-user view settings client-scoped.
- Use world data updates carefully to avoid race conditions.
- Keep mission data plain JSON so migrations are tractable.
