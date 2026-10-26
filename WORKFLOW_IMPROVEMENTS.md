# FunDEM 0.4.1 workflow guide

This guide describes the rebuilt Windows application. It replaces the old
single-workbench workflow, not the old files themselves. For complete
operating steps read the [User Manual](USER_MANUAL.md); for supplied cases
read the [Example Guide](EXAMPLE_GUIDE.md).

## 1. Separate definitions, execution and analysis

| Workspace | Owns | Does not do |
| --- | --- | --- |
| Setup | Materials, LS Geometries and centered Particle Templates | Assign event state, insert native particles or run a solver |
| Simulation | Execution/output settings and ordered Events with Particle Instances, pairs, Bonds, Wall motion, force modules and SPH | Replace integrated old particles with template initial data |
| Results | Immutable recorded definitions/frames and separate display, filters and plots | Edit physical records, recalculate contacts or resume an exact solver checkpoint |

Configure expensive geometry/template generation in transactional dialogs.
Add or Apply publishes accepted data; Cancel/failure does not leave partial
objects. Common Property edits apply when valid, without a generic Apply.
Stable IDs, reference-safe deletion and Undo/Redo preserve object relationships.

## 2. Build a case in dependency order

1. Choose DEM or LSDEM + SPH in New Project.
2. Define unified finite/Wall Materials and applicable LS Geometries.
3. Generate Particle Templates without materials, event placement or motion.
4. Add ordered Events and instantiate templates with material and initial state.
5. Define required unordered Contact Material Pairs, including same-material
   combinations. Use a later event's explicit Override/Revert for local changes.
6. Add Bonds, event-local prescribed Wall motion and force modules as needed.
7. For LSDEM + SPH, set shared SPH Parameters and add regular Blocks or true Jets.
8. Check the compute mode, writable output root, event steps and recording interval.

Zero-volume LS geometry requires Wall material and cannot acquire a made-up
finite mass. Wall geometry's skipped physical integration is not a numeric
zero-volume measurement of a finite material. Random LS placement uses bounded,
nonzero-volume geometries and conservative sphere non-overlap.

## 3. Preserve event meaning

Existing solid particles retain computed position, orientation, velocity and
angular velocity across events. Only new instances receive initial authored
state. Existing Bonds retain deformation/history, damage and broken status.
Effective material-pair values carry forward with explicit local overrides.

Prescribed motion and force modules are event-local; add them again if needed
later. SPH sources carry forward but inherited source definitions are not
editable in later events. Owned objects appear once under their original
event; the absence of duplicate rows does not remove cumulative particles.

Do not replace staged filling/settling with one simultaneous addition or turn
on wall vibration/rotation in an earlier event merely to shorten a demonstration.

## 4. Execution and locking

CPU is default. The CPU ZIP has no CUDA execution. The separate Windows CUDA
ZIP supports CPU, GPU and Hybrid; Hybrid runs Sphere/SPH on GPU and LS on CPU.
Both device modes require compatible hardware/driver, and never silently
substitute CPU. Compiled GPU targets do not establish testing on every device.

Run starts the ordered native session; Pause waits at a safe boundary; Run
resumes that same session. Step advances one DEM step. Once Run/Step begins,
Setup/Simulation Add controls and all editable Property controls remain locked
through Paused, Completed and Failed until Reset. Read-only navigation and
Results editing stay available.

Run after completion creates a new session/archive from unchanged authored
initial data, not the old final state. Reset releases authoring and discards
runtime state without deleting recorded results. A displayed snapshot is not
a native restart checkpoint.

## 5. Recording and playback

Output chooses a shared writable root; each event supplies a recording interval.
Fresh runs reserve new child directories and do not erase old results.
Recording currently produces native particle/Contact/Bond archives, not VTU
or energy.dat. Keep the run's manifest, project snapshot and frame files together.

Results opens saved frames without searching neighbors or rerunning physical
laws. Playback uses saved physical times. The accepted n / N counter stays
visible during asynchronous reads; a failed read retains the last valid frame.

View > Playback cache... controls bounded decoded-frame LRU reuse, default
512 MiB, zero off. It is not a total process/GPU memory limit; currently
displayed and pending frames are outside its estimate. Clear cache does not
delete archive files. A draft budget applies only on Apply; Cancel does not
undo an explicitly executed Clear.

## 6. Independent display and analysis

Particles initially show; Contacts/Bonds initially hide. Their Work Tree
checkboxes and opacity are independent. Hidden particles do not hide their
interactions. Results starts with recorded colors/opacity and owns later edits
separately from Simulation.

Contact/Bond cylinders or point-normal disks use Display size as maximum
radius from their smaller endpoint radius. Scale by value distributes radius
proportionally below that cap using the active Legend range; zero values have
no glyph. Color, glyph size and filters do not change contact area or physics.

Render View retains indexed geometry and shares repeated meshes across particle
instances. Camera motion does not rebuild unchanged geometry. These display
optimizations preserve full-resolution surfaces, depth/transparency relationships,
filters and scalar values; they do not change archived records or the solver.

Clip/Threshold target explicitly selected particle, SPH, Contact or Bond populations.
Empty Filter Objects means None and has no filtering effect. Clear selections
by unchecking the selected objects or groups and confirming; Cancel retains
the previous targets. A Source change clears incompatible targets, and stale
IDs never expand the selection. Selected Clip guides are visual aids, not
automatic enable switches or additions to Fit bounds.
Filters change scene/capture visibility, not Plot, DAT or archive records.

Plot instead requires explicit nonempty valid objects for every applicable
X/Y/data query. Scope chooses Events; histories retain all recorded samples
from those Events. The redundant curve time-window filter is removed. Property's
X/Y limits control the chart view without removing table/CSV samples, including
when a custom X range lies between or outside saved samples.
Reload is manual rather than triggered by playback or every incoming frame.
The accepted chart, data table and CSV share the same samples. Compatible units
are required on shared axes. Solid-system energy does not include fluid energy.

Legend supports History, Current frame and Custom. Current excludes masked
samples; History retains original archived scope without re-scanning against
filters. Enabling a filter never silently changes the range mode.

## 7. Interchange and export

DAT import/export preserves self-contained solid-instance material, geometry,
pose/radius and per-particle velocity/angular velocity. It is not a complete
simulation restart format: SPH and Contact/Bond history are not restored.
Export includes the complete instance even if its display is hidden or filtered.

PNG and Windows H.264 MP4 re-render accepted presentation at the chosen
resolution, not screen-capture the surrounding application. They include enabled
legends, the selected box-size overlay and selected Clip guide; choose another
node to omit that guide. FPS does not add physical time-step information.
Cancellation/failure does not overwrite a pre-existing destination with a
partial export.

## 8. Compatibility, examples and release status

Supported Next technical formats remain unchanged: project schema 3 with
FunDEM-Next-Preprocessor and versioned FunDEM-Next-Results archives. Legacy
Software schema-10 projects and archives are not generally auto-migrated.
Do not edit format markers to bypass validation; use an explicitly converted
example or the appropriate older application.

Seven converted examples open as writable copies. They preserve supplied
parameters, poses and event order; their presence is not physical validation.
Brazil nut and mixing drum omit the old artificial one-step output-alignment
event while retaining physical activation/motion boundaries and total time.
Their event-local output interval remains 0.05 s; the final stage's regular
recording grid shifts by one original time step rather than adding that event.
The ON-DEM Benchmark menu requires user-supplied authorized original coordinates.
No author dataset or converted payload is distributed publicly. A 100,000-step
diagnostic is not the complete reference campaign.

Application 0.4.1 targets Windows x64. Its local Store update package version
is 1.0.11.0; packaging alone does not establish Store submission/certification.
A new macOS package is not verified and MP4 export is Windows-only. Historical
macOS releases retain their own workflow and documentation.

There is no published headless CLI workflow in this rebuilt runtime; do not
apply old FunDEM-cli commands or external-DLL instructions to it. Public
packages do not contain desktop source, SDKs, tests, private design documents
or restricted benchmark data. The numerical core remains
[FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta), under Apache-2.0.
