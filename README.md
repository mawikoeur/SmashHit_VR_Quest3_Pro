# Smash Hit VR Remastered — Meta Quest Compatibility Patch

**Bringing the classic VR experience back to modern standalone Meta Quest headsets.**

Smash Hit VR was built for an older generation of mobile VR. On newer headsets, differences in projection geometry, rendering resolution, and runtime behavior can cause image misalignment and visual distortion. This project applies targeted compatibility changes to the original game's legacy VR pipeline rather than rebuilding the game from scratch.

Version **v0.14** returns to the clean **e0.25.6** baseline and packages separate rendering profiles for Meta Quest 2, Quest Pro, Quest 3, and Quest 3S.

## ✨ Features

- **Per-headset rendering profiles** — Selects a dedicated engine profile for Quest 2, Quest Pro, Quest 3, or Quest 3S.
- **Projection tuning** — Uses headset-specific camera FOV and vertical projection shift values to improve alignment and perceived scale.
- **Selective radial correction** — Keeps the existing custom radial post-process on Quest Pro and Quest 3, while bypassing it on Quest 2 and Quest 3S.
- **Stereo scaling** — Uses the relationship `stereo factor = IPD in mm / 2000`. The current fallback is 61 mm; an Android debug-property override can be supplied at launch.
- **Legacy VrApi compatibility** — Retains the original game's VrApi-based rendering path and the controller-input injection fix from e0.25.6.
- **Cleaner main package** — Excludes first-generation Quest from the main build and removes redundant native engine payloads.
- **Application label** — Uses **Smash Hit VR Remastered** as the Android application name.

## 🎯 Headset profiles

| Headset | Engine profile | Linear supersampling | Camera FOV | Vertical shift (M12) | Custom radial post-process |
|---|---|---:|---:|---:|---|
| Meta Quest 2 | `libsmashhit_q2.so` | 1.25× (fixed) | 90° | 0.00 | Bypassed |
| Meta Quest Pro | `libsmashhit.so` | 1.5× | 93° | 0.22 | Enabled |
| Meta Quest 3 | `libsmashhit_q3.so` | 1.5× | 95° | 0.22 | Enabled |
| Meta Quest 3S | `libsmashhit_q3s.so` | 1.5× | 90° | 0.00 | Bypassed |

These are the configured profile values, not a claim that every headset/firmware combination has been fully validated. The Quest Pro configuration at **61 mm IPD** remains the main calibration reference. Please test on each target device before treating the build as production-ready.

## 🧭 Current status — v0.14

v0.14 is based on the clean e0.25.6 codebase. It consolidates the four-device main-build profiles and removes first-generation Quest support from the main manifest. Quest 1 and Oculus Go are outside the scope of the main build and require a separate legacy path.

A dedicated in-game pause remap on controller **B/Y is not included** in v0.14. An experimental native hook caused a crash when pressed and was removed pending a correct implementation.

## 🔬 Technical approach

The project modifies selected parts of the legacy VR rendering pipeline:

- Model-based selection of the engine profile at startup.
- Headset-specific FOV and projection-shift values.
- Selective routing through the existing custom radial processor.
- Stereo scale derived from the configured IPD value.
- Compatibility with the game's existing controller-input injection path.

The renderer uses a linear supersampling profile per headset. Listed FOV values and profile settings describe the current binary configuration; actual runtime behavior can still depend on the headset, OS/runtime version, and game environment.

### IPD configuration

The stereo scale follows:

```text
stereo_factor = ipd_mm / 2000.0
```

The fallback is **61 mm**. For testing, set the launch-time Android property before starting the app:

```bat
adb shell setprop debug.smashhit.ipd_mm 64
adb shell getprop debug.smashhit.ipd_mm
```

Use a value in the supported 50–80 mm range. This is a launch-time override; the legacy VrApi path does **not** directly read the headset's physical IPD wheel as a live value.

## ⚠️ Limitations

- This is an unofficial community compatibility project and is not affiliated with Meta or the original game's developers.
- Quest 2, Quest Pro, Quest 3, and Quest 3S are the intended targets of the main build; testing on each device and firmware is still needed.
- First-generation Quest and Oculus Go are not included in the main build.
- The legacy rendering path may still exhibit visual artifacts, and profile tuning may differ by device/runtime.
- A dedicated B/Y pause remap is not available in this release.
- Stop using a build if it causes eye strain, nausea, or other discomfort.

## 🛠️ Project goals

- Validate each headset profile on physical hardware.
- Continue refining projection and radial-correction behavior where needed.
- Implement a safe, reliable in-game pause input for modern Touch controllers.
- Document the binary analysis and testing workflow so others can reproduce and improve it.

## 🙏 Credits

**Bassquake** — Original Smash Hit VR compatibility patch, which serves as the foundation for this project.

## 📜 Disclaimer

This is an independent compatibility and preservation effort. All trademarks belong to their respective owners. Users are responsible for supplying their own legally obtained copy of the original game.

---

*Keeping classic VR experiences alive, one projection matrix at a time.*
