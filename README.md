# FunDEM Software

FunDEM Workbench is a portable Windows desktop application for preparing, running, and reviewing particle-based simulations. It combines CPU-based DEM and SPH simulation with GPU-accelerated visualization. The package includes the FunDEMBeta computation core; users do not need to compile anything.

This public repository distributes the packaged application and English documentation. The application and computation-core source code remain private and are not published here.

## Download

[Download the latest Windows x64 package](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-Windows-x64.zip)

[Download the matching English user manual](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/USER_MANUAL.md)

These stable links follow the latest published release. See the [latest release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest) for the current version and available downloads.

## Start

1. Download and extract `FunDEM-Workbench-Windows-x64.zip`.
2. Keep the extracted directory intact.
3. Run `FunDEM.exe`.
4. Read `docs/USER_MANUAL.md` in the extracted package, or download the matching manual above, for project setup, simulation, playback, export, and post-processing.
5. Open a project from `examples` to explore its model and run a simulation.

The six bundled examples cover Gomboc self-righting, a physically interlocked chain, bonded cloth falling onto a box, dam-break flow around a square column, Brazil-nut segregation, and superellipsoids in a rotating drum. These are demonstration projects; their presence is not a claim of experimental validation.

Windows x64 is the supported distribution platform. Keep the runtime libraries, `platforms`, `force-modules`, example assets, documentation, and licenses with the executable. The package includes optional particle-force modules and supports recorded-frame playback, animation export, and VTU output for further analysis.
