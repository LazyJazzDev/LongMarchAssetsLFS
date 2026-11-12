# 2048 and Game of Life RHI migration

Captured on Meshy-PC-3090 (RTX 3090, NVIDIA 615.71.10), Linux Vulkan/X11,
Release/Ninja, using a dedicated 1920x1080 Xvfb display. Presentation is enabled
for these input replays. The PNGs are application readbacks of resolved frames,
not desktop/window captures. Baseline: LongMarch e233d265; candidate:
LongMarch branch migrate-demos-rhi. Both builds use seed 1 for 2048.

- `2048-moves-*`: 720x960, 3x supersampling per axis, Left/Down/Right/Up.
- `gol-controls-*`: 1280x720, 3x supersampling per axis, 32x32 grid; play/pause
  a stable block, edit a cell, zoom twice and right-drag the grid.
- `gol-resize-rhi.png`: 720x960 after slider input, portrait resize, unmap/map.

Every baseline/RHI pair has zero differing RGBA pixels. The matching baseline
for the portrait capture and all other numerical comparisons are recorded in
LongMarch's docs/demo-rhi-migration.md and its benchmark JSON report.
