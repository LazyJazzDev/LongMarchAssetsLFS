# Explicit shader contract without source rewriting

Source: `eee51b08aa242f864e7de11ebd4a8e8aeca3a073`. Baseline: `81b6b449df41313728d32d265b631056beda58f3`.

![Before and after removing shader compatibility rewriting](comparison.png)

Complete Cornell Box fixture: **64 x 64, 16 spp, 8 bounces**, one dispatch per
fresh process, Windows Ninja Release, RTX 3090 Ti, CUDA 12.9, OptiX 9.0 and
Slang 2026.7.1. Each of the five backends has pixel-identical output before and
after the refactor (PNG RMSE and maximum difference zero). All new linear PFM
outputs are finite. This compares each backend against itself, not against the
other backends. It is a fixture regression, not full-resolution Blender testing
or a new performance benchmark. Raw PFM outputs and logs remain in `out/`.

## What changed

The CPU and CUDA regex compatibility implementations are deleted, including
source lowering, template adaptation, entry/resource regex renaming, cbuffer
expansion and CPU module-global resource exports. CPU dispatch always supplies
an explicit context to ordinary JIT functions. CUDA/OptiX compiles explicit
resource declarations directly through Slang and NVRTC.

All compute inputs require compute_contract.hlsli in the VFS and use its resource
and invocation macros. Missing contracts are rejected with invalid_argument;
there is no implicit rewrite fallback. The low-level resource-array, constant
buffer, dispatch-ID, texture/sampler and OptiX tests now use this same contract.
Graphics shader compilation is unchanged. Source generation remains limited to
the resource prelude and ordinary CPU invocation/ABI wrappers; no input-source
regex pass remains.

## Validation

- **84 tests passed; one CPU-only oracle test skipped in the CUDA suite.**
- CPU and CUDA both explicitly reject raw inputs missing the compute contract.
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
  compatibility removal (CPU/CUDA rerun after test-source migration), with executable hashes recorded in `tests.json`.

Metal and other operating systems were not executed on this Windows machine.

[Scene](scene.json), [image metrics](results.json), [test counts and executable hashes](tests.json),
[CLI cases](cli-cases.json), [provenance](provenance.json).
