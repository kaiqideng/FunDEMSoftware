# FunDEM Workbench on macOS

## Platform and package

The macOS package targets Apple Silicon (M-series) Macs running macOS 14 or later. It contains a native ARM64 application, the CPU FunDEMBeta core, Qt frameworks, the OpenMP runtime, and the bundled particle-force modules. CUDA is not used. Intel Macs are not included in this package.

Download the Mac ZIP or DMG from the [official release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest). Open the DMG or extract the ZIP, then copy the complete `FunDEM.app` to Applications or another writable folder. Do not copy only the executable from inside the application bundle. No compiler, Qt installation, or Homebrew installation is required to run the package.

## First launch and signing

This package is ad-hoc signed for bundle integrity, not signed with an Apple Developer ID and not notarized by Apple. The ad-hoc signature does not verify the publisher's identity. macOS may block the first launch.

Only if you have verified that the download is from the official release page and trust it, follow [Apple's instructions for opening an unnotarized app](https://support.apple.com/en-us/102445): try opening the app, then use **System Settings > Privacy & Security > Open Anyway** if macOS offers that option. Managed Macs may require administrator approval. Do not disable Gatekeeper globally. If macOS reports that the file is damaged or malicious, stop and verify the download instead of bypassing that warning.

## Projects, examples, and results

The application keeps its examples and English documentation in `FunDEM.app/Contents/Resources`. In Finder, **Show Package Contents** exposes that directory. Copy an example to your own working folder before editing it, or use **Save As** in the application. Do not edit files inside the signed application bundle.

The interface and project format are shared with Windows. The platform's matching `.dylib` files are used for the bundled force modules; a Windows `.dll` itself cannot run on macOS. Third-party modules require a native ARM64 macOS build from their author.

On macOS, relative result paths are placed under your **Documents/FunDEM** directory. Explicit absolute result paths are kept as configured. The actual output path is shown in the project settings; it must be writable. When moving a saved project between computers, review its output directory because a saved absolute path can refer to the previous computer's user account. Results are never meant to be written into `FunDEM.app` or the mounted DMG.

The main [User Manual](USER_MANUAL.md) describes the model editor, CPU solvers, playback, VTU output, and post-processing. Windows installation instructions and `.dll` examples in that manual should be read together with this macOS-specific guide.

## Build from a private source checkout

Application and solver source code are not published in the public download repository. For an authorized source checkout, install CMake, Ninja, a compatible Qt 6.8+ macOS SDK, and the OpenMP runtime. On a Mac, the shared packaging entry point is:

```bash
bash FunDEMSoftware/scripts/build-macos.sh \
  --qt-root /absolute/path/to/Qt/6.8.3/macos \
  --build-dir /absolute/path/to/build-macos \
  --package-dir /absolute/path/to/package-macos \
  --architecture arm64 \
  --deployment-target 14.0
```

The packaging script builds and checks the program, deploys dependencies into the bundle, applies ad-hoc signatures, and prepares ZIP/DMG files. The private GitHub Actions workflow runs this same entry point and checks the deployed application. Public distribution is a separate, deliberate upload of the binary package and documentation, never a copy of the private source tree.
