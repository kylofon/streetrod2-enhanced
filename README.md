# Street Rod 2 Enhanced — SDL3

An enhanced native C/SDL3 port of California Dreams' *Street Rod 2: The Next Generation* (1991). It is built on the
faithful [SDL3 port](https://github.com/kylofon/street-rod-2-sdl3) (v0.1.0, b79b6e9), which runs the game's own code
reimplemented in C. Not an emulator. This project keeps that engine and its game rules unchanged and adds a new
picture for the races (high resolution, anti-aliasing, smooth motion between the game's frames, a longer draw
distance), presentation options and input options. `--classic` turns every enhancement off.

**You need your own copy of the original game.** No game files are included.

Status and plan: [PLAN.md](PLAN.md) (as a page: `python tools/plan_site.py`, then open `site/index.html`).
Changes: [CHANGELOG.md](CHANGELOG.md). Design: [ENHANCED.md](ENHANCED.md). Engine rules: [ENGINE.md](ENGINE.md).
Agent rules: [CLAUDE.md](CLAUDE.md).

## What is enhanced

* All five race kinds are drawn by a new renderer at 1280x800 with 2x2 anti-aliasing: the town cruise and the street
  drag race, the drag strip (Grudge Night), Mulholland Drive and the Aqueduct (night race). Road, walls, hillsides,
  rails, bridges, road-side objects, opponent, police, start tree, starter, sky and the rear-view mirror.
* Smooth motion: the picture is interpolated between the game's frames at the display rate.
* Draw distance 60 road segments (the original: 14) with haze into the sky colour; the mirror reaches up to 30
  segments behind.
* Picture shape (`--aspect`) and integer scaling.
* Bindable shift keys and gamepad extras (shoulders, triggers, Start).
* Menus, the garage and the other screens keep the original picture, scaled.

The simulation is the faithful port's: same races, prices, timing and results.

## Game files

Copy the installed game into a folder named `Game` next to the program, or pass `--game-dir DIR`. The port reads:

    SR.EXE  LIB1  LIB2  LIB3  SR2_DATA  SR2_SHP  SR2_END  MSR.SND  *.MUS

Saved games and the hall of fame are written to that folder, so it must be writable.

## Build (Windows, MSYS2 MinGW 64-bit)

Install [MSYS2](https://www.msys2.org/) and, in its MinGW 64-bit shell, the toolchain, CMake, Ninja and SDL3
(`mingw-w64-x86_64-gcc`, `mingw-w64-x86_64-cmake`, `mingw-w64-x86_64-ninja`, `mingw-w64-x86_64-sdl3`). With
`C:\msys64\mingw64\bin` first on `PATH`:

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=C:/msys64/mingw64/bin/gcc.exe
```

```bash
cmake --build build
```

The result is `build\sr2-enhanced.exe`; the build copies the DLLs it needs next to it.

## Run

```bash
build\sr2-enhanced.exe --game-dir Game v adlib
```

`v` (VGA) and `adlib` (or `speaker`) skip the game's text menus; without them the original menus appear. Check the
game files without opening a window:

```bash
build\sr2-enhanced.exe --check
```

A launcher (game folder, sound, switches, window and a "Picture" group) is planned (S3.2); until then use the
command line.

## Command-line options

Port options:

| Option | Default | What it does |
|---|---|---|
| `--game-dir DIR` | `Game` | folder with the original game files |
| `--scale N` | 3 | initial window size: 320x240 times N (wider with `--aspect`) |
| `--fullscreen` | off | start in full screen (Alt+Enter switches) |
| `--check` | - | load and verify `SR.EXE`, print a summary and exit |
| `--run-ms N` | - | developer aid: exit after N ms (headless runs) |

Enhanced options (all on by default; ignored with `--classic`):

| Option | Default | What it does |
|---|---|---|
| `--classic` | off | every enhancement off: the faithful port's picture and behaviour |
| `--res-scale N` | 4 | render size 320·N x 200·N (1..8; 4 = 1280x800) |
| `--aa N` | 2 | anti-aliasing samples per axis (1..4; lowered so that `res-scale` x `aa` ≤ 16) |
| `--motion-delay MS` | 110 | the picture runs this far behind the game to interpolate between frames (0..1000; 0 = latest frame, no interpolation) |
| `--enh-modes LIST` | `all` | race kinds drawn enhanced, comma-separated: `town`, `drag`, `strip`, `mulholland`, `aqueduct`, `all`, `none`; the others stay original |
| `--draw-distance N` | 60 | road segments drawn ahead (14..120; 14 = the original); picture only |
| `--draw-distance-scale X` | - | the same as `--draw-distance 14X` (1..8.57; used by the compare tools) |
| `--haze N` | 30 | percent of the sky colour at the far end (0..100; 0 = off) |
| `--aspect W:H\|window` | `4:3` | shape of the picture area, e.g. `16:9`, `16:10`; `window` fills the window. Stretches the picture; menus and mouse keep working |
| `--integer-scale` | off | whole-number scaling of the picture with black bars (not with `--aspect window`) |
| `--key-shift-up KEY` | `A` | key that shifts up in races (an SDL key name, e.g. `Q`) |
| `--key-shift-down KEY` | `Z` | key that shifts down in races |
| `--no-gear-keys` | off | no shift keys (A / Z, or the keys above); menus unchanged |
| `--no-pad-extras` | off | gamepad shoulders, triggers and Start do nothing |

The original's own switches are passed on unchanged: `1`, `nomouse`, `demo`, `nouemem`, and `e` `c` `v` `h` `t`
(video card; only VGA is ported). `adlib` or `speaker` chooses the sound card and skips the sound menu.

Environment variables for development: `SR_ENH_TRACE=file` (snapshot and render-time trace, `-` = stderr) and
`SR_ENH_COMPARE` (deterministic clock and the compare tool, see `ENHANCED.md` §5).

### `--classic`

`--classic` turns every enhancement off: the picture, the presentation options and the input options. The game then
behaves exactly as the faithful release; `python tools/classic_check.py` checks this frame for frame against the
faithful build. A / Z still shift in `--classic`, because the faithful port already has them.

## Keys and gamepad

| Key | What it does |
|---|---|
| Mouse, or arrows / keypad | point and click |
| Space, Ins, keypad 0 | click |
| Up, Down | gas, brake (races) |
| Left, Right | steer (races) |
| Space, Ins | shift: down when braking, else up (races) |
| A / Z | shift up / down (races; `--key-shift-up/-down`, `--no-gear-keys`) |
| Esc | skip the drive through town; end the demo |
| M | music on / off |
| Ctrl | sound on / off |
| Alt+Enter | full screen / window |

Gamepad: stick or D-pad steers, gas and brake; A = click / shift. Enhanced extras (off with `--no-pad-extras`):
right shoulder = shift up, left shoulder = shift down, right trigger = gas, left trigger = brake, Start = Esc.

## Layout

* `src/` — the faithful engine (`ENGINE.md`; changes marked `ENH:`).
* `src/enhanced/` — the enhancements.
* `docs/PORTING.md` — the faithful port's porting rules.
* `tools/` — build, packaging and check scripts.

## License

MIT ([LICENSE](LICENSE)). *Street Rod 2: The Next Generation* is © California Dreams; its files are not included.
