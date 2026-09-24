# LongMarch PR #56 — demo feature captures

Captured from LongMarch commit `a23cf51` on macOS / Apple M4 / Metal.
These are application-rendered screenshots, not interface mockups.

| Image | Scene / state | Output | Supersampling |
| --- | --- | --- | --- |
| `gol-sidebar.png` | 100 × 100 board, 295P5H1V1 preset, paused | 2560 × 1440 | 2× per axis |
| `gol-horizontal.png` | 100 × 25 board, Gosper glider gun preset, paused | 2560 × 1440 | 2× per axis |
| `2048-score-fit.png` | Game-over panel with injected score 2147483647 | 1440 × 1920 | 3× per axis |

The 2048 score is a synthetic layout stress input injected by a temporary runtime
probe, not a score obtained through gameplay. The screenshot uses the real
NoticeBoard layout and rendering path with the game-over transition completed.

GOL reproduction commands (from the main repository):

```sh
cmake-build-ninja/demo/gol/demo_gol 100 100 --pattern demo/gol/patterns/295P5H1V1.cells --frames 3 --screenshot assets/reports/pr56-demo-features/gol-sidebar.png
cmake-build-ninja/demo/gol/demo_gol 100 25 --pattern demo/gol/patterns/gosper-glider-gun.cells --frames 3 --screenshot assets/reports/pr56-demo-features/gol-horizontal.png
```

The captures show the final toolbar arrangement, file controls, paired size
sliders and score fitting. Static images do not demonstrate animation timing or
native file-dialog interactions.
