# Game of Life and 2048 demos

Actual window captures of `demo_gol` and `demo_2048` (LongMarch Release build,
NVIDIA GeForce RTX 3090 Ti, Windows 11), saved with each demo's
`--screenshot` option. Input was synthesized with Win32 mouse and keyboard
events on the running windows. The window sizes below are framebuffer sizes;
all frames are rendered at 3x supersampling and resolved to the window.

| File | Backend | Size | Scenario |
| --- | --- | --- | --- |
| `gol-random-vulkan.png` | Vulkan | 1280x720 | `--random 0.3`, 40x30 grid, paused |
| `gol-blinker-vulkan.png` | Vulkan | 1280x720 | Three cells clicked, play pressed, one step later |
| `gol-portrait-d3d12.png` | D3D12 | 684x861 | Window resized to portrait; panel moves to the bottom |
| `2048-moves-vulkan.png` | Vulkan | 720x930 | Ten arrow-key moves; merges and score 28 |
| `2048-menu-fade-d3d12.png` | D3D12 | 720x930 | Two moves, then MENU; overlay mid-fade |
| `2048-wide-vulkan.png` | Vulkan | 1084x661 | Window resized to landscape |
| `gol-randomize-metal.png` | Metal | 2560x1440 | `--random 0.5`, 40x30 grid, sidebar layout, randomize die visible |
| `gol-randomize-bottom-metal.png` | Metal | 2560x1440 | 80x14 grid, bottom bar layout with the randomize die |
| `gol-randomize-icon-metal.png` | Metal | 600x540 crop | Close-up of the randomize die button |

The same 60-frame `demo_gol --random 0.3` frame captured on Vulkan and D3D12
differs by at most 1/255 per channel.

The three `gol-randomize-*-metal.png` captures come from a later Apple M5 /
macOS build and show the randomize button added on top of the Windows
captures above; those Windows frames therefore predate the button. The board
was seeded with `--random 0.5`, which produces the same 50% live/dead
distribution one click of the die applies to every cell. The die button
randomizes all cells, keeps the play/pause state, and animates a tumble that
lands on a randomly chosen face.
