# FunDEM DEM Verification Reports

English report-only publication prepared on 30 October 2026 from retained verification records dated 28-29 October 2026.

## Scope and interpretation

- This collection contains **38 case reports: 35 LAMMPS-derived variants and 3 PFC-derived verification cases**. The original reported outcomes are **36 PASS and 2 FAIL**. Both failed Mindlin sliding-to-rolling cases are retained without changing their acceptance criteria or conclusions.
- Each case has its original English PDF and a browsable Markdown report. PDFs are copied byte for byte. Markdown changes are limited to replacing links to omitted local evidence with plain labels; scientific parameters, measured results, failures and limitations remain unchanged.
- These independent FunDEM comparison reports use public reference examples and analytic checks. They are not endorsements, certifications, or reports issued by LAMMPS or Itasca/PFC. The original reports state whether reference software was actually run; no local PFC execution is claimed here.
- The reports identify their numerical libraries and historical build scope. They do **not** establish byte identity with the latest packaged GUI executable or validate every current application feature.
- PASS applies only to the stated physics, parameter mappings, tolerances and endpoints. PFC coverage remains partial; consult the coverage review before generalizing.

## Case index

| Reference | Case | Original outcome | Report |
|---|---|---|---|
| LAMMPS | dem01-hertz-tsuji | **PASS** | [Markdown](dem01-hertz-tsuji/REPORT.md) / [PDF](dem01-hertz-tsuji/REPORT.pdf) |
| LAMMPS | dem01-hertz-twosphere | **PASS** | [Markdown](dem01-hertz-twosphere/REPORT.md) / [PDF](dem01-hertz-twosphere/REPORT.pdf) |
| LAMMPS | dem01-hooke-2d-lj | **PASS** | [Markdown](dem01-hooke-2d-lj/REPORT.md) / [PDF](dem01-hooke-2d-lj/REPORT.pdf) |
| LAMMPS | dem01-hooke-massvel | **PASS** | [Markdown](dem01-hooke-massvel/REPORT.md) / [PDF](dem01-hooke-massvel/REPORT.pdf) |
| LAMMPS | dem01-hooke-twosphere | **PASS** | [Markdown](dem01-hooke-twosphere/REPORT.md) / [PDF](dem01-hooke-twosphere/REPORT.pdf) |
| LAMMPS | dem01-hooke-twosphere-e02 | **PASS** | [Markdown](dem01-hooke-twosphere-e02/REPORT.md) / [PDF](dem01-hooke-twosphere-e02/REPORT.pdf) |
| LAMMPS | dem01-hooke-twosphere-e08 | **PASS** | [Markdown](dem01-hooke-twosphere-e08/REPORT.md) / [PDF](dem01-hooke-twosphere-e08/REPORT.pdf) |
| LAMMPS | dem01-hooke-veldamp | **PASS** | [Markdown](dem01-hooke-veldamp/REPORT.md) / [PDF](dem01-hooke-veldamp/REPORT.pdf) |
| LAMMPS | dem01-hookehistory-twosphere | **PASS** | [Markdown](dem01-hookehistory-twosphere/REPORT.md) / [PDF](dem01-hookehistory-twosphere/REPORT.pdf) |
| LAMMPS | dem01-legacy-hooke-twosphere | **PASS** | [Markdown](dem01-legacy-hooke-twosphere/REPORT.md) / [PDF](dem01-legacy-hooke-twosphere/REPORT.pdf) |
| LAMMPS | dem01-legacy-hookehistory-twosphere | **PASS** | [Markdown](dem01-legacy-hookehistory-twosphere/REPORT.md) / [PDF](dem01-legacy-hookehistory-twosphere/REPORT.pdf) |
| LAMMPS | dem02-hertz-peak | **PASS** | [Markdown](dem02-hertz-peak/REPORT.md) / [PDF](dem02-hertz-peak/REPORT.pdf) |
| LAMMPS | dem02-hertz-twosphere | **PASS** | [Markdown](dem02-hertz-twosphere/REPORT.md) / [PDF](dem02-hertz-twosphere/REPORT.pdf) |
| LAMMPS | dem02-hertzmaterial-twosphere | **PASS** | [Markdown](dem02-hertzmaterial-twosphere/REPORT.md) / [PDF](dem02-hertzmaterial-twosphere/REPORT.pdf) |
| LAMMPS | dem02-legacy-hertzhistory-twosphere | **PASS** | [Markdown](dem02-legacy-hertzhistory-twosphere/REPORT.md) / [PDF](dem02-legacy-hertzhistory-twosphere/REPORT.pdf) |
| LAMMPS | dem03-hertz-peak-wall | **PASS** | [Markdown](dem03-hertz-peak-wall/REPORT.md) / [PDF](dem03-hertz-peak-wall/REPORT.pdf) |
| LAMMPS | dem03-hertz-tsuji-wall | **PASS** | [Markdown](dem03-hertz-tsuji-wall/REPORT.md) / [PDF](dem03-hertz-tsuji-wall/REPORT.pdf) |
| LAMMPS | dem03-hertz-wall | **PASS** | [Markdown](dem03-hertz-wall/REPORT.md) / [PDF](dem03-hertz-wall/REPORT.pdf) |
| LAMMPS | dem03-hertzmaterial-wall | **PASS** | [Markdown](dem03-hertzmaterial-wall/REPORT.md) / [PDF](dem03-hertzmaterial-wall/REPORT.pdf) |
| LAMMPS | dem03-legacy-hertzhistory-wall | **PASS** | [Markdown](dem03-legacy-hertzhistory-wall/REPORT.md) / [PDF](dem03-legacy-hertzhistory-wall/REPORT.pdf) |
| LAMMPS | dem04-hertz-oblique | **PASS** | [Markdown](dem04-hertz-oblique/REPORT.md) / [PDF](dem04-hertz-oblique/REPORT.pdf) |
| LAMMPS | dem04-hookehistory-oblique | **PASS** | [Markdown](dem04-hookehistory-oblique/REPORT.md) / [PDF](dem04-hookehistory-oblique/REPORT.pdf) |
| LAMMPS | dem05-hookehistory-slip | **PASS** | [Markdown](dem05-hookehistory-slip/REPORT.md) / [PDF](dem05-hookehistory-slip/REPORT.pdf) |
| LAMMPS | dem05-mindlin-slip | **FAIL** | [Markdown](dem05-mindlin-slip/REPORT.md) / [PDF](dem05-mindlin-slip/REPORT.pdf) |
| LAMMPS | dem05-slip-mu02 | **FAIL** | [Markdown](dem05-slip-mu02/REPORT.md) / [PDF](dem05-slip-mu02/REPORT.pdf) |
| LAMMPS | dem05-sliptime | **PASS** | [Markdown](dem05-sliptime/REPORT.md) / [PDF](dem05-sliptime/REPORT.pdf) |
| LAMMPS | dem06-hookehistory-spin | **PASS** | [Markdown](dem06-hookehistory-spin/REPORT.md) / [PDF](dem06-hookehistory-spin/REPORT.pdf) |
| LAMMPS | dem06-spin-identical | **PASS** | [Markdown](dem06-spin-identical/REPORT.md) / [PDF](dem06-spin-identical/REPORT.pdf) |
| LAMMPS | dem07-hooke-rolling | **PASS** | [Markdown](dem07-hooke-rolling/REPORT.md) / [PDF](dem07-hooke-rolling/REPORT.pdf) |
| LAMMPS | dem09-viscous-linear | **PASS** | [Markdown](dem09-viscous-linear/REPORT.md) / [PDF](dem09-viscous-linear/REPORT.pdf) |
| LAMMPS | dem10-freefall | **PASS** | [Markdown](dem10-freefall/REPORT.md) / [PDF](dem10-freefall/REPORT.pdf) |
| LAMMPS | dem10-stack | **PASS** | [Markdown](dem10-stack/REPORT.md) / [PDF](dem10-stack/REPORT.pdf) |
| LAMMPS | dem11-regionwall | **PASS** | [Markdown](dem11-regionwall/REPORT.md) / [PDF](dem11-regionwall/REPORT.pdf) |
| LAMMPS | dem13-twist-sds | **PASS** | [Markdown](dem13-twist-sds/REPORT.md) / [PDF](dem13-twist-sds/REPORT.pdf) |
| LAMMPS | dem15-hooke-oblique | **PASS** | [Markdown](dem15-hooke-oblique/REPORT.md) / [PDF](dem15-hooke-oblique/REPORT.pdf) |
| PFC | pfc-linear-restitution-md0-beta02 | **PASS** | [Markdown](pfc-linear-restitution-md0-beta02/REPORT.md) / [PDF](pfc-linear-restitution-md0-beta02/REPORT.pdf) |
| PFC | pfc-linear-restitution-md0-beta06 | **PASS** | [Markdown](pfc-linear-restitution-md0-beta06/REPORT.md) / [PDF](pfc-linear-restitution-md0-beta06/REPORT.pdf) |
| PFC | pfc-linear-restitution-md0-sweep | **PASS** | [Markdown](pfc-linear-restitution-md0-sweep/REPORT.md) / [PDF](pfc-linear-restitution-md0-sweep/REPORT.pdf) |

## Diagnostics, limitations and coverage

- [LAMMPS excluded/superseded attempts and diagnostics](reports/LAMMPS-DIAGNOSTICS.md) / [original PDF](reports/LAMMPS-DIAGNOSTICS.pdf).
- [PFC verification-index coverage and unsupported operators](PFC-COVERAGE.md).
- [LAMMPS diagnostic: dem04-oblique-grazing](dem04-oblique-grazing/DIAGNOSTIC-REPORT.md).
- [LAMMPS diagnostic: dem06-mindlin-spin-heavy](dem06-mindlin-spin-heavy/DIAGNOSTIC-REPORT.md).
- [LAMMPS diagnostic: dem06-spin-wall](dem06-spin-wall/DIAGNOSTIC-REPORT.md).
- [Conditional-cap source diagnostics](reference/lammps/20261029-expanded-suite/cap-degeneracy-diagnostics.md).
- [LAMMPS active-tangential-history diagnostic exclusions](reference/lammps/20261029-expanded-suite/conditional-history-probe-results.md).
- [LAMMPS official granular verification coverage](reference/lammps/20261029-expanded-suite/coverage-matrix.md).
- [LAMMPS Hertz oblique: proved saturated-trajectory force equivalence](reference/lammps/20261029-expanded-suite/hertz-oblique-saturation-equivalence.md).
- [Research only: official PFC Hertz prescribed normal-loading stage](reference/pfc/hertz-normal-source/FEASIBILITY.md).
- [Diagnostic observation: every-step native result output](tools/2026-10-29-result-store-every-step-output-note.md).
- [Independent contact-law and dimensional-mapping audit: dem01-hooke-2d-lj](tools/dem01-2d-lj-contact-audit.md).
- [DEM01 contact-model mapping audit](tools/dem01-contact-audit.md).
- [Independent contact-law audit: dem01-hookehistory-twosphere](tools/dem01-history-contact-audit.md).
- [Independent contact-law audit: dem01-hooke-twosphere](tools/dem01-hooke-contact-audit.md).
- [dem02 independent contact and analytic audit](tools/dem02-contact-audit.md).
- [Contact and independent physics audit: dem02-hertzmaterial-twosphere](tools/dem02-hertzmaterial-contact-audit.md).
- [Contact and independent physics audit: dem03-hertzmaterial-wall](tools/dem03-hertzmaterial-contact-audit.md).
- [DEM04 Hooke-history oblique-impact contact audit](tools/dem04-contact-audit.md).
- [dem05 contact-model audit](tools/dem05-contact-audit.md).
- [dem05 Hooke-history sliding-to-rolling source and physics audit](tools/dem05-hooke-contact-audit.md).
- [dem05 lower-friction Mindlin slip: contact-model audit](tools/dem05-mu02-contact-audit.md).
- [DEM06 Hooke-history spinning-sphere impact: independent contact audit](tools/dem06-contact-audit.md).
- [dem07 Hooke rolling resistance: independent model audit](tools/dem07-contact-audit.md).
- [LAMMPS dem09: free fall with linear viscous drag](tools/dem09-viscous-linear-contact-audit.md).
- [dem10-stack: source and contact mapping audit](tools/dem10-stack-contact-audit.md).
- [DEM13 SDS twisting-resistance contact audit](tools/dem13-contact-audit.md).
- [PFC bond verification: reviewed against the implemented FunDEM bond path](tools/pfc-bond-feasibility-review.md).
- [PFC Hertz prescribed normal stage: excluded candidate](tools/pfc-hertz-normal-feasibility.md).
- [PFC linear restitution: selected full-dashpot subcase](tools/pfc-linear-beta02-contact-audit.md).
- [PFC linear restitution: selected full-dashpot subcase](tools/pfc-linear-beta06-contact-audit.md).

## Included evidence and deliberately omitted records

The package includes 38 author-created comparison PNG figures, 21 contact/source/physics-feasibility/output audit Markdown documents, three case diagnostic reports, the aggregate diagnostic report, the PFC coverage inventory, and five supplementary author-created reference/feasibility reviews. Public source citations remain available as external links.

This report collection is not a runnable benchmark or a complete raw-data deposit. Native result frames, original and converted projects, per-step trajectory/contact CSVs, execution logs, helper programs, private application source, third-party program distributions, downloaded vendor inputs/source fixtures, large JSON analysis records, and duplicate plot-only PDFs are omitted. Omissions do not remove failed cases or change reported results. Original PDFs may still contain local evidence references not supplied here. Markdown marks omitted links as **retained locally; not included**. That phrase describes the source archive's retention claim, not a fresh assertion that every historical target remains accessible.

`PUBLICATION-MANIFEST.json` records original and public hashes for copied reports/figures, original case outcomes, and omitted local-link targets. `SHA256SUMS.txt` covers public files. PDF source and published SHA-256 values are identical. These manifests are integrity records, not independent scientific validation.

No restricted ON-DEM coordinates, third-party executables or third-party source distributions are included.
