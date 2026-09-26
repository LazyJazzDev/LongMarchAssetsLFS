# Scene update optimization

`throughput.png` plots the measured before/after primary camera throughput on
RTX 3090 Ti, Windows, D3D12/Vulkan native ray query. Each condition uses the
original scene settings, 8 samples/dispatch and frames 10-59 of a 60-frame run.

Source data and generator are in LongMarch:
- `docs/reports/nsight-scene-update-frames.csv`
- `scripts/plot_scene_update_benchmark.py`

The baseline is `ab8c5c1`; tested optimization is `082d216` (replayed onto main as
`84dbed6` without source changes). These are single-run measurements. All six
same-backend before/after output images match exactly.
