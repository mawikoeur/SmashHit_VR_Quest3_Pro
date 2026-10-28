# Smash Hit VR Remastered — Meta Quest Compatibility Patch

**Bringing the classic VR experience back to modern standalone Meta Quest headsets.**

Smash Hit VR was built for an older generation of mobile VR. On newer headsets, differences in projection geometry, render resolution, and runtime behavior can cause image misalignment and visual distortion. This project builds on **Bassquake's original compatibility patch** and adapts the game's legacy VR pipeline for modern Meta Quest headsets.

Version **v0.15** refines the per-headset render-scale coefficients and radial UV alignment, updates the Quest Pro and Quest 3 rendering profiles, and adds the **REMASTERED** wordmark to the title screen.

## ✨ Features

- **Per-headset rendering profiles** — Separate engine profiles for Meta Quest 2, Quest Pro, Quest 3, and Quest 3S.
- **Adjusted render scaling** — Documents each profile's native render-scale coefficient alongside its intended linear supersampling target.
- **Radial UV correction** — Quest Pro and Quest 3 use dedicated radial post-processing profiles with headset-specific vertical centering and opposite horizontal UV offsets for the two eyes.
- **Updated projection parameters** — Refined FOV and vertical-shift values for Quest Pro and Quest 3.
- **Legacy VrApi compatibility** — Preserves the game's original VrApi-based rendering path and the existing controller-input compatibility fix.
- **Updated title screen** — Adds the **REMASTERED** wordmark.
- **Focused compatibility scope** — The main build targets Quest 2, Quest Pro, Quest 3, and Quest 3S. First-generation Quest and Oculus Go require a separate legacy build.

## 🎯 Headset profiles

| Headset | Engine profile | Linear SS target | Native render-scale coefficient | Camera FOV | M12 vertical shift | Radial correction |
|---|---|---:|---:|---:|---:|---|
| Meta Quest 2 | `libsmashhit_q2.so` | 1.25× | `1.420139` | 90° | `0.000` | Bypassed |
| Meta Quest Pro | `libsmashhit_pro.so` | 1.5× | `1.6875` | 92.9° | `0.209` | Enabled |
| Meta Quest 3 | `libsmashhit_q3.so` | 1.5× | `1.671429` | 94° | `0.193` | Enabled |
| Meta Quest 3S | `libsmashhit_q3s.so` | 1.5× | `1.567857` | 90° | `0.000` | Bypassed |

**Important:** the linear SS target and native render-scale coefficient are different values and are not interchangeable. The coefficient accounts for the intermediate render resolution used by that profile. Supersampled target dimensions below are calculated estimates, not measurements collected from a live headset session.

| Headset | Intermediate resolution per eye | Estimated target per eye at the listed linear SS |
|---|---:|---:|
| Quest 2 | 1636 × 1752 | 2045 × 2190 |
| Quest Pro | 1620 × 1752 | 2430 × 2628 |
| Quest 3 | 1872 × 1984 | 2808 × 2976 |
| Quest 3S | 1756 × 1840 | 2634 × 2760 |

The FOV values above are camera FOV arguments configured in the game engine. They are not direct measurements of the physical optical field of view visible through each headset's lenses.

## 🔬 Radial correction

Quest Pro and Quest 3 use separate radial shader profiles. The correction is applied in image space after the eye images have been rendered; it is **not a native asymmetric (Mode 5) projection**. Instead, it remaps texture coordinates to compensate for selected visual alignment and distortion effects.

| Parameter | Quest Pro | Quest 3 |
|---|---:|---:|
| Radial center X | `0.5000` | `0.5000` |
| Radial center Y | `0.6045` | `0.5965` |
| Horizontal UV offset per eye | `−0.04023 / +0.04023` | `−0.04023 / +0.04023` |
| Radial correction | Enabled | Enabled |

The vertical radial centers correspond to the configured vertical shifts through the relationship `centerY = 0.5 + M12 / 2`:

- Quest Pro: `M12 = 0.209` → radial center Y = `0.6045`.
- Quest 3: `M12 = 0.193` → radial center Y = `0.5965`.

The shader applies opposite horizontal UV offsets to the two eyes. These image-space corrections can improve apparent alignment, but they do not reproduce every property of true off-axis projection or recover image information that was never rendered. The additional radial pass also has a performance cost.

Quest 2 and Quest 3S bypass this custom radial post-process in v0.15 and retain their legacy symmetric rendering path.

## 👁️ Stereo and IPD configuration

The stereo factor follows:

```text
stereo_factor = ipd_mm / 2000.0
```

The initial fallback is **61 mm**, corresponding to a stereo factor of `0.0305`. For testing, an Android debug property can be set before launching the app:

```sh
adb shell setprop debug.smashhit.ipd_mm 64
adb shell getprop debug.smashhit.ipd_mm
```

Use a value in the supported 50–80 mm range. The bundled IPD watcher can update the engine's stereo factor from runtime eye-matrix data after startup, so the effective value may differ from the initial fallback. Verify runtime behavior on the target device when precise calibration is required.

## 🧭 Current status — v0.15

v0.15 keeps the radial-rendering approach for Quest Pro and Quest 3 rather than replacing it with the separate experimental Mode 5 UI-pipeline candidate. The focus is a comfortable, visually coherent result while preserving the game's existing menu and interface rendering paths.

The radial approach is an intentional image-space compromise, not a claim that radial correction is mathematically identical to asymmetric projection. The listed profile values are the configured binary values; target render dimensions are estimates, and validation across every headset and firmware combination is still needed.

A dedicated in-game pause remap on controller **B/Y is not included**. The previous experimental native hook caused a crash when pressed and was removed pending a reliable implementation.

## ⚠️ Limitations

- This is an unofficial community compatibility project and is not affiliated with Meta or the original game's developers.
- Quest 2, Quest Pro, Quest 3, and Quest 3S are the intended targets of the main build; testing on every physical headset and firmware version is not complete.
- First-generation Quest and Oculus Go are not included in the main build and require a separate legacy path.
- Radial correction is a post-process approximation and may not reproduce every property of native off-axis projection. Visible artifacts can vary by headset and runtime.
- The radial post-process adds rendering work; its performance impact may vary by device.
- A dedicated B/Y pause remap is not available in this release.
- Stop using a build if it causes eye strain, nausea, or other discomfort.

## 🛠️ Project goals

- Validate each headset profile on physical hardware.
- Continue refining radial correction and render scaling where measurements justify changes.
- Keep rendering and UI behavior stable while documenting the rationale behind each profile.
- Implement a safe, reliable in-game pause input for modern Touch controllers.
- Document the binary-analysis and testing workflow so others can reproduce and improve it.

## 🙏 Credits

**Bassquake** — Original Smash Hit VR compatibility patch, which serves as the foundation for this project.

## 📜 Disclaimer

This is an independent compatibility and preservation effort. All trademarks belong to their respective owners. Users are responsible for supplying their own legally obtained copy of the original game.

---

*Keeping classic VR experiences alive, one projection matrix at a time.*
