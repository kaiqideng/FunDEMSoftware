# FunDEM Workbench on macOS

## Platform and package

The macOS package targets Apple Silicon (M-series) Macs running macOS 14 or later. It contains a native ARM64 application, the CPU FunDEMBeta core, Qt frameworks, the OpenMP runtime, and the bundled particle-force modules. CUDA is not used. Intel Macs are not included in this package.

Download the Mac ZIP from the [official release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest). Extract the ZIP, then copy the complete `FunDEM.app` to Applications or another writable folder. Do not copy only the executable from inside the application bundle. No compiler, Qt installation, or Homebrew installation is required to run the package.

## First launch and signing

This package is ad-hoc signed for bundle integrity, not signed with an Apple Developer ID and not notarized by Apple. The ad-hoc signature does not verify the publisher's identity. macOS may block the first launch.

Only if you have verified that the download is from the official release page and trust it, follow [Apple's instructions for opening an unnotarized app](https://support.apple.com/en-us/102445): try opening the app, then use **System Settings > Privacy & Security > Open Anyway** if macOS offers that option. Managed Macs may require administrator approval. Do not disable Gatekeeper globally. If macOS reports that the file is damaged or malicious, stop and verify the download instead of bypassing that warning.

## Projects, examples, and results

Choose **File > Examples** to open one of the seven bundled projects without starting a calculation, then use **Run** explicitly when ready. The menu reads the installed examples in `FunDEM.app/Contents/Resources/examples`; it does not depend on the current working directory. The application keeps its English documentation in the same Resources directory. In Finder, **Show Package Contents** exposes these folders. Use **File > Save As...** to save edits in a writable, user-owned working folder. Keep the bundled meshes and force modules available, and do not edit files inside the signed application bundle. A missing installed example produces an **Example Not Available** warning without replacing the current project.

The interface and project format are shared with Windows. The platform's matching `.dylib` files are used for the bundled force modules; a Windows `.dll` itself cannot run on macOS. Third-party modules require a native ARM64 macOS build from their author.

On macOS, relative result paths are placed under your **Documents/FunDEM** directory; each fresh calculation creates its own child run directory. Explicit absolute result paths are kept as configured. The actual output path is shown in the project settings; it must be writable. When moving a saved project between computers, review its output directory because a saved absolute path can refer to the previous computer's user account. Results are never meant to be written into `FunDEM.app`.

The main [User Manual](USER_MANUAL.md) describes the model editor, CPU solvers, playback, VTU output, and post-processing. Windows installation instructions and `.dll` examples in that manual should be read together with this macOS-specific guide.

The [workflow guide](WORKFLOW_IMPROVEMENTS.md) covers model checks, saved-run archives, quantitative CSV exports, and the bundled headless command. These instructions require the matching v0.3.40 package; an older Mac download may not include those features. The public runtime contains no application source, module SDK, or development build templates.
