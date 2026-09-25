# Snow × Noise Interaction Model

Prepared by OpenCode AI assistant, 2026-09-25.

## The corrected model

The earlier framing — "cameras compound visual snow, full stop" — was refined during the session. The accurate principle:

**What matters is not "camera vs no camera" — it is whether magnification adds grain in lockstep with the letters.**

- Visual snow is a fixed-grain overlay: dynamic dots at roughly constant *angular* size across the visual field (IOVS quantification study, iovs.arvojournals.org/article.aspx?articleid=2793698).
- Sensor noise (thermal/photon grain) is a fixed-grain overlay at the sensor's pixel scale.
- Enlarging the text shifts the *ratio*: letter strokes become coarser, higher-contrast, and wider than the grain — less letter-edge contamination per glyph, easier temporal/spatial integration over the static, fewer saccades, lower spatial-frequency content. All reduce the effort of reading *through* snow.
- Evidence: enlarging text is a standard evidence-backed VSS accommodation (Visual Snow Initiative: classroom/workplace accommodations — "enlarging text" among the meaningful adjustments); low-vision literature treats print size + display size as first-order variables for fluent reading (NCBI PMC9357187, digital reading with low vision).

## The three magnification sources

| Path | Letters vs grain | Verdict |
|---|---|---|
| Optical magnifier (MaxDetail, dome) | Letters scale up; NO sensor grain introduced — only intrinsic snow | Pure win — best ratio |
| Native VR rendering (e-book page, QuestLens zoom) | Letters scale up as pure pixels; zero added grain | Pure win |
| Digital camera zoom (Q3 VisionAid, 1280×960 source) | Letters scale up but sensor grain scales identically — 8× zoom shows 16–64 screen-pixel blobs per sensor pixel; noise is baked into the letters | Ratio may not improve; at high zoom the grain becomes the same size as letter strokes — arguably worse than small clean text |

## Implications

1. **Low-zoom passthrough is testable, not dismissed:** at 1.5–2×, enlarged letters may out-compete the enlarged grain (Quest 3 passthrough is decent quality at 1×). Directly falsifiable in minutes with a sideloaded app.
2. **High zoom is noise-bound:** at 8× on a 1280×960 source, each camera pixel becomes a blob; the letters enlarge but resolution does not — enlarged mush.
3. **Grayscale/LUT passthrough filters may matter:** chromatic noise suppression via Godot's set_mono_map / set_color_lut (absent from the Unity original) is a design lever specific to snow.
4. **The sensor ceiling is the hard limit:** 1280×960 raw passthrough texture (Meta PCA; 1280×1280 in v83 covers more vertical FOV but still less than the visible passthrough — Meta developer docs). At 2× zoom ≈ 640×480 effective page view: plausibly readable for larger print. This pairs acceptably with the 2.1×-class magnification the vergence math actually needs.

## Entoptic / static facts (context)

- VSS is a cortical hyperresponsivity / network disorder (Aeschlimann, Klein & Schankin, Current Opinion in Neurology); dots affect the whole visual field, often worse on plain backgrounds (MDPI J Clin Med 12(12):3868).
- ICD-11 recognized 2025 (Annual Reviews, visual perception in VSS).

---

🤖 Co-authored with AI: OpenCode (ollama-cloud/glm-5.3-flash)