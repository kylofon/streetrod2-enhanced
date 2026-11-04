# Changelog

## v0.1.0 — unreleased

First release of Street Rod 2 Enhanced, built on the faithful Street Rod 2 SDL3 port v0.1.0 (b79b6e9). The game's
rules, timing and numbers are unchanged; every enhancement is its own option and `--classic` turns all of them off.

### Picture per race kind
- New renderer for the races at 1280x800 (`--res-scale`, default 4) with 2x2 anti-aliasing (`--aa`, default 2),
  colours through the game's live palette.
- Smooth motion: snapshots of the game's frames, interpolated at the display rate (`--motion-delay`, default 110 ms).
- Town cruise and street drag race: road, kerbs, walls, sky (turns at 1/8 speed when steering), road-side objects
  (trees, lamp posts, signs, hydrants, billboards, banners) at continuous size with no size steps, trees behind
  buildings, the street ahead ending in a house front, police cars.
- Drag strip (Grudge Night): flat road, barrier walls, start tree with its lamps, starter.
- Mulholland Drive: hillsides, drop-offs, banks, guard rails, truss bridges, bushes and rocks, own sky strip.
- Aqueduct (night race): channel, joint lines, wall openings, centre trough, boxes, pillars, floor shapes, finish
  band, smooth roll up the walls (the opponent tilts with it), night sky.
- Opponent, police car, start tree and starter drawn natively from the game's own pictures.
- `--enh-modes LIST` chooses which race kinds are drawn enhanced.
- Past a finish line: plain road (or plain channel) with no objects; after a race stops the picture stays enhanced.

### Draw distance and haze
- `--draw-distance N`: 60 road segments by default (the original: 14; range 14..120), in every race kind; picture
  only. `--draw-distance-scale X` sets the same as a factor.
- `--haze N` (default 30): the far road and scenery fade into the sky colour; 0 = off.
- Far objects fade in at the far end; the opponent stays visible far ahead.
- Mulholland: the far road ends at the foot of the mountains instead of covering them.

### Mirror
- Enhanced rear-view mirror in every race kind: road, walls, hillsides, rails, bridges, trees, banners, the channel,
  opponent or police car, starter.
- Mirror distance: min(N/2, 30) segments behind (30 at the default 60, 7 at `--draw-distance 14`); the car behind
  stays visible further back.

### Presentation
- `--aspect W:H|window` (default 4:3): shape of the picture area (16:9, 16:10, ...; `window` fills the window). It
  stretches the picture; menus, garage and the mouse keep the game's 320x200 coordinates.
- `--integer-scale`: whole-number scaling with black bars.

### Input
- `--key-shift-up KEY`, `--key-shift-down KEY`: choose the race shift keys (default A / Z).
- `--no-gear-keys`: no shift keys in races (menus unchanged).
- Gamepad extras: right / left shoulder shift up / down, right / left trigger gas / brake, Start = Esc;
  `--no-pad-extras` turns them off.
- `--classic` ignores all of these (A / Z still shift, as in the faithful port).

### Tools
- `tools/classic_check.py`: checks `--classic` against the faithful release frame for frame.
- `SR_ENH_TRACE` (snapshot and render-time trace) and `SR_ENH_COMPARE` (deterministic clock, compare tool,
  `tools/enh_compare.py`).

### Planned
- Launcher with a "Picture" group (S3.2).
