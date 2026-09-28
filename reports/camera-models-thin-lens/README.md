# Pinhole, thin lens and shadow-terminator geometry offset

Generated from LongMarch `1d8cece` on macOS 26.6.2, Apple M5, Metal native Ray Query.
These images are actual headless render outputs, **not application-window screenshots**.
The system window-capture API did not capture the demo window in this session.

All images: 1100 × 700, 64 frames × 8 samples = 512 samples/pixel, four bounces,
standard film view transform, the same procedural scene and camera transform.
The three colored spheres use `Sphere(32, 16)` and have no decorative surface dots.
The remaining background emitters demonstrate aperture bokeh.

- `thin-lens.png`: `CameraThinLens`, focus distance 5.4, aperture radius 0.22,
  six aperture blades, zero rotation, aspect ratio 1; geometry offset 0.1.
- `pinhole.png`: `CameraPinhole`; geometry offset 0.1.
- `pinhole-offset-off.png`: `CameraPinhole`; geometry offset disabled (0).

The geometry-offset comparison isolates that setting; the thin-lens versus
pinhole comparison switches the actual camera model. Geometry offset changes
shadow visibility near grazing angles, not mesh silhouettes or indirect path
origins. This demo uses a broad key light, so terminator changes are subtle;
these images do not establish correctness for every mesh or lighting setup.

```sh
cmake-build-release/demo/thin_lens/demo_thin_lens --backend metal --headless \
  --frames 64 --geometry-offset 0.1 --output thin-lens.png
cmake-build-release/demo/thin_lens/demo_thin_lens --backend metal --headless \
  --frames 64 --pinhole --geometry-offset 0.1 --output pinhole.png
cmake-build-release/demo/thin_lens/demo_thin_lens --backend metal --headless \
  --frames 64 --pinhole --geometry-offset 0 --output pinhole-offset-off.png
```

![Thin lens](thin-lens.png)
![Pinhole, geometry offset 0.1](pinhole.png)
![Pinhole, geometry offset disabled](pinhole-offset-off.png)
