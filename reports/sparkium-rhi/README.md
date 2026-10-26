# Sparkium RHI migration renders

Actual CLI output from LongMarch commit `8cf235bd`, Windows, NVIDIA GeForce RTX 3090, Release.
Scene: `scenes/texture/scene.json` (Sparkium Textured PBR), 2048 x 1024.
Both backends used the default Auto pipeline, resolved to native Ray Query.
Two frames, 16 samples per frame (32 accumulated samples per pixel), max 32 bounces.
PNG files show the SDR developed image; they are low-sample smoke-test renders, not converged references or performance comparisons.

- `d3d12-ray-query.png`: D3D12.
- `vulkan-ray-query.png`: Vulkan.

Command (replace BACKEND with d3d12 or vulkan):

```text
demo_sparkium_cli.exe assets/scenes/texture/scene.json --backend BACKEND --frames 2 --profile timings.csv -o result.png --hdr-output result.hdr
```
