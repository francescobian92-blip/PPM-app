# VIV PPM Calculator v4.1.1

**Release date**: 29 April 2026
**Type**: Patch release (data integrity + device cleanup)
**Live URL**: https://francescobian92-blip.github.io/PPM-app/

---

## 🐛 Critical bug fixes (data integrity)

This release fixes **4 numerical errors** in the surgical valve and TAVI databases that affected Predicted-mode AVAi calculations.

### 1. Trifecta 27/29 mm sizes were swapped

**Before**: `27mm = 2.7 cm²`, `29mm = 2.35 cm²` (29mm < 27mm, physically impossible)
**After**: corrected to FDA P100029 + Bavaria 2014 values

| Size | Before | After  |
|------|--------|--------|
| 19mm | 1.4    | 1.58   |
| 21mm | 1.7    | 1.83   |
| 23mm | 2.0    | 2.07   |
| 25mm | 2.3    | 2.30   |
| 27mm | 2.7    | **2.34** ⚠️ |
| 29mm | 2.35   | **2.50** ⚠️ |

Source: FDA P100029 Trifecta SSED Table 7 + Bavaria JE et al. *Ann Thorac Surg* 2014;97:1574-80.

### 2. Trifecta GT — same correction

Recalibrated alongside Trifecta classico, slightly higher EOA per Glide Technology.

### 3. Hancock II 29mm too low

**Before**: `29mm = 1.6 cm²` (equal to 27mm — violated monotonicity)
**After**: `29mm = 1.9 cm²`

| Size | Before | After |
|------|--------|-------|
| 21mm | 1.2    | 1.2   |
| 23mm | 1.3    | **1.4** |
| 25mm | 1.5    | 1.5   |
| 27mm | 1.6    | **1.7** |
| 29mm | 1.6    | **1.9** ⚠️ |

Source: Pibarot & Dumesnil 2009 *Circulation* Table 1.

### 4. Evolut R 26mm below 23mm

**Before**: `26mm = 1.69 cm²` (lower than `23mm = 1.70 cm²`)
**After**: `26mm = 1.85 cm²`

| Size | Before | After |
|------|--------|-------|
| 23mm | 1.70   | 1.70  |
| 26mm | 1.69   | **1.85** ⚠️ |
| 29mm | 1.97   | 2.00  |
| 34mm | 2.60   | 2.55  |

Source: Hahn RT et al. *JACC Cardiovasc Imaging* 2019 Table 4 (Evolut R US IDE).

---

## 🗑️ Device cleanup

- **Removed**: `Acurate neo` (1st gen, no longer manufactured) from TAVI database
- **Kept**: `Acurate neo2` (current Boston Scientific model)
- **Auto-migration**: schema v4 → v5 silently migrates any saved state with `taviModel: 'Acurate neo'` to `'Acurate neo2'` on next load

---

## 🎨 UX improvements

- **Re-check POST button** now visible in both Quick and Detailed result modes (was Detailed-only). This is the main call-to-action when PPM is moderate/severe and shouldn't depend on the user toggling detail level.
- **Mobile header overflow fixed** on iPhone-class viewports:
  - ≤420px: hide tour replay (`?`) and CI toggle (`σ`) buttons
  - ≤340px: also hide theme toggle (`🌙`)
  - Reset button now fits within the viewport on iPhone 12 Pro / SE / equivalent

---

## ✅ Verified end-to-end

All algorithms re-audited and analytically verified:

- **BSA**: DuBois 1916 + Mosteller 1987 (test 70kg/170cm → 1.81/1.82 ✓)
- **LVOT area**: π × (lvotD/20)² = 3.46 cm² for 21mm ✓
- **Stroke volume**: aL × VTI_LVOT
- **Echo AVA**: continuity equation
- **Hybrid Gorlin** (Bianchini 2024): AVA = MAP / (Zao × 44.3 × √ΔP), with Zao = MAP × LVET / SV
- **VIV adjustment**: Bapat 2014 leaflet design factors (0.92/0.85/0.75/0.90) + small SHV penalty + supra-annular bonus
- **TAV-in-TAV**: Landes 2020 + Tang 2021 size-stratified factors
- **PPM thresholds**: Pibarot 2019 Table 1 (standard + obese)
- **BVF/BVR gain**: Allen 2017 (+0.15 for fracturable, +0.07 for partial), Meier 2023 (+0.10 for remodelable)

All 40 valve definitions pass:
- Size monotonicity (EOA non-decreasing)
- Sanity check (EOA / geometric area ratio in [0.25, 1.5])

---

## 🧪 Regression suite — 5/5 pass

| Test                  | Result |
|-----------------------|--------|
| Healthcheck (14)      | ✓ pass |
| Reset (3 modes)       | ✓ pass |
| PPM classification    | ✓ pass |
| Stepper +/- clamps    | ✓ pass |
| Echo timing warning   | ✓ pass |

E2E clinical scenarios verified:
- Predicted: Magna Ease 21 + Sapien 3 23 in 60kg/160cm patient → MODERATE PPM ✓
- Measured: invasive data → AVA-Gorlin ≈ 1.41 cm², AVAi ≈ 0.86 → NO PPM ✓
- Schema v4 → v5 migration: Acurate neo automatically renamed to neo2 ✓
- Mobile 390px: Reset button visible, no header overflow ✓

---

## 📊 Performance (unchanged)

- File size: 302 KB (single-file)
- Initial load: 796ms
- First paint: 882ms
- JS heap: 9.5 MB
- DOM nodes: 131 (home)
- Scripts: 1
- Console statements: 4 (legitimate: schema migration log + error handling)
- TODO/FIXME: 0

---

## 🔄 Schema migration

Bumped from v4 to **v5**:

```
v4 → v5: rename 'Acurate neo' → 'Acurate neo2' in s.P.taviModel,
         s.D.taviModelM, s.D.degeneratedTaviModel, s.P.degeneratedTaviModel
         (legacy 1st-gen Acurate neo no longer in TAVI_DB)
```

Migration is **automatic and silent** for existing users — they will see Acurate neo2 selected on next load if they had the old neo selected.

---

## 📁 Deliverables

All files in `/dist/`:
- `index.html` (302 KB, v4.1.1, single-file)
- `sw.js` (cache `viv-tavi-v4-1-1`)
- `manifest.json` (v4.1.1)
- `CITATION.cff` (v4.1.1)
- `README.md` (badge v4.1.1)

---

## 🙏 Acknowledgments

Bug discoveries from rigorous data audit. Primary source is Bianchini et al. *Cardiovasc Revasc Med* 2024 (hybrid Gorlin methodology paper). Algorithm review against:
- Pibarot & Dumesnil 2009 *Circulation*
- Bapat et al. 2014 *JACC Intv*
- Hahn et al. 2019 *JACC Imaging*
- Pibarot et al. 2019 *JACC Imaging* (PPM thresholds)
- Allen et al. 2017 *JTCVS* + 2021 *Ann Cardiothorac Surg* (BVF)
- Meier et al. 2023 *JACC Intv* (BVR)
- Landes et al. 2020 *JACC* + Tang et al. 2021 *JACC Intv* (redo-TAVR)
- ESC/EACTS 2025 guidelines (PPM thresholds incl. obese)
