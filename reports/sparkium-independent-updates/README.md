# Independent Sparkium updates extracted from PR46

Source: LazyJazzDev/LongMarch `9099bdaf8982e428e4a4ba7461c4083a6eeeacfe`. Input scenes: `109bf9681cc2bd14af17b96a616da0dcdf511544`.
Windows, RTX 3090 Ti, Ninja Release, MSVC. This build requires no `_USE_MATH_DEFINES` override.

![AUTO and explicit Ray Query comparison](comparison.png)

Cornell Box, **128 x 128, 64 spp, 8 bounces**, one dispatch per fresh CLI process.
Rows: D3D12 and Vulkan. Columns: AUTO and explicit Ray Query. Images are enlarged for the montage.
Each API produces pixel-identical AUTO and explicit Ray Query outputs. CSV profiling confirms `native_ray_query=1`
for all four renders. This is a small regression fixture, not a full-resolution Blender benchmark or a CPU/CUDA test.
Cross-API pixel equality is not asserted.

The existing fallback suite plus the unsupported-API regression passes **17/17 on D3D12 and 17/17 on Vulkan**.
Tests exercise automatic selection, accumulation/reset, explicit pipeline RT selection, BVH traversal,
transparent shadows and shader compilation. Metal is not tested on this Windows machine.

The three Blender scene names parse as Blender Monster, Blender Classroom and Blender Junkshop.
Only scene names and their READMEs differ in the selected input-assets update; render parameters are unchanged.
VS Code settings/tasks parse as JSON and agree on Ninja, the build directory and task dependencies.
Main-repository pre-commit checks passed.

See [results.json](results.json) for counts, settings, source identity and executable hashes.
Original PNGs: [D3D12 AUTO](d3d12-auto.png), [D3D12 Ray Query](d3d12-ray_query.png),
[Vulkan AUTO](vulkan-auto.png), [Vulkan Ray Query](vulkan-ray_query.png).
