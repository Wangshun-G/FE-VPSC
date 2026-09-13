# Core examples for a frame-consistent FE–VPSC workflow

This repository provides **core methodological examples** associated with a sequential one-way FE–VPSC workflow of a dual-phase Mg–Li alloy.

It is intentionally **not a complete release of every production file or script**. The purpose is to make the key constitutive definitions, coordinate conventions, FE-to-VPSC reconstruction procedure, VPSC input-file data format, and kinematic verification examples independently inspectable and reproducible.

## Scope

Included:

- Hill'48 + Voce constitutive **core equations and SDV packing example** used by the Abaqus/Explicit VUMAT workflow.
- A compact Abaqus keyword excerpt showing the principal FE settings used in the manuscript.
- A Python 2.7 / Abaqus 2020 example that reconstructs the spatial velocity-gradient history `L^s(t)` in the fixed initial RD–TD–ND specimen frame from ODB quantities.
- VPSC input-file data format and a small example input file.
- Definitions and verified results of four constitutive-independent kinematic benchmarks; one complete simple-shear Abaqus example is included.
- Documentation of FE parameters, SDV definitions, coordinate frames, and the transfer workflow.

Not included:

- Complete production CAD/roller geometry files or the full production FE mesh.
- Raw ODB files.
- Every production automation/batch script.
- Complete phase texture, `.sx`, calibration, or post-processing datasets.
- All manuscript-specific production VPSC input/output files.

See [NOTICE_SCOPE.md](NOTICE_SCOPE.md) for the exact reproducibility scope.


## Software environment

The examples are written for the environment used in the study:

- Abaqus/Explicit 2020
- Abaqus Python 2.7
- Intel Fortran compiler compatible with Abaqus 2020
- NumPy available to Abaqus Python for the reconstruction example
- VPSC7
——Ph1.tex, Ph1.sx, Ph2.tex, and Ph2.sx are placeholders for the phase-specific texture and single-crystal input files and are not included in this methodological core release.

## Coordinate frames

Three frames are distinguished throughout:

- `c`: current Abaqus corotational material frame.
- `g`: Abaqus global frame.
- `s`: fixed initial specimen frame, with `s1 = RD`, `s2 = TD`, `s3 = ND`.

The tensor supplied to VPSC is the **complete spatial velocity gradient** `L^s(t)` expressed in the fixed initial specimen frame.

For two consecutive saved output states,

```text
Delta R_k = Q_k Q_(k-1)^T
Omega_k^g = Log(Delta R_k) / Delta t_k
Q_(k-1/2) = Exp[0.5 Log(Delta R_k)] Q_(k-1)
L_k^g = Q_(k-1/2) (D_k^c + W_rel,k^c) Q_(k-1/2)^T + Omega_k^g
L_k^s = Q_0^T L_k^g Q_0
```

No deviatoric projection is applied in the core transfer example.

## Repository structure

```text
01_Abaqus_FE/
  vumat_hill48_voce_core_example.f
  abaqus_frf_core_settings_example.inp
  README_FE.md
02_FE_to_VPSC/
  reconstruct_Ls_core_example.py
  coordinate_convention.md
  SDV_mapping.md
  example_output/
03_VPSC/
  example_VAR_VEL_GRAD.dat
  README_VPSC.md
04_Kinematic_Benchmarks/
  simple_shear_example.inp
  analytical_targets.md
  benchmark_results.csv
  README.md
05_Documentation/
  FE_parameters.md
  workflow.md
```

## Quick start

1. Run an Abaqus model that uses the same SDV definitions and writes `S` with local directions plus `SDV1`–`SDV10` at the selected integration point.
2. Edit the user settings at the top of `02_FE_to_VPSC/reconstruct_Ls_core_example.py`.
3. Run:

```bash
abaqus python reconstruct_Ls_core_example.py
```

4. The script writes:

- `reconstructed_Ls_history.csv`
- `VAR_VEL_GRAD_reconstructed.dat`

5. Reference the DAT file in the VPSC7 variable-velocity-gradient process (`IVGVAR = 1`).

## Important implementation note

`relSpinInc / dt` is the **relative spin in the Abaqus corotational frame**, not the absolute spatial spin. Likewise, SDV11–SDV19 (when retained in a production implementation) represent only the intermediate corotational tensor `L_rel^c = D^c + W_rel^c`; they are not directly used as the final VPSC loading tensor.

## Citation

Please cite the associated article when the final bibliographic information becomes available. A template is provided in `CITATION.cff`.
