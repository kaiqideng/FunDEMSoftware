# FunDEM 0.4.2 example guide

Windows x64 — seven built-in examples and user-supplied benchmark data

## Open, run and inspect an example

1. Start `FunDEM.exe` from the complete application folder, or open the installed
   application. No compiler or source checkout is needed.
2. Choose **File > Open Project > Examples**, then an example below. Opening
   creates a separate writable working copy; it does not run the simulation
   or overwrite the installed input. Use **Save As** to keep your own copy.
3. In **Simulation**, select an Event to inspect its particles. Review
   **Execution**, **Output** and the ordered Events before running. CPU is the
   default. GPU/Hybrid need the CUDA-enabled package and a compatible NVIDIA
   GPU/driver; unavailable modes are reported, not silently replaced by CPU.
4. Use the Render View **Run** button. **Pause** waits for a safe boundary;
   Run then resumes that same session. **Step** advances one DEM step.
5. Open **Results** to view committed recorded frames. Use the timeline to
   play, step or seek; an early recorded frame is not necessarily the final
   state. The run's `run.json` can also be reopened later.
6. Use Results Work Tree checkboxes to show particles, Contacts or Bonds.
   These populations are independent: hiding particles does not hide their
   interactions. Use **File > Export current frame image...** for PNG or
   **Export animation...** for Windows H.264 MP4.

Once Run/Step starts, Setup/Simulation settings remain locked until **Reset**,
including while paused and after completion/failure. Read-only inspection and
Results editing remain available. Reset releases authoring but does not delete
saved results. A new Run after completion starts from the unchanged authored
initial state, not from the old final frame.

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

The converted projects retain their supplied geometry, accepted particle poses,
physical parameters, event/activation order and recording intervals. The
presentation also retains the prior examples' per-group colors and transparency.
Changing only Results colors, opacity, legends or camera does not alter physics.

Before Run/Step, these are ordinary editable projects: change Materials, Event
parameters, Particle Instance placement/initial motion or compute mode, then
use Undo/Redo and Save As normally. Converted finite Particle Templates are
accepted imported samples, so their Reconfigure changes the Name only; it does
not regenerate their particle positions, orientations or count from a new
lattice recipe. Single-wall templates retain their geometry/orientation
configuration. Embedded accepted geometry remains available even if an
original OBJ path belongs to the example author's machine.

Full recomputation may take hours, especially for the cloth and high-resolution
LS cases. Actual cost depends on the selected backend, hardware, contact count,
SPH population and output cadence; there is no fixed completion-time promise.
Brazil nut and the drum use one event boundary at the actual prescribed-motion
onset, not an extra one-step event for output alignment. Their initial states,
motion clocks and total duration are unchanged. Recording remains event-local
at 0.05 s intervals; after motion starts, its regular grid is respectively
one original DEM step (1e-5 s / 2e-5 s) earlier than the old global output grid.
Do not delay motion to make the output frame numbers round.

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

The linear-stiffness and explicit-damping switches are enabled. The external
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
- The current application records native result archives, not VTU or
  `energy.dat`. Custom Plot can analyze recorded solid-system energy without
  enabling those file outputs. Plot data refresh uses **Reload**.
- Contacts and Bonds start hidden in Results; turn on their own checkboxes.
  A scope can be valid even when it has no saved interaction in one frame.
- PNG/MP4 exports use the accepted camera, display, legends, enabled box-size
  overlay and selected Clip guide. Select another node before export if the
  Clip guide should be omitted. FPS changes encoded timing, not physical data.
- The packaged [User Manual](USER_MANUAL.md) explains event inheritance,
  filters, plots, DAT interchange, replay caching and troubleshooting.
- Version 0.4.2 targets Windows x64; H.264 MP4 export uses Windows Media
  Foundation.
