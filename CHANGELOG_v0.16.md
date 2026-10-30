# Smash Hit VR Remastered — v0.16

v0.16 adds IPD-dependent FoV profiles for Quest 2 and Quest 3S, while refining the Quest Pro render scale and runtime performance configuration.

### Quest 2 & Quest 3S

- **IPD-dependent FoV profiles:** Added three nominal IPD profiles for the Quest 2 and Quest 3S rendering engines.
- **IPD Watcher integration:** The existing watcher samples predicted tracking data at approximately 4 Hz and estimates IPD from the separation between the left and right eye `ViewMatrix` translations.
- **Runtime profile selection:** Alongside updating the stereo factor, the watcher now publishes a nominal IPD profile selector for the Q2/Q3S engines. The engines use that selector to choose the matching horizontal camera FoV.
- **Three-point calibration table:**

  | Nominal IPD | Estimated binocular FoV | Engine horizontal FoV per eye |
  |---:|---:|---:|
  | 58 mm | 97° | 93.5° |
  | 63 mm | 91° | 90.5° |
  | 68 mm | 85° | 87.5° |

- **Profile switching thresholds:** The estimated IPD is assigned to the 58, 63, or 68 mm profile using bucket boundaries at **60.5 mm** and **65.5 mm**.
- **Projection compensation:** The Q2/Q3S main viewport adjusts its aspect coefficient alongside the selected horizontal FoV, preserving the vertical projection of the original 90° configuration.
- **Scoped engine changes:** The FoV profile table and projection hook are applied independently to the Quest 2 and Quest 3S engines. Quest 3 and Quest Pro engine and radial-correction libraries are unchanged.

The FoV values are calculated calibration targets, not physical measurements from Quest 2 or Quest 3S headsets. They use the project's assumed **90° binocular overlap**; the 63 mm binocular FoV is linearly interpolated between the 58 mm and 68 mm estimates. The projection adjustment applies to the main viewport; the auxiliary viewport is left unchanged.

### Quest Pro

- **Supersampling adjustment:** Reduced the linear supersampling target from **1.5× to 1.35×**, lowering the per-eye pixel workload while retaining the dedicated Quest Pro radial-correction profile.

### Performance & Rendering

- **CPU performance request:** Changed the legacy VrApi CPU clock-level request from **Level 2 to Level 4**.
- **GPU performance request:** Kept the legacy VrApi GPU clock-level request at **Level 2**.
- **Fixed Foveated Rendering:** Configured FFR to **High (Level 3)** to reduce peripheral rendering workload.

### Technical Notes

- The IPD Watcher continues to update the stereo factor using `stereo_factor = ipd_mm / 2000.0`; the new Q2/Q3S FoV selection is a separate, three-step profile choice.
- CPU/GPU clock-level values are legacy VrApi requests, not guaranteed hardware frequencies.
- FFR reduces peripheral rendering resolution; it does not crop the visible field of view.
- The Q2/Q3S FoV table is based on calculated targets and requires validation on physical headsets for optical accuracy and comfort.

### Compatibility

- Meta Quest 2
- Meta Quest Pro
- Meta Quest 3
- Meta Quest 3S

### Credits

Original compatibility-patch foundation by **Bassquake**.
