# Sparkium JSON scenes

Each scene lives in its own directory and uses `scene.json` as its entry point. This directory contains static versions
of the former `sparks_*` demos and imported Blender benchmark scenes. Every mesh and texture referenced by a converted
scene is copied into that scene's directory, so a directory can be moved or archived on its own.

The format identifier is `sparkium-scene` and the current version is `1`. A scene has these top-level sections:

- `film`: positive `width` and `height`, accumulation controls, and optional `view_transform`, `exposure`, `gamma`,
  and `contrast` display settings.
- `renderer`: `pipeline` (`auto`, `rasterization`, or `ray_tracing`), `samples_per_dispatch`, `max_bounces`,
  `alpha_shadow`, raster `ambient_light`, and ray-traced `background_color`.
- `camera`: `eye`, `target`, `up`, vertical `fov_degrees`, and optional thin-lens `aperture_radius`,
  `focus_distance`, `aperture_blades`, `aperture_rotation`, and `aperture_ratio`.
- `materials`: named `lambertian`, `specular`, `light`, `principled`, or runtime-compiled `shader_graph` materials.
- `geometries`: named OBJ `mesh`, compact `binary_mesh`, strand-based `hair`, generated `sphere`, or `inline_mesh`
  geometry. Binary meshes use the little-endian `SPKMESH1` layout emitted by
  `scripts/blender_to_sparkium.py`.
- `entities`: instances that refer to a named geometry and material, or standalone `point_light` entries.

All asset paths are resolved against the directory containing the JSON document. This applies to simple child paths
such as `textures/base.png`, parent-relative paths such as `../shared/model.obj`, and absolute paths. Paths stored in
the converted scenes only point inside their own scene directory.

Mesh entities accept either a conventional transform:

```json
{
  "type": "mesh",
  "geometry": "ball",
  "material": "metal",
  "transform": {
    "translation": [0, 1, 0],
    "rotation_degrees": [0, 45, 0],
    "scale": [1, 1, 1]
  }
}
```

For importers that must preserve a complete affine transform, `transform.matrix` accepts 16 column-major values.

or a camera-like `look_at` transform, which is convenient for rectangular area lights:

```json
{
  "type": "mesh",
  "geometry": "quad",
  "material": "light",
  "look_at": {
    "position": [0, 5, 0],
    "target": [0, 0, 0],
    "up": [0, 0, 1],
    "scale": [0.5, 0.5, 0.5]
  }
}
```

`principled` supports scalar/color properties with the same names as `sparkium::MaterialPrincipled`. Its `textures`
object accepts `normal`, `base_color`, `metallic`, `specular`, `roughness`, `anisotropic`, and
`anisotropic_rotation` and `emission`, plus the `normal_reverse_y` flag.

`shader_graph` stores a directed acyclic value graph and a `surface` map. When the scene loads, Sparkium emits HLSL
for each graph and uses the existing runtime shader compiler to create that material's closest-hit programs. Graph
inputs can be constants or `{ "node": "node_id", "output": "color" }` references. Version 1 supports value/RGB,
texture coordinates, vertex attributes, object info, image textures, mapping, noise, Voronoi, wave, gradient, brick
and sky textures, mix and math operations, invert, color ramps, RGB curves, hue/saturation, gamma, brightness/contrast,
layer weight, normal maps, bump pass-through, combine/separate, and reroutes. The graph's surface can drive base color,
metallic, specular, roughness, anisotropy, sheen, clearcoat, IOR, transmission, emission, and normal inputs of the
ray-traced Principled BSDF, plus opacity for transparent path continuation and alpha shadows. Blender Transparent,
Mix Shader, and Add Shader closures are converted into parameter graphs over this surface representation.
The optional `shadow_opacity` surface input overrides `opacity` only for shadow rays. This lets an emissive portal
remain visible and illuminate the scene while allowing a separate directional light to cast shadows through it.
Image nodes may specify `color_space` as `srgb` or `linear`; the Blender converter preserves the source image's
color/data role so base-color and emission textures are decoded while normal, roughness, and metallic maps remain linear.
Rendering uses linear Rec.709 primaries, matching Blender's default `scene_linear` role. `Film::Develop` supports
channel-clipped `standard`, a Filmic shoulder/toe approximation, and the legacy hue-preserving `normalized` display
transform. The Blender converter also preserves exposure, gamma, and High Contrast intent.

`hair` stores curve strands separately from triangle meshes and can be bound to its own material like any other
geometry. Its `path` points to a little-endian `SPKHAIR1` file containing strand offsets, positions, and per-point
radii; `radial_segments` controls the tapered tube fallback used by the current mesh-based render backends.

`point_light` accepts `position`, linear `color`, radiant `strength`, and optional `radius`. `sampling_weight` can
override the light-selection importance without changing its physical intensity, which is useful for distant-point
approximations of directional lights. When `soft_falloff` is
true, the finite source uses Blender/Cycles' shading-point-oriented disk model; otherwise it is sampled as a sphere.

Run a scene without a window:

```bash
./build-release/demo/sparkium_cli/demo_sparkium_cli assets/scenes/texture/scene.json \
  --frames 32 --output texture.png
```

Browse every scene below a directory:

```bash
./build-release/demo/sparkium_gui/demo_sparkium_gui assets/scenes
```

See `scene.schema.json` for the machine-readable structure. Version 1 intentionally describes static scenes only.
