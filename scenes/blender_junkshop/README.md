# Extended

Converted from Blender's official `junkshop.blend` benchmark scene with [`scripts/blender_to_sparkium.py`](../../../scripts/blender_to_sparkium.py). [Upstream benchmark](https://projects.blender.org/blender/blender-benchmarks/src/branch/main/cycles/junkshop). The directory is self-contained; see `conversion-report.json` for conversion statistics and approximations.

The static conversion includes all 17 active lights and the character's two particle-hair systems. The 3216 strands are
stored as two `SPKHAIR1` geometries and both instances use the scene's separate `Hair` material.
