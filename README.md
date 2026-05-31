<!--
Copyright (c) 2026 Jurjen Stellingwerff
SPDX-License-Identifier: LGPL-3.0-or-later
-->

# lavition — universal editor for rich game data

A general-purpose editor for hex-grid worlds with walls, materials, items, and
heights, supporting multi-level layering, stencil-driven authoring, and detection
of round / curved structures.

## What this is

- **Universal editor** — not tied to a single game.  Plugin model so games extend
  the data types they care about.
- **Hex-based geometry** — uses hexes to unlock direction richness a square grid
  can't reach:
  - **12 directions for buildings** — each hex has 12 valid placement
    orientations (vs 4 or 8 on a square grid), so structures align with the
    world's terrain flow without forcing a single facing.
  - **24 directions for walls, cliffs, and roads** — sub-hex segments give 24
    orientations for linear features, enough to express smooth curves, diagonal
    ridges, and intersection geometry without visible stepping.
- **Curve detection** — recognises round and curved structures from chains of
  short straight segments, so authored buildings, arcs, and roads render as
  smooth shapes instead of polygonal approximations.  Applies to all
  linear-feature layers (walls, cliffs, roads, foundations).
- **Vertical layers** — basement, ground floor, second floor, roof — each is a
  distinct editing layer so buildings can be authored per-level.  Additional
  "free placement" layers exist for props that don't participate in collision,
  letting decorative items occupy space already claimed by structural features.
- **Stencils — two uses, same data:**
  - **Stamps for rapid building authoring.**  A stencil captures a complete
    building, wing, or floor pattern; placing it writes the whole shape into
    the layer in one click.  Speeds up large-scale composition vs hex-by-hex
    drawing.
  - **Item instances at a smaller render scale.**  The same stencil can be
    placed as an item (vehicle, decorative element) and rendered at a smaller
    scale, which automatically gains detail density per unit area.  Items
    carry their own (limited) animation tracks, so a stencil-as-item is more
    than a static prop.

## How it fits — the loft ecosystem

Lavition is the *editor* tier of a two-tier ecosystem:

- **[loft](https://github.com/jjstwerff/loft)** — the embedded scripting
  language + standard libraries.  A pure runtime: games depend on loft
  at runtime, not on lavition.
- **lavition** (this repo / org) — the editor product built on top of
  loft.  Used during game development; not a runtime dependency.

Games (moros, dryopea, bumper-airplanes, future indie projects)
consume loft + its libraries at runtime; they use lavition during
development.  A built game ships without lavition.

## Library architecture

Lavition reads and writes data from the loft library ecosystem,
organised by tier-of-concern across six chunks under
[loft-lang](https://github.com/loft-lang):

| Chunk | Role |
|---|---|
| `loft-libs-core` | utilities (arguments, random, crypto) |
| `loft-libs-net` | networking (web client, server, game protocol) |
| `loft-libs-graphics` | rendering — OpenGL bindings, 2D canvas, sprite, text |
| `loft-libs-assets` | file formats — PNG, glTF binary |
| `loft-libs-game` | game systems — physics, particles, input, time, audio |
| `loft-libs-world` | hex-grid world data — addressing, walls, terrain, items |

A `lavition_stack` meta-package bundles the common subset so a new
game's manifest is one line, not seven.

**Lavition consumes everything**; games consume the subset they need.
A multiplayer server skips graphics + assets entirely; a single-player
game skips net; an asset-processing tool installs only `loft-libs-assets`.

### Editor plugins

Lavition's editor framework loads plugins from this org.  Each plugin
authors ONE data type from the loft library ecosystem:

- `lavition_hex_editor` — authors hex_walls
- `lavition_terrain_paint` — authors hex_terrain
- `lavition_item_placer` — authors hex_items (stencil-as-item placement)
- `lavition_layer_panel` — manages vertical layers
- `lavition_stencil_library` — manages reusable stencils
- `lavition_glb_inspector` — imports / exports glb assets

(Plugin set evolves; this list is the initial target.)

## Status

Namespace claimed 2026-05-31.  Library design + chunk topology
captured in
[`doc/claude/LAVITION.md`](https://github.com/jjstwerff/loft/blob/main/doc/claude/LAVITION.md)
in the loft monorepo.  Engine source is on the roadmap; this repo
will gain concrete content (`lavition_core`, plugin host, editor
binary) as the underlying library chunks ship.
