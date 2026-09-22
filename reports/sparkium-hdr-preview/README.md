# Sparkium HDR preview

Actual GUI capture on Metal with native Ray Query, Cornell Box, 960x720 film,
1 sample per frame, 757 accumulated spp at capture, 32 bounces, exposure 0 EV,
sample clamp 100 and max exposure 100. The scene is a temporary copy of the
bundled Cornell Box with the stated film and sampling overrides.

![HDR preview controls](cornell-hdr-gui.png)

The screenshot documents the HDR toggle, render controls and opaque panel.
It is an SDR screenshot, not a measurement of physical HDR luminance. Display
EDR headroom limits visible brightness. The bundled Cornell Box defaults to
max exposure 1, which clips accumulated highlights upstream; raise this setting
to at least 30 to preserve its emitter brightness.

A separate 192x192, 64 spp numerical check on Metal found identical results for
native Ray Query and software path tracing: max exposure 1 gave raw/HDR peaks
of 1 with no pixels above 1; max exposure 100 gave peaks of 30 and 230 pixels
above 1 out of 36864. This validates floating-point output, not display luminance.
