# BCS Class II End-to-End PBBM Pipeline
**PSD → Dissolution → IVIVC → GI-PBPK → Biowaiver Assessment | Python**

## Overview
Complete physiologically based biopharmaceutics modeling (PBBM) pipeline
for a BCS Class II drug — from raw physicochemical properties through particle
size distribution fitting, multi-bin Noyes-Whitney dissolution simulation,
IVIVC Level A correlation, 7-compartment GI-PBPK absorption modeling, and
final PBBM-based biowaiver assessment with dissolution safe space determination.

Built to directly support the FDA Division of Biopharmaceutics (OPQ/ONDP/CDER)
workflow for developing predictive dissolution methods for medium and high risk
solid oral IR products — the focus of Dr. Bhagwant Rege's PBBM model library project.

**Drug:** Ibuprofen (BCS Class II prototype — low solubility, high permeability)  
**Dose:** 400 mg oral IR tablet

---

## Why BCS Class II Is the Hard Problem

```
BCS Class I  (high S, high P):  Dissolution rarely limits absorption → biowaiver available
BCS Class II (low S, high P):   Dissolution controls absorption → high risk
BCS Class III (high S, low P):  Permeability limits → dissolution less critical
BCS Class IV (low S, low P):    Both limit → worst case
```

For BCS Class II drugs:
- Dose Number (Dn = Dose / S×250mL) >> 1 at gastric pH
- Particle size directly controls dissolution rate (∝ 1/r)
- A 6× reduction in D50 (150μm → 25μm) gives ~6× faster dissolution
- PBBM required to justify dissolution specifications
- BCS biowaiver NOT available — but PBBM supports dissolution spec setting

---

## Full Pipeline — 5 Steps

### Step 0 — Physicochemical Characterization
```
Henderson-Hasselbalch pH-dependent solubility:
  S(pH) = S_intrinsic × (1 + 10^(pH - pKa))   [weak acid]

Ibuprofen:
  pKa = 4.43 | logP = 3.97 | MW = 206 g/mol
  S_intrinsic = 0.045 mg/mL
  S at pH 1.2 (SGF):   0.05 mg/mL → Dn = 32 (very high risk)
  S at pH 6.8 (FaSSIF): 0.72 mg/mL → Dn = 2.2 (still limited)
```

### Step 1 — Particle Size Distribution
```
Log-normal PSD from laser diffraction data (D10, D50, D90):
  μ = ln(D50)
  σ = [ln(D90) - ln(D10)] / (2 × 1.2816)

Three grades compared:
  Reference:  D50 = 150 μm (coarse milling)
  Micronized: D50 = 25 μm  (standard micronization)
  Nano:       D50 = 1 μm   (nanosizing)

Discretized into 10 bins for multi-bin dissolution model
```

### Step 2 — In Vitro Dissolution (Noyes-Whitney Multi-bin)
```
For each particle bin i:
  dM_i/dt = -(3 × D_aq × (S_pH - C_bulk)) / (ρ × h × r_i) × M_i

Particle shrinkage (cube-root law):
  r_i(t) = r_i0 × (M_i/M_i0)^(1/3)

Media simulated:
  SGF pH 1.2 (gastric) | FaSSIF pH 6.8 (fasted intestinal)
  FeSSIF pH 5.0 (fed) | PBS pH 7.4

Key result: Micronized 87% dissolved at 30 min in FaSSIF
            Coarse < 15% at 30 min → dissolution failure
```

### Step 3 — IVIVC Level A
```
Wagner-Nelson deconvolution → fraction absorbed Fa(t):
  Fa(t) = [C(t) + ke × AUC(0→t)] / [ke × AUC(0→∞)]

Weibull fit to in vitro dissolution:
  F_vitro(t) = 1 - exp(-(t/α)^β)

IVIVC correlation: F_vitro vs Fa — R² > 0.90 → Level A established
FDA %PE validation: Cmax and AUC < 10% prediction error
```

### Step 4 — GI-PBPK (7-Compartment Absorption)
```
Physiological segments (Willmann 2004):
  Stomach | Duodenum | Jejunum×2 | Ileum×2 | Colon

Per segment:
  - pH-dependent dissolution (Noyes-Whitney)
  - First-order absorption proportional to Peff × surface area
  - Transit between segments at physiological rates

Systemic PK: 2-compartment model (CL=8 L/h, Vc=10 L, Vp=5 L)
```

### Step 5 — Biowaiver Assessment
```
f2 similarity factor (FDA method):
  f2 = 50 × log10(100 / √(1 + Σ(R_t - T_t)² / n))
  f2 ≥ 50 → similar profiles

Virtual BE (coarse vs micronized):
  AUC ratio and Cmax ratio within 80-125%?

Dissolution safe space:
  D50 → % dissolved at 30 min relationship
  Identifies maximum particle size for adequate exposure

PBBM-based dissolution specification:
  NLT 85% in 45 min in FaSSIF pH 6.8
```

---

## Key Results

| Analysis | Reference (150μm) | Micronized (25μm) | Nano (1μm) |
|---|---|---|---|
| % dissolved at 30 min (FaSSIF) | < 15% | ~87% | ~100% |
| IVIVC Level A R² | — | 0.90+ | — |
| f2 (vs micronized) | < 50 (fail) | Reference | > 80 |
| AUC ratio (vs micronized) | ~65% (fail BE) | 100% | ~105% |
| Cmax ratio | ~55% (fail BE) | 100% | ~108% |

**PBBM recommendation:** D50 < 30 μm required; FaSSIF pH 6.8 as biopredictive medium; spec NLT 85% in 45 min

---

## Regulatory Framework

| Document | Application |
|---|---|
| FDA Guidance: Dissolution Testing of IR Dosage Forms (1997) | USP Apparatus 2, paddle conditions |
| FDA Guidance: Waiver of In Vivo BA/BE — BCS (2017) | BCS Class II: biowaiver NOT available |
| FDA Guidance: IVIVC for ER (1997) | Level A principles applied to IR |
| USP <711> Dissolution | Method requirements |
| USP <1092> Dissolution Procedure | Development and validation |
| FDA/M-CERSI PBBM Workshop (2023) — Rege et al. | PBBM best practices for dissolution safe space |
| EMA PBPK Guideline (2018) | Biorelevant dissolution requirements |

---

## Connection to FDA PBBM Model Library Project

This pipeline directly implements the methodology described in Dr. Bhagwant Rege's
Division of Biopharmaceutics project (FDA-CDER-2026-0108):

> *"Develop a PBBM/PBPK model library for the most commonly prescribed medium
> and high risk drug products that assessors can readily use in their assessment
> of dissolution methods."*

Each step of this pipeline corresponds to a component of that model library:
- PSD fitting → input preparation module
- Noyes-Whitney dissolution → dissolution prediction module
- IVIVC → in vitro-in vivo linkage module
- GI-PBPK → absorption prediction module
- Biowaiver assessment → regulatory decision support module

---

## Files
- `bcs2_end_to_end_pipeline.ipynb` — Python implementation
- `bcs2_dissolution_profiles.csv` — dissolution data all conditions
- `bcs2_pk_metrics.csv` — AUC, Cmax, BE ratios by particle grade
- `bcs2_dissolution_safe_space.csv` — D50 vs % dissolved relationship

## Installation
```bash
pip install numpy pandas scipy matplotlib
```

---

## References
1. FDA Guidance: Dissolution Testing of IR Solid Oral Dosage Forms (1997)
2. FDA Guidance: Waiver of In Vivo BA/BE Studies for IR Solid Oral Dosage
   Forms — BCS (2017)
3. FDA Guidance: Immediate Release Solid Oral Dosage Forms — Scale-Up and
   Post-Approval Changes (SUPAC-IR, 1995)
4. Rege BD, Division of Biopharmaceutics FDA/M-CERSI PBBM Best Practices
   Workshop (2023) — patient-centric quality standards
5. Noyes AA, Whitney WR. The rate of solution of solid substances in their
   own solutions. J Am Chem Soc 1897;19:930-934
6. Wagner JG, Nelson E. Percent absorbed time plots derived from blood level
   and/or urinary excretion data. J Pharm Sci 1964;53(11):1392-1403
7. Willmann S et al. Development of a physiology-based whole-body PBPK model.
   J Pharmacokinet Pharmacodyn 2004;31(6):461-492
8. Geisslinger G et al. Pharmacokinetics of ibuprofen enantiomers.
   Eur J Clin Pharmacol 1989;37:423-426

---

## Author
Nadia Tasnim Ahmed, PhD
PBPK/PBBM Modeling · Biopharmaceutics · Regulatory Science
github.com/ahmedn12
