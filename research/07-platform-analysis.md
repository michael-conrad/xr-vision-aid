# Platform Analysis: Q3 VisionAid Source Review & Godot 4.7.2 Comparison

Prepared by OpenCode AI assistant, 2026-09-25. All 10 Unity source files read in full (archived locally: tmp/q3vision-src/ in the session workspace); Godot claims verified against GitHub source/releases/issues via API.

## Part 1: Q3 VisionAid source review (uzeray/meta-quest-vision-aid-unity)

**Project:** Unity 6000.4.8f1, URP 17.4.0, OpenXR 1.17.0 + Meta XR SDK 201.0.0, Input System 1.19.0, IL2CPP. Scripts under Assets/Scripts/Q3Vision/ — 10 files, ~1,292 lines (1207 by direct count).

### Meta-specific API surface (astonishingly small)

| API | Where used | Notes |
|---|---|---|
| `OVRPassthroughLayer.SetBrightnessContrastSaturation(nativeBrightness, contrast, saturation)` | Q3VisionAppController | Only brightness actually adjustable — contrast/saturation hardcoded 0.0 (README/code drift) |
| `Meta.XR.PassthroughCameraAccess.GetTexture()` | CameraMagnifierView | The ONLY member used; no permission request, no IsSupported check |
| Unity Input System `Gamepad` | RemoteInputReader | No Meta XR Input SDK at all — generic Gamepad abstraction |

### Per-file breakdown

| File | Lines | Content |
|---|---|---|
| Q3VisionAppController.cs | ~306 | Orchestrator: finds OVRPassthroughLayer via FindFirstObjectByType; stick Y→zoom, X→brightness; mode switch: zoom ≥1.05 → CameraMagnifier, ≤1.05 → NativePassthrough |
| ZoomController.cs | 51–54 | 100% portable: dead-zone 0.25, additive delta, clamp 1×–8×, Reset()→1.0 |
| BrightnessController.cs | 92–97 | ~85% portable: piecewise lerp around default 50 → −0.75…+0.75 native; magnifier multiplier Lerp(1.0,1.15)/Lerp(1.0,0.85); 0.05-level debounce |
| CameraMagnifierView.cs | ~328–352 | Runtime-built WorldSpace Canvas (panel 1.20×0.90 @ z=1.35), RawImage fed cameraAccess.GetTexture(); UV crop math (~40 lines, 100% portable): aspect-fit base UV, divide by zoom, centered offset; border-aware insets (18px outer + 8px cyan inner, shown ≥1.20×) |
| ZoomHudView.cs | 120 | Camera-child TextMesh, auto-hide 3 s |
| BrightnessHudView.cs | 214–224 | WorldSpace Canvas bar-fill HUD, auto-hide 2.5 s; uses deprecated builtin Arial.ttf (latent Unity 2022.3+ bug) |
| RemoteInputReader.cs | 36 | Gamepad.current / Gamepad.all[0]; leftStick.ReadValue(); buttonEast/selectButton/startButton for reset |
| RemoteInputFrame.cs | 28 | Readonly input snapshot struct |
| VisionMode.cs | 9 | Enum: NativePassthrough, CameraMagnifier |
| PrimitiveMeshFactory.cs | 66 | URP Shader.Find("Universal Render Pipeline/Unlit") — UNUSED by the other 9 files |

**Portable core ≈ 190 lines** of 1,207. The rest is Unity plumbing (uGUI construction, TextMesh, scene wiring) that any reimplementation rewrites anyway.

### Hidden complexity (README alone would mislead a reimplementer)

1. Passthrough layer may be null → native brightness silently no-ops (HUD still shows change).
2. Camera texture lifecycle is entirely borrowed: GetTexture() re-read EVERY frame (Meta owns it; can change size between frames). Never cache.
3. Three-threshold hysteresis: panel visible ≥1.05×, border ≥1.20×, mode flip-back below 1.05.
4. One 0–100 brightness level drives two different mappings (native −0.75…+0.75 AND magnifier RawImage.color 0.85–1.15) kept in sync.
5. Debounce asymmetry: zoom HUD updates on any change; brightness requires |Δ|≥0.05.
6. Silent-failure ladder in Start(): no camera → everything dead; camera but no layer → everything except native brightness.
7. Hand-tracking: declared in project settings, ZERO code — input is 100% controller.
8. RequestedResolution 1280×1280 @ 60fps in the scene's PassthroughCameraAccess building block (v83 API); source ceiling is the camera hardware (1280×960 effective per Meta docs).

## Part 2: Godot 4.7.2-stable capability comparison

**Version assumption locked:** Godot 4.7.2-stable (2026-08-18, latest stable) + Godot OpenXR Vendors plugin 5.1.0-stable (2026-05-19, requires Godot 4.6+).

### The three pillars — verified present

1. **Passthrough + brightness/contrast:** vendors plugin `OpenXRFbPassthroughExtension.set_brightness_contrast_saturation()` — identical Meta filter API (bounds −100..+100, verified in plugin source openxr_fb_passthrough_extension.cpp:757-774). Plus reconstruction passthrough (alpha-blend), projected passthrough (`OpenXRFbPassthroughGeometry` with hole-punch), color map / mono map / color LUT / interpolated LUT filters, edge color, texture opacity. Working sample: samples/meta-passthrough-sample (main.gd = 209 lines, includes live brightness/contrast sliders).
2. **Raw camera texture:** Meta's PassthroughCameraAccess is a wrapper over Android Camera2 (plugin maintainer, issue #331). Godot 4.5+ has CameraFeed on Android via Camera2 NDK (PR godotengine/godot#106094, merged 2025-05-13, milestone 4.5); `horizonos.permission.HEADSET_CAMERA` auto-injected by vendors plugin (PR #306, merged 2025-06-10, meta_export_plugin.cpp:471) and requested by core PermissionsUtil.java on HorizonOS. Community-proven in-VR on Quest 3 at 1280×960 (oudeis.co write-up, June 2025, Godot 4.5-dev4 + vendors v4.0.0 + CameraServerExtension).
3. **Controller input:** trivial — Input.get_joy_axis / action map.

### Godot advantages (better)

- **Passthrough filters the Unity app lacks:** set_mono_map (grayscale Curve — could suppress chromatic noise stimulation for visual snow), set_color_map (Gradient), set_color_lut / set_interpolated_color_lut, set_texture_opacity_factor, set_edge_color; separate OpenXRAndroidGlobalPassthroughDimmingExtension (0.0–1.0 dimming). The Unity original has brightness only (contrast/saturation hardcoded 0).
- **Explicit camera readiness:** OpenXRAndroidPassthroughCameraStateExtension.get_passthrough_camera_state() → INITIALIZING/READY/ERROR (vendors 4.2.0+, doc merged 2026-01-19). Beats Unity's silent null-polling ladder.
- **Meta anchor API** permission added (5.1.0 #491) → world-anchored magnifier panel possible.
- Clean-room reimplementation avoids the Unity repo's licensing void (no LICENSE — all-rights-reserved; copying not clean).
- GDScript: ~400–600 lines total vs 1,207 lines C# + Unity toolchain; hot-reload on device; Project Setup Wizard auto-configures Quest export.

### Godot disadvantages (worse)

- **Camera path is CPU-copy on Vulkan:** AImageReader → memcpy Y/UV planes → call_deferred → texture_2d_update. Zero-copy GL_TEXTURE_EXTERNAL_OES path exists in GLES3 (feed.glsl USE_EXTERNAL_SAMPLER; texture_external_update → eglEGLImageTargetTexture2DOES; CameraFeed.set_external exposed) but the Vulkan implementation is an empty stub (PR #97163, draft since 2024). GLES3/Compatibility renderer is viable for a lightweight app if zero-copy matters.
- **Open CameraTexture bugs:** fails to update on StandardMaterial3D (#111023, open — use TextureRect/Sprite3D or reassign texture); no CPU get_data() (#51830); first-launch camera failure + race conditions (format-set-before-activate, small waits — per oudeis and shiena writeups); YUV stride bug fixed only in 4.6+ (PR #110720, merged 2026-01-06, milestone 4.6 → fine on 4.7.2).
- **No camera-pose/intrinsics API:** Meta PCA provides camera extrinsics; Godot gives a flat feed — hand-tuned positioning. Acceptable for head-locked/anchored reading panel; not for world-locked zoom-onto-object.
- **Version floor:** vendors 5.x requires Godot 4.6+; camera stride fix requires 4.6+ (4.7.2 clears both).

### Effort estimate

Equivalent app in Godot ≈ **400–600 lines GDScript/C#**: XROrigin3D + alpha-blend passthrough, filter singleton calls, SubViewport + zoom shader (UV-crop math ports 1:1), controller input. Reference code: meta-passthrough-sample main.gd (209 lines) + GodotCameraFeedSample camerafeed.gd (515 lines, YCbCr→RGB shader included). Days-scale, not weeks.

### Platform-ceiling note (unchanged by engine)

The 1280×960 sensor ceiling is hardware, identical in both engines. The low-zoom testability conclusion carries over.

## Part 3: XR-design critique

The Unity app is structurally a 2D HUD pattern wearing XR clothing: a head-locked screen-space panel at fixed z=1.35m with a uGUI canvas. A proper XR design:

| Concern | Unity app (as written) | Proper XR design |
|---|---|---|
| No-zoom baseline | Native passthrough layer + brightness call | Same, as alpha-blend environment + filter API |
| Zoom > 1× | Camera texture in floating panel | Camera texture in SubViewport panel with zoom shader (UV crop math ports 1:1), head- or world-anchored |
| Page positioning | Not addressed — user aims the panel | World-anchored panel (Meta anchor API) locked over the book on chest/lap |
| Readiness handling | Silent-failure ladder | Explicit camera-state extension (READY/INITIALIZING/ERROR) |
| Real-world occlusion | Panel border only | Projected passthrough geometry (hole-punch) onto a page-shaped quad world-locked over the book |
| Visual-snow filters | Brightness only | Mono map / LUT / dimming — first-class Godot APIs, absent from the Unity original |

---

🤖 Co-authored with AI: OpenCode (ollama-cloud/glm-5.3-flash)