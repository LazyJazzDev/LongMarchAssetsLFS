# Independent in-memory scenes

Source: `5cf275f89faf6ae2d464ae60952a31d0d725a17d`. Baseline: `5a567ebb4cb9d5cbf7efef30f6378ae4d40008e6`.

![Before and after the scene API refactor](comparison.png)

Complete Cornell Box fixture: **64 x 64, 16 spp, 8 bounces**, one dispatch per
fresh process, Windows Ninja Release, RTX 3090 Ti, CUDA 12.9, OptiX 9.0 and
Slang 2026.7.1. Each of the five backends has pixel-identical output before and
after the refactor (PNG RMSE and maximum difference zero). All new linear PFM
outputs are finite. This compares each backend against itself, not against the
other backends. It is a fixture regression, not full-resolution Blender testing
or a new performance benchmark. Raw PFM outputs and logs remain in `out/`.

## What changed

`LoadScene(path)` eagerly resolves JSON, meshes, hair and texture pixels into a
backend-independent `SceneDefinition`. Its lifetime is independent of files and
renderers. `Renderer::SetScene` accepts that same snapshot on Graphics, CPU and
CUDA/OptiX. GUI switches keep the loaded snapshot. CLI and direct library users
use the same API. CPU textures borrow immutable scene pixels; CPU BVH construction
reads host meshes directly. Packed shader geometry remains derived backend data.

## Validation

- **79 tests passed; one CPU-only oracle test skipped in the CUDA suite.**
- A loader-only executable does not link the Graphics library or renderer.
- A scene containing an external OBJ and a textured material graph is loaded
  once, then the JSON, OBJ and texture files are deleted before rendering on
  D3D12, Vulkan, CPU, CUDA software tracing and OptiX.
- Independent concurrent CPU renderers share a scene while keeping accumulation
  separate. Borrowed texture storage retains ownership and rejects writes.
- GUI backend switches continue after source deletion; explicit reload detects
  the missing file and succeeds once it is restored.
- **31 CLI cases passed:** six basic scenes rendered at 96 x 96 through D3D12
  rasterization from a different working directory; 25 malformed inputs rejected.
- CPU and Vulkan CLI profiling exercised, including the retained `frame_wall`
  field. New CLI frame timings include host readback; historical performance
  tables retain their original source and timing definition.
- Loader/renderer tests and all five CLI comparisons were rerun after final
  profiling and test-fixture cleanup. Earlier unaffected suites retain their
  recorded executable hashes in `tests.json`.

Metal and other operating systems were not executed on this Windows machine.

[Scene](scene.json), [image metrics](results.json), [test counts and executable hashes](tests.json),
[CLI cases](cli-cases.json), [provenance](provenance.json).
