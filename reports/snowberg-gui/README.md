# Snowberg GUI migration

System window captures from macOS on Apple M5, using Metal. Captured with
`screencapture -l`; original window frame, title bar and system shadow are retained.

- `sparkium-window.png`: Sparkium Scene Browser, `area_light`, 1024 × 1024 film,
  SDR, automatic pipeline, 8 samples per dispatch. Accumulated sample count at
  capture was not recorded. This illustrates the controls, not a rendering
  quality or performance comparison.
- `gui-window.png`: Snowberg GUI demo with a screen panel, custom particles and
  a perspective world panel. The original ImGui demo is now `demo_gui`.

2048 and Game of Life preserve their original visuals and interactions while
sharing the GUI surface renderer, input and animation infrastructure.
