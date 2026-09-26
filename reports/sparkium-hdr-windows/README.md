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
