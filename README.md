# FunDEM Software

FunDEM 0.4.4 is a Windows x64 desktop workbench for spherical and level-set
DEM and coupled LSDEM-SPH simulations.

- **Setup:** materials, contact material pairs, geometries and reusable particle templates.
- **Simulation:** ordered Events, particle instances, Bonds, external constraints and native CPU/GPU/Hybrid execution.
- **Results:** recorded-frame playback, particle-state inspection, independent particle/contact/Bond displays, movable legends, Clip and Threshold filters.
- **Plot:** time histories, XY curves, scatter plots, histograms and uniaxial-compression measurements, with data tables, CSV and figure export.

Seven editable examples are included. PNG images, MP4 animation and
current-frame solid-particle DAT export are available.

## Download

- [Windows x64 CPU ZIP](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-Windows-x64.zip)
- [Windows x64 CUDA ZIP](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-Windows-CUDA-x64.zip)
- [SHA-256 checksums](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/SHA256SUMS.txt)
- [User Manual](USER_MANUAL.md) and [Example Guide](EXAMPLE_GUIDE.md)

Download the [latest release](https://github.com/kaiqideng/FunDEMSoftware/releases/latest),
verify its published SHA-256, extract the complete archive and open
`Windows/FunDEM.exe`. Keep its matching libraries, plugins, examples,
documentation and license notices together. No compiler or local CUDA toolkit
is required. CPU is the default; GPU and Hybrid require the CUDA package and
a compatible NVIDIA GPU and installed driver. Hybrid runs Sphere/SPH on GPU
and LS on CPU. An unavailable device mode is reported, not silently replaced
by CPU.

The CUDA package targets architectures 75, 80, 86, 89, 90, 100 and 120 with
CUDA 13.2. The release test device is an RTX 5060 (sm_120); compilation for
other architectures is not a claim of hardware testing on every GPU model.
Current releases support Windows x64; no macOS or DMG download is provided.

## New in v0.4.4

- Ordered Event Submit/Withdraw controls and retained-session continuation.
- Unified solid/SPH instance definitions and event-local external constraints.
- Separate State and Animation recording with immutable boundary replacement.
- Results frame-list refresh during playback without jumping to the latest frame.
- Updated Bond configuration and pure-data DAT exchange without repeated centroid correction.
- Shared particle tables, current-frame State inspection and compact playback controls.
- Updated editable examples and stricter runtime/configuration packaging checks.

## Numerical core and validation

FunDEM uses the separately open-source
[FunDEMBeta numerical core](https://github.com/kaiqideng/FunDEMBeta).
Comparison cases, reports and reproducibility instructions are maintained in
[FunDEMBeta validation](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation):

- [LAMMPS comparisons](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation/LAMMPSComparison)
- [PFC comparisons](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation/PFCComparison)
- [ON-DEM comparisons](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation/ONDEMComparison)

Use each case's `REPORT.md` for its maintained presentation. Historical
evidence applies to its documented source revision, parameters and criteria;
it is not a claim that every current desktop example has been physically
validated. Duplicate report downloads are no longer maintained here.

This repository distributes reviewed application packages and user
documentation, not private desktop source. The core's Apache-2.0 license does
not license all desktop or third-party components under the same terms.
Read the [core attribution](FUNDEM_CORE.md) and
[third-party notices](THIRD_PARTY_NOTICES.md).

## Projects, results and examples

Projects use `.fundem.json`. Keep required `.fundem-data` siblings when moving
a project; optional procedural caches can be rebuilt. Retired collection
layouts and numbered format markers are not automatically converted. Use the
updated bundled examples rather than relabeling an old project file.

Submit Events in order before Run/Step. Setup and submitted physical inputs
become read-only, while Display, Results and unsubmitted Events remain editable.
Reset confirms first and withdraws submissions without deleting saved archives.

Results are recorded observations, not resumable solver checkpoints. DAT
exports solid particles from one accepted Results frame and does not preserve
Contact/Bond history or SPH state. Plot Reload is manual. Display and Filters
do not change the recorded physical data.

Examples open as separate writable projects and never run automatically.
The ON-DEM Benchmark importer accepts user-supplied authorized input; its
author's coordinate dataset is not redistributed. Long examples can take
hours, and short regression runs are not full physical validation.

See the [User Manual](USER_MANUAL.md), [Example Guide](EXAMPLE_GUIDE.md) and
[Workflow Guide](WORKFLOW_IMPROVEMENTS.md). Simulation, replay and export run
locally. Download from the official release page and follow your organization's
software policy; do not disable security protections to run the application.

Publishing a GitHub release does not establish Microsoft Store submission,
certification or availability. Each runtime includes its governing license
texts and original component notices.
