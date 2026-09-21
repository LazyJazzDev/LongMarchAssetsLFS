# Persistent Renderer and replaceable Backend

Source: `f5d25b0f86ac9c1e8ce9d675e4d0f4c64d8aaa0a`. Baseline: `5cf275f89faf6ae2d464ae60952a31d0d725a17d`.

![Before and after the Renderer / Backend refactor](comparison.png)

Complete Cornell Box fixture: **64 x 64, 16 spp, 8 bounces**, one dispatch per
fresh process, Windows Ninja Release, RTX 3090 Ti, CUDA 12.9, OptiX 9.0 and
Slang 2026.7.1. Each of the five backends has pixel-identical output before and
after the refactor (PNG RMSE and maximum difference zero). All new linear PFM
outputs are finite. This compares each backend against itself, not against the
other backends. It is a fixture regression, not full-resolution Blender testing
or a new performance benchmark. Raw PFM outputs and logs remain in `out/`.

## What changed

`Renderer` is the long-lived coordinator owning an immutable `SceneDefinition`,
render settings and a replaceable `Backend`. `SetBackend` destroys execution state
and rebuilds from the same in-memory scene, with no scene-file reads. GUI uses
that same Renderer across backend switches. Backend/API/pipeline choices are no
longer part of Scene; `LoadSceneDocument` retains file preferences separately.

Graphics, CPU and CUDA each own geometry, texture, material and entity objects,
report pipeline capabilities and dispatch their supported pipelines. Raster code
lives under Graphics; shared path-tracing algorithms live under backend/common;
CPU BVH construction lives under CPU. There is no top-level pipelines directory.
Internal shared shader/resource bindings remain in use. CPU textures borrow
immutable source pixels and CPU BVHs read host meshes directly.

## Validation

- **82 tests passed; one CPU-only oracle test skipped in the CUDA suite.**
- A loader-only executable does not link the Graphics library or renderer.
- A scene containing an external OBJ and a textured material graph is loaded
  once, then the JSON, OBJ and texture files are deleted before rendering on
  D3D12, Vulkan, CPU, CUDA software tracing and OptiX.
- One persistent Renderer switches CPU -> D3D12 -> CUDA -> Vulkan -> CPU after
  deleting scene sources. Releasing Backend retains scene identity and settings.
  Failed switches retain Scene and recover; explicit unsupported pipelines reject.
- Independent concurrent CPU renderers share a scene while keeping accumulation
  separate. Borrowed texture storage retains ownership and rejects writes.
- GUI backend switches continue after source deletion; explicit reload detects
  the missing file and succeeds once it is restored.
- **31 CLI cases passed:** six basic scenes rendered at 96 x 96 through D3D12
  rasterization from a different working directory; 25 malformed inputs rejected.
- CPU and Vulkan CLI profiling exercised, including the retained `frame_wall`
  field. New CLI frame timings include host readback; historical performance
  tables retain their original source and timing definition.
- Loader/renderer/GUI tests and all five CLI comparisons were rerun after final
  backend capability cleanup. Earlier unaffected suites retain their
  recorded executable hashes in `tests.json`.

Metal and other operating systems were not executed on this Windows machine.

[Scene](scene.json), [image metrics](results.json), [test counts and executable hashes](tests.json),
[CLI cases](cli-cases.json), [provenance](provenance.json).
