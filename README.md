# FunDEM Software

FunDEM Workbench is a desktop application for Windows and Apple Silicon macOS, for preparing, running, and reviewing particle-based simulations. It combines CPU-based DEM and SPH simulation with GPU-accelerated visualization. The package includes the FunDEMBeta computation core; users do not need to compile anything.

This public repository distributes the packaged application and English documentation. The Workbench application source remains private. Its computation core is available separately in the [FunDEMBeta source repository](https://github.com/kaiqideng/FunDEMBeta); users of the application package do not need to download or compile it.

## Download

[Download the latest Windows x64 package](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-Windows-x64.zip)

[Download the matching English user manual](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/USER_MANUAL.md)

These stable links follow the latest published release. See the [latest release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest) for the current version and available downloads.

This documentation describes v0.3.39. Use the matching verified platform package from its release page; the latest links change only after the release assets are published. An older package may not include the force-chain display or workflow features described here.

### macOS Apple Silicon

- [macOS Apple Silicon ZIP](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/FunDEM-Workbench-macOS-arm64.zip)
- [English macOS guide](https://github.com/kaiqideng/FunDEMSoftware/releases/latest/download/MACOS_GUIDE.md)

The macOS target requires an Apple Silicon Mac running macOS 14 or later. Simulation runs on the CPU; this package does not include CUDA or support CUDA solver execution. Visualization uses the Mac's graphics hardware. Intel Macs are not included in this distribution.

## New in v0.3.39

- Force Chains now display **Sphere-Sphere**, **Sphere-LSParticle**, and **LSParticle-LSParticle** Contacts. Each actual Contact is drawn from its contact point to each finite-mass owner's center of mass, preserving separate surface Contacts instead of merging them into a center-to-center chain.
- An infinite-mass side is omitted. A finite particle contacting a fixed wall has one branch; two infinite-mass owners produce no visible branch.
- **Normal force**, **Tangential force**, and **Resultant force** remain available. Both branches of a Contact use that Contact's selected magnitude for width and color. Existing Packing selection and Clip Plane controls still filter the displayed Contacts; SPH-fluid Contacts do not generate Force Chains.

The packaged examples, model checks, protected run directories, saved-run archives, quantitative CSV exports, and `FunDEM-cli` workflow remain available. This is a Workbench visualization update; it does not change the FunDEMBeta contact laws or integration algorithms. Model estimates are not physical validation or convergence proofs. Open Results currently accepts saved run archives, not arbitrary raw run directories; archive checksums detect accidental corruption and are not cryptographic authentication. Query charts, cross-run energy comparison, and a parameter-scan GUI remain planned.

See the [user manual](USER_MANUAL.md), [seven-example guide](EXAMPLE_GUIDE.md), and [workflow guide](WORKFLOW_IMPROVEMENTS.md) for operation and current limits. Project filenames refer to the examples shipped inside the runtime, not private application source.

## Start on Windows

1. Download and extract `FunDEM-Workbench-Windows-x64.zip`.
2. Keep the extracted directory intact.
3. Open the extracted `Windows` folder and run `FunDEM.exe`.
4. Read `Windows/docs/USER_MANUAL.md` in the extracted package, or download the matching manual above, for project setup, simulation, playback, export, and post-processing.
5. Choose **File > Examples** to inspect a bundled model. This only opens it; press **Run** explicitly when ready. Save edits with **File > Save As...** in a writable user-owned folder.

The seven bundled examples cover Gomboc self-righting, a physically interlocked chain, bonded cloth falling onto a box, dam-break flow around a square column, Brazil-nut segregation, superellipsoids in a rotating drum, and irregular particles in a cylindrical mold. These are demonstration projects; their presence is not a claim of experimental validation.

In the Windows x64 package, keep `FunDEM.exe`, `FunDEM-cli.exe`, the runtime libraries, `platforms`, `force-modules`, example assets, documentation, and licenses together. The package includes the required meshes and compiled particle-force modules; no compiler, SDK, or separate Qt installation is needed. A missing installed example warns without replacing the current project.

For a Microsoft Store installation, launch FunDEM from Start and use the same **File > Examples** menu. Installed resources are read-only. Relative result roots resolve under `Documents/FunDEM`; portable Windows roots resolve beside the executable. Each fresh calculation creates its own child directory, and explicit absolute output paths remain unchanged.

From the extracted `Windows` folder, the headless check is:

```powershell
.\FunDEM-cli.exe --check .\examples\gombocSelfRighting.fundem.json
```

Use `--run project.fundem.json [--steps N --output root --archive newdir]` for an explicit calculation. The archive destination must be new. See the [workflow guide](WORKFLOW_IMPROVEMENTS.md#headless-checks-and-runs) for exit codes, interruption behavior, and argument limits.

**Settings > Display Storage** separates the **Viewport** presentation budget from the **Playback Cache** budget. Playback defaults to 256 MiB of recently used frames with shared LS geometry reuse; it can be adjusted or turned off without deleting the recorded disk history. These budgets do not limit total application memory. See the user manual for available settings and memory tradeoffs.

## Start on macOS

1. Download the macOS ZIP above, extract it, and move the complete `FunDEM.app` into Applications.
2. Open `FunDEM.app`. Keep its contents intact; users do not need to compile the application or install CUDA.
3. In the matching v0.3.39 app, use **File > Examples** to open a bundled project without starting calculation. Save edits with **File > Save As...** outside the application bundle.
4. Read the [macOS guide](MACOS_GUIDE.md). The application bundle also contains `Contents/Resources/docs/USER_MANUAL.md`, the workflow guide, and the seven projects in `Contents/Resources/examples`; Finder's **Show Package Contents** reveals these folders.
5. Check the Output directory before running a project. Relative output names resolve under `Documents/FunDEM` on macOS; an explicit absolute output path remains unchanged, so projects transferred from another computer may need a different output directory.

The macOS package uses an **ad-hoc signature**. It is **not signed with an Apple Developer ID and has not been notarized by Apple**. If Gatekeeper blocks the first launch, proceed only if you trust the download and have verified its source: first try opening the app, then follow Apple's application-specific **System Settings → Privacy & Security → Open Anyway** procedure. See [Apple's official instructions](https://support.apple.com/en-us/102445). Do not globally disable Gatekeeper or other macOS security protections. If macOS reports malware or a damaged application, stop and obtain a verified package instead of bypassing the warning.

## Offline operation and platform security

Built-in simulation, visualization, playback, and file export operate locally. They do not require an online account, online activation, an incoming network port, or a firewall exception. Third-party force modules and user-selected network file locations can have their own network requirements.

Network firewalls are separate from application trust checks. The Windows application is currently unsigned, so SmartScreen or Smart App Control may warn or block it; the Mac signature limitations are described above. No claim is made that every antivirus product or managed-computer policy will allow execution. Do not disable your firewall or antivirus to run FunDEM. Verify downloads from this repository, and consult your administrator if your organization's policy blocks the application. Microsoft notes that even a newly signed Windows release can receive a reputation warning; see its [SmartScreen guidance](https://learn.microsoft.com/en-us/windows/apps/package-and-deploy/smartscreen-reputation).
