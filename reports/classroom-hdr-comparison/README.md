# Blender Classroom: SDR and HDR from one 512 spp render

| SDR — Standard/sRGB PNG | HDR — BT.2020/PQ AVIF |
| --- | --- |
| ![SDR Classroom](classroom-sdr.png) | ![HDR Classroom](classroom-hdr.avif) |

Open the original [HDR AVIF](classroom-hdr.avif) in an HDR-capable browser and
on an HDR-enabled display. GitHub/proxies or SDR displays may tone-map or fail
to preview AVIF; an ordinary screenshot cannot demonstrate physical brightness.
The [linear RGBE source](classroom-linear.hdr) is also included.

Both images come from the same accumulated film: Metal native Ray Query on
Apple M5, 1280x720, **512 spp** (64 dispatches x 8 samples), 16 bounces, alpha
shadows enabled, persistence 1, sample clamp 100, max exposure 16, exposure 0 EV.
These are the bundled Classroom settings except film resolution (originally
1920x1080) and explicit selection of the Metal ray-query pipeline. No denoising.
Camera and framing are unchanged. PNG uses the scene's Standard view transform;
HDR bypasses that SDR transform. RGBE has shared-exponent quantization.

HDR AVIF uses 10-bit YUV 4:2:0, BT.2020 primaries, ST 2084 (PQ), BT.2020
non-constant-luminance matrix and limited range. Linear 1 maps to 203 cd/m².
Linear sRGB is converted to linear BT.2020 before PQ encoding. No extra exposure
or artistic tone mapping is applied. AVIF encoding is lossy (SVT-AV1 CRF 10).

The decoded RGBE maximum channel is 13.625; 104761/921600 pixels have a channel
above 1. This is real extended-range image data. No pixels exceed PQ's 10000
cd/m² bound. These are numerical image values, not measured display luminance.

## Reproduce

Create a temporary copy of `scenes/blender_classroom/scene.json`, resolve asset
paths relative to the original file, and set film width/height to 1280/720.
Keep samples_per_dispatch=8 and the remaining settings as above. Then run:

```sh
demo_sparkium_cli classroom-512.json --backend metal --pipeline ray_query \
  --frames 64 -o classroom-sdr.png --hdr-output classroom-linear.hdr
python3 scripts/hdr_to_avif.py classroom-linear.hdr classroom-hdr.avif
```

The conversion helper is in the LongMarch repository and requires NumPy and
FFmpeg with libsvtav1. It verifies output color signaling. Its regression checks
verify PQ reference values and recover known luminance patches from encoded AVIF.
