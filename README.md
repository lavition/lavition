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

## Relationship to other repos

- [`jjstwerff/loft`](https://github.com/jjstwerff/loft) — the embedded scripting
  language lavition uses for plugin code + project automation.
- [`loft-lang/loft-libs-world`](https://github.com/loft-lang/loft-libs-graphics)
  (planned) — the `hex_world` + `hex_walls` data primitives (libraries
  lavition edits and games consume at runtime).
- **Consumers:** moros, dryopea, bumper-airplanes and any future hex-grid
  game that imports the same data libraries.

## Status

Namespace claimed 2026-05-31.  Engine source is on the roadmap (see
`lib_plans/24-universal-editor/` in the loft monorepo); this repo will
gain concrete content as the design lands.
