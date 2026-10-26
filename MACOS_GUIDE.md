# FunDEM macOS release status

## Current release: 0.4.0

FunDEM 0.4.0 is currently documented and packaged for **Windows x64**. There
is **no verified new macOS application package for 0.4.0**. The rebuilt
Setup / Simulation / Results workflow, event-based file model and new
analysis features must not be assumed available in an older Mac binary.

Do not use a latest-release Mac download link for 0.4.0. Current Windows
CPU/CUDA downloads are on the [release page](https://github.com/kaiqideng/FunDEMSoftware/releases/latest).
H.264 MP4 export in the rebuilt application currently uses Windows Media
Foundation and is Windows-only.

## Historical macOS release

The [v0.3.42 release page](https://github.com/kaiqideng/FunDEMSoftware/releases/tag/v0.3.42)
retains its historical Apple Silicon package and version-specific information.
This does not claim that old assets were removed, rebuilt or replaced.
No DMG link is provided here.

If you use that older application, follow its own packaged manual, project
format and platform/signing requirements. The current
[User Manual](USER_MANUAL.md) and [Workflow Guide](WORKFLOW_IMPROVEMENTS.md)
describe 0.4.0 Windows, not retrofit instructions for the old Mac UI.
Keep an older application and its projects separate from the new workflow;
legacy Software files are not generally auto-migrated by 0.4.0.

A future macOS update requires its own native build, bundle/runtime deployment,
platform export implementation and regression verification. No delivery date,
feature parity, Intel-Mac support, notarization or Store publication is implied
by this status document.
