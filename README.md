# XR Vision Aid — Research Notes

Design-spec and research repository for an XR low-vision magnifier aid.

**Working context:** convergence insufficiency (CI, working diagnosis — plausibly unmasked/worsened by prior LASIK) + visual snow syndrome. Sustained near reading strains and induces drowsiness; monitor viewing at 2–3 ft is comfortable; hand-held print is not. The eventual device class (Quest-class headset vs other XR runtimes vs non-headset aid) is a **research output, not a settled assumption** — no platform is baked into this repository's scope.

## Document map

| Document | Contents |
|---|---|
| [research/01-case-background.md](research/01-case-background.md) | Symptoms, constraint set, analysis trail (what was ruled out and why) |
| [research/02-vergence-mechanics.md](research/02-vergence-mechanics.md) | Working-distance vs vergence-demand math; the 4–5 PD comfort zone |
| [research/03-device-survey.md](research/03-device-survey.md) | Optical devices evaluated (telescopes, domes, CCTV class) with verified prices/links |
| [research/04-clinical-path.md](research/04-clinical-path.md) | CI evaluation, CITT evidence, vision therapy, prism caution |
| [research/05-vr-vergence-training.md](research/05-vr-vergence-training.md) | Home exercises + VR therapy evidence (BMC 2022 RCT), store apps |
| [research/06-quest-magnifier-apps.md](research/06-quest-magnifier-apps.md) | Sideloadable Quest vision-aid apps, install paths, verified store standing |
| [research/07-platform-analysis.md](research/07-platform-analysis.md) | Q3 VisionAid source review; Unity vs Godot 4.7.2 capability comparison; XR-design critique |
| [research/08-snow-noise-model.md](research/08-snow-noise-model.md) | Visual snow × sensor-noise interaction model; the magnification-source principle |

## Key session facts (verified 2026-09-25)

- Sustained near reading → strain → drowsiness. Monitors at 2–3 ft with ~1.0–1.25 D readers: comfortable. Quest 3: no correction needed, no strain.
- Vergence demand gradient: 25 cm ≈ 12 PD → 29–40 cm ≈ 7.5–10 PD → 60–90 cm ≈ 3.5–5 PD. Interventions should land demand in the 4–5 PD zone (or train vergence).
- Cameras (CCTV, passthrough) introduce sensor grain; visual snow makes grain a first-class design variable — but magnification *source* matters: optical/rendered scaling improves the letter-vs-grain ratio; digital camera zoom scales both.
- Clinical path: binocular vision evaluation (NPC, fusional vergence, accommodation) → office-based vergence/accommodative therapy per NIH CITT (adults + post-LASIK case evidence) → prism only if measured.

## Status

Research phase. Design spec to follow in this repository.

---

🤖 Co-authored with AI: OpenCode (ollama-cloud/glm-5.3-flash)