# Smash Hit VR Remastered — Meta Quest Compatibility Patch

**Bringing the classic VR experience back to modern standalone Meta Quest headsets.**

Smash Hit VR was built for an older generation of mobile VR. Differences in projection geometry, render resolution, stereo calibration, and runtime behavior can cause image misalignment and visual distortion on newer headsets. This project builds on **Bassquake's original compatibility patch** and adapts the game's legacy VR pipeline for modern Meta Quest devices.

The project focuses on headset-specific native rendering profiles, runtime-based stereo calibration, and targeted image-space correction while preserving the original game's legacy VrApi rendering path.

## ✨ Features

- **Per-headset rendering profiles** — Dedicated native engine profiles for Meta Quest 2, Quest Pro, Quest 3, and Quest 3S.
- **Runtime IPD estimation** — The IPD Watcher reads predicted tracking data and estimates eye separation from the left and right view matrices.
- **Dynamic stereo adjustment** — Updates the engine's stereo factor from runtime eye-view data instead of relying only on a fixed fallback IPD.
- **Headset-specific render scaling** — Separate render-scale coefficients and supersampling targets for each supported headset.
- **Selective radial correction** — Dedicated radial shader profiles for Quest Pro and Quest 3; Quest 2 and Quest 3S bypass this custom radial pass.
- **Projection tuning** — Per-headset camera FOV and vertical-shift parameters.
- **Legacy VrApi compatibility** — Retains the original VrApi-based rendering pipeline and the existing controller-input compatibility fix.
- **Performance tuning** — Uses legacy VrApi CPU/GPU clock-level requests, with actual operating frequencies managed by the headset runtime.
- **Fixed Foveated Rendering** — Uses the runtime's FFR mechanism to reduce peripheral rendering workload.

## 🎯 Headset profiles

| Headset | Engine profile | Linear SS target | Native render-scale coefficient | Camera FOV | M12 vertical shift | Radial correction |
|---|---|---:|---:|---:|---:|---|
| Meta Quest 2 | `libsmashhit_q2.so` | 1.25× | `1.420139` | 90° | `0.000` | Bypassed |
| Meta Quest Pro | `libsmashhit_pro.so` | 1.35× | **1.51875** | 92.9° | `0.209` | Enabled |
| Meta Quest 3 | `libsmashhit_q3.so` | 1.5× | `1.671429` | 94° | `0.193` | Enabled |
| Meta Quest 3S | `libsmashhit_q3s.so` | 1.5× | `1.567857` | 90° | `0.000` | Bypassed |

**Render-scale note:** the linear supersampling target and native render-scale coefficient are different values. The coefficient accounts for the intermediate render resolution used by each headset profile. Target resolutions derived from these values are estimates, not measurements from every physical headset.

The FOV values are camera FOV arguments configured in the game engine. They are not direct measurements of the physical optical field of view visible through each headset's lenses.

## 👁️ Runtime IPD Watcher

The IPD Watcher periodically requests predicted tracking data through the legacy VrApi tracking interface.

It estimates the current IPD from the distance between the translation components of the left and right eye view matrices. It does not read a dedicated physical IPD field from the tracking structure.

The resulting stereo factor follows:

```text
stereo_factor = ipd_mm / 2000.0
```

The initial fallback is **61 mm**, corresponding to a stereo factor of `0.0305`. Runtime eye-view data can update this value after startup.

For testing, an Android debug property can override the fallback before launching the app:

```sh
adb shell setprop debug.smashhit.ipd_mm 64
adb shell getprop debug.smashhit.ipd_mm
```

Use a value in the supported 50–80 mm range. The watcher estimates IPD from runtime eye matrices; it does not directly read the physical IPD adjustment slider.

## 🔬 Radial correction

Quest Pro and Quest 3 use separate radial shader profiles. The correction is applied in image space after the eye images have been rendered. It is **not a native asymmetric (Mode 5) projection**: it remaps texture coordinates to compensate for selected visual alignment and distortion effects.

| Parameter | Quest Pro | Quest 3 |
|---|---:|---:|
| Radial center X | `0.5000` | `0.5000` |
| Radial center Y | `0.6045` | `0.5965` |
| Horizontal UV offset per eye | `−0.04023 / +0.04023` | `−0.04023 / +0.04023` |
| Radial correction | Enabled | Enabled |

The vertical radial centers correspond to the configured vertical shifts through:

```text
centerY = 0.5 + M12 / 2
```

- Quest Pro: `M12 = 0.209` → radial center Y = `0.6045`.
- Quest 3: `M12 = 0.193` → radial center Y = `0.5965`.

The shader applies opposite horizontal UV offsets to the two eyes. These image-space corrections can improve apparent alignment, but they do not reproduce every property of true off-axis projection or recover image information that was never rendered. The additional radial pass also has a performance cost.

Quest 2 and Quest 3S bypass this custom radial post-process and retain their legacy symmetric rendering path.

## ⚙️ Performance and FFR

The game continues to use the legacy VrApi performance-control path. CPU and GPU clock-level arguments are requests to the runtime, not guarantees of fixed hardware frequencies. The headset runtime retains control over actual operating clocks and may adjust them according to workload, thermal conditions, and system policy.

Fixed Foveated Rendering reduces rendering resolution in peripheral regions while retaining higher detail toward the center of the image. FFR does **not** crop or physically reduce the headset's visible field of view. Its visual impact and performance benefit depend on the headset, runtime, and scene.

## 🧭 Compatibility and limitations

**Supported headset profiles:**
- Meta Quest 2
- Meta Quest Pro
- Meta Quest 3
- Meta Quest 3S

The original Meta Quest (Quest 1) and Oculus Go are not included in the main build and require a separate legacy path.

This is an unofficial community compatibility project and is not affiliated with Meta or the original game's developers. Testing on every physical headset and firmware combination is not complete. Configured values describe the current profiles; visual behavior and performance may vary by device and runtime.

A dedicated in-game pause remap on controller **B/Y is not included**. A previous experimental native hook caused a crash when pressed and was removed pending a reliable implementation.

Stop using a build if it causes eye strain, nausea, or other discomfort.

## 🙏 Credits

**Bassquake** — Original Smash Hit VR compatibility patch, which serves as the foundation for this project.

## 📜 Disclaimer

This is an independent compatibility and preservation effort. All trademarks belong to their respective owners. Users are responsible for supplying their own legally obtained copy of the original game.

---

*Keeping classic VR experiences alive, one projection matrix at a time.*
