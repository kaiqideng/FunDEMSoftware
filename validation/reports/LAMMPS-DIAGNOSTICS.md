# LAMMPS diagnostic exclusions

Date: 29 October 2026. Five completed excluded attempts retain 22,325 frames; startup preflight contributes zero executions/frames. These are not official analytic FAIL outcomes or four-run validations.

- [dem04-oblique-grazing](../dem04-oblique-grazing/DIAGNOSTIC-REPORT.md): one CPU-only source-history mapping counterexample, 4,863 native frames.
- [dem06-spin-wall](../dem06-spin-wall/DIAGNOSTIC-REPORT.md): one CPU-only source-history mapping counterexample, 1,659 native frames.
- [dem06-mindlin-spin-heavy](../dem06-mindlin-spin-heavy/DIAGNOSTIC-REPORT.md): one CPU-only source-history mapping counterexample, 1,801 native frames.

The wrong E-times2 Tsuji wall mapping contributes 2 superseded base runs / 14,002 frames; corrected canonical runs are separate. Both source and native E_eff equal E/[2(1-nu^2)]. The cylinder padding rejection occurred before any integration.

Excluded-attempt ledger (retained locally; not included) | [PDF](LAMMPS-DIAGNOSTICS.pdf) | Source modulus correction (retained locally; not included).
