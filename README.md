# Lost Media Emulator (Local + Static Hosting)

A lightweight browser tool that loads an image, previews an animated lost-media simulation pipeline (including CRT display emulation), and exports an MP4 clip.

## Open locally

### Option A: open directly with `file://`
1. Double-click `index.html`.
2. Upload an image.
3. Tune controls and click **Export MP4**.

### Option B: static server (recommended)
```bash
python -m http.server 8080
```
Then open `http://localhost:8080`.

## Browser requirements

- Chromium-based browser recommended (Chrome/Edge 116+ preferred).
- Requires **WebCodecs** (`VideoEncoder`) for MP4 export.
- Requires network access the first time to fetch `mp4-muxer` from jsDelivr CDN.

## Known limitations

- `file://` mode can be stricter depending on browser security policies. If export fails in `file://`, use a local HTTP server.
- H.264 profile/codec support varies by OS/browser build.
- Large resolutions + long durations are CPU-intensive and may freeze the tab while encoding.


## Included presets

The preset list now includes a concrete “lost media” matrix that maps directly to this tool’s effect stack
(core CRT controls + advanced analog instability controls + pixelation controls).

- **Late-80s Home VHS**: consumer tape softness, head switching noise, and mild tracking drift.
- **90s Rental Tape (3rd Gen Dub)**: heavy generation loss, dropouts, and stronger chroma smear.
- **Hi8 Vacation Cam**: cleaner than VHS with gentle wobble and handheld-era tape character.
- **MiniDV Family Cam (2002)**: sharper digital-era baseline with interlace and mild artifacting.
- **Off-Air Analog Broadcast**: over-the-air jitter, cross-color artifacts, and RF-like instability.
- **Public Access Archive**: noisy mixed signal path with ghosting and interlace shimmer.
- **Early Web Rip (2006)**: macroblock-like chunkiness and compressed internet-era softness.
- **Security Camera Dump**: low-detail, high-noise surveillance aesthetic.
- **Bootleg Concert Cam**: extreme low-light bloom, clipping feel, and unstable tape behavior.
- **Damaged Archive Recovery**: severe dropout events and restoration-adjacent temporal damage.

These presets are intentionally non-destructive starting points; tweak from each baseline to dial in subtle
or extreme authenticity per shot.


## New lost-media controls

In addition to the original CRT + analog controls, the advanced panel now includes dedicated knobs for:

- **Frame stutter/drop** (temporal cadence instability)
- **RF interference bands** (horizontal signal bursts)
- **Exposure pumping** (auto-exposure breathing)
- **White balance drift** (warm/cool cast wandering over time)
- **Focus breathing** (periodic softness pulses)
- **Tape crease events** (localized horizontal warp/chew style damage)
- **Timestamp OSD** (camcorder-style date/time burn-in)
- **OSD style** (switches between camcorder/VCR/CCTV-like text treatments)
- **CCTV monochrome** (surveillance-style grayscale with slight green cast)
- **Quantization/crush** (reduced tonal levels for low-bitrate or low-quality capture feel)
- **Generation loss** (copy-of-copy dub degradation passes)

These are all deterministic per frame, so preview and export stay visually aligned.

## CRT tuning tips

- **Consumer TV look**: increase barrel distortion, bloom, chromatic aberration, and moderate scanlines.
- **PVM/BVM look**: reduce barrel distortion and bloom, increase phosphor mask clarity, keep flicker/noise low.
- For subtle realism, keep noise under `0.2` and flicker under `0.15`.

## Suggested mask additions for higher preset accuracy

The current mask set is strong for CRT/film structure, but the modern camera presets (doorbell, bodycam, action cam, covert cams) can be pushed further with sensor-era masks.

### Priority masks to add

- **`instantDyeCloud` (Polaroid / instant film):** irregular dye-cloud microtexture, edge pooling, and center-to-corner density shift.
- **`irBloomSpeckle` (night vision / IR security):** hot-center IR bloom, low-frequency sensor grain clumping, and slight fixed-pattern shimmer.
- **`cmosRollingColumn` (bodycam / spycam / doorbell):** faint column fixed-pattern noise + row/column response mismatch to mimic small CMOS sensors.
- **`lowBitrateBlockGrid` (spycam / Ring / bodycam cloud transcode):** soft 8x8/16x16 luma-chroma block boundaries that fluctuate with motion.
- **`fisheyeMicrolens` (GoPro / wide doorbell):** radial corner shading and periphery micro-contrast drop from wide lens + microlens angle falloff.

### Preset-specific recommendations

- **Polaroid SX-70 Instant:** use `instantDyeCloud` as default mask type; keep film grain/halation but add subtle uneven dye distribution.
- **Night Vision Camcorder:** use `irBloomSpeckle`; pair with monochrome mode to avoid CRT-style triad artifacts.
- **Police Bodycam 2016:** use `cmosRollingColumn`; combine with current timestamp + compression settings for better wearable-camera realism.
- **Covert Spycam Button Lens:** stack `cmosRollingColumn` with `lowBitrateBlockGrid` for tiny-sensor + cheap-encoder look.
- **Ring Doorbell Daytime:** use `fisheyeMicrolens` with mild `lowBitrateBlockGrid` to mimic app-stream transcode.
- **Ring Doorbell Night IR:** use `irBloomSpeckle` plus stronger `lowBitrateBlockGrid` for IR-lit + cloud-compressed output.
- **GoPro Hero3 Action Cam:** use `fisheyeMicrolens` with light `cmosRollingColumn` to capture early action-cam edge behavior.

### Broad accuracy gains across the entire preset library

- Keep **CRT masks** (`phosphor`, `aperture`, `slot`, `dot`) for display-emulation presets.
- Keep **film masks** (`filmSuper8`, `film16mm`) for analog stock presets.
- Route **digital capture presets** to sensor/compression masks above by default.

This keeps mask semantics aligned to image formation source (display phosphor vs. film grain vs. CMOS sensor/compression), which improves authenticity before any secondary tuning.

## Effect pass order

1. Geometry warp (barrel distortion)
2. Shadow mask and scanlines
3. Bloom/glow
4. Temporal flicker and deterministic noise

Export and preview both use deterministic frame timing (`frameIndex / fps`) so visual timing remains consistent.

## Windows desktop app (Electron)

This project now includes a native desktop wrapper and packaging pipeline for Windows.

### Desktop development run

```bash
npm install
npm run dev
```

### Build distributable `.exe`

```bash
npm install
npm run dist:win
```

Build outputs are written into `dist/` (NSIS installer + portable `.exe`).

## Performance upgrades included

- Added a WebGL2 GPU renderer path (`CRTRendererGPU`) and auto-selection at boot.
- Electron is launched with GPU rasterization and hardware acceleration flags enabled.
- Preview canvas contexts use low-latency (`desynchronized`) 2D context where supported.
- Existing CPU renderer remains as a fallback for compatibility and stability.

## GPU acceleration scope and limitations

- **GPU accelerated:** real-time preview pipeline when WebGL2 is available.
- **Fallback path:** Canvas2D CPU renderer is still used if WebGL2 is unavailable.
- **Export:** export flow still relies on browser media APIs (`WebCodecs` / `MediaRecorder`) and remains partly CPU-bound depending on codec + driver support.
- Hardware encode availability varies by Windows GPU driver and codec support in the embedded Chromium runtime.


### Build a runnable Windows `.exe` folder (recommended on Windows 10)

```bash
npm install
npm run package:win-exe
```

This generates a Windows app folder under `dist/win-unpacked/` that contains
`Lost Media Emulator PC Edition.exe` plus required runtime files.

### Build installer/portable artifacts

```bash
npm install
npm run dist:win
```

This builds NSIS installer + portable artifacts. On Linux/macOS hosts, NSIS finalization may require Wine.

## GPU acceleration: what is fully GPU vs CPU-bound

### Fully GPU-accelerated in this codebase
- Real-time preview effect shader pass in `CRTRendererGPU` (WebGL2 fragment shader).
- Canvas compositing path when the renderer backend is set to **GPU** or **Auto** with WebGL2 available.

### CPU-bound (by browser/runtime APIs)
- Source decode, file I/O, and upload orchestration.
- MP4/WebM muxing and part of encode control flow (uses `WebCodecs`/`MediaRecorder`).
- Some export-path overhead, progress bookkeeping, and frame scheduling.

### Practical recommendation
- Use **GPU backend** for interactive tuning/playback responsiveness.
- Keep CPU fallback available for compatibility or strict effect parity checks.
- Expect export speed to depend on driver/hardware codec availability in Chromium/Electron, not only shader speed.
