# Game of Life and 2048 demos

Actual macOS window captures from the Ninja Release build on Apple M5, using
Metal. Captured with `screencapture -l` at each game's default window size,
including the native title bar, rounded window corners, and system shadow.
No window content was cropped or composited. Window titles put the active
backend first: `[Metal] Game of Life FPS: …` and `[Metal] 2048 FPS: …`.

| File | Scenario |
| --- | --- |
| `gol-sidebar.png` | Paused 40x30 grid seeded with `--random 0.3`, vertical W/H sliders, red reset and blue randomize buttons |
| `gol-bottom-bar.png` | 80x14 grid seeded with `--random 0.35`, briefly simulated then paused, horizontal W/H sliders and lightning speed selected |
| `2048-board.png` | Manual arrow-key play with merged tiles and the score board |
| `2048-menu.png` | MENU offers KEEP GOING and NEW GAME |
| `2048-autoplay.png` | AI autoplay activated by five consecutive SCORE clicks, with its red score board and AI label |

Game of Life supports independent dimensions from 2 to 200, automatic fitting,
two-finger scrolling and macOS pinch zoom. The controls adapt between a sidebar
and a bottom bar. Lightning mode runs generations without an artificial wait.

2048 uses the same move and spawn rules for manual play and the expectimax AI.
One more SCORE click returns control to the player.
