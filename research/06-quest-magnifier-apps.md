# Quest Magnifier Apps (Sideloadable)

Prepared by OpenCode AI assistant, 2026-09-25. Verified live via browser (repo pages, release assets, install docs).

## QuestLens — free, pre-built APK, magnifies VR/virtual content only

- What it is: "A local magnifier for small text in Quest games, built with low-vision users in mind." Freezes the VR image, opens a large 2D zoom window (1×/2×/4×/6×/8×), returns to the game.
- Camera involvement: NONE for virtual content — magnifies rendered pixels; no sensor noise added; snow-safe. Local-only: no uploads, OCR, analytics, accounts.
- Controls: double-tap headset side to open/close; right trigger zoom in, left trigger zoom out; hold trigger + drag to pan; on-screen −/+/RESET/BACK alternatives.
- Device note: **tested on Quest 3S; Quest 3 unverified by the maker** ("other models... remain unverified").
- Price/license: free (all features), MIT; optional Patreon.
- Download (v0.4.3-preview APK, verified 43 MB via HTTP HEAD, 2026-09-04): https://github.com/crtx01/QuestLens/releases/tag/v0.4.3-preview
- **Full install instructions from the maker:** https://github.com/crtx01/QuestLens/blob/main/docs/INSTALL.md — developer mode, `adb install -r`, Windows installer script (`Install-QuestLens.ps1`), optional double-tap gesture via one-time ADB setup, per-session capture consent (captures the VR screen, not the room).
- Repo: https://github.com/crtx01/QuestLens

## Q3 VisionAid — the passthrough magnifier (covers the physical book) — no APK; self-build from Unity source

- What it is: open-source Unity prototype (Quest 2/Pro/3/3S): brightness/contrast/saturation over native passthrough + camera-magnifier panel (1×–8×, aspect-correct UV crop) activated when zoom > 1×. Left thumbstick: vertical = zoom, horizontal = brightness; face button resets. Built explicitly for low-vision users.
- **Verified:** the Releases page is EMPTY ("There aren't any releases here") — no APK, no Builds folder, no install docs, **no LICENSE file** (source is legally all-rights-reserved). Using it requires Unity 6 + Meta XR SDK and building from source.
- Repo: https://github.com/uzeray/meta-quest-vision-aid-unity
- README (the only docs): https://github.com/uzeray/meta-quest-vision-aid-unity/blob/main/README.md
- Releases page: https://github.com/uzeray/meta-quest-vision-aid-unity/releases
- Full source review: see 07-platform-analysis.md.

## Sideload basics (applies to any Quest APK)

1. **Enable Developer Mode:** Meta developer account at https://developers.meta.com/, register the headset serial, enable Developer Mode in the phone app (Settings > Headset Settings > Developer Mode).
2. **Install adb** (Android SDK platform-tools or an installer script); connect headset by USB; accept "Allow USB debugging" in headset.
3. **Install:** `adb install -r <path-to-apk>`.
4. **Launch** from Apps > Unknown Sources.
- Meta's official sideload documentation: https://developers.meta.com/horizon/documentation/packaging/mobile-sideload/

## Honest expectations

- **QuestLens (free, APK ready):** magnifies *virtual* pages with zero added sensor noise — the snow-safe digital path (e-books rendered in VR). Cannot magnify the physical book.
- **Q3 VisionAid (build-it-yourself):** the only app magnifying the *physical* book via Quest — but it is digital camera zoom over a 1280×960 passthrough source: at low zoom (1.5–2×) enlarged letters may out-compete grain (empirically testable); at high zoom, noise scales with the letters and readability degrades. Prototype quality; requires a Unity build.
- Both are hobbyist/open-source projects, not store-vetted products.
- The **optical path (MaxDetail) remains the only no-sensor physical-book solution.**

---

🤖 Co-authored with AI: OpenCode (ollama-cloud/glm-5.3-flash)