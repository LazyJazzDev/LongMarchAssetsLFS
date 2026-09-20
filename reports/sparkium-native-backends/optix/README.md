# OptiX integration with the isolated GUI

Source: LongMarch `542f5abab4dd1a6d517805a38ac9ac233908f811`. Windows Ninja Release, RTX 3090 Ti,
CUDA 12.9, official OptiX 9.0 SDK, vcpkg Slang 2026.7.1.

## Cornell Box backend comparison

`cornell-optix.png` uses CUDA hardware traversal through OptiX;
`cornell-cuda-software.png` uses CUDA software BVH traversal.
Both are actual CLI outputs using the complete Cornell Box scene, 256 x 256
pixels, 64 spp (4 dispatches x 16 samples), and 16 maximum bounces.
Geometry, materials, camera and film settings are identical.
The native scene resolution is 1024 x 1024; this is a reduced-resolution
integration check, not a full-resolution or Blender/Cycles comparison.
Every hardware frame recorded `optix_hardware_traversal=1`.

Normalized RGB PNG RMSE: 0.0000125076; maximum absolute difference:
0.0039215686 (one 8-bit code value). These are display-space metrics,
not linear radiance metrics. No performance conclusion is drawn.

| CUDA OptiX | CUDA software BVH |
|---|---|
| ![OptiX](cornell-optix.png) | ![Software BVH](cornell-cuda-software.png) |

## Independent GUI devices

![Separate rendering and display controls](gui-separated-rendering.png)

Actual GUI capture from the same build and 256 x 256 Cornell fixture.
The screenshot shows D3D12 rendering through Ray Query and a separate D3D12
display device, 16 samples per dispatch and 2976 accumulated spp; it is not
part of the 64-spp image comparison. The Render backend and Display backend
readouts document the retained API separation. The GUI was also launched
with CUDA rendering and D3D12 display. Automated worker tests exercise
CUDA/OptiX -> CPU -> CUDA/OptiX switching, transfer to an independent display
device, and OptiX/software pipeline switching with accumulation reset.

Validation: 10 CPU tests with child-process creation prohibited, 9 CUDA tests,
1 OptiX hardware test, 5 GUI worker tests, 3 CPU BVH tests and 6 thread-pool
tests passed. The CPU-specific BVH oracle is skipped on CUDA.
See `validation.json` for machine-readable settings and results.
