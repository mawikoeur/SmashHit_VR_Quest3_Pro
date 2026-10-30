# Smash Hit VR Remastered — v0.16

v0.16 expands runtime IPD support to Quest 2 and Quest 3S, refines headset-specific rendering behavior, and introduces further performance adjustments.

### What's Changed

- **Quest 2 & Quest 3S IPD support:** Extended the IPD Watcher to both headset profiles.
- **Runtime IPD estimation:** Estimates eye separation from the translation components of the left and right view matrices returned by predicted tracking data.
- **Dynamic stereo adjustment:** Updates the stereo factor from runtime eye-view data instead of relying exclusively on the initial fallback IPD.
- **Quest 2 & Quest 3S rendering:** Continued headset-specific investigation and refinement of projection and rendering behavior.
- **Quest Pro supersampling:** Reduced the linear supersampling target from **1.5× to 1.35×**, lowering the number of rendered pixels while preserving the existing profile-specific radial correction.
- **CPU performance tuning:** Changed the legacy VrApi CPU clock-level request from **Level 2 to Level 4**.
- **GPU performance request:** Kept the legacy VrApi GPU clock-level request at **Level 2**.
- **Fixed Foveated Rendering:** Set the targeted FFR level to **High (Level 3)** to reduce peripheral rendering workload.

### Technical Notes

- IPD is estimated from runtime eye-view matrix translations; the watcher does not read a dedicated physical IPD field.
- The stereo factor follows `stereo_factor = ipd_mm / 2000.0`.
- CPU/GPU clock-level values are legacy VrApi requests, not guaranteed hardware frequencies.
- FFR reduces peripheral rendering resolution; it does not crop the visible field of view.
- Quest 2 and Quest 3S rendering behavior still requires validation on their respective physical headsets.

### Compatibility

- Meta Quest 2
- Meta Quest Pro
- Meta Quest 3
- Meta Quest 3S

### Credits

Original compatibility-patch foundation by **Bassquake**.
