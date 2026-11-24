# FunDEM User Manual

Version 1.0 — Windows x64

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

This release targets Windows x64. H.264 MP4 encoding uses Windows Media
Foundation.

**File > New Project** offers **DEM** for Sphere/LS solids and **LSDEM + SPH**
for LS solids and fluid. The latter does not support DEM spheres. The left
rail switches between Setup, Simulation and Results. Work Tree selects
objects, Render View shows them, Property edits the selection, and Console
records operation milestones.
Immediately after Help, the main menu bar holds global solver controls behind
a vertical separator: Run/Pause/Resume, its run-budget selector and Reset,
then a gray Submitted n/N link that locates the next pending Event.
A thin shared divider separates the bar from the panels.
Original menu widths stay unchanged; no second row
or screen-right corner widget. These
stay available in every workspace; Results' bottom playback row is independent.
Work Tree is above Property in one left sidebar, and both always share its
width. Drag their inner vertical divider to change relative heights, or the
horizontal divider to resize the sidebar against Render View on the right.
Console spans the content width below. Setup, Simulation and Results reuse
this layout; the existing panel-visibility actions remain independent.

Before a project is open, Render View briefly states FunDEM's purpose and the
**Setup → Simulation → Results** workflow. It does not open a welcome dialog.
After opening a project, the normal workspace-specific selection hints replace
this introduction.

**Help > User Guide** explains getting started and the three workspaces: Setup
defines materials, geometries and particle templates; Simulation arranges events
and runs calculations; Results replays saved frames and supports filters and
plots. It defines DEM (discrete element method), LS (level set), and SPH
(smoothed particle hydrodynamics) centrally and identifies FunDEMBeta as the
numerical core. **Help > About FunDEM** shows the running version,
calculation core, execution modes and the location of license/attribution
notices. Both entries are available without a project. Parameter-help popovers
remain focused on the selected parameter rather than repeating the glossary.

### Examples

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
If opening is cancelled or fails, its task-owned copy and sidecars are removed;
your current project and installed examples remain unchanged.
Some full cases take hours; a short-run check is not full physical validation
and demonstration coefficients are not measured calibration.

## 2. Files and editing

Projects use `.fundem.json` with format `FunDEM-Preprocessor`, without a schema/version number.
Open and Save accept only this configuration suffix.
The JSON holds definitions and stable references; bulk data use automatically
named binary files beside it. A referenced `data-<sha>.fundem-data` file contains
irreplaceable imported geometry, imported particles and per-particle initial
state. Keep it with the JSON when copying or sharing the project. An optional
`cache-<sha>.fundem-cache` speeds Open; if omitted or invalid, procedural
geometry/templates rebuild from the configuration. SPH Block/Jet arrays derive
from count and spacing. Users name only the project, not these sibling files.
Shared SPH settings are saved only for LSDEM + SPH projects. DEM projects
do not require fluid parameters and do not save an inactive `sph` section.
Only the current unversioned project and sidecar layout is accepted.
Retired product identities, numbered schema/storage markers and retired embedded-array formats are rejected;
do not relabel an old file or remove a marker to pretend to convert its data.
Bundled assets are mechanically converted without changing their physical
parameters or accepted numerical arrays. An unsuccessful Open preserves the
current project.

Longer Open/Save and installed-example extraction show
the shared compact progress feedback after about half a second. Real stages
are named; a percentage appears only when there is a measurable total. The
interface remains responsive while detached file work runs, but conflicting
edits are guarded until the transaction finishes. Failure or cancellation
does not replace the accepted project or an existing saved destination.
The native title is FunDEM, without a repeated heading inside. Stage text
identifies the operation and uses the same size as ordinary interface text.
Cancel immediately changes the feedback to `Cancelling...` and asks the
worker to stop; it is complete only after a safe check and cleanup. Large
application-owned read/extraction loops check during their work. A single
native geometry, JSON parser or image codec call may still have to finish
before cancellation can be acknowledged; the interface does not falsely
report that such a call has already stopped.
Opening an installed example keeps extraction and
project reading in one continuous task. Known percentages appear beside the
stage; an unknown total uses a moving bar without a fabricated percentage.
Project Open and manual Open Results complete data reading, tree/Property
publication, first-frame binding and initial render preparation before closing
this window. There is no second visible loading phase after it finishes.
Animation preloading is completed within this opening operation; later frames
reuse the same bounded decoded-frame cache and reader rather than opening a
second loading window.
Opening does not promise that the entire archive fits in memory.
Save/Save As can be cancelled before final publication without changing the
previous saved project or clearing unsaved edits. Cancel disables during the
short atomic commit; a completed write is not reported as cancelled.

Configuration edits a draft: Add creates, Apply accepts Reconfigure, and
Cancel leaves accepted data unchanged. Errors and generation feedback remain
in that dialog. Valid Property edits commit directly without generic Apply.
Enter commits, Esc restores the uncommitted value. Units follow labels; vectors
use X/Y/Z and orientation angles are world X, then Y, then Z in degrees.
Material and Sphere/LS template creation window titles begin with `Add`, such
as `Add Material` and `Add Sphere lattice template`. Reconfigure titles keep
their existing wording; the title prefix does not change default object names
or the confirmation button's caption.
Event creation uses `Add Event`; its existing `Reconfigure Event` title and
lowercase default names `event0`, `event1`, and so on remain unchanged.

**Reconfigure** remains available when an editable Property contains invalid
text. It first checks the whole Property: valid pending edits are accepted
once; if any is invalid, none of that pending batch is accepted and the
Configuration opens from the last accepted object. Cancel or unchanged Apply
preserves that invalid draft, its inline error and the accepted model/history.
Apply validates Configuration and completes any required rebuild; an accepted
changed configuration then refreshes Property from the new accepted model.
Cancel does not undo valid edits already
accepted before Configuration opened. Other actions, including Add,
navigation, Submit, Save and SPH Lock, still require valid pending input;
Reconfigure does not bypass read-only or submission locks.

Calculated Information values use borderless, selectable text. Their parameter
names end with one colon, for example `Volume (m³):` or
`Inherited particle instances:`. Group headings and editable parameter names
do not acquire this punctuation; locking an ordinary input does not change
its name. The colon is presentation only and does not change values or units.
Information values that identify other objects use neutral gray reference
links: source Materials/Templates, inherited objects, applied Particle
Instances, Filter targets and Plot-curve sources. Activate a link to select
its stable-ID target; hovering does not select or change Render View.
Lists retain one accepted item per line in the common Vertical layout.
Empty lists remain None and missing items remain plain Unavailable; no
empty selection means All. Numerical statistics remain ordinary text.
Text remains selectable/copyable and follows the same single-line layout.
These inspection links remain usable when physical inputs are locked and do
not edit data, create Undo history or append Console entries.
Missing references show plain **Unavailable**. Resolved archived names remain
visible even if the current project differs. Their links may remain; clicking
an unmatched target explains its unavailability in English and leaves the
selection unchanged. Navigation never opens or replaces the current project
automatically.

Selected non-obvious parameters have a circled-question button immediately after the name.
Click it for a concise explanation; hovering does not open parameter help.
Only one help popover is shown at a time. Its text is selectable, remains
available for locked values, and closes with Esc or a click outside. Reading
help does not change parameter values or unlock editing.

Undo/Redo retains up to 100 accepted project states. Reconfigure preserves
object identity. References protect unsafe deletion. Same-category duplicate
names receive a suffix such as ` (1)`.

### Selecting objects through Work Tree

Select a geometry, template or Particle Instance in Work Tree to highlight it
in Render View and open its Property. Work Tree is the only object-selection
entry point: clicking a rendered geometry, particle or empty viewport space
does not change the tree, Property or current Event. Ctrl-click in Work Tree
adds/removes objects, and Shift-selection selects a contiguous range. Multi-selection shows a
selection summary, not mixed physical parameter editors.

Medium-gray semibold rows are collections, such as Materials or Particle
Instances. They have no Property. Click the title to expand/collapse without
changing the selected object, Property or Render View. The Events root stays
open. Dark regular rows are objects with Property; their selection has the
neutral-gray background. A named Event is one of these objects: use its
arrow to expand and its name to inspect its Property. Add and visibility
controls remain separate actions.

Property shows its caption on the left and the selected object's name on
the header's right. Long names are shortened visually. Collection expansion
keeps that name; clearing selection or selecting several objects clears the
single-object name.

The fixed upper-left Render View information identifies the inspected Setup
object as **LS Geometry: name**, **Sphere Template: name** or
**LS Particle Template: name**. Simulation lists every Event through
the current one in project order, for example **Event: Prepare - Settle - Compression**,
followed by **Initial state** on the second row. Future Events are excluded.
Long rows are visually shortened while their accessible text remains complete.
Selecting one solid or SPH instance changes the second row
to **Initial state · Selected particle instance: name**, without a type prefix.
This identifies the highlighted instance within the cumulative scene; it does
not hide the other Event particles. Simulation always previews authored initial
placements, including inherited definitions, rather than current calculated
positions. Results continues to show the accepted recorded frame and time.
All use the same borderless text template.

Render View has sixteen permanent icon buttons in the same order in Setup,
Simulation and Results:

| Group | Buttons in order |
| --- | --- |
| Framing | Fit scene, Fit selection, Reset camera |
| Standard views | Isometric, Front, Back, Left, Right, Top, Bottom |
| Reference | Orthographic, Axes |
| Grid | XY grid, XZ grid, YZ grid, Checkerboard |

Selection preserves camera framing. **Fit selection** frames visible selected
objects; **Fit scene** frames the visible scene. Both preserve the viewing
direction and projection. **Reset camera** restores the default viewing
direction, Perspective projection and scene fit. A standard-view button changes
direction while preserving projection, target and framing scale. Its selected
state follows the actual camera direction; free rotation clears that preset
when the camera leaves it. **Orthographic** toggles projection: checked
means Orthographic and unchecked means Perspective. It starts unchecked.
Standard-view icons identify faces with gray
fills on a shared cube. Plane icons show a horizontal XY grid or mirrored
vertical XZ/YZ grids. Hovering reveals each action's English name.
**Axes** toggles the coordinate indicator and starts on.
Click empty Work Tree space to clear selection and Property while keeping the
scene and camera.
An invalid pending Property value blocks a tree-selection change until corrected
or restored with Esc. Hovering, camera dragging and legend dragging never change
object selection.

Selecting hidden, fully transparent or filtered objects in Work Tree does not
show them automatically. Results selection uses accepted archive definitions,
not same-named objects in the editable project. Contacts/Bonds can be selected
and highlighted from their tree rows, just like visible particle instances;
their display glyphs are not object-selection entry points.
Selection changes no physical data, saved frames, visibility or Undo history.

**XY grid**, **XZ grid** and **YZ grid** select at most one world-zero
reference plane. All start off. Click a plane to enable it, another to switch,
or the selected plane again to hide it. Geometry SDF slices retain their own
independent choices. The reference plane preserves geometry, camera, bounds
and solver settings. Its cell spacing adapts internally to zoom.
**Checkerboard** toggles the grid style: checked displays cells and unchecked
displays lines. It starts checked. The toggle is disabled while all planes are
off and retains its state for the next enabled plane. Checkerboard uses neutral
gray/white cells with the same perspective and opaque-object occlusion as Lines.
The active plane also shows its two infinite coordinate-axis lines through the
origin, using the shared X/Y/Z colors: X/Y on XY, X/Z on XZ and Y/Z on YZ.
Thin antialiased axis strokes remain clear at the actual display scale.
Opaque objects occlude these lines as they do the grid. Clearing the plane
hides both lines. Grid detail and contrast blend smoothly as zoom changes;
adaptive cell spacing remains internal and adds no information text.
Wheel zoom moves smoothly toward the requested view; consecutive wheel input
accumulates. Fit, standard views, projection changes, dragging and restoring
a workspace camera stop any unfinished transition. Image/video capture uses
the currently presented camera, without carrying an interactive zoom onward.

## 3. Setup

### Materials

Add unified materials and edit density, Young's modulus, Poisson ratio and
restitution. Wall materials have infinite mass and need no density. Stiffness,
friction and damping belong to global Setup Contact Material Pairs, not bulk
materials. Materials are not classified as sphere-only or LS-only.

Materials **+** opens Configuration with Name and Wall. After Add, Property
contains physical parameters in the **Material** group only; it does not duplicate Name, Wall or an
Identity section. Use the bottom **Reconfigure** action to rename or change
Wall. Apply preserves the Material ID and rechecks all assigned instances;
zero-volume LS particles cannot use a finite-mass material.

### Contact Material Pairs

Create pairs in Setup immediately after Materials. Every object-creation **+**
opens the common Configuration with an editable Identity > Name, including
Contact Material Pairs. Set the pair Name (first available `Contact material
pair0`, `Contact material pair1`, ...), then select Material 1 and
Material 2 in the **Material pair** group and optionally check
**Custom damping coefficient**. This uses the shared checkbox with its caption on the right,
like Material Wall. The same Material may be selected twice. A/B and B/A are one
unordered combination, so duplicate global pairs are rejected.
The tree shows the authored Name; Property Information identifies the two
Materials. Different names cannot create duplicate material combinations.

Select the named pair. Its Property panel contains these groups:

- **Information** identifies **Source material 1** and **Source material 2**.
  Their shared reference links select the corresponding Setup Materials
  by stable ID; they remain usable when physical inputs are locked.
- **Shared coefficients** lists Normal and Sliding damping when custom
  damping is checked in Configuration, followed by Sliding friction.
- **Sphere-related contact** lists Stiffness source, its four Custom linear
  coefficients, custom Rolling/Torsional damping, then Rolling/Torsional
  friction.
- **LS–LS contact** owns Normal and Sliding stiffness per area.

There is no separate Damping group.

Reconfigure changes Name/reference/checkbox drafts while preserving the pair
ID and accepted coefficients; changing participants does not auto-rename it.
Reconfigure is the final common action below the groups. There are no section
children or extra navigation menus. The checkbox is edited only through
Add/Reconfigure Configuration, not duplicated or summarized in Property.

Unchecked (the default) derives damping from material restitution and hides
the four explicit damping rows without resetting their values. Checked
shows Normal/Sliding damping in Shared coefficients for both contact
families, and Rolling/Torsional damping in Sphere-related contact.
Rolling/Torsional friction and custom damping remain available regardless
of Stiffness source, including From materials. Reconfigure
retains the pair ID and all coefficients; Cancel leaves accepted settings
unchanged, and a successful Apply participates in Undo/Redo.
Sphere-related stiffness From materials
uses Hertz–Mindlin; Custom linear uses the entered four stiffnesses. Its
question mark covers sphere–sphere and sphere–level-set contacts. The separate
LS–LS question marks explain each per-area input. Assign both inputs before
a run involving those pairs. An explicit zero disables its elastic term.
The fixed groups edit one global record.

The global table applies throughout every Event. There is no pair inheritance,
Event override or Revert command. Reconfigure preserves stable identity.
Projects must use the global `materialPairs` collection. Retired Event-scoped
`eventMaterialPairs` records are rejected rather than merged or silently
selecting one Event's settings. Save uses the global table.

### LS Geometries

Triangle mesh (OBJ) is the first Add entry and opens its Configuration
directly. Other entries are grouped by Curved solids (Superellipsoid,
Irregular (superellipsoid-based), Cut sphere, Capsule), Polyhedra
(Tetrahedron, Octahedron, Dodecahedron, Icosahedron, Polygonal prism,
Pyramid), Rings (Torus, Link), and Walls (Plane, Box, Cylinder, Cone).
The irregular label changes the menu/dialog and future default names, not
existing object names or saved references. Configure surface discretization
and the SDF grid before generating. FunDEMBeta prepares the
surface/SDF and applicable centroid, volume and unit-density inertia once.
Wall geometry keeps the original local frame and skips mass/centroid
integration; unavailable physical properties are not invented.

Except for subdivision-based Superellipsoid/Irregular, closed particle
shapes use Surface spacing for surface refinement. Grid spacing controls
the Cartesian SDF independently. Prism circumradius is
center-to-vertex; Height and pyramid Base size are full lengths. Octahedron
Half-lengths are the axial distances to its vertices. Torus Major radius is
measured to the tube centerline and must exceed Tube radius. Capsule Straight
length excludes the hemispheres; zero makes a sphere. Cut sphere retains
z ≤ Cut height from the native sphere; zero makes a lower hemisphere,
and the cut must lie strictly inside the sphere. Link sweeps a tube around
an XY stadium centerline elongated along Y: Bend radius must exceed Tube
radius, and zero Straight length makes a torus. Polygonal prism supports
3–128 sides. Existing Hexagonal/Triangular prism records remain loadable
and reconfigurable; new equivalents use Polygonal prism with six/three
sides. Built-in Cylinder and Cone walls include their finite end caps,
despite their Wall mass policy.

Property shows calculated data, Display and bottom Reconfigure. Standalone
LS Geometry previews actual triangle edges without filled faces or point
markers. Edges are always enabled without a Mesh checkbox; Signed-distance
field XY/XZ/YZ checks start off. There is no Surface/Hide surface control.
Uniform color changes the edge lines, initially black `#000000`. Opacity
uses the common slider/value control, initially
100, range 0–100; 0 makes Mesh fully transparent. Enabled SDF planes remain
opaque even at Opacity 0, with
unchanged values, colors, range and legend. Alpha-only edits do not regenerate
or resample SDF. SDF planes share a bar. No separate preview program
is opened. Particle Template/Instance and Simulation/Results filled surfaces
are unchanged, and Particle Templates keep their gray `#A6A6A6` default.
Confirm Reconfigure to replace accepted generated data.

### Particle Templates

Setup exposes **Sphere Templates** (DEM only) and **LS Particle Templates**
directly as top-level categories. There is no extra Particle Templates parent.
Both categories use the same reusable-template model and reference rules.

Templates define centered reusable populations. Simulation assigns
material, mass state, event placement and initial motion. Sphere templates
offer Lattice, Random and Compacted specimen. LS templates also offer Single
particle, suited to individual walls or bodies.

To create a compression or bending specimen, use **Sphere Templates + >
Compacted specimen...**. Select **Cylinder** or **Cuboid** in **Mold** and
enter its dimensions. Set the radius range and seed in **Packing**. For a
bending beam, enable the Cuboid notch and set its width and depth.

**Auto fill** is the default for new templates. It chooses the particle count,
then tries to fill less populated areas and rearranges the packing. It keeps
the densest valid result found for the supplied settings and seed. Select
**Fixed count** to enter the initial particle count yourself. Earlier
specimens without a saved mode reopen in Fixed count.

**Relative overlap tolerance (%)** sets the maximum overlap depth as a
percentage of the midpoint of the radius range. In both modes the default is
0.08%. For radii from 1.75 to 2.25 mm, this allows 1.6 µm of overlap. Enter
zero to permit only numerical roundoff.

**Property > Information** shows **Particle count** and **Bounding box size
XYZ (m)**. For a Compacted specimen, it also shows **Actual specimen size XYZ
(m)**, **Estimated porosity (%)** and **Max. initial overlap (m)**. The bounding
box expands the center extrema by the largest radius along each local axis.
The actual specimen size measures the extents of the individual sphere surfaces.
Estimated porosity compares the sum of the
individual sphere volumes with the mold volume after any notch cut. The
combined nominal sphere volume must fit within that available volume.

Create the specimen instance, apply Bond and configure fixtures and loading
in Simulation. A self Bond on a notched specimen excludes candidate links
that intersect the original notch volume.

Sphere Random samples each radius independently from the
configured range using **Radius distribution**: Uniform (default) or Power
law. Power law uses the number density `p(r) proportional to r^(-q)`, with
the editable **Power-law exponent** q: zero is Uniform, positive favors smaller
spheres, and negative favors larger spheres. The distribution is weighted by
particle count. **Power-law exponent** starts at the editable default of 4;
choose a value appropriate to the intended distribution.
Uniform hides, but preserves,
the exponent. Equal radius bounds give equal spheres with either choice.
Existing configurations without these fields keep their Uniform sampling
and random-seed behavior. **Radius random seed** remains independent of
**Placement random seed**; sampling occurs before largest-first placement,
and position retries never redraw a radius.
LS Random draws from selected nonzero-volume geometries. A geometry pool
need not use every selected geometry.

Lattice Count X/Y/Z defaults to 1. Choose ideal bounding-sphere spacing or
explicit spacing. LS orientation can be Uniform world-axis angles or seeded
Random. Random placement has independent seeds and an Attempts budget.
Non-overlap uses conservative bounding spheres; nominal porosity is a
generated-volume target, not the measured void fraction after settling.

All generated and imported Templates are centered at `(0, 0, 0)` in
their accepted local frame. Their **Bounding-sphere cluster bounding box** uses
componentwise particle-center extrema expanded on every axis by one largest
particle bounding radius across the population, not individual-radius-tightened
bounds or wall-surface/SDF bounds. Reconfigure and load preserve this centered
frame and dependent Instance world placements. All templates report **Particle
count** and **Bounding box size XYZ (m)**. Only LS Random/imported templates also report
minimum/maximum bounding radius; Sphere Template Information omits both radius
summaries without changing radius inputs or accepted particle data.
A single LS particle also reports orientation. Its Display has the same
Opacity (%) and Uniform color controls, initially 100% and gray. A transparent
template remains selectable. These presentation values do not change physics
or a Simulation instance's independent appearance.
Large grids/populations require significant RAM; unsafe requests are rejected,
not silently generated at lower resolution.

Imported templates use **Reconfigure** to edit Name and individual particle
data. Select a fixed-order table row, then edit Position, Orientation and Sphere
Radius or LS Geometry in the common parameter controls. Particle numbers/count
cannot be exchanged, sorted, inserted or deleted here. Apply recenters changed
local data and compensates all referring instance positions; it preserves their
IDs, materials and initial velocities. Cancel makes no changes; Undo/Redo
restores the complete accepted edit.
The table and selected-row **Particle** readout use one-based numbers for
display; internal particle order and ordinal references remain zero-based.
Position is template-local. All headers/cells are centered, with explicit
headings such as **Position X (m)** and **Orientation X (deg)**. It shares the
State table's presentation: 12 px Body font, 28 px rows, 32 px header,
contained scrolling, precise numeric tooltips and selected-data TSV Copy.
Copy does nothing without a selection and reports an inline error above
100,000 cells or 16 MiB; it does not truncate the selection.

Bundled Single/Lattice templates recover generation parameters where possible
and use the same Reconfigure controls as newly created templates. LS lattices
offer Uniform or Random orientation and editable Count XYZ. Opening a project
or Configuration preserves its accepted particles; explicit Reconfigure uses
the normal generation recipe, not stored per-particle quaternion reuse.

All instances that reference a template use its current accepted data, including
instances in later events. Their per-particle materials and initial velocities
remain tied to the original particle numbers. If a procedural rebuild would
change count or cannot preserve the random-particle correspondence, Apply is
rejected to preserve those ordinal values. The current project remains
unchanged. Name-only edits never regenerate geometry or particles.

For a Single LS template, Reconfigure changes Geometry and Orientation. Change
its dimensions or resolution through the selected Geometry's Reconfigure;
its bounding radius is derived, not an independent sphere-radius parameter.
Imported single-particle templates use the same editable particle-data row as
larger imported templates.

## 4. Simulation

### Event settings and Submit

There is one ordered **Events** chain with sequential per-Event submission.
No separate preparation phase exists. Ordinary Events may own
Particle Instances (solid instances and applicable SPH sources), Bonds and
Particle External Constraints.
Solid and SPH creation/rename share the same Particle Instances name scope;
cross-family duplicates receive the normal ` (1)` suffix. Existing accepted
or archived names are not rewritten.
Each solid instance belongs to its adding Event and remains available in
later Events. Particles, Contacts, Bonds and SPH source state
continue from the integrated predecessor state; only new objects receive their
authored initial state.

### Duplicating Events

Select named Event rows with Ctrl-click or Shift-selection. Right-click a
selected row and choose **Duplicate event** or **Duplicate events**. Each
selected Event is appended once in its original order, with a unique name
suffix, fresh object IDs and an unsubmitted state. A range of Event rows keeps
the current preview; click one Event to inspect its scene. The new rows open and become
selected. One Undo removes the complete copy operation.

Each copy includes its owned instances, Bonds, constraints, accepted initial
data and Simulation Display. References between copied Events follow the new
objects. Earlier objects outside the selection remain shared references, as do
Materials, geometries and Particle Templates. Copies use authored definitions;
the current solver's integrated histories and results stay with the existing
run. Ordinary submitted or started Events can supply the definitions. The
imported State base cannot be duplicated.

### Fixed step schedule and diagnostics

Every Event runs its fixed configured number of steps. Time step, State
interval, Animation interval and Duration define the schedule; duration in
seconds is `timeStep * steps`. There is no automatic-ending selector.
Average unbalanced force ratio remains a diagnostic, not a stopping condition:
it is the sum of residual-force magnitudes divided by the sum of applied-force
intensities over free translational solid degrees of freedom, before artificial
global damping. Wall/continuously prescribed degrees of freedom and SPH sources
do not contribute. Opposing Contact/Bond loads remain separate in the denominator,
together with net applied force and gravity. An active force-free population
has ratio zero. The calculation does not shorten an Event.

### Public time and native state

Every Event advances run time by `timeStep * steps`. Start is the sum of all
preceding Event durations, and End adds this Event's duration. Integrated
particle/interaction state and native/source clocks continue across boundaries
and Pause/Resume; Jet age and SPH multirate state are not restarted.

The GPU backend retains its CUDA context and stream across Events. Unchanged
materials, geometry/SDF data, surface mappings and valid search layouts are
reused. Changed controls still refresh the required dynamic state, with
particle motion and Contact/Bond histories carried forward. New instances and
changed dependencies trigger their corresponding setup work.

### Recording and boundary publication

Animation and State use coordinated Event start/interval/end cadences. The old
Event end and the next prepared Event start share one boundary time. Hold them
before publication so each stream records one right-continuous boundary state
belonging to the new Event.
Saved frame files are never overwritten. When another submitted Event begins
at a retained frontier, its boundary may replace the indexed same-time tail;
the archive records the superseded immutable frame in its lineage.
Failure/cancellation preserves an accepted held endpoint where available.

### Submit, locks and persistence

The first ordinary Submit opens the common Configuration with
**Execution > Compute mode**, **GPU device** for GPU/Hybrid, and **Output >
Directory**. The shared GPU device dropdown lists each CUDA index and model name.
A valid saved index remains selected. If that index is unavailable, an inline
message asks you to choose a device explicitly. An empty list blocks GPU/Hybrid
submission. CPU hides the dropdown and retains the saved index. Cancel or
preparation failure preserves accepted settings and submissions. After Reset or complete
withdrawal releases the native session, the next Submit opens the same settings
window with the retained values. Later Submit actions directly reuse accepted
settings and accept every unsubmitted Event through the target as one validated
range.

Use Submit at the right of the last Event you want to accept. The range starts
at the first unsubmitted Event and includes that selected Event. Validation and
acceptance apply to the entire range atomically. Cancel or failure preserves
the accepted prefix. Use Withdraw on any submitted ordinary Event that has
yet to start. It withdraws the selected Event and all later submitted Events
directly as one transaction, retaining the earlier submitted prefix. Rejected
or failed operations preserve accepted submissions, native state, Results and
expansion. Preparing, pending prefix updates and Stopping guard repeated
Submit/Withdraw actions. Started/completed Events
and the imported base remain protected. Every withdrawn Event expands once
and becomes editable; submit it again when ready to continue the calculation.

First Submit prepares Ready without advancing time or recording a frame. It
sets the global Execution/Output draft and locks Setup. Every submitted Event
and its owned physical settings are read-only; unsubmitted Events remain
editable, including during execution. Display, navigation and Results remain
available. Later Submit retains the same global settings and native session.

Run requires at least one submitted Event. It advances only the accepted
prefix, then waits before the first unsubmitted Event. Submit another Event and
press Run to continue from the retained particle/Contact/Bond/SPH state. This
also permits adding a later Event after the currently authored prefix finishes;
it does not restart earlier Events. Submitting alone does not resume execution.

Reset asks for confirmation, with Cancel selected by default. Confirmation
stops computation and withdraws computing Event submissions; authored input and
saved archives remain. An imported read-only base Event and its payload are
retained. Ordinary submission status is not saved as a live-session checkpoint.
Explicit State export/import provides the separate history-preserving project
workflow described under State projects below.

Current saves write nine required definition arrays in one unversioned
layout, including `eventParticleInstances` and `eventVelocityConstraints`.
Continuous velocity constraints and periodic velocity resets share the latter
array, distinguished by their command kinds; periodic resets do not become
continuous motion or inherit into later Events.
All arrays remain required even when empty; every solid or SPH instance has an
owning `eventId`.
Numbered project markers and retired collection layouts are rejected without
rewriting supplied files. Project binary data use `FDMPRDAT` with no version
field; Results uses `FunDEM-Results` and `FDMFRAME` snapshots, also without
version fields. Saved Bond records retain the native Bond-point torque only;
endpoint-torque vectors and stress fallbacks are not part of the current layout.

### Particle Instances

Use **Particle Instances +** under the owning Event to introduce particles
when that Event starts. Every instance has an `eventId`; a template can be
reused with different placements/materials as separate instances. Existing
instances keep their integrated state in later Events rather than reapplying
their authored initial values. Current and inherited solid instances are
available for Bonds and constraints under the ordinary
eligibility rules. Submission locks the accepted Event's physical definitions;
Display remains editable. Simulation lists each instance under its owning
Event. Results lists it once under **Animation > Particle Instances**.

Add one or more instances from Setup templates, reusing a template if needed.
Assign material, initial position and applicable initial velocity/angular
velocity. Property's **Information** reports **Assigned material** and
**Source particle template**, followed by **State...** to inspect the particle table and
summary. Placement, count, mass and velocity/angular-velocity
extrema are not repeated as Property rows; the underlying particle state is
unchanged. Wall
constituents are infinite mass. Zero-volume LS geometry requires Wall material;
finite mass cannot be fabricated from skipped integration.

The **Bounding-sphere cluster bounding box** is defined in the template's local
axes: expand its componentwise particle-center extrema by the one largest
particle bounding radius in that template. Every accepted template centers
this box at `(0,0,0)`. Simulation State > Summary's **Placement origin XYZ (m)**
is this local origin placed by the Instance orientation and Position XYZ; it
therefore equals the configured Position XYZ. It is not a minimum corner or mass centroid.
Rotating an asymmetric template can change the midpoint of a refitted
world-axis box, but does not change the template's placement reference.

Existing particles keep their computed position, quaternion, velocity and
angular velocity across events. Only newly added instances use authored
initial state. In Simulation, event-owned objects remain under their original event in Work Tree
but still participate in later cumulative scenes and target selectors.
Imported heterogeneous per-particle material/state remains heterogeneous.
In Reconfigure, Velocity and Angular velocity independently offer **Keep imported
values**, **Set uniform values**, or **Add offset**. Keep is the default; Uniform
sets every particle's selected vector; Offset adds the entered world-axis vector
to each particle's existing value. Materials and particle order are unchanged.
The operations apply once, and reopening resets both choices to Keep. These are
authored initial-state edits, not changes to an active or archived simulation.

Solid-instance **Information** identifies **Assigned material** and **Source
particle template**. Imported instances list their actual materials in particle
order, deduplicated by stable material ID rather than name. Each material name
is a separate reference; the template name follows its accepted stable ID.
Simulation and Results links select the corresponding Setup object only when
its target and required source definitions match the loaded project. Results
resolves displayed names from its archived project. Activating an unmatched
reference explains why navigation is unavailable without discarding its
archived name or changing the project. Particle count is not repeated here.
SPH Information includes **State...** in Simulation and Results. A Jet also
shows **Jet duration (s)** and **Nozzle position XYZ (m)** when available.

Use **State...** in the last row of Property's **Information** group to inspect
particle state. The window title is **State — <instance name>**. The button has no name
label and aligns with the value column. **Reconfigure** remains outside all
groups at the bottom.
The resizable read-only dialog uses the common Configuration/table shell,
with **Close** only, scrollbars and selection/copy. It remains available when
physical settings are locked. Clicking State again for the same instance in
the same workspace and recording brings its open window forward instead of
opening a copy. Different instances can have separate State windows;
Simulation and Results inspections remain separate. After Reconfigure, clicking
Simulation State refreshes the existing window with the accepted initial
state. Close and reopen State for a fresh window. Opening or creating another
project closes its previous authored State windows.
The window contains only the table and a common
read-only **Summary** showing **Initial time (s)** in Simulation or
**Recorded time (s)** in Results, followed by **Bounding box center XYZ (m)**,
**Bounding box size XYZ (m)**,
**Max velocity magnitude (m/s)** and **Max angular velocity magnitude (rad/s)**.
Recorded box center and size use the selected frame's particle-center extrema expanded
by the one largest particle bounding radius, not LS surfaces or the SDF grid.
Simulation's time is its owning Event's planned start; Results uses the
currently displayed frame's recorded time. This applies to Sphere, LS and SPH.
The recorded time remains available even when the instance is absent from a
valid frame, and clears when that recording closes or changes.
In Simulation, the position Summary row reads
**Placement origin XYZ (m)** and retains the authored template placement
reference rather than a refitted world-box midpoint. Each maximum includes a clickable one-based
**Particle** reference that selects the matching table row.
Sphere and LS instances share thirteen columns, in order: one-based Particle
number, Position X/Y/Z (m), Orientation X/Y/Z (deg), Velocity X/Y/Z (m/s), and
Angular velocity X/Y/Z (rad/s). Orientation uses rotations about the world X,
then Y, then Z axes. Simulation shows the configured initial orientation;
Results shows the actual orientation recorded in the accepted current frame.
SPH retains ten columns without Orientation, and angular velocity is
**Unavailable**. No material, radius or geometry columns are included.
In **Simulation**, State always shows the configured initial world state,
including DAT-imported per-particle vectors without double rotation. It never
replaces those values with recorded poses after a run.
In **Results**, State follows the current frame displayed by Render View.
Changing the playback frame updates the table and Summary together from that
already decoded observation. No extra archive read or playback seek is made.
State has no Frame group, frame selector, separate timestamp header or recording-status header.
An instance absent from the current frame has an empty table and **Unavailable**
summaries, not another frame's or authored values. New saved frames do not move
this inspection unless Render View changes; same-index boundary replacement
refreshes it. Closing/changing recordings clears an old State window; reopen
State for the new recording. The cleared old window cannot be reused for a
later recording, including a reopened archive. Inspection never changes solver,
project, saved results or Undo history;
archived objects still have no Reconfigure.
This table uses the same common particle-table presentation and factory as
imported-template Reconfigure, including centered headers/cells and precise
bounded TSV Copy. Here positions are world-space and selection may include
multiple read-only cells; there is no selected-row editor below the table.
The displayed one-based number does not change internal zero-based ordinals.

### Bonds

Bonds select two available solid particle instances, possibly the same one.
Configuration contains Name and the two instances:

- Sphere–sphere: enter **Max. gap**, the nonnegative maximum surface separation
  in m. A pair qualifies when `center distance - radius 1 - radius 2 <= Max. gap`.
  The default zero includes touching and overlapping spheres; a positive value
  also permits separated spheres. It is required for new/reconfigured sphere
  recipes and is not their mechanical length.
- Sphere–LS or LS–LS: only actual native
  contacts at event entry are eligible, using the detected point and normal;
  separated bounding spheres do not generate these Bonds.

Sphere–sphere Bond geometry always uses **Radius factor**, in [0, 1] and
initially 1, to set
`r_b = factor * min(r1, r2)`, `A = pi*r_b*r_b` and `L = norm(x2-x1)`.
These reference values are calculated separately for every accepted pair
from its current state when the Bond is created, then remain fixed. Max. gap
still selects eligible pairs; it does not supply the length. Sphere–LS and
LS–LS use manual area and length. There is no Geometry source selector.

Choose the stiffness **Source** in Configuration: **Custom** or
**Euler–Bernoulli beam**. Set **Allow fracture** there too, and enable
**Progressive damage** if softening after strength onset is required. These
choices are changed later through **Reconfigure**, not within Property.
There is no generation-rule/criterion summary or length input in Configuration.

Property **Information** identifies the two particle instances as
**Applies to particle instance 1** and **Applies to particle instance 2**.
**Custom** source shows **Stiffness** with Normal, Shear, Bending and Torsional
coefficients for any particle pairing, including non-spherical ones. Their
units are N/m, N/m, N·m/rad and N·m/rad.
**Euler–Bernoulli beam** shows **Beam property** containing only
**Young's modulus** and **Poisson ratio**, for every particle pairing.
Both stiffness modes have a separate **Geometry** group. For sphere–sphere
it contains only **Radius factor**; for Sphere–LS or LS–LS it contains
**Bond area** and **Equivalent length**. Custom stiffness keeps the
authored four coefficients unchanged, whereas Beam derives them for each
connection's own L/A. Manual geometry uses one stored area A and length L,
independent of Max. gap. Beam requires E > 0, -1 < nu < 0.5, area A > 0 and
reference length L > 0:
`I=A*A/(4*pi)`, `J=2*I`, `G=E/(2*(1+nu))`, `kn=E*A/L`,
`ks=12*E*I/L^3`, `kb=E*I/L`, `kt=G*J/L`. Beam derives the existing four stiffness
inputs; it does not replace the original elastic force/energy formulas.
Zero length is allowed while editing a draft, but every Bond requires a
positive reference length before execution. LS-containing manual zero-length
definitions need an explicit value; loading does not invent one. Coincident
sphere centers are rejected during preparation. Radius factor zero gives
zero area and is usable only with
Custom stiffness and fracture off; Beam or enabled fracture requires a
positive factor/area. Neither automatic length nor area is averaged over pairs.

New and reconfigured sphere Bond recipes store a surface-gap limit. Older
recipes containing only a center-distance limit retain that original behavior
until explicitly reconfigured; opening them does not change their connections.
Reconfigure accepts the new Max. gap criterion while preserving the recipe ID
and physical coefficients.

Bond preparation checks Cancel while building and validating the pending
connections, around core searches, and before inserting the complete batch.
Cancellation before insertion adds no partial Bonds and retains an accepted
held Event endpoint where available. Core searches and final native insertion
are not interrupted mid-call; Cancel waits for the next safe checkpoint,
rather than rolling back native changes.

Bond help popovers are short input explanations, at most 45 words each.
Stiffness Source explains Custom versus Beam input; Radius factor explains
the frozen per-pair reference geometry. Manual geometry fields retain their
input explanations. Individual Custom stiffness rows have no help buttons.
The equations below remain the detailed reference.

**Allow fracture** is an explicit Configuration checkbox, initially off. Area is retained even
when fracture is off, so it can still define Beam stiffness. When fracture
is on, enter positive tensile strength and cohesion in Pa and a nonnegative
dimensionless internal friction coefficient. **Progressive damage**, also
initially off, distinguishes strength-only brittle failure from progressive
softening. GIc, GIIc (J/m²) and the BK exponent appear only with progressive
damage; hidden values are retained. Softening requires positive normal and
shear stiffness. The two flags select
unbreakable, brittle and progressive behavior. Property has no duplicate source,
fracture or damage switch: its Strength group appears only with fracture,
and its Damage group only with both flags enabled. Reconfigure retains the
recipe ID, section and coefficients, including values hidden by the current
choices. Apply accepts one undoable transaction; Cancel changes nothing.

The current core body uses undamaged Bond-point force and B3/B4 moment for
strength. With tension-positive axial stress `sigma_a`, bending magnitude
`sigma_b=|Mb|R/I` and shear/torsion magnitude `tau=|Fs|/A+|Mt|R/J`, the
strength normal stress is `sigma_n=sigma_a+sigma_b` and shear capacity is
`c_s=cohesion-friction*sigma_n`. With Progressive damage off, the Bond
breaks when `sigma_n>tensileStrength` or `tau>c_s`.
With Progressive damage on, onset uses
`(max(sigma_n,0)/tensileStrength)^2+(tau/c_s)^2>=1`;
the shear term is evaluated for positive tau. A nonpositive `c_s` reaches the
strength condition in either mode, including zero shear.
Progressive mode with both critical energies zero fails immediately at its
quadratic strength crossing.

The damage opening driver is different: `sigma_o=max(sigma_a,0)+sigma_b`.
Opening energy is `Uo=(sigma_a>0 ? max(Un,0) : 0)+max(Ub,0)`;
shearing energy is `Us=max(Ushear,0)+max(Ut,0)`.
The equivalent components are `dn=2*Uo/(A*sigma_o)` if sigma_o is positive
and `ds=2*Us/(A*tau)` if tau is positive; a zero denominator's component is
zero. Ordinary `d=hypot(dn,ds)` combines them. This formula does not divide
by kn or ks; their positive progressive-mode validity guard still applies.
The elastic evaluator retains the normal expression, uses
`Ub=0.5*kb*|nm+ns|^2`, and clamps raw torsional energy to `max(raw,0)`.

At sampled onset, freeze BK Gc from the opening/shearing energy weights,
`d0` and `sigma_e0=sqrt(sigma_o^2+tau^2)`. Failure separation is
`df=2*Gc/sigma_e0`; maximum separation and maximum attained damage make
evolution irreversible. Zero usable Gc/d0 or `df<=d0` gives immediate failure,
not an artificially enlarged softening interval. This is not a radial
work-conjugate-traction construction and does not establish an exact A*Gc
work identity for mixed-mode loading. Axial compression is retained before
D=1; complete failure clears the whole Bond, not an energy-conserving
transfer to contacts. These definitions are not full physical validation.
The two Configuration flags select native `fractureMode` explicitly:
unbreakable, brittle or damage; area/toughness do not select the mode.

Existing links retain history, damage and broken state across Events.
Current Bond records retain the four exact original stiffnesses, accepted
separation history, damage and the native Bond-point `torque` after damage
scaling. This moment excludes particle-center force lever arms and is the
sole moment source for section stresses; missing or invalid torque makes
stress unavailable, without reconstruction. The `fourModeEnergy` tag
identifies the observation layout. For a constitutive audit, record the
software build and FunDEMBeta source revision with the simulation.
Readers preserve the accepted scientific values, not a recalculated history.
Ordinary replay does not restore a native solver. Explicit State project
export/import can preserve these recorded Bond histories in a new initial base;
only the current unversioned layouts and required available history are accepted.

### Particle External Constraints

Each Event has one **Particle External Constraints** collection. Its **+** menu
groups choices under **Velocity (Angular velocity)** — Constant, Harmonic or Reset velocities —
and **Force (Torque)** — direct Constant, **Engineering scenario forces** >
Sphere hydrodynamics where available, then direct Global damping last.
These are menu groups, not additional Work Tree categories. Configuration selects
Name and one available solid Particle Instance; use another entry for another
target. These commands apply within their owning Event. Their Property
Information identifies the selected target as **Applies to particle instance**; Configuration
keeps its **Particle instance** selector label.

Constant/Harmonic velocity constraints support Sphere and LS instances with
finite or Wall materials. One command prescribes all translational and
rotational degrees of freedom of every target particle. Rotation is about each
particle's own center, not a shared template center; it adds no orbital
`angular velocity × offset` velocity. Constant mode keeps its Translation
Velocity XYZ (`m/s`) and Rotation Angular velocity XYZ (`rad/s`) fields.
Harmonic Property is ordered **Information**, **Oscillation**, **Translation**,
**Rotation**, then **Reconfigure**. Oscillation contains the shared Frequency
(`Hz`) and Phase (`deg`) for translation and rotation. Translation contains
Displacement amplitude XYZ (`m`), not a velocity amplitude. Rotation contains
Angular amplitude XYZ (`deg`), followed by Constant angular velocity XYZ
(`rad/s`). Each particle rotates about its own reference position, never a
common Instance pivot. The grouping changes no stored keys, defaults or motion
formulas, and Property has no duplicate mode selector.
Rotation uses the configured world-X/Y/Z composition. Material, mass and inertia
do not change. While constrained, each target particle's net force and torque
are cleared at Event entry and prescribed endpoints, and again after external
load assembly before the integration kick. This applies to CPU, GPU and Hybrid.
Coupled SPH integration also clears these loads before the solid half-kick.
Contact and Bond records remain available for interaction/reaction analysis;
zero particle loads do not mean those interactions are absent. There is no
separate constraint-reaction or prescribed-work balance. One velocity command
per target per Event prevents conflicting prescribed poses.
Velocity and Force constraints may act on the same Instance in the same Event.
All applicable forces are assembled first, with Global damping last among force
models; prescribed motion then takes precedence and clears the target's net
force/torque before integration kicks. The force models do not accelerate its
prescribed motion. Contact/Bond records remain available. Different force models
may be combined for one target; duplicate same-model entries and a periodic
velocity reset on a continuously constrained target remain disallowed.

**Reset velocities** Configuration/Reconfigure sets only **Name** and the target
**Particle instance**. In Property, **Reset** selects **Linear velocity**,
**Angular velocity**, or both; **Trigger** sets the interval. Information shows
only **Applies to particle instance**, without repeating those editable values.
The command clears the selected vectors for every target particle
every **Reset interval (steps)** completed steps: N, 2N, 3N, and so on
within its owning Event. The interval is a positive integer, default 1, no
larger than the Event's step budget. Pause/Resume keeps the cadence; later
Events do not inherit the command. On a scheduled reset step, both net particle
force and torque are also cleared, whichever velocity components are selected.
The final observation of that step retains the reset after endpoint finalization.
Other steps keep normal loads, so forces may accelerate particles between resets.
Position, orientation and Contact/Bond history remain unchanged. This discrete
command may coexist with force constraints, but not with
continuous Constant/Harmonic velocity on the same target.

Every Event Configuration variant contains only the applicable editable
fields and inline validation, without Information or read-only time previews;
Event Property Information shows configured Time step, Step range,
State frame range and Animation frame range. Ranges use inclusive
`start-end` endpoints on one line. Cumulative steps start at 0 and logical
stream frames start at 1. Adjacent Events share each stream's boundary number,
including after the successor supersedes the indexed boundary. An imported
zero-step State shows `0-0` steps and `1-1` in both streams; its successor starts
at those same indices. A legacy partial interval still records its final
observation. Every Event duration remains Time step multiplied by its fixed
step count.
Its **Inherited particle instances** list combines preceding solid instances
and SPH sources in Event order, with one clickable stable-ID reference per line.
**Inherited bonds** lists the inherited Bond definitions using the same reference
links. These links select the owning Work Tree objects while preserving the
Event and its submission state.
There is no separate Inherited SPHs row.

Constant force/torque applies to any solid instance. **Force per particle**
(`N`) and **Torque per particle** (`N·m`) are world-axis vectors: every particle
receives the entire configured F and T, not a divided instance-total load.
Sphere hydrodynamics targets one Sphere instance and sets flow, water density,
water level and drag coefficient. Global damping targets one solid instance
and sets one dimensionless **Damping coefficient**, default **0.7**. It uses
PFC-style local damping for each translational and rotational component:
`Fd_i=-alpha*abs(F_i)*sign(v_i)` and
`Td_i=-alpha*abs(T_i)*sign(omega_i)`. The undamped total force includes gravity
`m*g`; total torque includes the applicable interaction and applied loads.
A zero velocity component adds zero damping. Unlike viscous drag, its
magnitude follows force/torque rather than speed; it is not a contact dashpot
or exponential velocity-decay setting. A zero undamped force or torque
component also adds no damping, even when its velocity or angular velocity is
nonzero. Local damping does not stop force-free motion; **Reset velocities**
remains a separate explicit operation. See
[PFC Energy and Dissipation Mechanisms](https://docs.itascasoftware.com/itasca960/pfc/docproject/source/manual/numerical_simulations_with_pfc/pfc_formulation/energy_and_dissipation_mechanisms.html).

The coefficient must be finite and nonnegative. Normally use 0–1; this is
guidance, not an upper-bound validation rule. Zero disables its contribution.
Local damping is intended for numerical relaxation/quasi-static use; impact
and free-flight cases require care rather than treating it as calibrated
contact dissipation. This replaces the previous Global damping law, not a
second model. Old `dampingRate` and `rotationalDragCoefficient` parameters are
rejected: they cannot be converted uniquely, and existing user files are not
silently rewritten.

Independent constant and hydrodynamic loads accumulate before local damping,
regardless of menu/add order. The Average unbalanced force ratio uses the
undamped loads. If the target also has a continuous velocity constraint,
kinematic enforcement follows force assembly on CPU, GPU and Hybrid.
For coupled SPH/solid steps, the adapter remembers only its own previous
artificial-load increment. After the core refreshes the wall reaction, it
removes that increment, measures the undamped force ratio and recomputes
damping before the solid kick. An unchanged Event handoff retains this scratch
history; changed controls, Reset or a full force assembly replace it. This is
internal working data, not project, archive or recorded State data.
Built-ins need no DLL import; none applies a velocity constraint to SPH particles.

### Boundary

Set Boundary in Event Property, not in Particle External Constraints. **Free**
is the default. **Periodic** reveals minimum/maximum corners and X/Y/Z choices.
It defines one event-local shared domain for Sphere, LS and SPH,
not a per-instance load. Select at least one periodic axis and finite positive
extents on every axis; periodic Bonds are unsupported. Each enabled extent must exceed four
times the largest solid bounding radius (walls included) and, for SPH, four
times the smoothing length. Run validates the built populations; large walls
are not silently exempted or replicated. SPH keeps continuous internal positions
and uses minimum-image neighbors; display/recorded positions wrap into the
domain without introducing ghost mass. Each later Event uses its own Boundary
selection. Unchanged domains preserve continuation; changed/removed domains
rebuild affected state before advancing. This does not replace native force laws.

Saved particle positions are wrapped observations and have no periodic image
counters. Displacement derived from recorded coordinates can jump when a
particle crosses a periodic boundary; it is a coordinate offset, not unwrapped
cumulative motion. Display and Plot cannot reconstruct the missing crossing
history. This archive/analysis limitation does not change the periodic dynamics.

Opening an older project moves its former periodic Force Module into the owning
Event Boundary. Duplicate or conflicting definitions fail without replacing the
accepted project; other force records and stable target IDs are preserved.

### SPH

In LSDEM + SPH, select **SPH Parameters** beside Events. Set spacing
and smoothing length in **Discretization**, and reference density and dynamic
viscosity in **Fluid**. Click **Lock source parameters** to validate these
settings and make the two groups read-only. The same footer action becomes
**Unlock source parameters**. In **Particle Instances +**, this lock enables
**SPH block...** and **SPH jet...**. **Solid particle...** is available in
either lock state. In DEM the Add action opens solid-instance Configuration
directly.

To change Discretization or Fluid later, click **Unlock source parameters**.
If the project contains SPH sources, this action asks for confirmation before
removing **all Blocks and Jets in all Events** and their generated arrays.
Choose **Yes** to confirm the undoable change or **Cancel** to retain the
accepted state. After editing, lock the source parameters before adding
replacement sources. An editable Block or Jet can be Reconfigured using
the locked shared spacing.

**Solver → Artificial speed of sound (m/s)** stays editable after the source
lock. Adjusting it preserves every Block and Jet. The solver derives its
maximum design-velocity scale as sound speed divided by ten. The first
successful ordinary **Submit** locks the sound speed and the footer for
the retained session. **Reset** restores sound-speed editing for ordinary
projects and preserves the existing source lock and instances. A project with
retained SPH from an imported State uses the saved sound speed read-only
for continuation, including after Reset.

Block sets Count XYZ and Position XYZ and generates a regular array centered
at that placement with zero authored velocity. Jet defines outlet, direction,
speed and duration with a maintained inlet
buffer; it is not a prefilled moving fluid column. Before execution its outlet
is a guide. SPH sources carry forward, but later events cannot edit inherited
definitions. In Simulation and Results, Jet Information shows **Jet duration
(s)** and **Nozzle position XYZ (m)** when available, above **State...**. The
duration is the configured emission period. For an authored Jet, Placement
and Jet settings determine the outlet center in world coordinates. A Jet
restored from State shows the saved physical outlet even when its imported
particle cloud has a different center. Block Information contains the
**State...** action. Display
follows Information for both sources.
Solid and SPH Simulation Display contains Opacity (%) and Uniform color,
not a Hide particles checkbox. Opacity 0 makes an object fully transparent
without removing its definition. Old Simulation snapshot `hidden` values are
ignored; new snapshots carry color/opacity only. Results tree visibility
remains an independent control, initially checked for particles.

During LSDEM + SPH execution, existing LS particles retain their local boundary
virtual-particle arrangement across Events. A newly introduced LS particle receives
its own samples when fluid is available. Those samples move with their LS owner;
the solver refreshes world geometry, inlet masks and neighbor searches at the
Event boundary. CPU, GPU and Hybrid use the same sampling lifecycle.

## 5. Run control and locking

The first ordinary Event-row Submit opens Execution / Compute mode,
GPU device for GPU/Hybrid, and Output / Directory in the common Configuration.
The shared device dropdown shows CUDA index and model name and retains a valid
saved selection. An unavailable index requires explicit selection; an empty
device list blocks GPU/Hybrid submission. CPU hides the dropdown and retains its
stored index. Submit prepares
detached native state asynchronously; successful Ready advances no time and
writes no archive/frame. Validation and acceptance cover the complete pending
range atomically. Later Submit actions directly reuse accepted settings.
Withdraw directly removes the target unstarted Event and its submitted suffix.
Shared preparation progress and error feedback remain available.
Cancel or failure preserves accepted input, Results and expansion state.
Successful Submit collapses every newly accepted Event once; successful
Withdraw expands each Event in its withdrawn range once. Manual expansion survives ordinary refresh and
workspace navigation.

Run/Pause/Resume, the run-budget choice and Reset sit inline immediately
after Help behind the shared vertical separator, followed by subdued status.
Run and Reset use the shared Submit compact base template: compact framed text,
24 px height and the menu font, without icons.
The choice is also 24 px high. Center all three in the unchanged 28 px menu
bar, keeping the original menu widths and stable Run/Pause/Resume width.
With at least one submitted Event, the available Run/Pause/Resume action
uses black-background/white-text emphasis. Reset remains the same-height
secondary button; disabled controls remain gray. Full withdrawal or confirmed
Reset clears Run emphasis; cancelled/failed Submit preserves accepted state.
This feedback is independent of keyboard focus or checked state.
Click the gray **Submitted n/N** status to locate the next pending Event in
Simulation. The Event is selected, expanded and scrolled into view; no Submit
or calculation is started. Normal pending-input validation still applies.
The status is disabled when no pending Event exists. Waiting/runtime details
remain available in its tooltip rather than expanding the visible text.
A thin shared divider separates the menu/controls from the central panels.
The visible choices are `∞`/`1`/`10`/`100` with item data 0/1/10/100,
default `∞`. English hover/accessibility explains no native-step limit.
Changing the choice does not execute or alter Event/output settings.
Run/Resume uses the chosen budget: zero runs without a command-step limit;
positive values advance up to that many completed native steps, then pause
or stop at frontier/completion. While running the same button is Pause and
the budget is disabled. No separate Step button, action or queued request.
Reset retains its confirmation; Results playback remains independent.

Setup and submitted Event physical fields/Add/Reconfigure actions
are gray/read-only; unsubmitted Events remain editable. Reaching the accepted
frontier enters Waiting without discarding native state. Submit the next Event
and press Run to continue. Display controls, navigation, copying and Results
presentation remain available.

The first Event records its initial boundary. Every Event then records its
coordinated Animation/State intervals and final boundary; adjacent old-end/
next-start samples share one right-continuous boundary observation. Internal
integration/source clocks and displayed cumulative time continue. Native
Contact/Bond histories and SPH source age are retained, not recreated.

Reset confirms before stopping and withdrawing all submissions; Cancel leaves
the current run unchanged. It preserves definitions and saved result
directories. Paused/Waiting Run resumes the same native state, not an exported
frame. Reset starts again from the project's authored or imported initial
state. Use the explicit State project workflow to create a new selected subsystem
with its saved Contact/Bond histories; ordinary replay alone does not do that.

## 6. Results

Open the current recorded run, another run directory or `run.json`. Committed
frames contain particle state, Contacts and Bonds with stable instance/local
index mappings. Replay is immutable: it does not search contacts, rerun forces
or restore a solver checkpoint. The current technical identity is
`FunDEM-Results`, with one current unversioned manifest and binary layout.
The current recording shares fixed particle properties, Bond reference data
and coefficients across dynamic frames. New objects append fixed definitions;
each frame keeps motion, forces, spring and damage history, energies and SPH
evolution. Contact effective mass and radius are generated by Contact search
when the solver initializes. Repeated IDs/types use dictionary references.
Reading reconstructs the complete observation for Display, State inspection, Filter and
Plot. Materials, material pairs, geometry/SDF and templates remain in the
archive's immutable project snapshots.
Keep the entire result directory, including its required definition files.
State export embeds its required definitions in the exported project's data
sibling. Earlier archives and State projects that already use shared
definitions remain readable. Archives from the former per-frame layout and
numbered formats are rejected. Opening keeps supplied files unchanged.

During a run, every successfully saved Animation or State sample automatically
updates Results' frame lists, even while Setup or Simulation is active. This
includes starting frames and finalized event boundaries. The displayed frame
stays selected: new output does not jump to the latest frame, interrupt manual
inspection, switch workspace or automatically reload a Plot. A refresh arriving
during another read waits for that read; playback itself does not defer the
frame-list update or pause/restart. Playback continues in recorded order through
newly available frames, without automatically seeking to the newest one. No unsaved intermediate
Results frame is invented.

Results Work Tree has two top-level rows, **Animation** and **Plot**.
Animation contains **Particle Instances**, **Bonds**, **Contacts**,
**Legend** and **Filter**. Particle Instances combines solids with applicable
SPH Block/Jet sources.
Their Results Property keeps State inspection and Display without Particle count
or Initial velocity summaries from the authored source.
Each object appears once in its category, regardless of which Event introduced
it. There is no Events submenu, Event selection or Event Property/frame-range
panel. Object selection shows its own read-only information and editable
Display without seeking to an Event. Contacts retains only eligible unordered
solid-instance pairs, excluding Wall–Wall and single-particle self-pairs.
The archive retains Event ownership, recorded steps and times, and boundary
provenance.
Plot has no Event scope selector; its existing time-history and explicit
single-frame query rules are unchanged.

The timeline visits saved animation frames at saved physical time, stops at the end,
and supports stepping/seeking. Render View shows **State frames: n / N**, **Animation frames: n / N** and
**Time: ... s** in a fixed upper-left three-row HUD. Time is stored with the displayed
frame, not playback wall time or an estimate from the frame index. All three rows use
the shared Body text and aligned name/value layout. While a
replacement is reading, the last accepted frame number and time remain visible;
failure keeps them unchanged. With no accepted frame, this HUD block is hidden.
The Animation counter follows the displayed Animation sample. The State
counter identifies the same immutable snapshot when shared, otherwise the last
recorded State at or before it. Equal-time Event boundaries use Event provenance,
so a future Event's State never describes an earlier Event's picture. A run with
no State samples shows **State frames: 0 / 0**. These counters use metadata only;
new records refresh their totals without moving the accepted picture or reading
another State frame.

**View > Resource Usage...** contains Overview and Cache. Overview groups the
measurements into System memory, Graphics memory and Disk space. System memory
shows FunDEM usage, Peak usage and System available. Graphics memory shows each
adapter's name in a Device row, followed immediately by its Usage / budget row.
Both rows update together. This budget is the operating-system allowance for that
process and device, and can change as other applications run.
Disk space places Available disk space next to Estimated remaining disk space,
which gives the additional State/Animation storage still needed. Help identifies
the output directory, forecast source and uncertainty. Insufficient known disk
space appears as inline feedback.

Cache groups reusable data into Result frames and Render data. Each usage row
pairs `≈used / budget`; the budget is the current automatic cache target.
Cached animation frames shows cached / total available Animation frames.
Preload animation pauses playback and fills the existing bounded cache through
the common cancellable task window. Clear cache in Result frames retains the
accepted frame; Clear cache in Render data retains the active scene and camera.
Refresh and Close stay in the footer on both tabs. Estimated values carry ≈.
Budgets grow automatically with available capacity while retaining system and
operation headroom. The RAM/GPU/Files status links open this same window.
Measurements describe current process use; cache values and future output
requirements are estimates.
Opening Results completes bounded preload and first-view preparation before its
shared loading window closes.

Cache hits avoid another background read. One low-priority decoder prepares
up to two seconds of upcoming recorded time within budget. User seeks take
priority and rapid dragging keeps only the newest requested target. If the
next playback frame is not ready, the picture and accepted HUD values remain
intact and playback timing pauses immediately, without a Buffering caption.
Playback then continues
without skipped frames or a rapid catch-up. Switching workspaces and changing
colors, transparency or camera do not discard decoded animation frames.
Scene preparation reuses unchanged frame/filter/range data and particle bounds.
None of these caches changes recorded precision, frame order or physical data.

The playback row contains five fixed-size buttons and a stretching slider,
with 2 px gaps; the slider reaches the right inset. Frame/time labels and
Buffering text do not occupy this row. Render View instead shows three fixed
rows, **State frames: n / N**, **Animation frames: n / N** and **Time: ... s**, using the shared aligned name/value
layout. Legend bars avoid that block and existing axes/annotations; the
information anchor does not move. Resizing does not add gaps
or move controls as accepted numbers change. Visible time keeps at most five
decimal places with trailing zeros removed, using scientific notation for
very small/large values. Recorded doubles keep their full precision. Busy or
Play/Pause changes do not rewrite unchanged accepted text or rebuild the toolbar.
While calculation saves new records, background frame-list refresh does not
disable or flash Play/Pause; playback can start during that metadata refresh.
Opening an archive, reading a requested frame/seek, or having no next saved
frame still guards Play. During playback, the button remains Pause rather
than following unrelated background activity.
PNG images and MP4 animations include these HUD annotations. A selected-frame
image freezes its explicit source Animation observation and both stream indices/totals. An
animation resolves State/Animation counters from each source sample and the
same frozen manifest; Time follows that sample, independently of encoded FPS.

New Event Configuration records complete State every S steps and Animation
every K State intervals, with canonical animation cadence A=S*K steps.
Every Animation frame records complete State at the same step and time.
State interval is at least one, and the Animation step interval is its positive
integer multiple. Accepted Events ending between Animation intervals keep their
exact final step and observation. Existing Results retain their recorded data.
Playback, animation export and the visible frame counter use animation frames.
Plot reads complete-state samples and offers its own saved-State frame choices;
it does not infer data from animation images or interpolate missing samples.
When both intervals sample the same step, one complete snapshot file is shared
by both indices. Older single-stream archives remain readable by both workflows.
Console reports each successfully saved Animation frame and its time. State-only
saves retain their archive data and Results updates. GUI delivery batches Console
records in order and combines frame-list notifications to the latest complete
counts, so dense output keeps a bounded pending queue and retains every recorded
sample.

Ordinary frame seeks, playback and live archive refresh show no loading bar
or separate task window, including in the bottom status area. The last
accepted image, frame number and time stay together until the replacement is
ready; playback waits silently without a Buffering caption. Rapid
seeks supersede older requests; a failed read keeps the accepted image and
time and reports its cause in status/Console. Cache hits, background lookahead
and historical Legend scans remain silent. Opening a run or choosing explicit
Preload instead uses the one common foreground task window through bounded
cache preparation and the accepted first render. Pause/Reset may show ordinary
status text, not a loading bar. Native preparation retains its common task
window; no progress bar is inserted inside Render View.

### Display and legends

Contact **Information** identifies the selected pair as **Source particle instance 1**
and **Source particle instance 2**. Neither Contact nor Bond Information shows
contact, total Bond or broken-Bond counts. The recorded interactions and damage
state remain intact for rendering, Filter and Plot; this removes only summary rows.

Results Bond Property contains only **Information** and **Display**. Its
Information uses the same **Applies to particle instance 1** and **Applies to particle instance 2**
captions as Simulation, without count rows. Geometry, stiffness, Beam, strength
and damage settings are not
repeated here; use Simulation to author those parameters before Submit.
Contact and Bond endpoint names link to their archived Results Particle
Instances by stable ID. Selecting one preserves the accepted frame, camera,
visibility and Display settings; it does not open Configuration or seek.

Particles initially show; Contacts/Bonds initially hide. Their Work Tree
checkboxes are independent and are the only visibility controls. Hiding
particles does not hide interactions. Results color/opacity initially copy
the recorded Simulation appearance; later edits are independent.

Particle Instance **Display > Shape** selects **Particle** (default) or
**Arrow** for Sphere, LS and SPH. Particle shape offers Uniform color, velocity
and displacement magnitude; SPH also offers pressure, clamped nonnegative for
display only. Arrow offers velocity magnitude (default) or displacement
magnitude, choosing both direction and color. Each shape remembers its metric.

Arrows begin at recorded particle centers. **Display size** (1–100, default
50) caps their tail-to-tip length at that percentage of each particle's
bounding radius; SPH uses half its spacing. Zero or unavailable vectors are
omitted. **Scale by value** is initially off, giving finite nonzero arrows
the configured length. Turn it on to scale the complete glyph linearly by
its magnitude divided by the largest absolute active Legend endpoint, capped
at 1; an all-zero range draws no arrows. Both shapes obey visibility, opacity
and filters. These are undoable Results appearance changes; Simulation,
saved particle state and physics are unchanged. Contacts/Bonds offer available recorded force/energy/stress
quantities. Bond maximum tensile and compressive section stresses are separate
nonnegative magnitudes including bending; Maximum shear stress is nonnegative.
Missing values
are unavailable, not invented zero.

Interactions draw center-to-center cylinders or point-normal disks, including
Wall endpoints. Display size sets `r_max = size/100 * min(endpoint radii)`,
range 1–100, default 50. Scale by value uses
`r = r_max * clamp(abs(value)/V_ref, 0, 1)`, with `V_ref` the greatest absolute
active Legend endpoint. Zero/all-zero values draw no glyph. Without scaling
or with Uniform color, radius is fixed at `r_max`. Visual radii use absolute
magnitude and never become negative.

Bond stress coloring offers **Maximum tensile stress**, **Maximum compressive
stress** and **Maximum shear stress**, all in Pa and nonnegative. For axial
force N positive in tension, reference section area A, radius R and bending
moment magnitude Mb, tensile stress is `max(0,N/A+Mb*R/I)` and compressive
stress is `max(0,-N/A+Mb*R/I)`. Bending can make both positive; an absent
component is zero. Shear combines uniform shear and torsion. These use the
recorded Bond-point moment and original section area, not the display radius
or a damage-reduced area. Non-positive area makes stress unavailable. The
Stress legend remains shared; its colors encode the selected magnitude, not
a signed compression/tension label. No core failure criterion is changed.

Legend supports History, Current frame and Custom ranges. Every category
defaults to Current frame, including solid/SPH particles, Contacts and Bonds;
explicit History or Custom choices remain unchanged during replay and
metadata refresh. Each physical category starts with a distinct palette,
shared by its scene colors and bar. Set **Low color** and **High color** with
the existing color panel, HEX or RGB inputs to choose any two endpoint colors.
All objects using that category share the change; other categories and runs
keep their own colors. **Restore default colors** restores only the selected
category's palette, preserving range mode, limits and bar position. Color
changes use Results Undo/Redo and are frozen for image/animation export.
The stress scale uses neutral at zero. Values outside the selected range
use the color at its nearest limit. Current uses actual visible, filter-kept
data in this frame. History uses all saved
frames of the currently enabled instance/pair/Bond-recipe scopes and their
exact selected Color by metrics, not unrelated objects or other quantities.
That scope remains stable across events; hiding an object or changing its
metric updates the range. History does not rescan earlier frames against filters.
Older archives build their scoped history in the background only when needed,
without changing files or playback caching. Until ready (or on a nonmodal
reported read failure), the bar explicitly shows the current-frame fallback.
Bars are movable. Render View retains the shared sixteen-button toolbar
for framing, standard views, projection, reference planes and grid style.
Selected templates, particle instances and
visible interactions use a lightweight outline, with the same perspective as
the scene; opaque foreground objects hide its rear edges. It is omitted from
exported images and animations. Standalone LS Geometry inspection has no
selection box and still supports **Fit selection**. There is no standalone
Bounding box button or Min/Max annotation. Calculated Bounding box size,
template centering and camera fitting still use the accepted conservative bounds.

### Filters and plots

Clip and Threshold also apply to SPH Blocks and Jets. In Filter Add/Reconfigure,
open **Target > Objects** and select the desired SPH sources in the **Particle
Instances** group. The same selector groups Bonds and Contacts separately;
choose only solids, only SPH sources, only Bonds or only Contacts per filter.
There is no separate Source field. Accept the dialog, then enable the Filter's
tree checkbox. Property's Information lists the selected targets; use
Reconfigure to change them.
Clip uses the saved
particle centers; Threshold can select Velocity, Density, Pressure
(nonnegative) and the other shared SPH quantities. Only already-emitted Jet
particles in that frame participate. Empty Objects filters nothing; other
sources and solid interactions remain unchanged.

Large scenes retain shared LS surface buffers between frames; only changing
particle state and appearance are uploaded again. LS triangle resolution is
preserved, and spheres/SPH use GPU sphere impostors. There is no automatic
particle-count truncation or mesh decimation. Available graphics memory and
per-buffer API limits determine capacity; a failed allocation reports an error
instead of displaying an incomplete population. Hiding an unused population
can reduce rendering load without modifying saved results.

Reusable CPU meshes, inactive workspace scenes and GPU groups use automatic
budgets coordinated through **View > Resource Usage... > Cache**. Their estimates
exclude the active scene and framebuffer. Workspace navigation retains cameras
and eligible cached scenes; geometry/Display/Filter/frame changes rebuild their
affected presentation. Clear releases inactive entries and preserves the active
scene and cameras. Project replacement resets workspace bindings. All workspaces
share one live viewport/context.

Filter + offers disabled-by-default Clip and Threshold. Add and Reconfigure
open the common Configuration with Name and Target > Objects, without Source.
The selector groups Particle Instances (solid and SPH), Bonds and Contacts.
Property's read-only Information shows only
the complete target label (Applies to particle instance, Applies to bonds, or
Applies to contacts) as selected names one per line or None, using common
colon rows without target selectors. Available names are stable-ID links
to the corresponding Results objects, including SPH sources, Bond recipes
and Contact pairs. Selecting one preserves the accepted frame, camera and
visibility and does not enable the Filter. Change targets through Reconfigure;
plane or data-range settings remain editable in Property.
Its internal physical families remain solid particles, SPH particles,
Contact instance pairs and Bond recipes, not additional Source controls.
Empty Filter Objects means None:
the filter has no effect until objects are explicitly selected. The shared
selector supports individual and group checkboxes. Uncheck every choice and
confirm to remove all targets; Cancel retains the previous selection. The
selected objects determine the internal data family, with incompatible selections
reported before acceptance; changing targets never selects All implicitly.
Clip keeps the plane's positive side by
saved centers/interaction points. Selecting it shows a semitransparent guide,
even if disabled; another selection removes it. Threshold is an inclusive
range using shared Plot quantities. For Contacts, native surface-node records
for one physical particle pair share that pair's aggregate value and the same
keep/reject decision; Bonds are sampled individually. Clip tests each saved
interaction point separately. Masks are population-specific and do not change
Plot, DAT export, archives or physics.

Plot + offers Time history, XY curve, Scatter, Histogram and Uniaxial
compression. For the first four kinds, set **Identity > Name** and
**Data > Data source**: **Particle Instances** (solid and SPH together),
**Bonds** or **Contacts**,
then expand the named Plot and use the right-side **+** beside its **Curves**
child to add a curve. An empty chart points to this same action. Plot names
have no **+**. Select the named Plot for X axis, Y axis, Figure and applicable
Histogram Distribution;
its Reconfigure changes Name and Data source. All its curves belong to that
one category. Remove all curves before changing Data source; attempting a
category change while curves exist shows an inline instruction and leaves
accepted curves/data unchanged. No automatic deletion or query remapping occurs.
Select a curve leaf for Information and Display. Information lists accepted
source objects as vertical reference links: **Source objects** for Time
history/Histogram, **X objects** and **Y objects** for XY/Scatter, or
**Specimen**, **Upper platen** and **Lower platen** for Uniaxial compression.
Three-point bending links **Specimen**, **Loading roller**, **Support 1**
and **Support 2**.
A name selects its Results object without seeking a frame or editing the
query; missing references are Unavailable. Display owns Color and
applicable line/marker styles; its Reconfigure edits that curve's Name and
complete X/Y or Data settings. The native checkbox left of each curve leaf
controls visibility; shared tree context/Delete removes the curve with
Results Undo/Redo. Removal immediately drops its chart series and table rows;
other accepted points and Histogram bins stay unchanged until Reload.
Structural **+** nodes have no Property. There is no
Current curve selector, Edit menu, Visible field or Add curve button in
Property. Each curve owns its X/Y/data
query, applicable Frame and style, but its X/Y/Data editors have no separate
Source selector: they inherit the Plot's category. Plot requires explicit nonempty object
selections: empty never means All. Shared axes require compatible units.
Plot has no Event Scope selector. Time history evaluates the complete run's
committed State samples. New recordings have one right-continuous interior
boundary sample per stream; existing archives may retain distinct same-time
observations, which remain in their recorded order.
XY curve, Scatter and Histogram each select one saved **State frame** per curve.
**Time (s)** displays the selected State sample's recorded time. Each curve
uses that sample without interpolation. Every curve uses its Plot's Data
source; XY/Scatter additionally use the same
object population for X/Y, matching individual samples by stable identity.
XY connects those points; Scatter uses markers. These three single-frame
types have no population Reduction control. Cancel or failed reading retains
the accepted recipe and matching chart/table.

Plot and Threshold present **Quantity** under **Vector** and **Scalar**.
Plot has 19 Particle Instance, 11 Contact and 12 Bond quantities. Vectors
offer X, Y, Z and Magnitude. Particle Instances combines solids and SPH.
Common quantities appear once, and family-specific quantities remain
unavailable for other families.

- **Coordination number** counts distinct finite-mass solid neighbors in
  each frame. Multiple contact nodes for one LS pair count once. Wall
  contacts are excluded. An isolated eligible solid contributes zero.
- **Volume-equivalent diameter** uses a sphere's actual diameter or the
  diameter of a same-volume sphere for an LS particle with positive
  integrated volume. Both values use metres.
- **Broken** reports 1 for a completely broken Bond and 0 otherwise. In
  Time history, choose **Sum** for the count or **Mean** for the fraction
  among selected Bonds that exist in each frame.

Bond curves default to **Elastic energy**. Contact **Overlap** takes the
largest recorded overlap for each particle pair. SPH **Pressure** displays
negative values as zero and preserves the recorded value in the archive.
**Uniaxial compression** instead opens its first curve Configuration directly;
Add creates chart and first curve together after a successful read, and Cancel
or failure creates neither. Its Name initially names both; later curve
renaming does not rename the chart. Later Curves + and curve Reconfigure use
that same dedicated measurement form, not a single-frame XY query.

| UCS group | Parameters |
| --- | --- |
| Identity | Name |
| Specimen | Source: one or more explicitly selected non-Wall solid instances; positive nominal Initial height (m) and Section area (m²) |
| Loading platens | Upper source and Lower source: distinct Wall-material solid instances, each containing exactly one particle |
| Measurement | Loading axis: X/Y/Z; Reference time range (s): inclusive Start/End; Loading method: Upper, Lower or Average (default) |

Enter physical specimen dimensions; the conservative cluster box is not a
measurement of specimen height or section. The first valid complete-State
sample within the range establishes platen reference positions. Engineering
strain is their relative closure divided by nominal height, positive in
compression. It keeps one reference across Events.

The reactions sum recorded contacts and crossing Bonds between the selected
specimen and each platen, not the wall particle's force accumulator or
unrelated contacts. With positive loading axis `e`, raw reactions are
`Ru=dot(Fupper,e)` and `Rl=dot(Flower,e)`, where F is the force on each platen
from the specimen. Upper stress is `Ru/A`, Lower is `-Rl/A`, and Average is
`(Ru-Rl)/(2*A)`. No absolute-value clamp or initial-stress subtraction is applied.
The chart shows Axial strain (%) versus Axial stress (MPa). Its table retains
State frame, Event, Step, Time, Curve, strain, stress and both raw reactions
in N; the lower reaction is normally negative during compression.

Only committed complete-State records in the inclusive range are used, in
recorded time/order, including any legacy coincident Event-boundary samples.
New-run boundary coalescing happens before publication, not in the Plot reader. There is no
interpolation, smoothing, time averaging or reference reset between Events.
Reload is manual. Curve styling, axes, chart/table, numerical/figure export,
inline errors, Cancel and Results Undo/Redo use the existing Plot workflow;
they do not modify physical data or rerun the solver.
Solid-system energy excludes fluid energy; gravitational energy uses each
Event's gravity and the zero-potential plane Z = 0. Reload is manual; playback/incoming frames do not reload
charts. Table/CSV and chart use the same accepted data.

### Three-point bending Plot

Choose **Plot + > Three-point bending**, select the specimen, the loading
roller and two distinct supports. Choose the positive world axis from the
supports toward the central roller and set the inclusive State time range.
For a roller moving down in Z, choose **Z**. The first complete State in the
range sets zero deflection. The X axis measures travel toward the supports
relative to their mean position in mm. The Y axis shows the compressive
reaction at the loading roller in N. Both support reactions are available
in the Data table and CSV. Results come from recorded specimen–fixture
contacts and intact Bonds. A missing specimen or fixture produces a gap.

### Plot axes and appearance

Each **X axis**/**Y axis** group provides Title, Automatic/Custom Range and
Logarithmic scaling. Leave Title blank to use the recorded quantity and unit;
a custom title does not convert units or change samples. Major ticks can be
Automatic or an approximate Target count of 2–12. Tick format offers
Automatic, Decimal or Scientific; Significant digits is a minimum of 1–17
(default 4), increased when necessary to distinguish adjacent ticks. These
are label settings, not rounding of saved data. Minor ticks and Grid lines
are independent for each axis; both grid directions also obey Figure's master
Grid switch. **Figure** defaults to 14 px axis titles, 12 px tick labels,
Inward ticks and enabled Grid/Legend; Outward ticks are also available.
Axis and Figure changes use Results presentation Undo/Redo, without Reload.

The toolbar has one fixed-width **Chart**/**Data table** toggle showing the
current view; click it to switch. Table values and column/row headers are
centered. Chart, table, Copy TSV and Save CSV retain the same accepted data;
alignment and tick-label formatting do not reduce numerical export precision.
Copy is limited to 100,000 cells and 16 MiB of generated text, including the
bounding rectangle of a sparse selection. An oversized copy shows an inline
error without changing the clipboard or truncating the data. Use **Save CSV...**
for larger tables.

Charts share one scientific-figure style and measured renderer with figure
export: white background, neutral axes, a subtle grid, colorblind-friendly
default colors, readable tick labels and wrapped legends. User colors and
line/marker styles remain unchanged. No smoothing or resampling is added for
appearance. If the chart is too small for its labels, enlarge it or hide Legend.

Use **Export figure...** on the Chart toolbar for **PNG image**, **SVG vector**
or **PDF vector**. The shared Configuration has one Output group: Format,
browse-only File path, Width (mm), Height (mm), and PNG-only Resolution (dpi).
It defaults to 160 × 100 mm and 600 dpi. Each dimension accepts 40–400 mm; PNG
accepts 150–1200 dpi, up to 32,000,000 pixels and 16,384 pixels on either edge.
Oversized images, incompatible filename suffixes and layouts that cannot fit
their labels stay inside Configuration as errors, rather than silently
changing the output. SVG/PDF use the same vector painter and need no dpi field.
Screen, PNG, SVG and PDF share the same renderer, including accepted axis
titles, fonts, tick formats/direction and grid choices. Layout adapts to the
requested canvas size without smoothing or changing the samples.
Save is left of Cancel. Existing-file replacement requires confirmation;
publication is atomic. Export preserves the accepted samples/current style
and never reloads results or seeks the scene. Use Save CSV for numerical data.
Figure export captures the accepted chart once, then performs layout, drawing,
encoding and writing in the background. Cancel is checked during sample batches
and streamed PNG output. The live chart stays available and its accepted data
and appearance remain unchanged.

Longer Reload operations show actual scanned-frame progress and Cancel while
keeping the last accepted chart/table visible. Cancel waits for a safe read
boundary and does not publish a partial chart. Save CSV uses the accepted
table snapshot, full numeric precision and atomic background writing, with
the same delayed feedback. Playback and incoming frames still do not reload
the Plot automatically.
Reload reuses results from unchanged State frames. It detects a replaced event
boundary even when the frame count stays the same. Editing a curve reuses
accepted results for the other unchanged queries. Histogram curves still use
a common bin range. Data Table prepares its view when opened. Dense charts use
screen-resolution drawing while the table, CSV and figure exports retain the
full accepted data. Results Undo/Redo restores the Plot configuration.
For an older dataset that has left the undo cache, use Reload to read its data.

Results Edit uses separate Undo/Redo and can remove detached Plot/Filter
definitions, not archived physical objects/files or project parameters.

## 7. Interchange and image/animation export

File > Import particle instance... reads DAT materials, sphere positions/radii
or embedded LS geometry/orientations, plus per-particle velocities/angular
velocities. It is available from any workspace when an unlocked authoring project
is open; an archive alone is not an authoring project. Give imported
definitions/instance names and choose an editable Event. The protected imported
State base and submitted Events are excluded; if no editable Event exists, import
creates a default ordinary Event. Cancel or failure preserves unfinished Property
text, the project and current selection. Success opens Simulation with the new
instance selected and records one authoring Undo transaction, leaving archived
Results and Results Undo/Redo unchanged. DAT uses one unversioned
header, `FUNDEM_INSTANCE` followed immediately by `TYPE`; a numeric version
token is not accepted. Sphere files declare `GEOMETRY_STATE NONE`, and LS files
require `GEOMETRY_STATE PREPARED`. Numbered headers, raw triangle-only payloads
and recipe-bearing definitions are rejected without deleting or rewriting the
file; there is no silent conversion or raw-mesh import mode. Numeric text uses
17 significant digits.

Prepared LS data contain exactly one accepted corrected surface point set,
triangles, volume, unit-density inertia and SDF origin, spacing, dimensions and
signed samples. No primitive type/recipe, geometry flags, padding, explicit
bounding radius, original-source points or centroid offset are stored. Import
derives radius from the largest point distance from the local origin and uses
positive volume to identify integrated geometry. Internal source points are
empty and the internal offset is zero. Accepted discretization comes from the
file; import offers no grid override.
Import adopts the accepted data using FunDEMBeta's data-only prepared-LSInfo
factory. It does not rebuild the grid, recompute SDF, integrate mass properties,
or correct the centroid. Surface points, grid origin and particle state remain
unchanged, including the SDF sign. Open planar-wall data are supported through
their accepted grid and zero-volume properties. All imported LS geometries
become ordinary Triangle mesh definitions: Reconfigure opens that existing
Configuration, not the former primitive editor. Opening or cancelling it keeps
accepted data; explicitly applying changed settings is a separate mesh/grid
rebuild with normal reference/frame safeguards. Closed-mesh future Reverse signed-distance field
is inferred from the accepted signs using the native Triangle mesh query,
without rebuilding the imported grid. Open planar walls retain their grid for
import, solve and Name-only Reconfigure; an explicit Triangle mesh grid rebuild
requires a closed two-manifold surface and otherwise fails without changes.
Replacing the mesh with a valid closed OBJ is supported. Inconsistent data are rejected.

Import centers the resulting template and stores the matching Instance
translation. This preserves each current world pose, velocity,
angular velocity and material assignment in the same particle order; quaternion
normalization can introduce last-bit roundoff but does not change the rotation.
The import creates fresh IDs. Failure or cancellation adds nothing.

In Results, **File > Export particle instance...** lets you choose a complete
**State frame** by its one-based number. **Time (s)** shows that State's recorded
time. The initial choice is the State nearest to the displayed Animation time,
with the earlier State selected on a tie. Choose **Instance** from the eligible
Sphere/LS instances at that State, then choose the **DAT file**. Changing State
updates Time and the instance choices; an ineligible selection is cleared and
must be explicitly selected again. Export includes the whole recorded instance,
including particles hidden/filtered in Render.
The dialog keeps a frozen archive snapshot. It does not seek playback, use the
displayed Animation data, or change its source when new frames arrive.
LS export carries
the accepted PREPARED geometry, not just a surface to be discretized again.
DAT is interchange, not an exact solver checkpoint: it does not restore
Contact/Bond histories, material-pair laws, SPH state or the run timeline.

### State projects

**File > Export State...** exports a new self-contained `.fundem.json` project
and its required `.fundem-data` file. Select a complete **State frame**, inspect
its recorded **Time (s)**, and use **Particle instances** to select one or more
instances actually present at that sample. Nothing is selected implicitly when
the selection is empty. Hidden/filtered particles remain part of the scientific
state. The common Configuration contains Source and File groups. Source also
contains **Include bonds**, checked by default. Retained Contacts must have both
endpoints selected; the same rule applies to Bonds when Include bonds is checked.
Uncheck it to export no Bonds, including broken records and their histories or
saved Bond parameter groups. Selected particle states and Contact histories are
retained with their normal dependencies. This choice does not change the source
recording or its project.

The project includes required material definitions and material-pair laws, accepted
geometry/SDF/mass data, centered snapshot templates, ordinal particle states and
the retained actual Contact connections/histories and optionally Bonds. Keep
the JSON and its content-addressed numerical sibling together. Large arrays are stored in the
sidecar, without a numeric format version. The export is bound to an immutable
selected State and never seeks Render View or changes the source archive.

Use **File > Import State...** to open the exported JSON. A complete
validation/loading task publishes a new project only after all required data and
references are ready. Cancel/failure preserves the previous project. The ordinary
**Imported state** Event starts and ends at **0 s**, is read-only/accepted, and
shows the retained Bond groups and their saved parameters when exported.
An export with Include bonds unchecked has no initial Bond groups or Bond
connections. Results immediately shows its retained particle/Contact/Bond
observation. The original recorded time is kept as source information. Add, configure and submit later ordinary Events to compute
from that imported base; the base itself consumes no steps.
When the first computing Event starts at 0 s, its start frame replaces the
indexed imported frame in both State and Animation. Results shows one frame
at the shared boundary, including any newly entered particles or Bonds.
The original snapshot files and the project's physical initial State remain
unchanged for restoration and Reset.

Before submitting the first ordinary computing Event, an imported Bond group's
Name, strengths, fracture energies/mixity and fracture/damage choices can be
edited through its normal Property and Reconfigure controls. Stiffness source,
the four stiffnesses, Beam values, length, area and radius factor stay read-only.
Use Reconfigure to select Allow fracture and Progressive damage. Property shows
Strength when Allow fracture is enabled, and Damage when both options are
enabled. Hidden coefficients retain their configured values. Its source particle
instances appear as read-only Information; connections and generation selection
remain fixed.
The imported base Event, particles, materials/material pairs, geometry and
templates remain protected. Once a computing Event is submitted, the normal
physical-edit locks apply.

Bond parameter edits preserve the saved endpoint frames, damage and broken
state. Already broken Bonds retain their broken state when fracture is turned off.
Existing frozen onset/damage history remains; initialization applies the changed
fracture/damage settings without regenerating connections or changing recorded
stiffness/geometry, then rebuilds loads without advancing damage. The new
settings can change the subsequent failure response. Save/Undo/Redo retain
the original observation separately from the accepted parameter changes.

Particle positions, quaternions, per-particle material/velocity/angular velocity
and actual spring/Bond damage histories are retained. Import restores Contact
geometry and the saved sliding, rolling and torsional spring histories.
Once computation starts, the native Contact search computes effective mass
and radius before the initial force assembly. Template centering changes
only the local reference and matching Instance placement; geometry centroid/SDF
correction is not repeated. Existing Bonds are restored with their saved
connections and broken states; an export without Bonds resumes the
selected subsystem without those connections. Removed external interactions
cannot remain as a false force in the first integration kick.

Restorable SPH State samples additionally carry raw unwrapped fluid state and Jet
source/supply/release progress. The imported source continues its remaining supply
at rebased source times. SPH searches and hydraulic loads start a new acoustic
interval at the represented state, preserving position, velocity, density and
pressure. This selected-subsystem acoustic rebase is not claimed to be a bitwise
continuation of a future fluid endpoint evaluated with the old neighbours.
Older SPH samples lacking the required payload cannot be restored and produce an
explicit diagnostic rather than resetting density or inventing Jet progress.

### Images and animation

**File > Export image...** and **Export Animation...** use the loaded recorded
run independently of the current workspace, selected Work Tree object, Property
form or Plot. They never commit unrelated pending edits. Without a committed
Animation frame they remain unavailable; File does not fall back to capturing
an authored Setup/Simulation view.

For PNG, **Source** contains **Animation frame** and its read-only **Time (s)**.
Choose a one-based recorded Animation index; the initial choice is the accepted
Animation frame when available, otherwise the first saved frame. **Output**
contains File path/Browse and 1080p/4K Resolution.

For Windows H.264 MP4, **Timeline** always shows **First animation frame**,
**Start time (s)**, **Last animation frame**, **End time (s)**, Playback speed
(0.1x–10x, default 1x) and calculated Duration (s). The bounds initially span
all committed Animation frames and can be edited directly; there is no
All/Custom range switch. Timestamp previews use frozen metadata without
changing replay. **Output** contains File path/Browse, 1080p/4K Resolution and
30/60 fps. FPS affects encoding, not solver steps or physical information.

Both workflows freeze source identity, manifest and Results camera/Display,
legends, filters, reference grids/axes and the selected Clip guide. Captures omit
selection outlines, menus and panels. Frame decoding uses background work;
detached GUI-thread OpenGL capture neither seeks nor resizes the live view.
File exports publish only a complete successful file. Cancellation/failure
preserves existing destinations, source archives and authored draft values.
The internal static-view PNG API remains separate from these File commands.

PNG encoding and file writing use detached background data and common delayed
feedback; this does not mean every rendering/filter step is asynchronous.
Successful publication completes the export. PNG encoding checks cancellation
between bounded output writes while retaining the existing destination.

Closing FunDEM confirms the current runtime and unsaved-project choices, then
finishes an asynchronous shutdown. It requests Reset, cancels background reads
and drains outstanding work with responsive feedback before closing the window.
The current native step or Windows encoder operation completes at its safe
boundary; committed result files remain available.

## 8. Troubleshooting

- GPU unavailable: check the actual package, device and driver. Building needs
  a toolkit; using the deployed binary does not.
- Finite LS instance rejected: integrate a valid positive-volume geometry, or
  use Wall material for zero-volume geometry.
- Contacts/Bonds invisible: check their own checkbox, opacity, filters, saved
  data and scalar availability. Each interaction category has its own visibility control.
- Plot empty: choose valid explicit objects/ranges and Reload; missing saved
  values cannot be reconstructed by changing display.
- Unexpected origin: use core-corrected local geometry plus centered
  template/instance placement, not raw OBJ origin assumptions.
- Setup/submitted Events remain read-only at the submission frontier. Edit an
  unsubmitted Event, or use Withdraw from an awaiting submitted Event to unlock
  it and every later submission together. Started Events require confirmed
  Reset, which withdraws ordinary submissions while preserving authored
  definitions and saved results.
- Old project rejected: only the current unversioned layout is supported. Do not rename format markers. Keep mandatory binary siblings and all nine required definition arrays, including empty arrays; each solid instance requires its owning eventId.

Read [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) and
[FUNDEM_CORE.md](FUNDEM_CORE.md) for attribution. Examples and UI checks do not
replace independent engineering validation of a new physical model.
