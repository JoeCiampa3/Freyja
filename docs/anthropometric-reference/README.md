# Freyja — Anthropometric & Joint Reference

Body segment inertial parameters (BSIP), joint coordinate-system definitions, joint-centre placement, and range-of-motion data for the **Freyja** biomimetic humanoid project.

This repository holds the single authoritative reference for every anthropometric and joint quantity the mechanical and control design depends on. Mass, centre-of-mass, and full inertia tensors per segment; ISB joint frames and sign conventions; joint-centre regressions; and anatomical-vs-functional range of motion — all anchored to named primary literature, instantiated for a **65 kg / 1700 mm female** target, and built so a change to body mass or a segment length propagates through every derived value.

---

## Why this exists

Downstream CAD and controller decisions should reference one provenance-tracked source rather than re-deriving numbers or pulling them from memory. The workbook is parametric: normalized literature values are entered once, and the 65 kg / 1700 mm instantiation is computed by formula. Change the body mass or a segment length in one cell and the masses, CoM offsets, inertia tensors, and ROM headroom all recompute.

Design philosophy is literature-primary: every quantity is committed to a named primary source (with an explicit edition/corrigendum where relevant) and, where available, a secondary cross-check. Human values are treated as a sized reference for the robot, not an automatic design target — deliberate departures are logged rather than silently absorbed.

---

## Files

| File | Role |
|------|------|
| `Freyja_BSIP_Reference.xlsx` | **Authoritative source.** Live formulas; all derived values compute from input cells. Make every edit here. |
| `*.csv` (one per sheet) | Flat exports for in-browser viewing and diffing on GitHub. **Static snapshots — see note below.** |
| `README.md` | This file. |

### ⚠️ Note on the CSV files

GitHub does not render `.xlsx` natively in the file browser, so each sheet is also exported as a `.csv` for viewing and version-diffing online.

**The CSVs are static snapshots, not live copies.** They contain the computed values *as of export time* and do **not** recompute. The `.xlsx` is the only canonical artifact:

- Make all edits in the `.xlsx`. Treat the CSVs as read-only views.
- After changing any input in the `.xlsx` (body mass, stature, a segment length, a transcribed source value), **regenerate the CSVs** or they will silently drift out of sync with the workbook.
- Formulas, cell comments, colour-coding (input vs computed vs cross-sheet link), and the conditional flags do not survive the CSV export — they exist only in the `.xlsx`.

---

## Document structure

The workbook has six sheets, in two groups.

**Anthropometry (BSIP)**

- **`README & Conventions`** — purpose, instantiation, coordinate-system and sign conventions, units policy, the BSIP source commitment, and key caveats.
- **`BSIP Reference`** — per-segment table: segment length, mass fraction → mass (kg), CoM vector (% of length → mm), and the full inertia tensor (radii of gyration → kg·m², including signed products of inertia). A count-weighted mass-fraction sum at the bottom reconstructs body mass as a transcription tripwire.
- **`Validation`** — target (linked from `BSIP Reference`) vs achieved (from CAD mass-properties), with auto-computed deviations and a deviation log.

**Joints**

- **`Joint Sources & Conventions`** — the joint source commitment, the verified citation table (below), and the JCS/rotation-sequence/sign conventions.
- **`Joint Definitions`** — one row per joint: proximal/distal segment, joint-centre source and placement, SCS origin, the e1/e2/e3 floating-axis JCS with each axis's clinical motion, rotation sequence, sign conventions, axis-obliquity notes, and a per-joint status/deviation log.
- **`Joint ROM`** — one row per movement direction: anatomical ceiling and its source, functional demand and its source/task, design target, a hard-constraint flag, and an auto-computed **headroom = ceiling − functional** (negative ⇒ the task demands more than population-mean anatomy).

### How the numbers are computed

- `mass (kg) = (mass fraction / 100) × body mass`
- `CoM offset (mm) = (CoM % / 100) × segment length`
- `moment of inertia (kg·m²) = mass × (r% / 100 × L_mm / 1000)²`
- `product of inertia = sign(r) × mass × (|r|% / 100 × L_mm / 1000)²`  *(per Dumas: rᵢⱼ = (1/L)·√(Iᵢⱼ/m); "(i)" in source tables = negative product)*
- `ROM headroom (°) = anatomical ceiling − functional demand`

---

## Sourcing

### Source commitment

| Quantity | Primary | Cross-check / secondary |
|----------|---------|--------------------------|
| Mass, CoM, full inertia tensor | **Dumas 2007** female (+ corrigendum 2007b for Head & Neck; + Dumas 2015 for Thorax & Abdomen) | de Leva 1996 female (limbs only — trunk partitioning differs) |
| Joint coordinate systems & sign conventions | **ISB JCS** — Wu 2002 (ankle, hip, spine), Wu 2005 (upper limb), Grood & Suntay 1983 (knee) | — |
| Joint-centre placement | **Reed 1999** for trunk centres (female coefficients via the Dumas 2007 appendix); **Harrington 2007** override for the hip joint centre | Bell 1990 (HJC) |
| ROM — anatomical ceiling | **Soucie 2011** (female, young-adult) | Boone & Azen 1979 (male-only); AAOS |
| ROM — functional demand | **Riener 2002** (stairs); Winter (level gait) | task-specific kinematics |

### Verified citations

DOIs confirmed against the primary sources / publisher records.

| Source | Reference | DOI / ID |
|--------|-----------|----------|
| Dumas et al. 2007 | *Adjustments to McConville et al. and Young et al. body segment inertial parameters.* J Biomech 40(3):543–553 | 10.1016/j.jbiomech.2006.02.013 |
| Dumas et al. 2007b | *Corrigendum* [Head & Neck]. J Biomech 40(7):1651–1652 | 10.1016/j.jbiomech.2006.07.016 |
| Dumas et al. 2015 | *Thorax and abdomen BSIP adjusted from McConville et al. and Young et al.* Int Biomech 2(1):113–118 | 10.1080/23335432.2015.1112244 |
| de Leva 1996 | *Adjustments to Zatsiorsky-Seluyanov's segment inertia parameters.* J Biomech 29(9):1223–1230 | 10.1016/0021-9290(95)00178-6 |
| Wu et al. 2002 | *ISB JCS recommendation, part I: ankle, hip, spine.* J Biomech 35(4):543–548 | 10.1016/s0021-9290(01)00222-6 |
| Wu et al. 2005 | *ISB JCS recommendation, part II: shoulder, elbow, wrist, hand.* J Biomech 38(5):981–992 | 10.1016/j.jbiomech.2004.05.042 |
| Grood & Suntay 1983 | *A joint coordinate system for the clinical description of 3-D motions: knee.* J Biomech Eng 105(2):136–144 | 10.1115/1.3138397 |
| Reed et al. 1999 | *Methods for measuring and representing automobile occupant posture.* SAE 1999-01-0959 | 10.4271/1999-01-0959 |
| Harrington et al. 2007 | *Prediction of the hip joint centre … based on MRI.* J Biomech 40(3):595–602 | 10.1016/j.jbiomech.2006.02.003 |
| Soucie et al. 2011 | *Range of motion measurements: reference values and a database.* Haemophilia 17(3):500–507 | 10.1111/j.1365-2516.2010.02399.x |
| Boone & Azen 1979 | *Normal range of motion of joints in male subjects.* JBJS Am 61(5):756–759 | PMID 457719 |
| Riener et al. 2002 | *Stair ascent and descent at different inclinations.* Gait Posture 15(1):32–44 | 10.1016/s0966-6362(01)00162-x |
| Winter (Thomas, Zeni & Winter) 2022 | *Biomechanics and Motor Control of Human Movement,* 5th ed., Wiley | ISBN 9781119827023 |

### Provenance notes

- **Population skew.** Dumas female parameters trace to the USAF women of Young et al. (1983) — young, fit; not a population median. Soucie is mixed-sex, ages 2–69 (young-adult female values used here). Boone & Azen is male-only, hence secondary for a female build. Document, don't assume a median.
- **Version handling.** Dumas 2007 originally treats the trunk as a single "Torso" segment; the 2015 paper splits it into Thorax + Abdomen, and the 2007b corrigendum corrects the Head & Neck segment. The workbook uses the corrected/split values and records which source feeds each row.
- **Coordinate coherence.** Joint frames are defined to coincide with the Dumas segment frames the BSIP tensors are expressed in (e.g., the ISB pelvic frame = the Dumas pelvis SCS), so joint definitions and inertial data share one set of frames with no transform between them.

---

## Conventions

- **Instantiation:** body mass 65 kg, stature 1700 mm (input cells on `BSIP Reference`).
- **Axes (right-handed):** +x anterior, +y cranial (up), +z to the subject's right. Each segment carries its own ISB/Dumas SCS.
- **Units:** mass kg; length and CoM offsets mm; inertia kg·m²; angles degrees. Normalized literature values are entered exactly as published (mass as a body-mass fraction; CoM and radii of gyration as percent of segment length).
- **Rotation sequence:** ISB Cardan/Euler per joint (Grood–Suntay floating-axis convention: e1 fixed to the proximal segment, e3 to the distal, e2 floating).
- **Reference pose:** anatomical neutral standing = all joint angles zero.

---

## Notable finding (ankle dorsiflexion)

The two-column ROM design surfaced a result worth recording. For ankle dorsiflexion: the female young-adult **anatomical ceiling is 13.8°** (Soucie 2011, mean), while **stair-descent demand is ~15–20°** (Riener 2002, normal to steepest inclination). The headroom is therefore **negative (≈ −4°)** — stair descent already requires more dorsiflexion than the average woman has, which is the biomechanical reason stairs are demanding. Freyja's **≥25° design target** clears even the steepest measured stair demand and sits at roughly the +2 SD tail of the female distribution (within the human maximum of 36°). The ankle must be engineered for the flexible tail of human ROM — logged as a deliberate above-median departure, not designed to the typical value. By contrast, hip- and knee-flexion headroom against stair demands is large; anatomy is not the limiter there.

---

## Status

| Block | State |
|-------|-------|
| BSIP — all 10 segments | Complete; instantiated; mass-sum cross-check passing (~96.9%, expected for the Dumas set). |
| Joint Definitions — hip, knee, ankle, lumbosacral, thoracolumbar, cervical | Frames + centres complete (Wu 2002, Grood & Suntay 1983, Harrington 2007). |
| Joint Definitions — shoulder, elbow, wrist | Pending Wu 2005. |
| Joint ROM — lower-limb sagittal | Complete (Soucie 2011 ceiling + Riener 2002 functional). |
| Joint ROM — frontal/transverse, spine, upper limb | Pending (Boone & Azen / AAOS / spine-specific). |

---

## Citing & attribution

The numerical reference values in this document are reproduced from the copyrighted primary sources listed above. This repository is a structured compilation for the Freyja project; it does not supersede or redistribute the original works. For any use of the underlying data, consult and cite the original publications.