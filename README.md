# VIV PPM Calculator

[![Version](https://img.shields.io/badge/version-4.0.0-blue.svg)](https://github.com/francescobian92-blip/PPM-app/releases)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Web App](https://img.shields.io/badge/Web%20App-Live-brightgreen)](https://francescobian92-blip.github.io/PPM-app/)

A calculator for predicting and assessing patient-prosthesis mismatch (PPM) in valve-in-valve transcatheter aortic valve implantation (VIV-TAVI). The tool offers three independent modes — predicted (pre-procedural), measured (intra-procedural via hybrid Gorlin), and echo post (post-procedural via continuity equation).

**🌐 Live app:** [https://francescobian92-blip.github.io/PPM-app/](https://francescobian92-blip.github.io/PPM-app/)

---

## ⚠️ Disclaimer

**For research use only.** This tool is not a CE-marked medical device and is not intended for clinical decision-making. It is provided as-is for research and educational purposes. Users assume full responsibility for any use of this tool. The author disclaims any liability arising from the use of this software.

---

## Modes of operation

| Mode | When | Inputs | Method |
|------|------|--------|--------|
| **Predicted** | Before procedure | Surgical valve OR previous TAVI (redo-TAVI) + intended TAVI | EOA database (Pibarot 2009 / Hahn 2018) + VIV adjustment (Bapat 2014) |
| **Measured** | During procedure | Echo SV + invasive hemodynamics (mean Pao, mean ΔP) + optional BVF | Hybrid Gorlin |
| **Echo post** | After procedure | Echo TTE (LVOT, VTI, gradient, AR) | Continuity equation + VARC-3 SVD classification |

When all three modes are completed in the same session, the tool displays a comparative panel: **Predicted → Measured → Echo post**.

The Predicted mode also supports **TAV-in-TAV** scenarios (failed previous TAVI), with empirical constraining factors based on Akodad 2023 and Sathananthan 2021 bench data.

---

## Hybrid Gorlin methodology

The intra-procedural mode operationalizes a hybrid Gorlin approach: it uses the stroke volume measured at baseline by echocardiography to calibrate a patient-specific aortic impedance (Z<sub>ao</sub>), then computes aortic valve area from intra-procedural mean aortic pressure and mean transvalvular gradient alone.

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

---

## PPM classification

Following Pibarot et al. JACC Cardiovasc Imaging 2019 (Table 1), endorsed by VARC-3 and EACVI:

| AVAi (cm²/m²) | Standard | BMI ≥ 30 |
|---------------|----------|----------|
| **No PPM** | > 0.85 | > 0.70 |
| **Moderate** | 0.66 – 0.85 | 0.56 – 0.70 |
| **Severe** | ≤ 0.65 | ≤ 0.55 |

---

## Predicted-EOA database

The Predicted mode uses a tiered evidence base for EOA reference values:

### Surgical bioprostheses (21 models)

**Primary source — Pibarot & Dumesnil 2009 Circulation (Table 1):**
- Carpentier-Edwards Perimount, Magna *(limited data)*
- Mosaic, Hancock II, Epic / Biocor *(limited data)*
- Mitroflow *(limited data)*
- Freestyle, Toronto SPV (stentless aortic root)

**Secondary source — Manufacturer technical specifications:**
- Magna Ease, Inspiris Resilia, Trifecta, Trifecta GT, Avalus, Epic Supra, Aspire, Prima Plus, Perceval, Intuity
- **Crown PRT (LivaNova)** — added in v2.0 (supra-annular pericardial)
- **Hancock II Ultra (Medtronic)** — added in v2.0 (improved porcine with anti-calcification + supra-annular design)

### TAVI devices (19 models)

**Primary source — Hahn et al. 2018 JACC Cardiovasc Imaging (Tables 2 & 4):**
Pooled core-lab data from PARTNER trials (Sapien family) and CoreValve US Pivotal + Evolut R IDE studies:
- Sapien (1st gen): n=2342
- Sapien XT: n=1471
- Sapien 3: n=1470 (size 20mm based on n=47, flagged as limited data)
- Sapien 3 Ultra / Ultra Resilia: hemodynamic profile assumed equivalent to Sapien 3
- CoreValve (classic): n=835 (size 23mm excluded due to n=19, flagged in paper as "requires confirmation")
- Evolut R: n=255 (size 23mm excluded due to n=3, insufficient data per Hahn 2018)

These are EOA values measured in TAVR for native annular aortic stenosis, used as the **nominal THV EOA** on which the Bapat 2014 VIV constraining factor is then applied.

**Secondary source — Manufacturer technical specifications and registry data:**
Myval / Myval Octacor, Evolut PRO/PRO+/FX/FX+, Acurate neo/neo2, Portico, Navitor, JenaValve Trilogy, Allegra (NVT). For Acurate neo, secondary source includes Möllmann et al. EuroIntervention 2018 (SAVI-TF registry).

**Withdrawn devices (added in v2.0 for VIV scenarios in patients with prior implant):**
- **Lotus / Lotus Edge (Boston Scientific)** — mechanically-expandable, intra-annular. EOA values from Asgar 2020 (RESPOND post-market) + REPRISE III FDA SSED. Device withdrawn from market in 2020. The app shows a visible warning when this device is selected.

Balloon-expandable: Sapien (1st gen), Sapien XT, Sapien 3, Sapien 3 Ultra, Sapien 3 Ultra Resilia, Myval, Myval Octacor

Self-expanding: CoreValve (classic), Evolut R/PRO/PRO+/FX/FX+, Acurate neo/neo2, Portico, Navitor, JenaValve Trilogy, Allegra

Mechanically-expandable: Lotus / Lotus Edge (withdrawn)

### VIV constraining model (Bapat 2014)

When TAVI is deployed inside a degenerated surgical bioprosthesis, the achievable EOA is constrained by the leaflet design (true ID < stent ID). Adjustment factors based on Bapat et al. (Figure 6, Table 2):

| Surgical valve design | Examples | True ID vs Stent ID | Factor |
|------------------------|----------|---------------------|--------|
| Pericardial leaflets **outside** stent | Trifecta, Mitroflow | = stent ID | **0.92** |
| Pericardial leaflets **inside** stent | Perimount, Magna, Inspiris, Avalus, Perceval, Intuity | −1 mm | **0.85** |
| Porcine leaflets inside stent | Hancock II, Mosaic, Epic, Aspire | −2 mm | **0.75** |
| Stentless aortic root | Freestyle, Toronto SPV root, Prima Plus | variable | **0.90** |

**Modifiers:**
- −0.05 if SHV label size ≤ 21 mm (Bapat caveat for borderline sizes)
- +0.05 if TAVI is supra-annular (Evolut series, Acurate neo, JenaValve)

---

## Features

- **3 independent modes** (Predicted, Measured, Echo post), each saved separately and viewable side-by-side
- **Auto-calculated time interval** between procedure date and echo post date
- **Real-time calculation** with visual PPM badge and AVAi scale marker
- **VARC-3 SVD classification** in echo post mode (no/moderate/severe hemodynamic SVD)
- **Clinical decision support** with context-aware recommended actions for each PPM category
- **Predicted-EOA database**: 19 surgical bioprostheses + 18 TAVI devices with traceable references (DOI per model where applicable)
- **Offline support** via service worker (installable as PWA on iPhone/iPad/Android/desktop)
- **Dark mode** with premium navy-blue palette
- **Exportable results** (copy to clipboard + printable A4 layout)
- **Privacy-first**: no tracking, no analytics, no data leaves the device (localStorage only)

---

## How to use

1. Open the [web app](https://francescobian92-blip.github.io/PPM-app/).
2. On iPhone/iPad: Safari → Share → Add to Home Screen (works offline afterwards).
3. On Android: Chrome → menu → Install app.
4. On desktop: Chrome/Edge → address bar → install icon.

### Input workflow

#### Predicted (4 steps, ~30 seconds)
1. **Patient**: weight, height, sex
2. **Surgical valve**: model, size *(or override EOA manually)*
3. **Intended TAVI**: model, size *(or override VIV-adjusted EOA)*
4. **Result**: predicted PPM with AVAi scale, vulnerability assessment

#### Measured (5 steps, ~1 min during procedure)
1. **Patient**: weight, height, sex, procedure date
2. **Echo (pre-procedure)**: LVOT diameter, LVOT-VTI, transvalvular VTI *(or direct SV)*
3. **Calibration (baseline)**: mean Pao + LVET from polygraph (Freeze → Measure → read Mean and Δt)
4. **PRE/POST deployment**: mean Pao and mean ΔP before and after TAVI release
5. **Result**: real-time PPM with clinical actions

#### Echo post (3 steps)
1. **Patient**: weight, height, sex, echo date
2. **Echo**: LVOT D, LVOT VTI, AV VTI, mean gradient, peak velocity, AR severity
3. **Result**: PPM + DVI + VARC-3 SVD classification + months post-procedure

---

## Scientific basis

The hybrid Gorlin methodology underlying the **Measured** mode is based on:

> **Bianchini F**, et al. *A hybrid approach to estimate aortic valve area during valve-in-valve transcatheter aortic valve implantation.* Cardiovasc Revasc Med. 2024.

The **Predicted** mode and **PPM thresholds** are based on the following peer-reviewed sources (DOIs verified):

| Reference | Use in this tool | DOI |
|-----------|------------------|-----|
| **Pibarot P & Dumesnil JG.** *Prosthetic heart valves: selection of the optimal prosthesis and long-term management.* Circulation 2009;119:1034-48 | EOA reference values for surgical prostheses (Table 1) | [`10.1161/CIRCULATIONAHA.108.778886`](https://doi.org/10.1161/CIRCULATIONAHA.108.778886) |
| **Hahn RT, Leipsic J, Douglas PS, et al.** *Comprehensive Echocardiographic Assessment of Normal Transcatheter Valve Function.* JACC Cardiovasc Imaging 2019;12(1):25-34 | EOA reference values for TAVI devices (Tables 2 & 4) — Sapien family, CoreValve, Evolut R | [`10.1016/j.jcmg.2018.04.010`](https://doi.org/10.1016/j.jcmg.2018.04.010) |
| **Bapat VN, Attia R, Thomas M.** *Effect of valve design on the stent internal diameter of a bioprosthetic valve.* JACC Cardiovasc Interv 2014;7:115-27 | VIV-adjusted EOA model (true-ID concept by leaflet design) | [`10.1016/j.jcin.2013.10.012`](https://doi.org/10.1016/j.jcin.2013.10.012) |
| **Pibarot P, Magne J, Leipsic J, et al.** *Imaging for predicting and assessing prosthesis-patient mismatch after aortic valve replacement.* JACC Cardiovasc Imaging 2019;12:149-62 | PPM thresholds (Table 1, EACVI/VARC-2 endorsed) | [`10.1016/j.jcmg.2018.10.020`](https://doi.org/10.1016/j.jcmg.2018.10.020) |
| **VARC-3 Writing Committee, Généreux P, Piazza N, et al.** *Valve Academic Research Consortium 3: updated endpoint definitions for aortic valve clinical research.* Eur Heart J 2021;42:1825-57 | SVD classification in Echo post mode | [`10.1093/eurheartj/ehab395`](https://doi.org/10.1093/eurheartj/ehab395) |

---

## Validation status

⚠ **Prospective validation of the Measured mode is ongoing.** This tool should not be used for clinical decision-making until its accuracy has been validated against reference standards in a prospective cohort.

If you are interested in collaborating on validation, please open an issue.

---

## Technical details

- Single-file HTML app, no backend required
- No external runtime dependencies (only Google Fonts `Inter` for typography, loaded with fallback to system fonts)
- Uses `localStorage` for session persistence (no data leaves the device)
- Service worker enables full offline functionality after first load
- Installable as Progressive Web App (PWA) on all modern browsers
- File size: ~120 KB uncompressed

---

## Citation

If you use this tool in your research, please cite:

```
Bianchini F. VIV PPM Calculator [Software]. Version 3.1. 2026.
DOI: 10.5281/zenodo.XXXXXXX
Available at: https://francescobian92-blip.github.io/PPM-app/
```

A BibTeX entry is available in [`CITATION.cff`](CITATION.cff).

---

## Changelog

### v3.1 (April 2026) — bug fix release

**Critical fixes**
- **PPM color-coding now correctly respects obese (BMI ≥ 30) thresholds** in all three modes (Predicted, Measured, Echo post). Previously the result-card color used hardcoded thresholds (0.65 / 0.85) that ignored the obese-adjusted thresholds (0.55 / 0.70). For obese patients, an AVAi of 0.62 was incorrectly displayed in red (Severe) when it should have been amber (Moderate). The textual label was also affected. Now the central `classifyPPM()` function is used everywhere for consistent classification.
- **HTML structural fix** in the F() input helper for Measured mode: a missing closing `</div>` caused all input fields to be DOM-nested inside one another. On mobile this was invisible (vertical stacking), but on desktop (≥720px viewport) the wrapped fields appeared side-by-side with units floating between them. Layout now renders correctly across all viewports.
- **Cross-check false-positive** when BVF is planned: the "VIV-adjusted EOA > nominal EOA is impossible" warning now correctly subtracts the BVF EOA gain (+0.15 cm² for fracturable, +0.07 for partial) before checking, since BVF legitimately recovers some of the constrained EOA.
- **exportText (Copy results)** now respects obese thresholds — previously the copied/pasted text always used non-obese cutoffs.
- **BMI ≥ 30 threshold line** in Measured result now uses `classifyPPM()` for consistency (was using `>=` while the function uses `>` strictly).

### v3.0 (April 2026)

**Cath-lab ergonomics & UX**
- **Quick / Detailed result view toggle**: switch between essential clinical readout (PPM badge, AVAi, decision card, cross-checks if issues) and full detail (sensitivity range, comparison table, thresholds tables, footer info). Preference persisted in localStorage. Reduces scrolling time during cath-lab use.
- **Numeric input steppers (+/−)**: every numeric input field across all three modes now has compact ± buttons with intelligent step sizes (gradient ±5, AVAi ±0.05, pressures ±5, LVET ±10, peak velocity ±0.1). Speeds up small adjustments in cath-lab where typing on mobile keypads is slow. Values clamp at 0 (no negatives).

**Scientific robustness**
- **Echo-procedure timing warning**: when the gap between baseline echo and procedure exceeds 30 days, an amber alert flags the increased uncertainty of the hybrid Gorlin assumption (constant stroke volume between echo and cath-lab).
- **BSA formula toggle (DuBois / Mosteller)**: configurable in About modal; persisted in localStorage. DuBois (default) is most cited; Mosteller performs better in extreme body sizes (BMI <18 or >35). Affects all AVAi and PPM classification calculations.

**Engineering & infrastructure**
- **Schema versioning + automatic migrations**: localStorage state has a `schemaVersion` field; old states are migrated forward seamlessly when the data structure evolves (e.g., new fields added). Corrupted state triggers graceful reset rather than crash.
- **JSON structured export**: alongside PDF, results can now be exported as machine-readable JSON for downstream research/analysis (R, Python, SPSS). Contains tool version, schema version, all inputs, and computed outputs.
- **Service Worker update notification**: when a new app version is deployed, a non-intrusive banner offers the user to reload (avoids stuck-on-old-version PWA installations).
- **Self-test healthcheck page**: accessible via `?healthcheck=1` URL parameter; runs synthetic tests for BSA, Bapat factors, PPM thresholds, hybrid Gorlin, and database integrity. Used by maintainers to verify deploys.
- **Error boundary + bug-report**: uncaught JS errors trigger a discrete banner with a one-click `mailto:` button pre-filled with stack trace, tool version, schema, and user agent. No passive telemetry.

**Bug fixes**
- **Reset (Azzera)** now correctly clears all three modes (Measured, Predicted, Echo FU), all step counters, all open help panels, and the modal. Previously only Measured was being reset, leaving stale data in the other two modes.

### v2.0 (April 2025)
- **Fracturable flag** annotated for all 20 surgical bioprostheses (`true` / `false` / `'partial'`) with literature references (Allen 2017, Brinkmann 2019, Lewis 2020) — drives BVF feasibility logic
- **BVF planning toggle in Predicted mode**: pre-procedural BVF planning with automatic fracturability check; expected EOA gain (+0.15 cm² fracturable, +0.07 cm² partial) applied to predicted AVAi; toggle disabled with red warning when SHV is non-fracturable
- **Sensitivity range in Predicted result**: AVAi shown at ±15% EOA-VIV perturbation (3-column display: worst case / estimated / best case); auto-detection of "Fragile" (PPM category may shift) vs "Robust" (stable)
- **Strategy Comparison Table in Predicted**: shows up to 4 alternative strategies side-by-side — current selection, same TAVI + BVF (if applicable), one size larger, alternative platform (BE↔SE cross-class) — with PPM category color-coded for at-a-glance pre-procedural decision support
- **EOA database expanded**: Crown PRT (LivaNova) and Hancock II Ultra (Medtronic) added to surgical bioprostheses; Lotus / Lotus Edge (Boston Scientific) added to TAVI devices with visible withdrawn-device warning
- **TAVI EOA references upgraded** from manufacturer specs to Hahn 2018 peer-reviewed (PARTNER + CoreValve US Pivotal + Evolut R IDE) for Sapien family, CoreValve, Evolut R
- **TAV-in-TAV mode** for redo-TAVI scenarios with empirical constraining factors (Akodad 2023, Sathananthan 2021)
- **Sensitivity analysis "What if"** in Measured mode: identifies AVAi fragility against ±% perturbations of LVOT VTI, LVOT diameter, SV, mean ΔP, baseline pressure, POST aortic pressure
- **Cross-checks (data consistency)** active in all three modes: gradient direction, AVA direction, Bernoulli coherence, DVI plausibility, Zao physiological range, EOA-size mismatch detection
- **Bioprosthetic Valve Fracture (BVF) toggle** in Measured mode (refs Allen 2017, Chhatriwalla 2017)
- **Confidence intervals (opt-in)** with mode-specific coefficients of variation (cv 0.18 Predicted, 0.14 Measured, 0.13 Echo post)
- **Internationalization**: complete IT/EN with persistent language preference
- **Override EOA fields** for both surgical valve and intended TAVI (with informational tooltips)
- **Procedure date / echo date** with auto-calculated days/months interval
- **Onboarding tour** (5-step) and **dark mode** with premium navy palette
- **Range warnings** on input fields with physiological out-of-range detection

### v1.0 (initial release)
- Three independent modes (Predicted, Measured, Echo post)
- Hybrid Gorlin methodology in Measured mode
- Pibarot 2009 EOA database for surgical valves; manufacturer data for TAVI devices
- Bapat 2014 VIV constraining model
- VARC-3 SVD classification
- PPM thresholds per Pibarot 2019 (standard + BMI ≥ 30)
- Privacy-first design (localStorage only, no telemetry)
- Installable as PWA with offline support

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
