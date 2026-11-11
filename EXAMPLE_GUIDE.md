# FunDEM 0.4.4 example guide

Windows x64 — seven built-in examples and user-supplied benchmark data

## Open, run and inspect an example

1. Start `FunDEM.exe` from the complete application folder, or open the installed
   application. No compiler or source checkout is needed.
2. Choose **File > Open Project > Examples**, then an example below. Opening
   creates a separate writable working copy; it does not run the simulation
   or overwrite the installed input. Use **Save As** to keep your own copy.
3. In **Simulation**, select an Event to inspect its particles. Review
   the ordered Events and use Submit's **Execution**/**Output** draft before running. CPU is the
   default. GPU/Hybrid need the CUDA-enabled package and a compatible NVIDIA
   GPU/driver; unavailable modes are reported, not silently replaced by CPU.
4. Use the first Event row's **Submit** to edit Execution mode/device and Output directory
   and review read-only Overview totals, then confirm preparation in the same
   dialog. Only successful Ready accepts those settings; Cancel/failure leaves
   the accepted model unchanged. Ready means native input is prepared,
   not that time or recording has advanced. Use **Run** to start recording and
   calculation. **Pause** waits for a safe boundary;
   Run then resumes that same session. **Step** advances one DEM step. Submit
   further Events in order; Run stops at the submitted frontier and later Run
   continues the same native state after the next submission.
5. Open **Results** to view committed recorded frames. Use the timeline to
   play, step or seek; an early recorded frame is not necessarily the final
   state. The run's `run.json` can also be reopened later.
6. Use Results Work Tree checkboxes to show particles, Contacts or Bonds.
   These populations are independent: hiding particles does not hide their
   interactions. Use **File > Export current frame image...** for PNG or
   **Export animation...** for Windows H.264 MP4.

Submit Events sequentially using the right-hand Event-row actions. Submission
locks Setup and the accepted Event prefix, not future unsubmitted Events.
Failure/cancellation preserves accepted input and Results. Run stops at Waiting
before an unsubmitted Event; submit the next and press Run to continue the same
native state/archive. Only the submitted, unentered tail may Withdraw. Reset
asks for confirmation (default Cancel), then stops calculation and withdraws
all Events without deleting saved results. Use Reset before starting a fresh
calculation from unchanged authored initial state. Display, read-only inspection
and Results editing remain available.

The supplied examples use ordinary fixed-step Events. To add a relaxation
stage in a working copy, create another Event with an explicitly chosen fixed
duration and recording cadence. Submit each Event in order; at least one accepted Event
is required before Run/Step.

Every Event advances time by `timeStep * steps` while preserving integrated
particle/Contact/Bond/SPH state. Both streams use normal start/interval/end
recording. Adjacent Event-end/next-start candidates coalesce at their ordinary
shared boundary before publication. A later submitted boundary can supersede only
the indexed frontier tail using a new immutable file plus explicit lineage;
existing frame files are never overwritten. A project/result file is not a resumable checkpoint.
Preserve supplied case parameters unless intentionally testing a changed copy.

## Global damping model

**Cloth drop into a box** (`clothBoxDrop`) now omits Global damping because
PFC-style local damping can alter free fall and impact. Its restitution
**e = 0.05** and sliding friction **mu = 0.6** are retained. None of the current
seven maintained examples configures Global damping. This correction does not
change other project parameters or rewrite old user projects/results; earlier
frames remain observations of their original inputs. No complete physical
revalidation of the revised cloth case is claimed. The general model remains
available; review its
[formula and usage guidance](USER_MANUAL.md#particle-external-constraints)
before adding it to a dynamic/free-flight case.

## The seven examples

| Example menu entry | What to inspect | Workload and operating note |
| --- | --- | --- |
| Gömböc self-righting | The non-spherical body's original orientation, contact with its support and subsequent self-righting motion | A useful first example. Preserve its supplied orientation and drop position; changing them changes the problem. |
| Interlocked chain | Links physically interlock; fixed end bodies and free links evolve under gravity | The connections are geometric, not replacement Bonds. Keep the original link shapes, resolution and placement. |
| Cloth drop into a box | The bonded sheet falls and deforms against the box | Many Bonds make a full CPU run expensive. Enable the Bond recipe's Results checkbox if you want to inspect links. |
| Dam break around a square column | Fluid release, flow around the column and interaction with LS boundaries | Uses LSDEM + SPH. SPH computation and frequent stored frames can be expensive. Fluid pressure/velocity colors are Results display choices, not changes to the solver. |
| Brazil nut segregation | Sequential additions followed by prescribed vibration | Keep the event order. An early short run cannot demonstrate the later vibration or segregation stage. |
| Superellipsoid mixing drum | Sequential filling/settling followed by drum rotation | Keep all stages and the event-local wall-motion commands. Beginning rotation early changes the intended scenario. |
| Random-shape particle column | Irregular high-resolution LS bodies settling within their boundary | Large geometry caches require substantial RAM and load/generation time. Do not lower surface/grid resolution silently to make a run appear faster. |

The maintained inputs contain the current geometry construction, material and
Bond parameters, ordered Events, recording intervals and per-instance appearance.
The schedule table below is the current baseline, not a promise that every value
matches a historical application or an external reference model. Imported poses
remain explicit data. Procedural Brazil-nut and drum populations rebuild from
their saved recipes; random orientations need not match an older generator.
Changing only Results colors, opacity, legends or camera does not alter physics.

Before submission, these are ordinary editable projects: change Materials, Event
parameters, Particle Instance placement/initial motion or compute mode, then
use Undo/Redo and Save As normally. Recoverable Single/Lattice templates expose
their generation settings. Restored LS lattices offer the ordinary Uniform or
Random orientation modes and editable Count XYZ; there is no special built-in
mode that reuses cached orientations. Loading a valid optional cache does not
resample it; opening without that cache rebuilds procedural data from the
saved recipe. Neither path alters the event sequence. Nonprocedural templates remain imported, with a
fixed-order particle-data editor for individual positions, orientations and
Sphere radii or LS geometry references. Neither editor silently sorts particles
or changes stable instance references. Single-wall templates retain their
geometry/orientation configuration. Required accepted geometry remains available
even if an original OBJ path belongs to the example author's machine.

Template editing uses the ordinary authoring controls below. Accepted imported
states remain authoritative; procedural templates use the saved recipe and
orientation policy described above:

| Example | Template editing |
| --- | --- |
| Gömböc | Single LS geometry and orientation |
| Interlocked chain | Two movable LS lattice recipes; existing single wall templates |
| Cloth | Sphere HCP lattice recipe; existing single box template |
| Dam break | Existing single tank/column templates and event SPH sources |
| Brazil nut | Lower/upper LS lattices with ordinary Uniform/Random orientation controls, and a Single LS nut |
| Mixing drum | Three LS lattice batches with ordinary Uniform/Random orientation controls; existing single drum |
| Random-shape column | Original explicit arrangement through the imported-particle data editor |

Opening a Reconfigure dialog leaves accepted poses unchanged. A valid optional
cache is reused on Open; absent/stale caches are rebuilt. Explicit successful
Reconfigure uses the same generation recipe as
an ordinary user-created template, including its selected orientation mode,
angles/seed and Count XYZ. It does not copy prior quaternions by ordinal.
Stable IDs and references remain unchanged, and normal dependent-state checks
apply to changes in particle count or correspondence.

## Event timing and recording

Event Configuration uses three positive integers:

- **State interval (steps)**, S: integration steps between complete State samples.
- **Animation interval (states)**, K: State intervals between Animation samples.
- **Duration (frames)**, N: Animation intervals to calculate in that Event.

The Animation step interval is `A = S * K`; the Event step count is
`steps = A * N`. Event Configuration has no Information group, read-only
time preview or Start/End rows; Event Property retains
its accepted-definition information. Planned duration is not completed work.
All seven built-in examples represent their accepted schedules exactly with
integer S/K/N; this compatibility update changes no physical parameter,
initial pose, Event order or prescribed-motion coefficient.

| Example | Steps in Event order | S | K | N in Event order |
| --- | --- | ---: | ---: | --- |
| Gömböc | 1,200,000 | 500 | 1 | 2,400 |
| Interlocked chain | 150,000 | 2,500 | 1 | 60 |
| Cloth | 300,000 | 5,000 | 1 | 60 |
| Dam break | 150,000 | 2,500 | 1 | 60 |
| Brazil nut | 30,000 / 30,000 / 60,000 / 680,000 | 5,000 | 1 | 6 / 6 / 12 / 136 |
| Mixing drum | 50,000 / 50,000 / 50,000 / 250,000 | 2,500 | 1 | 20 / 20 / 20 / 100 |
| Random-shape column | 200,000 | 5,000 | 1 | 40 |

New recordings publish one sample at each interior Event boundary in each
stream. The prepared next Event's start is the right-continuous sample at
that timestamp, including its newly added objects. The preceding end is held
before publication, never overwritten after publication; failure/cancellation
retains that held end. The final run end is retained. A complete exact run
therefore plans `1 + sum(N)` Animation frames and `1 + sum(K * N)` State frames.
Older archives with separate equal-time boundary records remain readable
without rewriting them.

Full recomputation may take hours, especially for the cloth and high-resolution
LS cases. Actual cost depends on the selected backend, hardware, contact count,
SPH population and output cadence; there is no fixed completion-time promise.

Keep enough free space for recorded frames and do not start a long run merely
to test whether the project opens. Short runs verify only the executed prefix,
not later events, convergence or agreement with experiment. Example material
coefficients are demonstrations, not measured calibration for another material.

## ON-DEM periodic-shear benchmark

The entry is **File > Open Project > Benchmarks > ON-DEM bumpy-wall shear flow
(import data)...**. Unlike the seven examples, it requests an external data file.
The original author coordinates and converted benchmark payload are not
distributed in the public application package.

1. Read the [ON-DEM benchmark description](https://on-dem.atlassian.net/wiki/spaces/Index/pages/244744204).
2. Obtain your own authorized copy of the author's original
   [GranularShearFlow.data](https://raw.githubusercontent.com/dalilavescovi/GranularShearFlows/56b7cfdfd774cf5ae057a1c5c90e5191a0deb97b/InitialGeometricalConfiguration/GranularShearFlow.data),
   or locate your existing unmodified copy. The application does not download it.
3. Select that `.data` file through the Benchmark import entry. The importer
   verifies the original file and ordered coordinates; an edited/reformatted
   substitute is rejected. Cancellation/failure preserves the open project.
4. Successful import creates a separate editable project and does not start
   calculation. Inspect its settings and use Save As before running.

The imported diagnostic uses the following preset:

| Setting | Value |
| --- | --- |
| Total sphere count | 3,480, including both bumpy walls |
| Sphere radius; finite-particle density | 0.5 m; 1 kg/m³ |
| Gravity | Zero |
| Periodic directions | X and Y |
| Bottom/top wall X velocity | −1 / +1 m/s |
| Time step | 7.192545032e−5 s |
| Steps; recording interval | 100,000; 1,000 steps |
| Normal/sliding stiffness | 200,000 / (400,000/7) N/m |
| Normal/sliding explicit damping | 15 / (30/7) N·s/m |
| Sliding friction | 0.3 |
| Rotational stiffness, damping and friction | Zero |

The imported material pairs use **Stiffness source > Custom linear**, and
**Custom damping coefficient** is checked in their Configuration. The external
periodic-boundary service wraps the selected directions and supplements seam
contacts; FunDEMBeta evaluates native contact force/history on the combined
pairs once. No old external benchmark contact-force override is used.

This 100,000-step diagnostic is **not** the full 200-million-step reference
campaign, nor a claim of exact trajectory/contact/energy agreement with another
implementation. Obtain permission before redistributing the author's data;
converting or embedding the coordinates does not remove that requirement.

## Result and export reminders

- Output creates a new run directory; preserve it with its manifest, project
  snapshot and frame files when reopening or sharing recorded results.
- Save/Save As keeps authored input, not the computed endpoint. Keep all required
  `.fundem-data` siblings with a copied project; a missing optional
  `.fundem-cache` rebuilds from its recipe and is not a missing result frame.
- The current application records native result archives, not VTU or
  `energy.dat`. Custom Plot can analyze recorded solid-system energy without
  enabling those file outputs. Plot data refresh uses **Reload**.
- Contacts and Bonds start hidden in Results; turn on their own checkboxes.
  A scope can be valid even when it has no saved interaction in one frame.
- PNG/MP4 exports use the accepted camera, display, legends, Axes, Grid
  and selected Clip guide, omitting object-selection overlays. Select another node before export if the
  Clip guide should be omitted. FPS changes encoded timing, not physical data.
- The packaged [User Manual](USER_MANUAL.md) explains event inheritance,
  filters, plots, DAT interchange, replay caching and troubleshooting.
- Version 0.4.4 targets Windows x64; H.264 MP4 export uses Windows Media
  Foundation.
