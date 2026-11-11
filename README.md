# FunDEM 1.0

FunDEM is a Windows x64 desktop workbench for spherical and level-set discrete
element simulations, including coupled LSDEM + SPH fluid–solid problems.

## Features

- **Setup:** materials, contact material pairs, LS geometries and reusable
  particle templates. Generate single particles, lattices or random populations
  with radius distributions, multiple shapes and reproducible orientations.
- **Simulation:** named Events, particle instances, Bonds, free/periodic
  boundaries and external constraints. Configure motion, force/torque,
  periodic velocity resets, local damping and sphere hydrodynamics.
- **CPU/GPU/Hybrid:** native execution with sequential Event submission,
  Run/Pause, single Step and Reset. Existing particle, Contact, Bond and SPH
  state continues into later Events.
- **SPH:** shared fluid properties, regular Blocks and finite-duration Jets
  coupled to level-set solids.
- **Results:** recorded-frame playback, particle State tables, independent
  particle/Contact/Bond displays, movable legends, Clip and Threshold filters.
- **Plot:** time history, XY curve, Scatter, Histogram and uniaxial-compression
  measurements, with configurable curves/axes, data tables, manual Reload,
  CSV and figure export.
- **Exchange/export:** import solid-particle instances from DAT, export the
  accepted Results frame to DAT, and save PNG images or MP4 animations.

## Windows packages

[Download FunDEM 1.0](https://github.com/kaiqideng/FunDEMSoftware/releases/tag/v1.0)

| Portable ZIP | Execution modes |
| --- | --- |
| CPU | CPU; no NVIDIA GPU required |
| CUDA | CPU without an NVIDIA GPU; GPU/Hybrid with a compatible NVIDIA GPU and installed driver |

CPU is the default in both packages. Hybrid places Sphere/SPH computation on
GPU and LS on CPU. GPU/Hybrid require the CUDA package and supported hardware;
unavailable modes report an error instead of silently using CPU. No compiler, source checkout or
local CUDA toolkit is required.

Verify the supplied SHA-256 checksums, extract the complete ZIP and start
`Windows/FunDEM.exe`. Keep all matching libraries, plugins, example data,
documentation and notices together. Save working projects/results outside
the installation folder.

## Included examples

**File > Open Project > Examples** opens seven editable projects:

1. Gömböc self-righting
2. Interlocked chain
3. Cloth drop into a box
4. Dam break around a square column
5. Brazil nut segregation
6. Superellipsoid mixing drum
7. Random-shape particle column

Each opens as a separate writable copy and does not run automatically.
The ON-DEM periodic-shear Benchmark importer accepts the user's own authorized
input; the author's coordinate dataset is not redistributed.

## Files and documentation

Projects use `.fundem.json`; retain their required `.fundem-data` siblings.
Use the updated projects: retired development fields/layouts are not silently
converted. State and Animation have separate recording intervals.
Results are observations, not solver restart checkpoints. DAT exports one
accepted Results frame and does not preserve SPH or Contact/Bond histories.
Display filters do not change physical data, Plot queries or DAT export.

See the [User Manual](USER_MANUAL.md), [Example Guide](EXAMPLE_GUIDE.md) and
[Workflow Guide](WORKFLOW_IMPROVEMENTS.md).

## Numerical core and notices

The numerical core is the separately open-source
[FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta).
Its [validation directory](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation)
contains comparison cases, reports and reproducibility instructions.
Each report applies to its documented revision, parameters and criteria;
examples and regression checks are not universal physical validation.

This repository distributes application packages and user documentation, not
private desktop source. FunDEMBeta is Apache-2.0 licensed; this does not put
all desktop and third-party components under the same license. Preserve the
[core attribution](FUNDEM_CORE.md), [third-party notices](THIRD_PARTY_NOTICES.md)
and original license texts in each package. A GitHub release does not establish
Microsoft Store submission, certification or availability.
