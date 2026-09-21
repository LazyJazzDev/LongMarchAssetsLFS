# Independent backend resources and compilation

Source: `81b6b449df41313728d32d265b631056beda58f3`. Baseline: `4e50b0af4196d92603317742378c451a8e27eec1`.

![Before and after removing backend/common](comparison.png)

Complete Cornell Box fixture: **64 x 64, 16 spp, 8 bounces**, one dispatch per
fresh process, Windows Ninja Release, RTX 3090 Ti, CUDA 12.9, OptiX 9.0 and
Slang 2026.7.1. Each of the five backends has pixel-identical output before and
after the refactor (PNG RMSE and maximum difference zero). All new linear PFM
outputs are finite. This compares each backend against itself, not against the
other backends. It is a fixture regression, not full-resolution Blender testing
or a new performance benchmark. Raw PFM outputs and logs remain in `out/`.

## What changed

Graphics, CPU and CUDA each own their material code generator, shader graph
compiler and packed trace layout. The backend/common directory is removed.
CPU and CUDA own distinct buffer, image, memory, sampler, program, command context,
binding and shader types; no resource constructor selects the backend with a
boolean flag. CPU directly retains immutable scene texture pixels and uses
ordinary LLVM JIT functions plus its thread pool. CUDA owns device allocations,
Slang-to-CUDA/NVRTC compilation and optional OptiX launch state. CPU has no GPU
module, OptiX launch or hardware acceleration-structure binding storage.

Project-defined native naming in Sparkium implementation, shader ABI, build
options and test targets is replaced with CPU/CUDA/compute/hardware terminology.
The common HLSL algorithms remain under shaders, using compute_contract.hlsli.
Renderer keeps the same in-memory Scene across backend replacement.

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
- All nine suites and all five image comparisons were exercised after the
  resource/compiler split, with executable hashes recorded in `tests.json`.

Metal and other operating systems were not executed on this Windows machine.

[Scene](scene.json), [image metrics](results.json), [test counts and executable hashes](tests.json),
[CLI cases](cli-cases.json), [provenance](provenance.json).
