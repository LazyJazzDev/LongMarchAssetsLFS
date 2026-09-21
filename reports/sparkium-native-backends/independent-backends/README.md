# Independent backend execution

Source: `4e50b0af4196d92603317742378c451a8e27eec1`. Baseline: `f5d25b0f86ac9c1e8ce9d675e4d0f4c64d8aaa0a`.

![Before and after independent backend execution](comparison.png)

Complete Cornell Box fixture: **64 x 64, 16 spp, 8 bounces**, one dispatch per
fresh process, Windows Ninja Release, RTX 3090 Ti, CUDA 12.9, OptiX 9.0 and
Slang 2026.7.1. Each of the five backends has pixel-identical output before and
after the refactor (PNG RMSE and maximum difference zero). All new linear PFM
outputs are finite. This compares each backend against itself, not against the
other backends. It is a fixture regression, not full-resolution Blender testing
or a new performance benchmark. Raw PFM outputs and logs remain in `out/`.

## What changed

Each Graphics, CPU and CUDA backend directly implements the Backend contract and
owns its scene translator, resource factories, accumulation/readback lifecycle,
path-tracing scene and pipeline controller. ExecutionBackend, SceneObjectSet and
ComputeDevice are removed. CPU has no Ray Query/OptiX/graphics-RT state; Graphics
has no CPU BVH or OptiX branch; CUDA owns software traversal and OptiX selection.

The common directory retains reusable resource/binding ABI helpers, embedded
compiler support, pure material shader text generation and the packed shader
instance layout. It owns no scene controller or backend lifecycle, and contains
no backend-enum dispatch. Shared HLSL algorithms remain reused. Renderer still
retains the same scene across backend replacements without reopening files.

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
- Nine suites were exercised. CPU/CUDA suites and all five image comparisons
  were verified after the final legacy dispatch guard fix. Unaffected suites
  retain their recorded executable hashes in `tests.json`.

Metal and other operating systems were not executed on this Windows machine.

[Scene](scene.json), [image metrics](results.json), [test counts and executable hashes](tests.json),
[CLI cases](cli-cases.json), [provenance](provenance.json).
