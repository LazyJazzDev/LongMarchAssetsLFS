# LongMarch PR #56 — demo feature captures

Captured from LongMarch commit `a23cf51` on macOS / Apple M4 / Metal.
Captured with macOS `screencapture -x -l <window-id>` from running application windows.
All PNGs retain native window chrome, title bars, and system shadows in their alpha
channels. No framebuffer exports or composited window decorations are used.

| Image | Scene / state | PNG including window/shadow | Application supersampling |
| --- | --- | --- | --- |
| `gol-sidebar.png` | 100 × 100 board, 295P5H1V1 preset, paused | 2784 × 1728 | 2× per axis |
| `gol-horizontal.png` | 100 × 25 board, Gosper glider gun preset, paused | 2784 × 1728 | 2× per axis |
| `2048-score-fit.png` | Game-over panel with injected score 2147483647 | 1664 × 2208 | 3× per axis |

The 2048 score is a synthetic layout stress input injected by a temporary runtime
probe, not a score obtained through gameplay. The screenshot uses the real
NoticeBoard layout and rendering path with the game-over transition completed.

Launch the GOL windows with these commands, then capture each window ID with
`screencapture -x -l <window-id> <output.png>` (do not use `-o`, which removes the shadow):

```sh
cmake-build-ninja/demo/gol/demo_gol 100 100 --pattern demo/gol/patterns/295P5H1V1.cells
cmake-build-ninja/demo/gol/demo_gol 100 25 --pattern demo/gol/patterns/gosper-glider-gun.cells
```

The captures show the final toolbar arrangement, file controls, paired size
sliders and score fitting. Static images do not demonstrate animation timing or
native file-dialog interactions.
