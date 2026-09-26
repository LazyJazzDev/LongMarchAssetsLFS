# Windows HDR preview validation

Actual Windows GUI window captures on an NVIDIA RTX 3090 Ti (driver 596.49).
Both use the Cornell Box at 768 x 768, Auto resolved to Path Tracing - Ray Query,
1 sample per frame, 8 maximum bounces, exposure 0 EV and maximum exposure 100.
The temporary capture scene changes only these settings from the bundled Cornell Box.

- `d3d12-hdr.png`: D3D12, 7135 accumulated spp at capture.
- `vulkan-hdr.png`: Vulkan, 1517 accumulated spp at capture.

These captures show the enabled HDR preview control, linear HDR display mode and
resolved Auto pipeline. They are not matched-sample image comparisons, performance
benchmarks or evidence of physical HDR luminance: PNG screenshots are SDR captures
of an HDR-capable presentation path.

HDR/SDR switching with and without ImGui passed on both backends with debugging
enabled, including Vulkan synchronization validation. Surface-format selection and
HDR film development tests also passed (4 tests total).

## Automatic reference-white alignment

The newer captures `d3d12-reference-white.png` (3116 spp) and
`vulkan-reference-white.png` (3134 spp) show the window querying the Windows SDR
reference white as 480 nits and applying a 6x scale to the scRGB presentation.
Scene/settings are the same as above. UI colors are decoded to linear before
composition; the completed floating-point scene/UI image is scaled once.

The earlier `*-hdr.png` captures predate reference-white alignment. These are
not controlled exposure or matched-sample comparisons. Windows PrintWindow PNGs
can clip/tone-map HDR content and do not establish physical monitor luminance.
The numbers in the UI come from the OS configuration, not a light meter.

Seven HDR tests pass with Vulkan synchronization validation, including GPU
readback of scene and UI gray values, source/alpha preservation, window resize,
repeated SDR/HDR switching, reference-white changes, and unknown-value fallback.

## Windows HDR headroom

`d3d12-headroom.png` shows the final minimal API/UI at code commit `8a29413`:
only the computed HDR headroom is exposed, with DXGI peak luminance kept internal.
D3D12, Cornell Box, 768x768, Auto -> Ray Query, 1 sample/frame, 8 bounces,
0 EV, max exposure 100; captured at 228 accumulated spp using PrintWindow.
The UI reports SDR reference white 280 nits (3.50x) and HDR headroom 3.62x.
Windows estimates headroom as max(1, DXGI reported peak / system SDR white).
This is not a real-time measurement or a physical brightness comparison.
The updated GUI/test targets build successfully; all 8 HDR tests pass on Windows,
including D3D12 and Vulkan, with Vulkan synchronization validation enabled.
