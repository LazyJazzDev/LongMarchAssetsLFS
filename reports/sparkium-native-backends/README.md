# Sparkium native backend visual report

These are actual renderer outputs supporting LongMarch PR #46, not illustrative reconstructions.

![CPU correction and CUDA reference](render-correctness.png)

Columns: CPU before the finite-check fix, corrected CPU, CUDA reference.
Rows: Blender Monster, Classroom and Junkshop. Long edge 256, original aspect
ratio, 64 spp, complete geometry/materials, and original bounce limits (32 for
Monster, 16 for Classroom/Junkshop). The remaining noise is visible at this
sample count; the comparison establishes backend agreement, not equivalence to
Cycles or a full-resolution validation.

| Scene | CPU/CUDA PNG RMSE before | After |
|---|---:|---:|
| Monster | 0.534599 | 0.002702 |
| Classroom | 0.569426 | 0.003144 |
| Junkshop | 0.695012 | 0.003603 |

The threshold is unchanged at 0.01. Images come from the scalar `AllFinite`
repair; the tested equivalent source revision is `0d76f2b`. The subsequent PR
split/rebase was checked for source equivalence. CPU before images are retained
from the failing run; CUDA reference images use the same scene inputs.

## Feature fixtures

| Fixture (64 x 64, 32 spp) | CPU | CUDA |
|---|---|---|
| Material graph | ![CPU graph](graph_smoke-cpu.png) | ![CUDA graph](graph_smoke-cuda.png) |
| Camera and film | ![CPU camera/film](camera_film-cpu.png) | ![CUDA camera/film](camera_film-cuda.png) |

`validation-results.json` includes metrics, dimensions, sample counts, input
hashes, and the scope of validation. Blender source scene attribution is retained
in the respective [scene folders](../../scenes/).
