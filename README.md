# FunDEM Software

FunDEM Workbench is a desktop application for Windows and Apple Silicon macOS, for preparing, running, and reviewing particle-based simulations. It combines CPU-based DEM and SPH simulation with GPU-accelerated visualization. The package includes the FunDEMBeta computation core; users do not need to compile anything.

This public repository distributes the packaged application and English documentation. The application and computation-core source code remain private and are not published here.

## Download

[Download the latest Windows x64 package](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-Windows-x64.zip)

[Download the matching English user manual](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/USER_MANUAL.md)

These stable links follow the latest published release. See the [latest release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest) for the current version and available downloads.

### macOS Apple Silicon

- [macOS Apple Silicon ZIP](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-macOS-arm64.zip)
- [macOS Apple Silicon DMG](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-macOS-arm64.dmg)
- [English macOS guide](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/MACOS_GUIDE.md)

The macOS target requires an Apple Silicon Mac running macOS 14 or later. Simulation runs on the CPU; this package does not include CUDA or support CUDA solver execution. Visualization uses the Mac's graphics hardware. Intel Macs are not included in this distribution.

## Start on Windows

1. Download and extract `FunDEM-Workbench-Windows-x64.zip`.
2. Keep the extracted directory intact.
3. Run `FunDEM.exe`.
4. Read `docs/USER_MANUAL.md` in the extracted package, or download the matching manual above, for project setup, simulation, playback, export, and post-processing.
5. Open a project from `examples` to explore its model and run a simulation.

The six bundled examples cover Gomboc self-righting, a physically interlocked chain, bonded cloth falling onto a box, dam-break flow around a square column, Brazil-nut segregation, and superellipsoids in a rotating drum. These are demonstration projects; their presence is not a claim of experimental validation.

In the Windows x64 package, keep the runtime libraries, `platforms`, `force-modules`, example assets, documentation, and licenses with the executable. The package includes optional particle-force modules and supports recorded-frame playback, animation export, and VTU output for further analysis.

## Start on macOS

1. Download either the DMG or ZIP above. For the DMG, open it and drag `FunDEM.app` into Applications, then eject the disk image. For the ZIP, extract it and move the complete `FunDEM.app` into Applications.
2. Open `FunDEM.app`. Keep its contents intact; users do not need to compile the application or install CUDA.
3. Read the [macOS guide](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/MACOS_GUIDE.md). The application bundle also contains `Contents/Resources/docs/USER_MANUAL.md` and the six projects in `Contents/Resources/examples`; Finder's **Show Package Contents** reveals these folders.
4. Check the Output directory before running a project. Relative output names resolve under `Documents/FunDEM` on macOS; an explicit absolute output path remains unchanged, so projects transferred from another computer may need a different output directory.

The macOS package uses an **ad-hoc signature**. It is **not signed with an Apple Developer ID and has not been notarized by Apple**. If Gatekeeper blocks the first launch, proceed only if you trust the download and have verified its source: first try opening the app, then follow Apple's application-specific **System Settings → Privacy & Security → Open Anyway** procedure. See [Apple's official instructions](https://support.apple.com/en-us/102445). Do not globally disable Gatekeeper or other macOS security protections. If macOS reports malware or a damaged application, stop and obtain a verified package instead of bypassing the warning.

## Offline operation and platform security

Built-in simulation, visualization, playback, and file export operate locally. They do not require an online account, online activation, an incoming network port, or a firewall exception. Third-party force modules and user-selected network file locations can have their own network requirements.

Network firewalls are separate from application trust checks. The Windows application is currently unsigned, so SmartScreen or Smart App Control may warn or block it; the Mac signature limitations are described above. No claim is made that every antivirus product or managed-computer policy will allow execution. Do not disable your firewall or antivirus to run FunDEM. Verify downloads from this repository, and consult your administrator if your organization's policy blocks the application. Microsoft notes that even a newly signed Windows release can receive a reputation warning; see its [SmartScreen guidance](https://learn.microsoft.com/en-us/windows/apps/package-and-deploy/smartscreen-reputation).
