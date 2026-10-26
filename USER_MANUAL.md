# FunDEM User Manual

Version 0.4.0 — Windows x64

FunDEM prepares, runs and inspects DEM and coupled LSDEM–SPH simulations.
Its numerical core is [FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta).
The application adds transactional modeling, event scheduling, native run
control, recorded results, visual filters and plots.

## 1. Install and open a project

Keep the complete delivered folder together and start `FunDEM.exe`. A deployed
binary needs no source checkout, compiler or local CUDA toolkit. A CUDA-enabled
package offers CPU, GPU and Hybrid; GPU modes also need a compatible NVIDIA
device and driver. An unavailable backend is reported, not silently replaced
with CPU. Do not mix runtime DLLs from different releases or compiler kits.

This release targets Windows x64. A new macOS package has not been verified;
older Mac downloads do not establish 0.4.0 compatibility. H.264 MP4 encoding
currently uses Windows Media Foundation and is Windows-only.

**File > New Project** offers **DEM** for Sphere/LS solids and **LSDEM + SPH**
for LS solids and fluid. The latter does not support DEM spheres. The left
rail switches between Setup, Simulation and Results. Work Tree selects
objects, Render View shows them, Property edits the selection, and Console
records operation milestones.

### Examples and benchmark

**File > Open Project > Examples** contains seven converted projects:

| Example | Purpose |
| --- | --- |
| Gömböc self-righting | Non-spherical contact and self-righting |
| Interlocked chain | Physical geometric interlocking |
| Cloth drop into a box | Bonded solid sheet |
| Dam break around a square column | SPH/LS interaction |
| Brazil nut segregation | Staged additions and wall vibration |
| Superellipsoid mixing drum | Staged filling and rotation |
| Random-shape particle column | Irregular high-resolution LS particles |

Opening creates a separate writable copy, never modifies installed inputs,
and does not start a calculation. Save As retains the copy in your own folder.
Some full cases take hours; a short-run check is not full physical validation
and demonstration coefficients are not measured calibration.

The **Benchmarks** menu retains ON-DEM periodic-shear import. Its author's
original coordinates and converted payload are not included in public
downloads. Supply your own authorized input when requested. Import does not
run the solver. The diagnostic is not the complete 200-million-step reference
campaign or a claim of exact agreement with the reference implementation.

## 2. Files and editing

Projects embed definitions, stable references, geometry/SDF and generated
particle caches. The retained technical suffix is `.fundem-next.json`, with
format `FunDEM-Next-Preprocessor`, schema 3. These identifiers preserve
existing supported Next files; they do not make this a preview product.

Old FunDEMSoftware schema-10 `.fundem.json` projects use another architecture
and are not generally auto-converted. Do not change a suffix/schema marker to
pretend to convert a file. The seven bundled examples were explicitly converted.
An unsuccessful open preserves the current project.

Configuration edits a draft: Add creates, Apply accepts Reconfigure, and
Cancel leaves accepted data unchanged. Errors and generation feedback remain
in that dialog. Valid Property edits commit directly without generic Apply.
Enter commits, Esc restores the uncommitted value. Units follow labels; vectors
use X/Y/Z and orientation angles are world X, then Y, then Z in degrees.

Undo/Redo retains up to 100 accepted project states. Reconfigure preserves
object identity. References protect unsafe deletion. Same-category duplicate
names receive a suffix such as ` (1)`.

## 3. Setup

### Materials

Add unified materials and edit density, Young's modulus, Poisson ratio and
restitution. Wall materials have infinite mass and need no density. Stiffness,
friction and damping belong to event Contact Material Pairs, not bulk
materials. Materials are not classified as sphere-only or LS-only.

### LS Geometries

Create a superellipsoid/variant, OBJ mesh or built-in wall. Configure surface
discretization and the SDF grid before generating. FunDEMBeta prepares the
surface/SDF and applicable centroid, volume and unit-density inertia once.
Wall geometry keeps the original local frame and skips mass/centroid
integration; unavailable physical properties are not invented.

Property shows calculated data, Display and bottom Reconfigure. Display hides
faces, shows triangle edges through Mesh, enables central X/Y/Z SDF planes
and sets Uniform color. SDF planes share a bar. No separate preview program
is opened. Confirm Reconfigure to replace accepted generated data.

### Particle Templates

Templates define centered reusable populations, not material, mass state,
event placement or initial motion. Sphere/LS Single, Lattice and Random
workflows are available where applicable. Single LS templates suit individual
walls/bodies. Sphere Random samples each radius from the configured range;
LS Random draws from selected nonzero-volume geometries. A geometry pool
need not use every selected geometry.

Lattice Count X/Y/Z defaults to 1. Choose ideal bounding-sphere spacing or
explicit spacing. LS orientation can be Uniform world-axis angles or seeded
Random. Random placement has independent seeds and an Attempts budget.
Non-overlap uses conservative bounding spheres; nominal porosity is a
generated-volume target, not the measured void fraction after settling.

Templates are centered using accepted conservative AABBs. Property reports
actual particle count, Bounding box size and Random minimum/maximum radius.
A single LS particle also reports orientation. Color does not change physics.
Large grids/populations require significant RAM; unsafe requests are rejected,
not silently generated at lower resolution.

## 4. Simulation

### Execution, output and events

Execution selects CPU, GPU or Hybrid and a device where applicable, for the
whole ordered run. Hybrid runs Sphere/SPH on GPU and LS on CPU. Output selects
a writable result root. Each event defines its time step, gravity, step count
and recording interval. Defaults are `1e-5 s`, Z gravity `-9.81 m/s²`, `100000`
steps and a 1000-step recording interval. Fresh runs reserve new child
directories; they never clear older results. Current recording writes native
result archives, not VTU or `energy.dat`.

### Particle Instances

Add one or more instances from Setup templates, reusing a template if needed.
Assign material, initial position and applicable initial velocity/angular
velocity. Property reports material(s), particle count, minimum/maximum mass,
initial position and minimum/maximum speed/angular-speed magnitudes. Wall
constituents are infinite mass. Zero-volume LS geometry requires Wall material;
finite mass cannot be fabricated from skipped integration.

Existing particles keep their computed position, quaternion, velocity and
angular velocity across events. Only newly added instances use authored
initial state. Older objects remain under their original event in Work Tree
but still participate in later cumulative scenes and target selectors.
Imported heterogeneous per-particle material/state remains heterogeneous.

### Contact Material Pairs

Choose two materials, including the same one twice. Pairs are unordered;
duplicate combinations are reported. Configure LS normal/sliding stiffness
per area, four ordinary stiffnesses, three friction coefficients and four
dashpots. The linear switch applies to sphere-related contacts; when off,
native Hertz–Mindlin uses bulk elastic properties. Explicit damping has a
separate switch; when off the core derives damping from restitution. Required
LS per-area values must be assigned; blank differs from explicit zero.

Pair values carry forward. A later event may explicitly override a pair and
revert its local override to inherited values.

### Bonds and prescribed motion

Bonds select two available solid instances, possibly the same one. Particle
center distance uses a maximum center distance; Contacts uses actual core
contacts at the event boundary. Length, stiffness and BK damage are edited in
Property. Length must be positive before Run. Bond area is the damage law's
reference section; zero disables fracture-area handling, not the elastic link.
Existing links retain history, damage and broken state rather than being
regenerated in later events.

Prescribed motion targets one Wall-material instance and uses the available
constant/simple-harmonic settings. It is event-local, not inherited.

### Force Modules

Sphere hydrodynamics selects one sphere instance and sets flow, water density,
water level and drag coefficient. Particle global damping selects one solid
instance and sets damping rate (`1/s`) and rotational drag (`N·m·s`). Add
another entry for another target. Built-ins need no DLL import and do not
carry forward automatically. The periodic-boundary service supports the
sphere-only benchmark domain; it is not general LS/SPH periodicity or a
replacement native contact model.

### SPH

In LSDEM + SPH, the shared SPH Parameters node sets spacing, smoothing length,
reference density, viscosity and design velocity/sound-speed settings. Event
SPHs + offers Block and Jet. Block is regular with zero authored velocity.
Jet defines outlet, direction, speed and duration with a maintained inlet
buffer; it is not a prefilled moving fluid column. Before execution its outlet
is a guide. SPH sources carry forward, but later events cannot edit inherited
definitions. Each source has Information and Display.

## 5. Run control and locking

The Simulation Render toolbar offers Run, Pause, Step and Reset. Pause stops
at a safe native boundary; Run resumes the same paused session. Step advances
one DEM step and returns to a stopped boundary. Inspection does not restart
SPH clocks or replace calculated state with template positions.

Run/Step locks Setup/Simulation Add actions and every editable Property
input, selector, checkbox and mutation action, including Display, until Reset.
Paused, Completed and Failed remain locked. Navigation, selection and reading
or copying values remain available, as do fixed camera tools and independent
Results controls.

Run after completion starts a fresh session/archive from unchanged authored
initial data, not the old final state. Reset releases authoring and safely
discards runtime state without deleting definitions or saved results.

Console uses timestamp, INFO/WARN/ERROR and source. It records file/generation
milestones, confirmed run commands, event boundaries and errors. Normal
parameter edits, camera movement and each playback frame do not spam it.
Lightweight progress appears separately in status.

## 6. Results

Open the current recorded run, another run directory or `run.json`. Committed
frames contain particle state, Contacts and Bonds with stable instance/local
index mappings. Replay is immutable: it does not search contacts, rerun forces
or restore a solver checkpoint. Technical identity remains
`FunDEM-Next-Results`; frame version 2 can read supported version-1 archives.

The timeline visits all saved frames at saved physical time, stops at the end,
and supports stepping/seeking. The accepted counter remains `n / N` while
reading. Failure keeps the last good frame. View > Playback cache... sets a
decoded-frame LRU, default 512 MiB, zero off. Current/pending frame state and
GPU buffers are outside that estimate. Clear cache is immediate; Cancel
retains the old budget but does not undo Clear.

### Display and legends

Particles initially show; Contacts/Bonds initially hide. Their Work Tree
checkboxes are independent and are the only visibility controls. Hiding
particles does not hide interactions. Results color/opacity initially copy
the recorded Simulation appearance; later edits are independent.

Particles offer Uniform color, velocity and displacement magnitude. SPH also
offers pressure, clamped nonnegative for display only; raw archive/physics
are unchanged. Contacts/Bonds offer available recorded force/energy/stress
quantities. Bond maximum normal section stress is tension-positive and includes
bending; maximum tangential section stress is nonnegative. Missing values
are unavailable, not invented zero.

Interactions draw center-to-center cylinders or point-normal disks, including
Wall endpoints. Display size sets `r_max = size/100 * min(endpoint radii)`,
range 1–100, default 50. Scale by value uses
`r = r_max * clamp(abs(value)/V_ref, 0, 1)`, with `V_ref` the greatest absolute
active Legend endpoint. Zero/all-zero values draw no glyph. Without scaling
or with Uniform color, radius is fixed at `r_max`. Sign affects color only.

Legend supports History, Current frame and Custom ranges. Current excludes
filtered data; History retains its original archived scope without filter
rescans. Bars are movable. Bounding box starts off; when enabled for a visible
selection, its wireframe and Box size (m) X/Y/Z use the exact same AABB.

### Filters and plots

Filter + offers disabled-by-default Clip and Threshold. Configure Target,
plane or data range in Property. Sources are Solid particles, SPH particles,
Contact instance pairs and Bond recipes. Empty Filter Objects means all
available objects for that Source. Clip keeps the plane's positive side by
saved centers/interaction points. Selecting it shows a semitransparent guide,
even if disabled; another selection removes it. Threshold is an inclusive
range using shared Plot quantities. Masks are population-specific and do not
change Plot, DAT export, archives or physics.

Plot + offers Time history, XY curve, Scatter and Histogram. Create a name,
choose event Scope and add/configure curves. Each curve owns its X/Y/data
query, applicable range and style. Plot requires explicit nonempty object
selections: empty never means All. Shared axes require compatible units.
Solid-system energy excludes fluid energy; potential energy uses the chosen
reference policy. Reload is manual; playback/incoming frames do not reload
charts. Table/CSV and chart use the same accepted data. Automatic/custom axis
limits and applicable logarithmic axes are available.

Results Edit uses separate Undo/Redo and can remove detached Plot/Filter
definitions, not archived physical objects/files or project parameters.

## 7. Interchange and image/animation export

File > Import particle instance... reads DAT materials, sphere positions/radii
or embedded LS meshes/orientations, plus per-particle velocities/angular
velocities. Give imported definitions/instance names. Import centers the
template and stores the matching initial position while preserving world
geometry and rigid velocities. Failure adds nothing.

Results exports a complete selected solid instance at the accepted frame to
DAT, including particles hidden/filtered in Render. DAT is interchange, not
an exact checkpoint: it does not restore Contact/Bond histories or SPH state.

File > Export current frame image... saves PNG. Export animation... saves
Results as Windows H.264 MP4. Choose frame range, playback speed, path,
1080p/4K resolution and 30/60 fps. FPS changes encoded images, not solver steps
or physical information. Rendering preserves camera, appearance, legends,
bounds overlay and a selected Clip guide; choose another node to omit that
guide. It captures no menus or panels.

Export uses saved physical times rather than interpolating new physics,
restores the replay frame and publishes only a complete successful file.
Cancellation/failure preserves existing destinations and source archives.

## 8. Troubleshooting

- GPU unavailable: check the actual package, device and driver. Building needs
  a toolkit; using the deployed binary does not.
- Finite LS instance rejected: integrate a valid positive-volume geometry, or
  use Wall material for zero-volume geometry.
- Contacts/Bonds invisible: check their own checkbox, opacity, filters, saved
  data and scalar availability. Particle visibility is not their switch.
- Plot empty: choose valid explicit objects/ranges and Reload; missing saved
  values cannot be reconstructed by changing display.
- Unexpected origin: use core-corrected local geometry plus centered
  template/instance placement, not raw OBJ origin assumptions.
- Editing locked after completion: Reset intentionally releases it without
  deleting saved results.
- Old project rejected: schema 10 is not schema 3; do not rename format markers.

Read [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) and
[FUNDEM_CORE.md](FUNDEM_CORE.md) for attribution. Examples and UI checks do not
replace independent engineering validation of a new physical model.
