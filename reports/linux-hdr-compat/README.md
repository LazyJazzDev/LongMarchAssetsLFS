# Linux HDR compatibility validation

`x11-sdr-fallback.png` is an actual X11 client-window capture made on
2026-09-26 with ImageMagick `import -window`, without image editing.

- LongMarch code: `9ea197b7`, Ninja Release, optional Wayland feature enabled.
- Runtime: `LONGMARCH_WINDOW_SYSTEM=x11`, Vulkan, GNOME 50.1 XWayland,
  NVIDIA 595.91.07, RTX 3090 Ti.
- Scene: bundled Cornell Box, temporary JSON copy set to 768x768,
  1 sample/frame, 8 bounces, max exposure 100; Auto selects Ray Query.
- Capture: 3652 accumulated spp, 0 EV, actual framebuffer 768x768.
- Launch requested `--hdr`; the X11 surface exposed no supported HDR format.
  The GUI explains the fallback and continues displaying SDR.

This screenshot demonstrates X11 compatibility and unsupported-HDR fallback.
It does not demonstrate native Wayland PQ output or measure physical luminance.
Native Wayland HDR is covered separately by the GPU/protocol tests described in
LongMarch's `docs/linux-hdr.md` and the Linux compatibility PR.
