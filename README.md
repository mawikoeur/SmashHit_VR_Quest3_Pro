# Smash Hit VR — Meta Quest Compatibility Patch

**Bringing the classic VR experience back to life on modern Meta Quest headsets.**

Smash Hit VR was designed for an older generation of mobile VR headsets. On modern hardware such as the Meta Quest Pro, differences in projection geometry and rendering behavior can lead to severe visual distortions, incorrect image alignment, and an uncomfortable VR experience.

This project explores a software-based solution to make the original game playable again on modern standalone VR headsets, without rebuilding the game from scratch.

## ✨ Features

- **Projection correction** — Adjusts the game's projection matrices to improve image alignment and reduce visual distortion.
- **Radial image correction** — Experimental image-space correction designed to compensate for residual geometric distortions.
- **Vertical projection adjustment** — Fine-tuned to significantly reduce vertical distortion and object deformation on the Meta Quest Pro.
- **Stereo rendering adjustments** — Customizable stereo scaling to improve binocular alignment.
- **Legacy compatibility** — Works with the original game's legacy VR rendering pipeline.

## 🎯 Current Status

**Experimental — Meta Quest Pro**

The current Quest Pro configuration combines projection matrix adjustments, radial image correction, and stereo tuning. Testing has resulted in a substantial reduction in visual distortion and a much more comfortable experience.

Current tuning parameters are specific to the tested configuration and should not be assumed to work identically on other headsets.

Support for additional Quest headsets, including the Meta Quest 3, is a work in progress.

## 🔬 Technical Approach

Rather than replacing the original game, this project modifies parts of its legacy VR rendering pipeline.

The main areas of investigation are:

- Horizontal and vertical projection offsets
- Stereo projection scaling
- Radial image-space correction
- Headset-specific projection geometry
- Compatibility behavior on modern Meta Quest hardware

The project is developed through binary analysis, controlled parameter testing, and in-headset validation.

## ⚠️ Limitations

- This is an unofficial community project and is not affiliated with or endorsed by Meta or the developers of Smash Hit.
- Compatibility and visual quality may vary depending on the headset, firmware, and game version.
- Some rendering artifacts may remain.
- Quest 3 support requires separate testing and headset-specific tuning.
- Experimental builds may introduce visual artifacts or discomfort. Stop using a build if it causes eye strain or discomfort.

## 🛠️ Project Goals

- Refine the Quest Pro projection correction.
- Investigate whether radial correction is necessary after projection adjustments.
- Implement dynamic IPD-based stereo scaling.
- Adapt the correction pipeline to the Meta Quest 3.
- Document the findings so the work can be reproduced and improved by others.

## 📜 Disclaimer

This project is an independent compatibility effort intended for research, experimentation, and preservation of legacy VR software.

All trademarks belong to their respective owners. Users are responsible for supplying their own legally obtained copy of the original game.

🙏 Credits

Bassquake — Original Smash Hit VR compatibility patch, which serves as the foundation for this project.

---

*Keeping classic VR experiences alive, one projection matrix at a time.*
