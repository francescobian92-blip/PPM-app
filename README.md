# VIV PPM Calculator

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Web App](https://img.shields.io/badge/Web%20App-Live-brightgreen)](https://francescobian92-blip.github.io/viv-tavi-ppm/)

A real-time calculator for predicting patient-prosthesis mismatch (PPM) during valve-in-valve transcatheter aortic valve implantation (VIV-TAVI). Uses a hybrid Gorlin approach combining pre-procedural echocardiographic stroke volume with intra-procedural invasive hemodynamics.

**🌐 Live app:** [https://francescobian92-blip.github.io/viv-tavi-ppm/](https://francescobian92-blip.github.io/viv-tavi-ppm/)

---

## ⚠️ Disclaimer

**For research use only.** This tool is not a CE-marked medical device and is not intended for clinical decision-making. It is provided as-is for research and educational purposes. Users assume full responsibility for any use of this tool. The author disclaims any liability arising from the use of this software.

---

## Overview

During a VIV-TAVI procedure, predicting patient-prosthesis mismatch in real time is clinically valuable because it can guide the decision to perform additional maneuvers (post-dilation, bioprosthetic valve fracture) before the patient leaves the cath lab.

This tool operationalizes a hybrid Gorlin methodology: it uses the stroke volume measured at baseline by echocardiography to calibrate a patient-specific aortic impedance (Z<sub>ao</sub>), then computes aortic valve area from intra-procedural mean aortic pressure and mean transvalvular gradient alone.

### Key formula

**Calibration (once, at baseline):**
```
Z_ao = (ms₀ × LVET₀) / SV_echo
```

**Real-time AVA (during procedure):**
```
AVA = ms / (Z_ao × 44.3 × √ΔP_mean)
AVAi = AVA / BSA
```

Where `ms` is the mean systolic aortic pressure and `ΔP_mean` is the mean transvalvular gradient, both measured on the cath-lab polygraph.

### PPM classification

Following ESC/EACTS 2025 guidelines:

| AVAi | Standard | BMI ≥ 30 |
|------|----------|----------|
| No PPM | ≥ 0.85 | ≥ 0.70 |
| Moderate | 0.65 – 0.85 | 0.55 – 0.70 |
| Severe | < 0.65 | < 0.55 |

---

## Features

- **5-step guided workflow**: Patient → Echo → Z<sub>ao</sub> calibration → PRE/POST measurements → Result
- **Real-time calculation** with visual PPM badge and scale marker
- **Clinical decision support** with context-aware recommended actions for each PPM category
- **Predicted EOA database**: 23 surgical bioprostheses and 18 TAVI devices with peer-reviewed EOA reference values and a VIV-adjustment model
- **Offline support** via service worker (installable as PWA on iPhone/iPad/Android/desktop)
- **Dark mode** with premium navy-blue palette
- **Exportable results** (copy to clipboard + printable A4 layout)
- **Privacy-first**: no tracking, no analytics, no data leaves the device (localStorage only)

---

## How to use

1. Open the [web app](https://francescobian92-blip.github.io/viv-tavi-ppm/).
2. On iPhone/iPad: Safari → Share → Add to Home Screen (works offline afterwards).
3. On Android: Chrome → menu → Install app.
4. On desktop: Chrome/Edge → address bar → install icon.

### Input workflow

1. **Patient**: weight, height, sex
2. **Echo (pre-procedure)**: LVOT diameter, LVOT-VTI, transvalvular VTI (or direct SV)
3. **Calibration (baseline, before deployment)**: mean systolic aortic pressure + LVET from the Philips polygraph (Freeze → Measure → read Mean and Δt)
4. **PRE/POST deployment**: mean pressure and mean ΔP before and after TAVI release
5. **Result**: PPM classification, AVAi position on scale, and recommended clinical actions

---

## Scientific basis

The hybrid Gorlin methodology used by this tool is based on:

> **Bianchini F**, et al. *A hybrid approach to estimate aortic valve area during valve-in-valve transcatheter aortic valve implantation.* Cardiovasc Revasc Med. 2024.

Additional references for the predicted EOA database:
- Pibarot P, Dumesnil JG. *Prosthesis-patient mismatch: definition, clinical impact, and prevention.* JACC 2009;53:1883-96
- Dvir D, et al. *Transcatheter aortic valve implantation in failed bioprosthetic surgical valves* (VIVID registry). JAMA 2014;312:162-70
- Webb JG, et al. *Transcatheter aortic valve implantation within degenerated aortic surgical bioprostheses: PARTNER 2 valve-in-valve registry.* JACC 2017;69:2253-62
- Bapat VN, et al. *Effect of valve design on the stent internal diameter of a bioprosthetic valve.* JACC Cardiovasc Interv 2014;7:115-27
- ESC/EACTS 2025 Guidelines for the management of valvular heart disease

---

## Validation status

⚠ **Prospective validation is ongoing.** This tool should not be used for clinical decision-making until its accuracy has been validated against reference standards in a prospective cohort.

If you are interested in collaborating on validation, please open an issue or contact the author.

---

## Technical details

- Single-file HTML app, no backend required
- No external runtime dependencies (only Google Fonts `Inter` for typography, loaded with fallback to system fonts)
- Uses `localStorage` for session persistence (no data leaves the device)
- Service worker enables full offline functionality after first load
- Installable as Progressive Web App (PWA) on all modern browsers
- File size: ~65 KB uncompressed

---

## Citation

If you use this tool in your research, please cite:

```
Bianchini F. VIV PPM Calculator [Software]. Version 1.0. 2025.
DOI: 10.5281/zenodo.XXXXXXX
Available at: https://francescobian92-blip.github.io/viv-tavi-ppm/
```

A BibTeX entry is available in [`CITATION.cff`](CITATION.cff).

---

## License

This software is distributed under the [Creative Commons Attribution-NonCommercial 4.0 International License](LICENSE).

You are free to:
- **Share** — copy and redistribute in any medium
- **Adapt** — remix, transform, and build upon the material

Under the following terms:
- **Attribution** — you must give appropriate credit
- **NonCommercial** — you may not use the material for commercial purposes

---

## Author

**Francesco Bianchini, MD**
Interventional Cardiologist
Fondazione Policlinico Universitario A. Gemelli IRCCS, Rome, Italy

---

## Contributing

Bug reports and feature requests are welcome via GitHub Issues. Pull requests should not change the core methodology without prior discussion with the author.
