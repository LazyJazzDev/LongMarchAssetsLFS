# Game of Life desktop updates

Actual macOS window captures of `demo_gol` from Ninja Release builds on Apple M5,
using Metal on a 60 Hz display, captured with `screencapture -l` including the
native title bar, rounded corners and system shadow. No window content was
cropped or composited.

| File | Build | Scenario |
| --- | --- | --- |
| `layout-landscape.png` | `feat/gol-orientation-layout` | 1100x700 window, 96x64 grid, `--random 0.3`: sidebars on the short edges |
| `layout-portrait.png` | `feat/gol-orientation-layout` | 700x900 window, same grid: randomize/reset on top, dense controls in the bottom panel |
| `lightning-main-200.png` | `main` (2bdc305) | 1100x700 window, 200x200 grid, `--random 0.3 --play`, lightning mode, 59 FPS at 27% CPU |
| `lightning-cell-grid-256.png` | `perf/gol-cell-grid` | Same window, 256x256 grid, lightning mode, 60 FPS at 5% CPU |

CPU is process CPU time over wall time during 8 title FPS samples taken 1.1 s
apart after a 5 s warmup. FPS is vsync-bound in both lightning captures.
