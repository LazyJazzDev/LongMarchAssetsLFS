# Sparkium realtime GI prototype

Apple M5, Metal, software BVH traversal (no hardware ray queries).

- `classroom-realtime.png`: Blender Classroom, 1920x1080 output, shading divisor
  4 (480x270 lighting grid), 3 surface interactions, update period 16, history 16,
  after 320 lighting frames. These are not path-tracer spp. SDR development.
  The final shader-layout refactor reproduced the earlier image byte for byte.
- `classroom-gui.png`: GUI snapshot with history 64 and otherwise the same
  settings, at lighting frame 363; displayed 42.0 FPS / 23.79 ms. This is a point
  observation, not a percentile or minimum-frame-rate benchmark.

The prototype reconstructs direct and indirect lighting at low resolution;
coarse textures, blotchy noise, and temporal convergence remain visible.
