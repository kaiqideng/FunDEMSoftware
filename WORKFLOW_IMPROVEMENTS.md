# FunDEM 0.4.4 workflow guide

This guide summarizes the current Windows workflow. It is not a publication
or physical-validation record. For complete controls and equations read the
[User Manual](USER_MANUAL.md); for supplied cases read the
[Example Guide](EXAMPLE_GUIDE.md).

## 1. Definitions, execution and analysis

| Workspace | Owns | Does not do |
| --- | --- | --- |
| Setup | Materials, global Contact Material Pairs, LS Geometries and centered Particle Templates | Insert native particles or run the solver |
| Simulation | Ordered Events, particle instances, Bonds, external constraints and applicable SPH sources | Replace inherited runtime state with initial conditions |
| Results | Recorded frames and separate Display, Filter, Legend and Plot settings | Edit physical observations or restore a native checkpoint |

Work Tree is above Property in one shared-width left sidebar. Render View is
on the right and Console below. Tree selection drives inspection; clicking a
scene object does not reverse-select the tree. The five fixed inspection tools
are Fit, Fit selection, Camera, Axes and Grid.

## 2. Author in dependency order

1. Choose DEM or LSDEM + SPH in New Project.
2. Define Materials and unordered global Contact Material Pairs, including
   same-material pairs when needed. Pair values apply across all Events;
   no Event-specific Override/Revert is used.
3. Generate LS Geometries and Sphere/LS Particle Templates. Generation does
   not assign materials, infinite mass or Event placement.
4. Add Events with Time step, State interval, Animation interval and Duration.
5. Add material-assigned Particle Instances and their initial states, then
   Bonds and event-local external constraints.
6. For LSDEM + SPH, configure and Lock shared SPH Parameters before adding
   regularly spaced Blocks or outlet-driven Jets. Confirmed Unlock removes
   authored SPH sources in one undoable change; it does not silently rebuild them.

Add/Reconfigure works on a draft. Add or Apply commits one accepted transaction;
Cancel/failure leaves the model unchanged. Reference checks and Undo/Redo
preserve stable IDs. Zero-volume LS sources require Wall material.

## 3. Submit in sequence

Each Event row has Submit/Withdraw at its right. Only the next unsubmitted
Event can be submitted, forming one contiguous executable prefix. First
Submit includes Execution mode/device and Output directory. Preparation
reaches Ready without advancing time or recording a frame; Cancel/failure
does not accept partial settings.

Successful submission locks Setup and submitted physical definitions.
Unsubmitted Events remain editable, including during calculation. Display,
navigation, State inspection and Results remain available. Withdrawal works
backward from the submitted tail and only before that Event starts.

Run/Pause and Step operate the same retained native session. Execution stops
before the first unsubmitted Event. Submit another Event and use Run to
continue without recreating existing particles or histories. Reset confirms
first, stops calculation and clears every submission without deleting saved
result files. Saved Results are not resumable solver checkpoints.

## 4. Event state and constraints

Existing solid/SPH particles retain their computed state across Events.
Contacts and Bonds retain history, damage and broken status. Only newly
introduced instances use authored initial state. Event ownership remains
stable even when later scenes include earlier particles.

External constraints are Event-local, not inherited. Forces are assembled
first, with Global damping last among force models. Continuous velocity
constraints then take precedence and clear target net force/torque before
integration kicks. A scheduled velocity reset clears loads only on its reset
steps, not on every step. These presentation controls do not erase saved
Contact/Bond observations.

Global damping uses a dimensionless PFC-style local-damping coefficient,
default 0.7. It is not the previous dimensional drag/rate law, and old
coefficients cannot be uniquely converted. Review damping in user projects;
retired parameters are rejected rather than silently changed.

## 5. Recording and State inspection

Every Event runs a fixed positive number of solver steps. State interval is
the number of solver steps between complete samples. Animation interval is
the number of State intervals per animation sample. Duration counts Animation
intervals. Their checked integer products determine fixed Event steps.

Animation samples drive replay/export, while complete State samples drive
Plot. Adjacent Event boundaries coalesce to the next Event's right-continuous
state. Published files remain immutable; a retained submission frontier may
replace the indexed same-time tail using a new file and recorded lineage.

New committed samples refresh saved lists in any workspace without seeking
the displayed Render frame. Summary/table in Results State inspection follows
that accepted frame directly. Simulation State instead displays authored
initial world data. There is no separate State frame selector or Frame group.
Unavailable observations remain unavailable rather than invented zeros.

Playback uses one bounded decoded-frame cache. Automatic size is 25% of
available RAM capped at 4 GiB, with a 512 MiB fallback when unavailable. Zero
manual budget disables optional caching. This is not a total process/GPU
memory limit. Clear cache does not delete recorded files.

## 6. Display and quantitative analysis

Particles initially show; Contacts and Bonds initially hide. Their tree
checkboxes are independent: hiding particles never hides interactions.
Results begins with recorded Simulation colors/opacity and edits them separately.
Depth-tested selection outlines aid inspection but are omitted from capture.

Clip and Threshold require explicit particle, Contact or Bond targets. Empty
Objects means None, not All. They affect scene/capture only, not Plot samples,
DAT exports or physics. Legends default to the current frame's enabled data;
History and Custom remain available, and multiple movable bars can coexist.

Plot + offers Time history, XY curve, Scatter, Histogram and Uniaxial
compression. The four general types first choose Particle Instances, Bonds or
Contacts as Data source; solid/SPH fields share the particle catalogue.
Curves require explicit valid objects. Non-time-history curves choose a saved
State frame. There is no Event Scope selector. Reload is manual and does not
follow playback. Chart, data table and CSV share the same accepted samples;
axis limits change the view, not the numerical data.

## 7. Interchange, export and distribution

Projects use one current `.fundem.json` layout with automatic binary siblings.
Keep required imported/per-particle data; procedural caches are optional.
Numbered/retired project and result layouts are rejected.

Unnumbered DAT contains material definitions, solid world state and accepted
Sphere data or prepared LS surface/SDF/mass properties. Import does not
repeat centroid correction. Reconfigure of an imported LS shape uses the
Triangle mesh workflow, not an absent primitive recipe. Results export uses
one accepted frame and includes the whole selected instance, even if hidden.
DAT does not restore SPH or Contact/Bond histories.

Export scene PNG or Windows H.264 MP4 through File. Chart export supports
PNG/SVG/PDF; numerical export uses CSV. Failed/cancelled exports do not publish
partial files. No current desktop VTU/energy-DAT output or general checkpoint
restore is provided.

Seven Examples open as independent editable copies and do not start running
automatically. The ON-DEM Benchmark requires authorized user-provided data;
its coordinates are not bundled. Comparison cases and reports are maintained
in [FunDEMBeta validation](https://github.com/kaiqideng/FunDEMBeta/tree/main/validation).
Historical evidence retains its own scope and does not establish calibration
of every Example. No duplicate report ZIP is published with the desktop.

Application 0.4.4 targets Windows x64. A GitHub release is not evidence of
Microsoft Store submission or certification.
Public downloads contain runtime files, licensed examples and English user
documents, not private source, SDKs or developer tests. The numerical core is
[FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta), under Apache-2.0.
