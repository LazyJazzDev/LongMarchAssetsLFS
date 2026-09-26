# HDR UI vertex precision regression

The plot uses float vertex upload values recorded by
HDRImGuiUploadTest.PreservesDarkColorsAlphaAndOriginalVertices in the GTest XML.
The previous path is calculated by rounding those values to 8-bit linear RGB.
This is a numeric test plot, not a screen capture or physical brightness measurement.
The CSV preserves all 256 input gray levels; the plot focuses on levels 0 through 32.

On Windows RTX 3090 Ti / driver 596.49, D3D12 and Vulkan GPU readback additionally
verify an sRGB 6/255 UI rectangle with reference-white alignment on and off.
Tests also cover repeated HDR/SDR switching, alpha/source preservation, resize,
and Vulkan recovery after actually retiring the old swapchain and injecting failure.
16 tests pass; 2 SDR-only compatibility cases skip on this HDR desktop.
Metal adapter generation is checked on Windows; native Metal runtime is not tested.
