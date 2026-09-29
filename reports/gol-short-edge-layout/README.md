# Game of Life controls on the short window edges

Actual macOS window captures of `demo_gol` from Ninja Release builds on Apple M5,
using Metal, captured with `screencapture -l` including the native title bar,
rounded corners and system shadow. Each grid is seeded with `--random 0.3`.

| File | Build | Window | Grid | Controls |
| --- | --- | --- | --- | --- |
| `main-wide-landscape.png` | `main` (2bdc305) | 1100x700 | 200x40 | Top and bottom panels, on the long edges |
| `branch-wide-landscape.png` | `feat/gol-orientation-layout` | 1100x700 | 200x40 | Sidebars, on the short edges |
| `main-tall-portrait.png` | `main` (2bdc305) | 700x900 | 40x200 | Sidebars, on the long edges |
| `branch-tall-portrait.png` | `feat/gol-orientation-layout` | 700x900 | 40x200 | Reset and randomize on top, dense controls in the bottom panel |
| `branch-wide-portrait.png` | `feat/gol-orientation-layout` | 700x900 | 200x40 | Same as above: the grid's aspect does not move the controls |
