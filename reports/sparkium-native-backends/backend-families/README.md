# Sparkium rendering backend families

Source: `5a567ebb4cb9d5cbf7efef30f6378ae4d40008e6`.
Baseline: `15bc473fa555732f79fd51f9e8967a4f1da68810`.

![Graphics backend with a separate graphics API selector](gui-backend-families.png)

Actual GUI capture: Cornell Box at 256 × 256, 16 samples per dispatch,
245008 accumulated spp at capture. The window displays an enlarged preview.
Rendering uses Graphics / D3D12 Ray Query; presentation uses a separate D3D12
device. The Render backend selector offers Graphics, CPU and CUDA; Graphics
exposes a separate Graphics API selector. This progressive GUI screenshot is
not a benchmark image or a fixed-sample comparison.

## Migration regression

The before/after images below use the same complete Cornell scene at **64 × 64,
16 spp, 8 bounces**. One dispatch per fresh process, Windows Ninja Release,
RTX 3090 Ti, CUDA 12.9, OptiX 9.0, Slang 2026.7.1. All five paths have identical
PNG pixels and zero linear PFM RMSE across the migration; every linear image is
finite. Raw PFM files remain in the local test record. This checks this fixture,
not full-resolution Blender accuracy or performance.

| Path | Before | After | PNG RMSE | Linear RMSE |
|---|---|---|---:|---:|
| CPU software | [PNG](cpu-before.png) | [PNG](cpu-after.png) | 0 | 0 |
| CUDA software | [PNG](cuda-before.png) | [PNG](cuda-after.png) | 0 | 0 |
| CUDA OptiX | [PNG](optix-before.png) | [PNG](optix-after.png) | 0 | 0 |
| D3D12 Ray Query | [PNG](d3d12-before.png) | [PNG](d3d12-after.png) | 0 | 0 |
| Vulkan Ray Query | [PNG](vulkan-before.png) | [PNG](vulkan-after.png) | 0 | 0 |

[Scene settings](scene.json), [metrics and executable hashes](results.json),
[test counts](tests.json), [GUI provenance](provenance.json).

Regression suites: **69 passed, one CPU-specific test skipped in the CUDA suite**.
Includes native device boundaries, CPU with subprocess creation prohibited,
CUDA/OptiX, GUI family/API switching, CPU BVH, thread pool, and 16 graphics tests
each on D3D12 and Vulkan. Compiler syntax checks also cover CPU-only,
CUDA without OptiX, and graphics-only factory configurations. Platform coverage
is Windows; Metal execution and other operating systems were not tested here.
