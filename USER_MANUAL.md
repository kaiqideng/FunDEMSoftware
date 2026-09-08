# FunDEM Workbench User Manual

This manual describes the Windows release of FunDEM Workbench. The application is a native Qt desktop program that calls the FunDEMBeta C++ core in-process. The project document owns the editable model. FunDEM containers are created transactionally only when **Run** or **Single Step** is requested, so adding, removing, or renaming editable objects cannot directly corrupt solver indices.

## 1. Release folder and startup

A complete portable release contains at least:

```text
FunDEM.exe
Qt6Core.dll
Qt6Gui.dll
Qt6Widgets.dll
Qt6OpenGL.dll
Qt6OpenGLWidgets.dll
platforms/qwindows.dll
styles/qmodernwindowsstyle.dll    (when supplied by the selected Qt kit)
docs/
examples/
studio/
forceModules/
force-modules/
```

Keep the DLLs and the relative locations of `platforms`, any supplied `styles`, and `force-modules` unchanged. Lowercase `force-modules` contains runtime libraries and the packaged SDK; camel-case `forceModules` contains authoring documentation and the reference source template. Copying only `FunDEM.exe` to another folder normally prevents Qt from loading its Windows platform plugin.

Start the program in one of these ways:

1. Double-click `FunDEM.exe`.
2. Drag a `*.fundem.json` file onto `FunDEM.exe`.
3. Start it from PowerShell:

```powershell
.\FunDEM.exe .\examples\elasticSphereDrop.fundem.json
```

Relative result paths are resolved from the executable folder, not from the project-file folder. Use a separate absolute result directory for every production simulation.

## 2. Main-window organization

The main window has four stable regions:

- **Project**, on the left, contains the model tree and collection add actions.
- The central **viewport** displays particles, LS surfaces, sphere-sphere force chains, Bond cylinders, Packing bounds, and the optional Clip Plane.
- **Properties**, on the right, edits the selected object. Rows, editors, choices, check boxes, and action buttons are produced by reusable UI templates.
- **Output**, at the bottom, contains the Console and live Monitor.

The playback bar is below the viewport. It remains inactive until output frames exist. After calculation, it can select frames, step backward or forward, or play them according to simulation time.

## 3. Recommended modeling order

Create model data in dependency order:

```text
Solver
  -> Material
  -> LS Geometry, when LS particles are used
  -> Particle Type
  -> Particle Packing or SPH Block/Jet
  -> Bond Packing
  -> Output
  -> Post-processing
  -> Run
```

Materials, geometries, and particle types are reusable definitions. They do not place particles in the scene. Positions are created only by a Particle Packing, SPH Block, or SPH Jet. A `1 x 1 x 1` Particle Packing is the standard way to place one rigid particle.

A referenced definition cannot be deleted directly. For example, reassign or remove every dependent Particle Type before removing its Material. Removing a Particle Packing also removes Bond Packings that reference it. Saved Post-processing selections are cleaned through the same model transaction.

## 4. Solver selection

A new project initially exposes only **Analysis > Solver**. Choose the formulation before creating other objects. The tree then shows only the capabilities supported by that formulation.

### 4.1 Sphere + level-set DEM

Use this formulation for spheres, LS particles, and their interactions. It supports:

- Sphere Material and LS Material;
- Sphere Type and LSParticle Type;
- Sphere Packing and LSParticle Packing;
- sphere-sphere, sphere-LSParticle, and LSParticle-LSParticle Bonds;
- optional Particle Force Modules;
- the CPU solver path exposed by the workbench.

### 4.2 SPH + level-set DEM

Use this formulation for SPH fluid and LS solids or boundaries. It intentionally does not expose sphere DEM particles. SPH spacing, smoothing length, reference density, dynamic viscosity, design velocity, and artificial sound speed form one solver-level parameter set shared by every Block and Jet.

### 4.3 Common solver values

- **Time step** is the base DEM step in seconds.
- **Total steps** is the number of additional steps performed by the next Run; it is not an absolute stop index.
- **Gravity Z** is the global gravitational acceleration along Z in m/s².
- **Output interval** is the number of DEM steps between output events.

The default time step is `1.0e-5 s`, and the default Run length is `100000` steps. These are editing defaults, not universal stability guarantees. Re-evaluate the time step for the stiffest material, smallest mass, contact model, and SPH stability limits in the actual model.

## 5. Materials

### 5.1 Sphere Material

Sphere Material is valid for Sphere Types. Its initial values are:

| Property | Default | Unit |
|---|---:|---|
| Density | 2500 | kg/m³ |
| Normal stiffness | 1.0e6 | N/m |
| Sliding stiffness | 0.5e6 | N/m |
| Sliding friction coefficient | 0.1 | dimensionless |
| Restitution coefficient | 0.9 | dimensionless |
| Rolling stiffness | 0 | N/m |
| Torsional stiffness | 0 | N/m |
| Rolling friction coefficient | 0 | dimensionless |
| Torsional friction coefficient | 0 | dimensionless |

### 5.2 LS Material

Every LSParticle Type must reference an LS Material. A Sphere Material cannot be assigned to an LSParticle Type. A sphere-LS contact takes its effective stiffness directly from the sphere material. LS Material retains the sliding, rolling, and torsional friction settings used by LS interactions.

Material parameters define mechanics, not viewport colors. Infinite-mass coloring belongs to the Particle Type and View policies.

## 6. LS Geometry

Supported geometry families are:

- Sphere;
- Superellipsoid;
- Plane Wall;
- Box Wall;
- Cylinder Wall;
- Cone Wall;
- OBJ Triangle Mesh.

Creating a Geometry does not place it in the viewport. It becomes visible only when an LSParticle Packing references an LSParticle Type that uses the Geometry.

### 6.1 Signed-distance parameters

- **Grid spacing** is the LS grid interval in metres. Smaller spacing improves SDF query resolution but increases preprocessing and memory cost.
- **Padding size** is the number of grid layers outside the geometry bounds.
- **Reverse SDF** changes the signed-distance convention and any required source winding normalization. It does not change visibility, depth ordering, or transparency in the viewport.
- **Fixed integral properties** allows a fixed boundary to skip volume, centroid-offset, and unit-density inertia integration.

### 6.2 Solver surface and display surface

**Surface subdivision** controls the sphere or superellipsoid surface nodes used by FunDEMBeta contact detection. It therefore affects physical contact discretization and runtime.

The viewport uses a separate display surface:

- Sphere and Superellipsoid display meshes use at least subdivision level 5.
- The display mesh is never written back to the solver.
- Increasing display quality does not add contact nodes.
- One geometry-local mesh is shared by every LSParticle instance using that Geometry.
- Area-weighted smooth vertex normals are used.
- Smooth closed particles do not show false feature edges derived from ordinary triangle curvature.
- Walls and imported meshes retain their actual structural edges.

For imported OBJ files, the application can only render triangles present in the file. Improve a visibly polygonal silhouette in the mesh authoring tool instead of increasing the LS display subdivision.

### 6.3 SDF Viewer

Select an LS Geometry and open the SDF Viewer:

1. Choose the X, Y, or Z slice axis.
2. Select the slice index.
3. Read the signed-distance magnitude and sign from the color bar.
4. Inspect continuity around the zero level set.

The viewer builds only the selected geometry grid when needed. It does not create particles or solver containers.

## 7. Particle Types

### 7.1 Sphere Type

A Sphere Type references one Sphere Material and stores a physical radius. It does not store a scene position.

### 7.2 LSParticle Type

An LSParticle Type references one LS Material and one LS Geometry. Mass, centroid data, inertia, and bounding information are derived from geometry data and material density during model compilation.

### 7.3 Infinite mass

Infinite mass is a Particle Type property, not a Packing property. When enabled:

- inverse mass is zero;
- ordinary velocity and angular-velocity integration cannot move the particle;
- the viewport uses the shared light-gray fixed-particle color instead of the Packing color;
- a Packing of this type may use Prescribed Motion.

## 8. Particle Packings

A Particle Packing is the only owner of rigid-particle placement and group display settings.

### 8.1 Placement values

- **Particle** references a Sphere Type or LSParticle Type.
- **Lattice** selects SC, BCC, FCC, or HCP.
- **Particle count X/Y/Z** gives the exact number of generated positions along each direction.
- **Spacing X/Y/Z** gives the lattice base interval in metres.
- **Origin X/Y/Z** gives the Packing origin in metres.
- **Activation step** gives the solver step at which the Packing enters the running model.

Every lattice creates exactly `X x Y x Z` positions. BCC, FCC, and HCP alter layer offsets and compactness; they do not multiply the requested count by a conventional-cell atom count.

### 8.2 Initial kinematics

Velocity and angular velocity support:

- **Constant**, where every particle receives one vector;
- **Uniform random**, where each component is sampled independently between configured lower and upper limits.

Orientation supports:

- a constant quaternion;
- a uniform random 3-D rotation.

Random values are determined by the Packing seed and local particle index. The same project, seed, and Packing order reproduce the same initial state.

### 8.3 Ideal equal-sphere spacing

The ideal-spacing action uses the Sphere radius or conservative LS Geometry bounding radius to choose an initially non-overlapping equal-sphere interval. It is a placement aid. It cannot guarantee that arbitrary non-spherical shapes with random orientations do not intersect.

### 8.4 Prescribed Motion

Prescribed Motion is available only when the Packing references an infinite-mass Particle Type. Its start step must be equal to or greater than the Activation step.

**Constant velocity** translates the Packing at the configured velocity.

**Simple harmonic** translation uses:

\[
\mathbf{x}(t)=\mathbf{x}_0+\mathbf{A}\sin(2\pi f t+\phi)
\]

Displacement amplitude is measured in metres, frequency in hertz, and phase in radians. The Packing remains in its initial state before the prescribed-motion start step.

### 8.5 Packing display

- **Eye** controls whether the Packing is shown and remains available during calculation.
- **Opacity** uses a slider and remains editable during calculation.
- **Color mode** selects Packing color or Velocity magnitude.
- **Move in View** is available only for an editable model. Drag the Packing-bounds center handle to translate the group. Left-dragging elsewhere continues to orbit the camera.

Different Packings use different colors. Infinite-mass particles always use light gray. Selecting a Packing displays one group bounding box rather than one selection ring per particle.

Sphere Packing bounds use particle positions plus or minus physical radii. LSParticle Packing bounds are the union of every transformed display-surface point. They do not substitute the LS bounding sphere for the actual geometry bounds.

## 9. SPH Blocks and Jets

SPHDEM uses one global SPH property set, but the project may contain multiple Blocks or Jets.

### 9.1 Block

A Block creates a regular fluid volume from its minimum position, 3-D particle count, and global spacing. Activation step supports staged fluid insertion.

### 9.2 Jet

A Jet uses inlet position, inlet velocity, radius, and length to create a cylindrical particle source. Velocity must be nonzero because it also defines the jet axis.

While a generated jet particle remains inside its active virtual inlet pipe, FunDEMBeta marks it as constrained and restores the prescribed inlet velocity. A constrained particle keeps its current density: density reinitialization, density-rate evaluation, and density integration are skipped for that particle. It still participates normally as a neighbor in the pressure, viscosity, and density calculations of unconstrained particles. The constraint clears automatically when the particle leaves the pipe or the finite jet duration ends.

### 9.3 Display representation

- **Particles** shows individual SPH particles.
- **Fluid** provides a more continuous fluid-oriented appearance while using the same underlying particle state.

Default scientific SPH output includes position, velocity, mass, and density. A simplified viewport representation does not replace VTU output.

## 10. Bond Packings

The editable project does not maintain a top-level collection of individual Bonds. A Bond Packing generates Bonds in bulk between two Particle Packings. The compiler stores the generated values in the corresponding FunDEMBeta interaction container.

### 10.1 Distance rule

A Bond is generated whenever the particle-center distance does not exceed **Maximum center distance**, even when the two bounding spheres do not physically touch.

- Bond point is the midpoint between the two facing bounding-sphere surface points.
- Equivalent length may be custom or derived from particle-center distance.
- Fracture area may be custom or derived as `pi * minimumBoundingRadius²`.

### 10.2 Contacts rule

This rule is valid for a project exported from a playback frame because a normal initial project has no saved Contacts.

- Bond point uses the saved physical Contact point.
- Normal uses the saved Contact normal.
- Equivalent length may be custom or derived from the saved particle positions.
- Fracture area may be custom or use the saved Contact area.

### 10.3 Stiffness and BK damage

A Bond Packing sets normal, shear, bending, and torsional stiffness, Mode-I and Mode-II critical energies, the mode-mixity exponent, and the damage-initiation ratio.

A fracture area of zero disables fracture evaluation while allowing an elastic Bond response.

The viewport renders each Bond as a cylinder:

- center: Bond point;
- axis: Bond normal;
- length: equivalent length;
- effective area: `fractureArea * (1 - damageFactor)`;
- physical radius: `sqrt(effectiveArea / pi)`.

### 10.4 Selecting Bond Packings for display

In the project tree, select **Post-processing > Packings > Bonds**, then use **Select Bond Packings...**:

1. Check each Bond Packing to display.
2. Select **Apply**.
3. The live frame, preview, and playback frames immediately use the same selection.

This selection is independent of the Force Chain selection. It affects only the viewport and never deletes Bonds, changes mechanics, or changes VTU output.

## 11. Force Chain display

The viewport displays only sphere-sphere Contacts, using one aggregated force chain for each sphere pair. Sphere-LSParticle and LSParticle-LSParticle Contacts remain active in the solver and may be written to VTU, but they do not generate viewport geometry.

When a sphere pair has multiple low-level Contacts, the viewport sums their normal-force vectors and draws one center-to-center cylinder. Width uses square-root scaling:

\[
r=r_{min}+(r_{max}-r_{min})\sqrt{\frac{F_n-F_{min}}{F_{max}-F_{min}}}
\]

Color and width use the same normal-force magnitude. The color-bar maximum is the historical maximum observed for the currently selected Sphere Packing scope. Changing that selection resets the corresponding history.

In the project tree, select **Post-processing > Interactions**, then use **Select Force-chain Packings...**. A chain is shown when either sphere endpoint belongs to a selected Sphere Packing. LSParticle Packings are intentionally absent from the dialog.

## 12. Post-processing and camera controls

### 12.1 Navigation structure

There is no View menu or View item in the project tree. Display controls are organized as follows:

- **Post-processing > Packings** in the project tree contains every rigid-particle Packing and SPH Block/Jet display page, plus the **Bonds** page.
- **Post-processing > Interactions** contains Force Chain visibility and Sphere Packing scope.
- **Post-processing > Filters** contains the global coordinate axes and Clip Plane.
- **Post-processing > Legends** contains scalar-range controls, including Velocity Magnitude, and **Packing Bounds > Bounding-box dimensions**.
- The top **Post-processing** menu provides the quick toggles **Packings > Show Bonds** and **Interactions > Show Force Chains**, plus **Maximum Display Storage** and workspace-dock controls.

Camera controls do not appear in the Post-processing tree or menu. The camera-icon button immediately after **Reset** on the Simulation toolbar is the only Camera menu.

### 12.2 Default state

A new project enables only the global coordinate axes. Force Chains, Bonds, Clip Plane filtering, **Show plane**, and **Packing Bounds > Bounding-box dimensions** all start disabled. Each Particle Packing, SPH Block, or Jet owns its own Eye and opacity controls.

### 12.3 Camera

- Left drag orbits without an artificial vertical-angle limit.
- Right or middle drag pans.
- The mouse wheel zooms.
- Open the camera-icon menu immediately after **Reset** to use **Fit Scene** or a preset.
- **Fit Scene** frames every Packing and the complete current particle state.
- The seven presets are Isometric, Front, Back, Left, Right, Top, and Bottom.

Camera changes affect only viewport and projection matrices. They do not alter particle state, regenerate LS display meshes, or upload an unchanged opaque-particle instance buffer.

### 12.4 Scene bounds

Scene bounds are the union of the true bounds of every Particle Packing, SPH Block, and Jet, independent of visibility and Activation step. During calculation and playback, the full unsampled current-particle AABB is included so moving particles remain inside the fitted scene. Force Chains, Bonds, axes, and the Clip Plane do not enlarge the scene. The bounds drive camera fitting, projection, axes, and clip ranges; no separate grid or bounds-box object is drawn.

Selecting a Particle Packing provides one editing selection box for the complete Packing. **Post-processing > Legends > Packing Bounds > Bounding-box dimensions** controls only the X/Y/Z length annotations in metres. Turning the annotation off does not remove the selection box, disable its placement handle, or change scene fitting. The setting is persistent in the project and is off by default.

### 12.5 Transparency and depth

Every physical object uses one perspective and depth convention:

1. Opaque surfaces render first and write depth.
2. Transparent surfaces blend from back to front without writing depth.
3. A closed transparent boundary is split into far and near surface layers so internal particles are rendered between them.
4. Edges, Bond cylinders, and Force Chains obey world-space depth.
5. Only text, numeric overlays, selection indicators, and manipulation handles use the final screen overlay.

This policy prevents a boundary edge from appearing permanently in front of particles and prevents a transparent wall from applying inconsistent colors to members of one Packing.

### 12.6 Clip Plane

- **Enabled** applies the positional filter.
- **Show plane** controls only the orange plane guide.
- **Normal axis** selects X, Y, or Z.
- **Retained side** chooses coordinates less than/equal to or greater than/equal to the position.
- **Section position** is measured in metres.

Particles are retained or hidden as complete objects according to their center positions; triangles are not physically cut. A Force Chain is classified using its force-weighted representative Contact point. A Bond is classified using its Bond point. Drag the orange center handle to move the plane along its normal.

### 12.7 Controls available during calculation

After the solver starts, every control that changes mechanics, references, or solver parameters remains locked until Reset. The following viewport-only controls remain available:

- camera orbit, pan, zoom, Fit Scene, and standard views;
- Packing Eye, opacity, and color mode;
- SPH representation;
- axes, Force Chains, and Bonds visibility;
- Sphere Packing scope for Force Chains;
- Bond Packing scope for Bonds;
- Clip Plane settings and manipulation;
- Packing bounding-box dimension annotations;
- playback-frame selection.

These controls modify camera state or separate presentation descriptions and masks. They do not regenerate, reorder, or resize solver particle arrays, Contact/Bond arrays, or recorded playback-history arrays. **Maximum Display Storage** changes only the number of particle instances sampled for presentation.

## 13. Particle Force Modules

Both formulations can load external rigid-particle force laws from dynamic libraries. A module declares whether it supports spheres, LS particles, or both. SphereDEM dispatches its compatible sphere and LS arrays; SPHDEM dispatches only LS boundary particles because this ABI does not expose SPH fluid state. Module metadata declares identity, supported families, parameter types, units, bounds, and defaults. Properties uses the shared templates to generate the required rows.

The reference hydrodynamics module declares:

- Enable;
- Water velocity;
- Fluid density;
- Free-surface height;
- Drag coefficient.

The bundled hydrodynamics module supports spheres only, so it belongs in SphereDEM projects. Modules run after Contact, Bond, and other internal forces have been assembled. The CPU host packs one state array, invokes each compatible module, validates the accumulated result, and adds it to particles. The reference callback itself applies its formula in parallel. Load only trusted native libraries built for the host operating system, architecture, and ABI. See `forceModules/README.md` for the callback and error-handling contract.

## 14. Output

### 14.1 Output events

Output interval is expressed in DEM steps. One output event:

1. writes scientific VTU;
2. updates `energy.dat`;
3. stores one compact View playback frame;
4. stores one full restart checkpoint used by frame export.

All three products use the same frame number, solver step, and simulation time.

### 14.2 Mandatory and optional fields

The application always writes fields required to reconstruct basic geometry and lets the user select other FunDEMBeta fields in **Output**. VTU uses binary encoding by default.

- Sphere: position, radius, and velocity are mandatory.
- LSParticle: surface-node position, node velocity, and required mass data are mandatory; finite- and infinite-mass particles are written separately.
- SPH: position, velocity, mass, and density are mandatory.
- Contact: point and normal are mandatory; force, spring, and energy fields are optional.
- Bond: point and normal are mandatory; stiffness, damage, endpoint, torque, and energy fields are optional.

The render frame contains only data required for interactive display, but its paired restart checkpoint preserves every particle state plus retained Contacts and Bonds. Both stay in process memory until Reset or project replacement. For very large models, choose the Output interval from the available host memory as well as the desired animation smoothness; VTU or DAT remains the appropriate quantitative record.

### 14.3 `energy.dat`

`energy.dat` records the solid DEM system and does not mix SPH fluid energy into the same total. Inspect:

- kinetic energy;
- gravitational potential energy;
- contact elastic energy;
- Bond elastic energy;
- total mechanical energy.

Even a nominally nondissipative model can show integration error from finite time steps, stiff contacts, or initial overlap. Reduce the time step and inspect initial geometry before changing display settings.

## 15. Run, Pause, Single Step, and Reset

### 15.1 First Run

The first Run:

1. validates every stable ID and reference;
2. expands Packings;
3. creates material, geometry, particle, and interaction containers;
4. generates Bond Packings;
5. initializes the CPU solver;
6. clears VTU and DAT files from the configured result directory;
7. advances by Total steps.

A later Run continues from the current state for another requested round. Time, global step, output numbering, and playback history remain continuous, and the output directory is not cleared again.

### 15.2 Pause

Pause stops the worker at a safe step boundary. It does not unlock the physical model. Continue with Run or Single Step.

### 15.3 Single Step

The first Single Step performs the same project compilation and then advances one DEM step. Use it to inspect initial Contacts, Force Chains, and Bonds.

### 15.4 Reset

Once the solver has started, physical parameters remain locked even after the current Run has finished. Select Reset to return to editable state. Reset removes the current runtime state and playback history but does not delete project definitions.

## 16. Playback

Every playback frame corresponds to one output event. The playback tools can:

- scrub the timeline;
- select the previous or next frame;
- play at 1:1 simulation-time speed;
- pause and inspect from any camera;
- change Post-processing settings while playing.

Playback never reruns the solver. A large Output interval causes visible jumps between frames. Reduce the interval when smoother animation is required.

## 17. Saving an animation

**File > Save Animation...** exports recorded output frames as Animated PNG, Motion-JPEG AVI, or H.264 MP4. The command is available only when the compiled session is synchronized, the solver is not running, and at least two recorded frames exist. Pause an active calculation or wait for the current Run to finish before exporting. Animation export reads recorded presentation snapshots; it does not rerun the solver or modify particle, Contact, Bond, or checkpoint data.

### 17.1 Procedure

1. Set the viewport camera and every required Post-processing option before starting the export. Packing visibility, colors, opacity and velocity coloring, Force Chains, Bonds, their Packing scopes, the coordinate axes, legends, and the Clip Plane are captured as currently configured. Transient editing aids such as selection rings, Packing bounds, dimension labels, alignment guides, and manipulation handles are omitted.
2. Choose **File > Save Animation...**.
3. Select **Format**, then set **First frame**, **Last frame**, **Frame stride**, and **Playback rate**. **Loop playback** is available only for Animated PNG.
4. Check the live **Output summary**.
5. Select **Save Animation**, then choose the destination. The file dialog uses the extension of the selected encoder: `*.png`/`*.apng`, `*.avi`, or `*.mp4`.
6. Follow progress in the modal task window. Select **Cancel** if the export is no longer required.

The source canvas is the current viewport size in physical display pixels, including operating-system display scaling. Resize the viewport before opening the dialog when a different output resolution is required. Every frame uses the same canvas; export never scales or crops a frame. APNG and AVI preserve the source dimensions exactly. H.264 requires even dimensions, so an odd width or height is increased by one pixel by extending the outermost source edge; scene content is not rescaled.

### 17.2 Frame selection and timing

- **First frame** and **Last frame** are inclusive recorded-frame indices.
- **Frame stride** exports the first frame, every requested interval after it, and always the selected last frame even when the stride does not land on it exactly.
- **Playback rate** scales recorded simulation time. `1.0` preserves real recorded timing, `2.0` plays twice as fast, and `0.5` plays at half speed.
- **Loop playback** has a precise, format-specific meaning. It starts enabled for APNG; enabling it writes the APNG play count as zero, which instructs compatible viewers to restart at the first frame indefinitely, while disabling it writes one play. It does not duplicate source frames or alter any duration in the summary. AVI and MP4 do not carry this application-level loop instruction, so the checkbox is disabled for those formats and repeat playback is controlled by the video player.

One common presentation timeline is calculated from the original simulation times before an encoder is selected. Increasing the stride therefore removes intermediate images without accelerating the represented motion. The final frame uses a positive neighboring recorded interval so it remains visible at the end of a play or before an APNG loop restarts. Each format represents that timeline as follows:

- **Animated PNG (`*.png`, `*.apng`)** stores the calculated duration with each lossless, full-color frame. Both extensions contain identical APNG data. The embedded play count is the only format-level Loop behavior provided by the application.
- **Motion-JPEG AVI (`*.avi`)** JPEG-compresses every image independently at quality 90. AVI uses a fixed time base, so the writer repeats frames as needed to approximate the calculated per-frame holds; this can introduce small timing quantization and can increase the encoded frame count. The Qt JPEG image-format plugin must be available. AVI 1.0 output is limited to 4 GiB, and exceptionally long or highly irregular timelines can exceed its internal expanded-frame limit.
- **H.264 MP4 (`*.mp4`)** stores lossy H.264 video with the calculated presentation timestamps and durations. It usually produces the smallest delivery file. This encoder uses Windows Media Foundation and is therefore offered only by the Windows build; it does not require FFmpeg.

The summary reports:

- **Exported frames**: the number of selected source images after range and stride selection; an AVI may contain additional repeated encoded frames to represent holds;
- **Simulation span**: selected last-frame time minus selected first-frame time;
- **Playback span**: simulation span divided by Playback rate;
- **Animation duration**: encoded duration including the final-frame hold;
- **Output size**: encoded canvas width and height in physical pixels, including the possible one-pixel H.264 even-dimension extension.

### 17.3 Output safety and state restoration

APNG is the lossless archival choice, Motion-JPEG AVI favors simple independently decodable frames, and H.264 MP4 is the compact delivery choice. High-resolution APNG and AVI output can be substantially larger than MP4. Use a narrower frame range, a larger stride, or a smaller viewport when file size matters. Every format contains presentation pixels only; use VTU, `energy.dat`, or an exported frame project for quantitative data and restart state.

The destination is written atomically. Canceling the progress window or encountering an encoding error discards the temporary output; no partial animation replaces the requested file. While export is active, playback and conflicting project/file commands are temporarily disabled. When export finishes, fails, or is canceled, the application restores the previously selected live or playback frame, resumes the snapshot polling state, and resumes recorded playback when it had been active before export.

## 18. Exporting a frame and continuing calculation

Use **File > Export Playback Frame as Project...** and choose which Particle Packings and SPH sources to retain.

### 18.1 Always-exported definitions

The exporter always retains:

- Materials;
- LS Geometries;
- Sphere Types;
- LSParticle Types.

### 18.2 Particle and interaction filtering

- A selected active Packing is expanded into exact single-particle states from the selected frame.
- A selected future Packing is retained, with Activation step reduced by the completed source step and clamped to zero.
- A Contact or Bond is retained only when both endpoint particles are exported.
- Removed particles cause the retained container indices to be rebuilt contiguously.
- Contact and Bond master/slave indices use the same old-to-new mapping.

### 18.3 Immutable prefix

After loading an exported-frame project, existing Materials, Geometries, Particle Types, Packings, and saved states form an immutable prefix. They cannot be edited, removed, or moved. New elements may still be appended to every collection.

The exported project restarts at local step zero. Existing Activation step and Prescribed Motion start step values are reduced by the completed source step and clamped to zero.

## 19. Undo and Redo

Use:

- **Edit > Undo** or `Ctrl+Z`;
- **Edit > Redo** or `Ctrl+Shift+Z`.

Dragging a Packing creates one history operation when the drag ends. Camera motion is not project history. Runtime state changes are also not model-edit history.

## 20. Performance and display quality

### 20.1 Maximum Display Storage

Use **Post-processing > Maximum Display Storage** to select a 128 MiB to 4 GiB presentation budget. It limits display instances, not particles in the solver. When demand exceeds the budget, sampling is allocated fairly by visible Packing: each non-empty visible Packing receives representation when capacity permits, then remaining capacity is distributed in proportion to its remaining particle count. Calculation, Contacts, Bonds, VTU output, and playback-history particle arrays still retain every particle.

### 20.2 Sphere and SPH display

Sphere and SPH particles use depth-correct two-triangle GPU impostors. They do not construct a traditional sphere mesh per particle and are therefore suitable for high instance counts.

### 20.3 LSParticle display

LSParticle uses real instanced triangles. GPU vertex work scales with instance count multiplied by triangle count per Geometry. The independent level-5-or-higher display mesh improves close-up silhouettes without changing solver nodes, but many high-resolution LSParticle instances can still be expensive.

Recommended practices:

- choose solver Surface subdivision from physical accuracy, not viewport appearance;
- reuse one Geometry for identical shapes;
- remove invisible internal faces and duplicates from imported OBJ files;
- hide Packings that are not being inspected;
- increase display storage only within available GPU memory.

### 20.4 Antialiasing

The program requests 4x MSAA for pixel-edge smoothing. Regular LS silhouettes are additionally improved by the independent display mesh. MSAA cannot repair a genuinely low-resolution OBJ silhouette.

## 21. Troubleshooting

### 21.1 Qt platform-plugin error at startup

Verify that `platforms/qwindows.dll` is in the `platforms` folder beside `FunDEM.exe`. Do not move the executable alone.

### 21.2 Unknown ID during Run

A Particle Type, Packing, Bond Packing, or Module references an invalid stable ID. Return to the corresponding reference selector and choose an existing object.

### 21.3 LSParticle is not visible

Check, in order:

1. the LSParticle Type references a valid LS Material and Geometry;
2. an LSParticle Packing references that type;
3. Packing Eye and opacity;
4. Activation step;
5. whether the Clip Plane hides its center;
6. whether display-storage sampling is active.

### 21.4 Force Chains are not visible

The viewport displays only sphere-sphere Force Chains. Confirm:

1. a sphere-sphere Contact exists;
2. **Post-processing > Interactions > Visible** is enabled, or the top-menu quick toggle **Post-processing > Interactions > Show Force Chains** is checked;
3. at least one endpoint Sphere Packing is selected;
4. aggregated normal force is positive;
5. the Clip Plane retains the representative Contact point.

Inspect sphere-LS and LS-LS Contacts through VTU.

### 21.5 Bonds are not visible

Confirm:

1. **Post-processing > Packings > Bonds > Visible** is enabled, or the top-menu quick toggle **Post-processing > Packings > Show Bonds** is checked;
2. the source Bond Packing is selected;
3. the Bond point lies on the retained side of the Clip Plane;
4. equivalent length is positive;
5. normal is valid;
6. the Distance or Contacts rule generated at least one Bond.

As damage reaches one, effective area approaches zero and the displayed cylinder contracts accordingly.

### 21.6 Colors differ inside a transparent box

Run the `FunDEM.exe` distributed with this manual. Closed transparent boundaries use far/near surface layers and real surface-depth sorting. Reverse SDF does not participate in main-View transparency sorting.

### 21.7 Result directory was cleared

The configured result directory is cleared only at the first Solve of one project session. Continuing with Run does not clear it again. Use a dedicated folder and back up important results.

## 22. Recommended first validation

1. Open `examples/elasticSphereDrop.fundem.json`.
2. Inspect Solver, Materials, Particle Types, and both Packings.
3. Use Single Step to verify the initial View and result path.
4. Run the complete configured round.
5. Inspect falling and rebound in playback.
6. Plot total mechanical energy from `energy.dat`.
7. Open VTU files in ParaView and verify positions, velocities, and selected fields.
8. Export one playback frame as a project, reload it, and Run to verify restart behavior.

## 23. Diagnostic commands

Run the packaged interface and solver check:

```powershell
.\FunDEM.exe --smoke-check smoke.png
Get-Content .\smoke.png.txt
```

A report beginning with `PASS` confirms project round-trip, one real solver step, shared layout templates, Packings, Bonds, SDF, and core View integration.

Run the display-capacity check:

```powershell
.\FunDEM.exe --capacity-check capacity.png
Get-Content .\capacity.png.txt
```

This check creates and displays a large set of GPU sphere impostors without running DEM. Use it to distinguish display-capacity issues from solver performance.

When working from source, run the non-GUI engine/module integration check independently of the packaged diagnostics:

```powershell
cmake --build build-windows --target fundem_software_release --parallel
ctest --test-dir build-windows --output-on-failure --no-tests=error
```

`scripts/build-windows.ps1` performs this CTest gate automatically before invoking the CMake install manifest and adding Qt/compiler runtime files.

## 24. Documentation map

- `README.md`: overview, build, and quick start.
- `docs/USER_MANUAL.md`: this complete manual.
- `docs/ARCHITECTURE.md`: ownership, compilation, threading, snapshots, and rendering.
- `docs/CODE_REFERENCE.md`: concrete file, class, and function index.
- `studio/UI_DESIGN_SYSTEM.md`: property-row, editor, button, dialog, spacing, and state templates.
- `forceModules/README.md`: force-module ABI and development procedure.
- `force-modules/sdk/`: packaged ABI and callback-support headers used to compile external modules.
- `examples/README.md`: packaged example descriptions.
