# Game of Life and 2048 demos

Window captures of `demo_gol` and `demo_2048` from the macOS build (Metal,
Apple M5), taken with the system screenshot tool, so each image keeps the
window frame and its shadow. All four are the whole window: the demos were run
at their default size and nothing was cropped.

| File | Scenario |
| --- | --- |
| `gol-sidebar.png` | 40x30 grid seeded with `--random 0.3`; the panel sits on the left, with refresh and the randomize die above the speed and play controls |
| `gol-bottom-bar.png` | 80x14 grid seeded with `--random 0.35`; the panel moves to the bottom bar, play and speed on the left, the die and refresh on the right |
| `2048-board.png` | A fresh game: the board, the 2048 logo, and the score board with **SCORE** and **MENU** |
| `2048-autoplay.png` | The autoplay running, started by five consecutive clicks on the score board: the title reads **AI**, the board is red, and the strategy is playing |

The die randomizes every cell independently at 50% live/dead, keeps the
play/pause state, and animates a tumble that lands on a randomly chosen face.
The 2048 autoplay answers with a move of its own and ignores the arrow keys
until the score board is clicked once more.
