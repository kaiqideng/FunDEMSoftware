# FunDEM 0.4.1 third-party notices

These notes identify the current desktop dependencies. Preserve the complete
vendor license texts and notices supplied with the actual binary package.
This document is not a substitute for them or a declaration that an arbitrary
locally assembled package is ready for redistribution.

## FunDEMBeta

The numerical core is [FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta),
Copyright 2026 Kaiqi Deng, supplied under **Apache License 2.0**. Preserve
[FunDEMBeta-LICENSE.txt](licenses/FunDEMBeta-LICENSE.txt) and
[FunDEMBeta-NOTICE.txt](licenses/FunDEMBeta-NOTICE.txt).

Core NOTICE:

> FunDEMBeta
>
> Copyright 2026 Kaiqi Deng
>
> This product includes software developed by Kaiqi Deng and FunDEM contributors.

The application source is a separate private project. The core license does
not make the entire desktop application source publicly available.

## Qt 6

The desktop dynamically links the matching Qt 6 kit's Core, Gui, Widgets,
OpenGL, OpenGLWidgets, **Concurrent** and **Svg** modules. The delivered platform,
image-format, SVG icon-engine and optional style plugins belong to that same
kit. Do not replace MSVC Qt binaries with MinGW binaries, or mix versions.

Qt is provided by The Qt Company and contributors. The selected open-source
build uses its applicable LGPLv3/GPLv3 terms and component notices; package
the original license texts and SBOM/notices for the actual Qt base and Qt SVG
modules, including their bundled third-party components. Preserve the user's
ability to replace dynamically linked Qt libraries as required by the
applicable terms. Optional Qt modules/plugins must be reviewed when added,
not assumed covered by an unrelated previous package's notice.

Compression uses the matched Qt zlib where available; a build using a separate
zlib must additionally retain that supplied component's license/notice.

## Fluent System Icons

Selected monochrome interface icons are from Microsoft's Fluent System Icons,
Regular set. Their detailed provenance is maintained in the source asset
notice. The FunDEM application logo and approved original toolbar artwork
are not Microsoft Fluent artwork. The following MIT notice applies to the
Fluent assets, not to all application/core code.

### MIT License

Copyright (c) 2020 Microsoft Corporation

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Windows compiler and OpenMP runtimes

The MSVC package includes the selected x64 Microsoft Visual C++ redistributable
runtime files. The applicable Microsoft installation/redistribution terms and
its original ThirdPartyNotices govern those files; a copied notice alone is
not an additional license grant.

The selected host parallel runtime is redistributable LLVM OpenMP `libomp.dll`.
Keep its original supplied license in the package. Do not substitute an
experimental compiler-only runtime. A distinct LLVM-MinGW build, if supplied,
must carry its own LLVM, unwind and MinGW runtime notices and may not reuse
MSVC kit notices as proof of its dependencies.

## Optional NVIDIA CUDA runtime

A CUDA-enabled Windows build packages only reviewed redistributable runtime
files from its selected toolkit, not the toolkit/compiler or NVIDIA driver.
Include the actual NVIDIA CUDA Runtime and CUDA C++ Core Libraries terms and
notices where applicable. The current build profile uses CUDA 13.2; users
still need a compatible installed NVIDIA driver and supported device.
CPU-only builds do not require distributing a CUDA runtime.

Compilation architectures and verified physical devices are separate facts.
Do not describe every compiled architecture as hardware-tested, or a runtime
copy as redistribution authorization beyond the supplied vendor terms.

## Platform APIs and examples

Windows H.264 MP4 export uses operating-system Media Foundation APIs; no
FFmpeg executable or codec SDK is bundled by this implementation. The
current non-Windows implementation explicitly reports MP4 as unavailable.

The seven converted built-in examples are distributed as accepted project
inputs. Keep any accompanying example-asset attribution. The ON-DEM author's
coordinate dataset has no stated redistribution license and is **not**
included in public packages or source commits, nor as an embedded converted
payload. Users must supply their own authorized copy for the benchmark import
workflow. A converted data representation does not remove that restriction.

ParaView, Blender, FEBio Studio and VS Code are workflow/design references,
not bundled applications. No endorsement by their owners is implied.
