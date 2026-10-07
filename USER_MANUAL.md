# FunDEM Workbench User Manual

This manual describes the shared FunDEM Workbench interface and simulation workflow. The application is a native Qt desktop program that calls the FunDEMBeta C++ core in-process. The project document owns the editable model. FunDEM containers are created transactionally only when **Run** or **Step** is requested, so adding, removing, or renaming editable objects cannot directly corrupt solver indices. Windows startup is described below; Apple Silicon Mac users should also read the [macOS guide](MACOS_GUIDE.md) for installation and platform-specific paths.

For model checks, protected run directories, Save Run/Open Results, quantitative CSV exports, and the headless command, see [Workflow improvements](WORKFLOW_IMPROVEMENTS.md). That guide distinguishes completed features from the remaining roadmap.

## 1. Release folder and startup

A public Windows x64 ZIP contains a `Windows/` runtime folder with:

```text
Windows/
  FunDEM.exe
  FunDEM-cli.exe
  Qt6Core.dll, Qt6Gui.dll, Qt6Widgets.dll, Qt6OpenGL.dll, Qt6OpenGLWidgets.dll
  compiler and graphics runtime DLLs
  platforms/qwindows.dll
  imageformats/qjpeg.dll
  styles/qmodernwindowsstyle.dll    (when supplied by the selected Qt kit)
  docs/USER_MANUAL.md
  docs/WORKFLOW_IMPROVEMENTS.md
  examples/                       (seven projects, their guide, and required meshes)
  force-modules/
    README.md
    fundemSphereHydrodynamics.dll
    fundemParticleDamping.dll
    SphereHydrodynamics.md
    ParticleDamping.md
  licenses/
  README.md
  THIRD_PARTY-NOTICES.md
  FUNDEM_BETA_CORE.txt
```

Keep the DLLs and the relative locations of `platforms`, `imageformats`, any supplied `styles`, `examples`, and `force-modules` unchanged. The two bundled particle-force modules are ready to load; their guides describe the physical models. The public ZIP and Store runtime do not include application or calculation-core source, module SDK headers, implementation source, build templates, or development documents. A full install made from a development checkout may additionally contain SDK headers, module implementation examples, and development documents; application and core source remain in the checkout. Copying only `FunDEM.exe` to another folder normally prevents Qt from loading its Windows platform plugin.

Start the program in one of these ways:

1. Double-click `FunDEM.exe`.
2. Drag a `*.fundem.json` file onto `FunDEM.exe`.
3. Start it from PowerShell:

```powershell
.\FunDEM.exe .\examples\gombocSelfRighting.fundem.json
```

In a portable Windows installation, relative result paths are resolved from the executable folder, not from the project-file folder. In a Microsoft Store/MSIX installation and on macOS, relative result paths are placed under `Documents/FunDEM` rather than inside the read-only installation or signed `.app` bundle. Explicit absolute output paths are preserved on all platforms. Use a separate absolute result directory for every production simulation.

For a Microsoft Store release, install from the published Store listing and launch **FunDEM** from Start; do not move files out of its installation directory. Installed examples and bundled libraries are read-only. Saving an installed example opens **Save As** in a user-owned location, while bundled mesh/module references remain resolvable after an application update. Store publishing support does not mean a certified Store release is already available. An unsigned GitHub ZIP or unsigned submission MSIX is a separate distribution and may still trigger SmartScreen. Do not disable Windows protection to install it.

### 1.1 Open a bundled example

Choose **File > Examples** and select one of the seven installed projects. This opens its initial model without starting a calculation; inspect it first, then use **Run** or **Step** explicitly. The menu finds the examples shipped with the application, including in a Microsoft Store installation or macOS app bundle.

Use **File > Save As...** to save edits in a writable, user-owned folder, such as Documents. Do not overwrite installed resources. Keep the bundled meshes and force-module libraries available; missing resources are reported when the project is opened or checked. If the selected example file itself is missing, **Example Not Available** warns you and leaves the current project unchanged.

## 2. Main-window organization

The main window has four stable regions:

- **Project**, on the left, has a five-icon category rail and one model tree with collection add actions. The icons select **Analysis**, **Materials**, **LS Geometry**, **Packings**, or **Post-processing**; only the selected category's children appear. Analysis contains separate **Formulation**, **Loop Parameters**, and **Output** entries. There is no separate **Particles** category, **Simulation Model** header, or visible project-name root row. The project name remains part of the saved document.
- **Properties**, below Project in the default left column, edits the selected object. Rows, editors, choices, check boxes, and action buttons are produced by reusable UI templates. The mouse wheel scrolls the panel but does not change numeric inputs or closed drop-down selections; click or type to edit them.
- The **viewport**, to the right of the Project and Properties column, displays particles, LS surfaces, rigid-particle force chains, Bond cylinders, Packing bounds, and the optional Clip Plane.
- **Output**, at the bottom, contains the Console and live Monitor.

The playback bar is below the viewport. It remains inactive until output frames exist. After calculation, it can select frames, step backward or forward, or play them according to simulation time.

Within each **Project** category, objects with the same meaningful name prefix and a final numeric index are automatically collected into a collapsible group when at least two match. For example, `geometry0`, `geometry1`, and `geometry2` appear under `geometry`. This is a tree-display grouping only: object names, stable IDs, container order, solver indices, and references do not change. Expand the group to select or edit an individual object; Packing Eye and Move controls remain on the individual Packing rows. Renaming an object updates its grouping. Different model categories are never merged, and a group row is not a new material, particle, Packing, or solver container.

Simulation statistics are consolidated in **Live Monitor**. The viewport does not overlay a **Displayed samples** counter, and the bottom-right status area does not duplicate Step, simulation time, or CPU usage. State labels and operation/loading notices remain in the status bar; scientific legends and explicitly enabled per-Packing bounds and dimensions remain available in the viewport.

The **Quantity** column has a fixed, content-measured width that fits the header and every quantity name. Resizing the panel changes the **Value** column, not the quantity labels. Value has a minimum width of 120 logical pixels, increased when the font needs more space for scientific notation. The statistics pane reserves both columns and scrollbar space, so dragging its divider cannot squeeze the values below that minimum. Font/style changes recalculate these widths.

Live Monitor contains two side-by-side panes: the statistics table and an energy chart. The chart has no extra title above its controls and axes. Drag their divider to allocate more room to either pane. Enable **Monitor solid energy** in the project's **Output** properties to collect energy history; it is off by default. When enabled, the chart records the entire solid system, independently of Packing visibility or viewport sampling. SPH-fluid energy is excluded.

The Output panel can shrink to 240 logical pixels. In narrow windows, Live Monitor scrolls inside its own viewport rather than extending beneath Properties. Quantity/Value columns and the plot retain their readable minimum widths; widening Output removes the extra scrollbar when the two panes fit again. The Console retains its own normal scrolling behavior.

Use **Curves and axes...** to select any combination of kinetic, gravitational potential, Contact elastic and Bond elastic energies. Kinetic energy includes translation and rotation of finite-mass solids. Gravitational potential uses the solver's world-origin reference and can be negative. Contact elastic energy sums the normal, sliding, rolling and torsional springs; Bond elastic energy sums normal, shear, bending and torsional contributions. The Bond elastic curve defaults to off when the current system contains no Bonds. Curve choices remain user-controlled: playback across Bond activation does not overwrite an explicit selection.

The horizontal axis is **Time (s)**. The vertical axis title is always **Energy (J)**, without a logarithmic suffix. Logarithmic tick labels use `1e` followed by an integer exponent (for example, `1e-12`); linear tick labels prefer integers or concise scientific notation. The energy axis defaults to **Logarithmic (base 10)**, with an automatic lower limit of `1e-12 J`; its automatic upper limit follows positive values on the selected curves within the time window. Non-positive values cannot appear on the logarithmic axis, but their original signed data are retained. Select **Energy axis > Linear** to inspect signed gravitational potential energy. Each axis supports automatic limits or custom minimum/maximum values; maximum must exceed minimum, and logarithmic energy limits must both be positive. Positive values below the current lower limit are clipped from the plot, not changed or deleted. A gray dashed line marks the displayed frame's time. Settings remain editable during calculation. These presentation settings do not modify the solver or scientific output and are not stored in project JSON.

When live energy monitoring is enabled, sampling follows newly published solver states in the live View, not just VTU output frames. It includes the initialized state, intermediate live updates, output frames, and the final published state. Capturing the same step and simulation time again does not add a duplicate sample. A repaint, camera movement, or playback of an existing frame does not run another solver-energy scan. The curve between samples is a visualization interpolation, not a new simulation sample. Further calculation rounds append to the same history. Reset or loading a newly compiled project starts a new history; merely changing visibility or playing earlier frames does not delete past samples. Existing output files are not imported automatically as plot history.

The optional project JSON field `solver.monitorSolidEnergy` defaults to `false` for new projects and older project files that omit the field. An explicitly saved `true` value remains enabled; set it to `true` before calculation to collect live energy samples. While it is `false`, the additional live-monitor energy scans and plot history collection are skipped. This does not disable or change `energy.dat`; scientific energy output still follows its normal output schedule. Each collected monitor sample scans the solid particles, Contacts, and Bonds, with cost `O(particles + contacts + bonds)`. Its actual runtime overhead has not been benchmarked, so do not assume that frequent monitoring is free for large models.

The **Live Monitor > FunDEM CPU (%)** row measures the current FunDEM process, including its solver, rendering, and background threads. It is not the computer's total CPU load. Usage is averaged over approximately 0.75 seconds and normalized to all online logical processors: one fully busy core on a 16-logical-processor machine is about 6.25%, and all 16 fully busy cores are 100%. The first valid sample appears after a short sampling window; an unavailable sample is shown as a dash. Sampling uses its own non-blocking UI timer, so it continues before compilation, while paused, during playback and animation export. If the UI event loop is busy, the next sample covers the longer elapsed interval rather than blocking the application. Reset clears simulation quantities without clearing this live process indicator.

The **Bonds** row immediately follows **Contacts** and reports the stored bond count in the current frame. Hiding Bond Packings does not change this count.

### 2.1 Background operations and progress

All operations report progress in **Output > Console** without opening a progress window. This includes parameter changes that rebuild the preview, creating/opening/saving a project, importing a force module, exporting a playback-frame project, building an SDF, and preparing or controlling the solver with Run/Pause/Step/Reset. Animation export also reports its frame progress in the Console. The current scene remains visible while work proceeds, and the normal file chooser, animation settings, unsaved-change confirmation, error messages, and completed geometry preview still appear when needed.

The Console reports descriptive operation stages, such as **Preparing geometries** and **Scene preview completed**, followed by completion, cancellation, or failure with elapsed time. Background operations do not append generic item counters to these messages. Animation export retains completed/total frame progress.

The workspace is temporarily locked during background preparation, import/export, and file operations, including model editors, menus, toolbars, camera and Post-processing controls, and playback navigation. The Output dock is shown with Console selected, and progress continues to display while the operation runs. The normal controls are restored after completion, failure, or safe cancellation. Once solver preparation finishes and normal calculation begins, camera and display-only controls become available again under the usual calculation-state rules.

Cancellation is cooperative. Closing the application requests cancellation when supported or waits for safe completion. A long geometry routine may need to finish its current stage before acknowledging cancellation, and solver-state changes and file writes must finish safely. Read-only results are checked again for cancellation before being applied.

Failed or canceled project loading does not replace the current document or scene. Saving writes a snapshot taken when the operation starts and marks it clean only if the document revision is still current. Before discarding or closing a project, the normal Save/Discard/Cancel confirmation still applies.

## 3. Recommended modeling order

Create model data in dependency order:

```text
Formulation and Loop Parameters
  -> Material
  -> LS Geometry, when LS particles are used
  -> Particle Packing configuration (including its particle definition), or SPH Block/Jet configuration
  -> Bond Packing
  -> Output
  -> Post-processing
  -> Run
```

Materials and geometries are reusable definitions. Sphere and LS particle-type definitions remain in the project for solver references, but there is no separate **Particles** tree category. Configure radius/material or geometry/material inside the Packing workflow. Positions are created only by a Particle Packing, SPH Block, or SPH Jet. A `1 x 1 x 1` Particle Packing is the standard way to place one rigid particle.

New objects receive the first unused zero-based name in their category: **Sphere Material0**, **Level-Set Material0**, **Level-Set Geometry0**, **Sphere Packing0**, **LS Particle Packing0**, **SPH Block0**, **SPH Jet0**, and **Bond Packing0**. Lattice and random Packings share their category's numbering. You can edit a name without changing its stable internal ID; existing project names are preserved when loaded.

A referenced definition cannot be deleted directly. For example, change or remove the dependent Packings before removing their Material or LS Geometry. Removing a Particle Packing also removes Bond Packings that reference it. Saved Post-processing selections are cleaned through the same model transaction.

To delete a whole collection or a shared-name group, select its expandable row in **Project**, then use **Delete Group Objects...** from the context/Edit menu or press **Delete**. Collapsed children are included. The confirmation reports the selected objects and any additional dependent Bond Packings. A batch is all-or-nothing: a read-only object or a reference from an object outside the batch blocks the entire deletion. One **Undo** restores the complete batch, its dependent Bonds, and display selections. Post-processing rows are display controls, not deletion targets; physical deletion remains unavailable after running until **Reset**.

## 4. Solver selection

A new project initially enables only the **Analysis** icon and its **Formulation** entry. Choose the formulation before creating other objects. The remaining category icons then become available, and their trees show only the capabilities supported by that formulation. **Analysis > Loop Parameters** is a separate entry for time stepping, gravity, and applicable SPH properties; the same category also contains Output and Particle Force Modules. Formulation remains locked after the first model object is added.

### 4.1 Sphere + level-set DEM

Use this formulation for spheres, LS particles, and their interactions. It supports:

- Sphere Material and LS Material;
- Sphere and LS particle definitions configured within their Packings;
- Sphere Packing and LSParticle Packing;
- sphere-sphere, sphere-LSParticle, and LSParticle-LSParticle Bonds;
- optional Particle Force Modules;
- the CPU solver path exposed by the workbench.

### 4.2 SPH + level-set DEM

Use this formulation for SPH fluid and LS solids or boundaries. It intentionally does not expose sphere DEM particles. SPH spacing, smoothing length, reference density, dynamic viscosity, design velocity, and artificial sound speed form one solver-level parameter set shared by every Block and Jet.

### 4.3 Common solver values

- **Time step** is the base DEM step in seconds.
- **Steps this run** is the additional step count for a new calculation round; a paused unfinished round resumes its existing target. It is not an absolute stop index.
- **Gravity Z** is the global gravitational acceleration along Z in m/s².
- **Output interval** is the number of DEM steps between output events.

The default time step is `1.0e-5 s`, and the default Run length is `100000` steps. These are editing defaults, not universal stability guarantees. Re-evaluate the time step for the stiffest material, smallest mass, contact model, and SPH stability limits in the actual model.

## 5. Materials

The Materials tree has separate **Sphere Materials** and **Level-Set Materials** groups. Add a material in its group; **Sphere Materials** is available only when the selected solver supports DEM spheres. The group determines the material kind, so Properties does not show a redundant **Type** field. Both kinds organize their Properties into **Material**, **Mass Properties**, **Contact Stiffness**, **Frictional Response**, and **Collision Damping** sections.

### 5.1 Sphere Material

Sphere Material is valid for sphere definitions configured within Sphere Packings. Its initial values are:

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

Every LSParticle Type must reference an LS Material. A Sphere Material cannot be assigned to an LSParticle Type. LS Material exposes **normal contact stiffness per unit area** and **shear contact stiffness per unit area** (both N/m³), sliding friction, restitution, and density; it has no rolling/torsional friction controls. For LS-LS contacts, each per-area stiffness is multiplied by the surface-node contact area before computing the force. Sphere-LS contacts take all four stiffnesses and both rolling/torsional friction coefficients directly from the sphere material. Sliding friction and restitution still combine both materials using the harmonic mean. LS-LS contacts retain their surface-node force model without separate rolling/torsional resistance springs.

Older project files may contain rolling/torsional friction values for LS Materials. The loader ignores these unused values, and saving the project omits those two LS-only legacy fields. Sphere Material values are preserved unchanged.

Material parameters define mechanics, not viewport colors. Infinite-mass coloring belongs to the Particle Type and View policies.

## 6. LS Geometry

Supported geometry families are:

- Sphere (supported in existing projects and Reconfigure, but not offered by the new-geometry menu);
- Superellipsoid;
- Irregular superellipsoid, generated as an embedded triangle mesh from one superellipsoid base with bounded surface offsets;
- Plane Wall;
- Box Wall;
- Cylinder Wall;
- Cone Wall;
- OBJ Triangle Mesh.

The **LS Geometries > +** menu offers **Superellipsoids and variants** (**Superellipsoid...** and **Random geometry...**), a direct **Triangle mesh (OBJ)...** action, and **Built-in walls** (Plane, Box, Cylinder, Cone). Choosing a shape or clicking **Triangle mesh (OBJ)...** opens Configure directly; there is no Import OBJ submenu. Configure displays the chosen **Geometry type** as read-only text instead of a Primitive dropdown. Existing Sphere geometries can still be loaded and reconfigured, but this menu does not create new ones.

Enter the source-shape and level-set parameters, then confirm. Configure validates the draft but does not build a surface or calculate volume/inertia before confirmation. Preparation starts after confirmation and reports real progress in the Console; a canceled or failed operation leaves the previous project unchanged. Properties then shows the cached results of the prepared geometry: surface-node and triangle counts, bounding radius, volume (m^3), and the full 3-by-3 centroidal unit-density inertia tensor (m^5). While preparation is pending, calculated values say so; fixed geometry shows **Not applicable (fixed geometry)** for volume and inertia. Values with magnitude below the default numerical tolerance are displayed as `0` without changing the calculated data. Select an existing geometry and use **Reconfigure Geometry...** to edit its structural settings; the confirmed rebuild keeps its stable ID and Packing references. Creating a Geometry does not place it in the viewport. It becomes visible only when an LSParticle Packing uses it.

**Random geometry...** opens **Configure Random Geometry** and creates one irregular superellipsoid per **Create Geometry** confirmation. Its configuration sets one superellipsoid base, minimum/maximum signed surface offsets, random seed, surface subdivision, grid spacing, padding, and base name. The generated embedded mesh is the saved physical source. New geometries also retain optional procedural-source settings so **Reconfigure Geometry...** can regenerate that mesh from edited parameters without treating it as an OBJ file. Older embedded meshes with no procedural source remain ordinary triangle meshes; opening them does not invent the original random settings.

Enabling **Skip mass integration** forces LS particle types using that geometry to **Infinite mass**. Disabling it later does not automatically clear an already saved Infinite mass setting.

### 6.1 Signed-distance parameters

- **Grid spacing** is the LS grid interval in metres. Smaller spacing improves SDF query resolution but increases preprocessing and memory cost.
- **Padding size** is the number of grid layers outside the geometry bounds.
- **Reverse SDF** changes the signed-distance convention and any required source winding normalization. It does not change visibility, depth ordering, or transparency in the viewport.
- **Fixed integral properties** is passed to `LSInfo::buildLSGrid` and defaults to enabled for all wall geometries. Fixed geometry keeps its input frame and skips volume, centroid-offset, and unit-density inertia integration; its bounding radius is still calculated.

For movable LS geometry, grid construction automatically calculates the volume and centroid, shifts both the surface nodes and grid origin into the centroidal frame, and caches the unit-density inertia tensor and bounding radius. Preview, random-packing placement, SDF display, and solver input use these prepared coordinates. The solver copies this data and does not integrate or recenter the geometry again.

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

### 6.3 Combined geometry preview

Select any LS Geometry and click **Preview Geometry...**. This opens one independent, non-modal 3-D window for both actual surface points and signed-distance slices, using the same viewport and camera controls as the main View:

- drag with the left mouse button to rotate;
- scroll the mouse wheel to zoom;
- drag with the right or middle mouse button to pan;
- use **Fit Scene** in the upper-right camera menu to frame the complete geometry again.

The compact top toolbar contains **Surface points** and the independent **XY**, **XZ**, and **YZ** buttons under **SDF planes**. A pale-blue button means enabled, and all three planes may be displayed together. Surface points and XY are initially enabled. Plane names describe the plane's span (XY has its normal along Z); hover over a button for the full explanation. Use Tab to focus an option and Space to toggle it. Narrow windows wrap whole groups without splitting their buttons or clipping labels. A separate camera-icon button at the upper right contains **Fit Scene**, seven standard views (Isometric, Front, Back, Left, Right, Top, Bottom), and optional **Coordinate axes**, initially off. Toggling layers or axes preserves the camera; only an explicit camera command changes its framing or orientation. The compact bottom row shows the surface-point count and **Close**.

Each slice is fixed at the exact middle of its grid extent. For an odd node count, the middle layer is used directly. For an even node count, the two middle layers are interpolated with equal weights and displayed halfway between them. These are geometry-local grid centers, not necessarily global coordinate zero or the particle centroid. All visible slices use one **SDF (m)** color bar in the upper-right corner of the 3-D viewport: negative is blue, positive is red, and zero is white. The endpoints cover the actual minimum and maximum across all enabled slices. Numeric labels use scientific notation with four significant digits, such as `1.234e-03`, with zero shown as `0.000e+00`. The bar overlays the scene without a white background panel, remains anchored when the window is resized, and does not intercept camera mouse input. With no SDF planes enabled, the bar is hidden. Planes and point glyphs share true 3-D projection and depth ordering; the SDF colors are unlit and do not change with camera orientation.

The point layer uses actual configured surface nodes in prepared geometry-local coordinates, including centroid correction for movable geometry. It does not use the independently smoothed display mesh. The point count is shown in the window. Glyph sizes only aid inspection and do not change contact-node positions or physical particle radii.

The selected geometry is built once in the background for both layers, with progress reported in Console. All geometry families, imported meshes, batch-created geometry and saved-frame geometry share this preview. SDF-plane switches update only the scalar planes and their legend; they do not resend the point snapshot or regenerate the geometry. No solver particles are added, and the main View camera is unchanged. Each window is a snapshot of the geometry at opening time; reopen it after editing. If the complete points or center planes exceed the configured display-memory budget, the operation reports an error instead of silently removing samples.

## 7. Particle definitions inside Packings

The model still serializes reusable Sphere and LSParticle types so old projects, stable references, and solver compilation remain compatible. The workbench does not display a separate **Particles** tree branch. Use a single-type Packing's **Configure Particle Packing** or **Reconfigure Particle Packing...** window to set its definition. For a multi-type random Sphere Packing, set **Radius variant count**, **Minimum radius**, **Maximum radius**, and **Radius method** directly in **Sphere Radii and Material**; Random mode also offers **Radius random seed**. Then use **Choose sphere material...**. For a multi-type random LS Packing, use **Select LS geometries...** and **Choose LS material...** in its **LS Geometries and Material** section. Neither random dialog exposes a **Particle Types** section; the required type references are maintained internally.

### 7.1 Sphere Type

A Sphere Type references one Sphere Material and stores a physical radius. Set both in a single-type Sphere Packing configuration. A multi-type random Sphere Packing generates radius variants from a count, range, and arithmetic or random method in its own dialog; Random mode has a separate radius seed. All variants use one Sphere Material. The type itself does not store a scene position.

### 7.2 LSParticle Type

An internal LS particle definition references one LS Material and one LS Geometry. Select both for a lattice LS Packing; for a random LS Packing, select the geometries and their shared material in its Configure dialog. LS grid construction prepares centroidal coordinates, volume, unit-density inertia, and bounding information for movable geometries. During model compilation the particle derives its mass and inertia from these cached geometry properties and material density.

### 7.3 Infinite mass

Infinite mass remains a Particle Type property, although its control is shown in the Packing configuration. When enabled:

- inverse mass is zero;
- ordinary velocity and angular-velocity integration cannot move the particle;
- the viewport uses the shared light-gray fixed-particle color instead of the Packing color;
- a Packing of this type may use Prescribed Motion.

## 8. Particle Packings

A Particle Packing is the only owner of rigid-particle placement and group display settings. Choose **Sphere Packings > +** or **LS Particle Packings > +**, then **Lattice packing...** or **Random packing...**. A lattice Packing always opens **Configure Particle Packing** before creation, including its radius/material or geometry/material. **Choose sphere material...** and **Choose LS material...** use the same searchable name-prefix tree as **Choose LS geometry...**. Each picker shows only the compatible material kind and retains the selected stable ID. Expand a group or search to select an existing definition. A random Sphere Packing sets **Radius variant count**, **Minimum radius**, **Maximum radius**, and **Radius method** directly in **Sphere Radii and Material**; Random mode also sets **Radius random seed**. Then use **Choose sphere material...**. The LS dialog uses **LS Geometries and Material > Select LS geometries...** and **Choose LS material...**. After creation, select the Packing and use **Reconfigure Particle Packing...** in Properties. The Packing Properties do not repeat the lattice radius/geometry/material values; reopen Configure to inspect or change them. Reconfiguration preserves the Packing ID and does not silently change other Packings that shared its particle definition. The random dialog has its own Reconfigure action. Configure dialogs omit **Activation step**; set it on the created Packing's Properties page.

### 8.1 Placement values

- **Particle definition** is configured with the Packing: sphere radius/material or LS geometry/material and infinite-mass state. Existing projects retain the underlying type reference.
- **Lattice** selects SC, BCC, FCC, or HCP.
- **Particle count X/Y/Z** gives the exact number of generated positions along each direction.
- **Lattice spacing** gives the X/Y/Z lattice base intervals in metres. Ideal spacing is shown as read-only **Lattice spacing (m)**; Custom spacing has three editable components.
- **Origin X/Y/Z** gives the Packing origin in metres.
- **Activation step** gives the solver step at which the Packing enters the running model.

Every lattice creates exactly `X x Y x Z` positions. BCC, FCC, and HCP alter layer offsets and compactness; they do not multiply the requested count by a conventional-cell atom count.

### 8.2 Initial kinematics

Velocity and angular velocity support:

- **Constant**, where every particle receives one vector;
- **Uniform random**, where each component is sampled independently between configured lower and upper limits.

Orientation supports:

- constant X/Y/Z angles in degrees, applied about fixed world axes in X-then-Y-then-Z order;
- a uniform random 3-D rotation.

The editor converts constant angles to a quaternion when saving; existing project files and the solver continue to store orientations as quaternions. Random values are determined by the Packing seed and local particle index. The same project, seed, and Packing order reproduce the same initial state.

### 8.3 Packing spacing source

The single-type Packing configuration defaults to **Ideal bounding-sphere spacing**. It uses the Sphere radius or the conservative LS Geometry bounding radius to calculate the lattice X/Y/Z intervals for the selected SC, BCC, FCC, or HCP arrangement. The calculated values are displayed read-only and update when the particle radius, LS Geometry, or lattice changes. Select **Custom spacing** to enter the three intervals yourself; switching from ideal starts with the currently calculated values. The chosen source and the resulting numeric spacing are saved with the Packing. Older projects without a spacing-source field retain their saved numeric spacing as Custom. Ideal spacing is a placement aid, not a guarantee that arbitrarily shaped or oriented LS particles cannot intersect.

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
- For Sphere, LS Particle, and SPH Packings, **Coloring** selects **Packing color** or **Velocity magnitude**. New Packings receive distinct default colors; Packing color exposes the RGB picker, while Velocity magnitude hides it.
- **Packing Bounds > Visible** shows this Packing's bounding box. **Bounding-box dimensions** adds its X/Y/Z lengths in metres only when both switches are on; its editor is disabled while Visible is off. Both switches are off by default and are saved independently for each Sphere, LSParticle, or SPH Packing.
- **Move in View** is the four-arrow toggle at the right of each preprocessing Packing row, not an inspector button. It starts off and uses the same control for Sphere, LSParticle, Random particle, and SPH Packings. It is available only for an editable model. Enable it to show the active Packing's bounding box and center move handle, even when its Post-processing bounds switch is off. Drag the handle to translate the group; left-dragging elsewhere continues to orbit the camera. While Move is active, the **Placement** fields are temporarily locked against competing manual edits. Their position components show the current drag coordinates live; the stored project and its undo history change once on release, not on every mouse movement. Disable Move to restore normal Placement editing, subject to the normal model and imported-frame locks.

Each new Sphere, LS Particle, or SPH Packing receives a color based on its Packing order, which can then be customized independently. Infinite-mass particles always use light gray.

Select Packings in the tree, not by clicking particles in the viewport. Tree selection in either preprocessing or Post-processing does not automatically display a bounding box. Explicitly enabled boxes remain visible when another tree item is selected. A hidden Packing or one with zero opacity displays neither its box nor its dimension labels.

Saved explicit Packing colors remain unchanged when old projects are opened. A retired automatic color mode is converted to Packing color using its former visible color, so the editor always has a valid choice; heterogeneous legacy Packings consequently use one shared color after conversion. The selected coloring mode is saved with the project and survives frame export and restart.

Sphere Packing bounds use particle positions plus or minus physical radii. LSParticle Packing bounds use the minimum and maximum of all surface-node positions after each particle's position and orientation are applied; neither the LS bounding sphere nor a display sample substitutes for these points. Box edges obey the same world-space depth test as other geometry and can be occluded by objects in front.

### 8.6 Batch definitions and random packing

Geometry preparation and particle placement remain separate operations. Sphere radius/material and LS geometry/material are configured inside the Packing workflow; the serialized particle types remain internal definitions. All generation dialogs reuse the standard property rows, units, selectors, validation and action buttons. Work before Run or after Reset.

1. **LS Geometries > + > Superellipsoids and variants > Random geometry...** uses one superellipsoid base: three semi-axes and two exponents, plus signed radial surface-height bounds. Set a base name (default **Level-Set Geometry**), seed, subdivision, grid spacing, and padding. One confirmation generates one geometry with the first available numbered name, not a batch. Heights are offsets from the base surface, not world Z values or absolute distances from the final centroid. Smooth bounded perturbations preserve the radial topology; they do not model holes or overhangs. After confirmation, the geometry goes through `LSInfo::buildLSGrid()` for volume, inertia, and centroid correction, the same path used by Preview and the solver. This step needs no material and creates no particle types.
2. **Sphere Packings > +** and **LS Particle Packings > +** each offer **Lattice packing...** and **Random packing...**. Lattice creation opens the common Packing configuration with the relevant particle values and SC/BCC/FCC/HCP controls. Choose material and radius for spheres; choose material and existing geometry for LS particles. The dialog must be confirmed before the Packing or its definition is added.
3. **Random packing...** also opens a configuration window before generation. For spheres, set **Radius variant count**, **Minimum radius**, **Maximum radius**, and **Radius method** directly in **Sphere Radii and Material**. **Arithmetic sequence** spaces the variants evenly across both bounds when there is more than one; one variant uses the minimum. **Random** draws uniform radii from the range and exposes **Radius random seed** so the same settings reproduce the variants. Then use **Choose sphere material...** for one shared Sphere Material. For LS particles, use **LS Geometries and Material > Select LS geometries...** to select multiple existing bounded LS geometries in the searchable, name-prefix-grouped picker, then **Choose LS material...** to assign one LS Material shared by them. Walls and reversed-SDF container geometries are excluded. Geometry generation still happens separately under **LS Geometries**. One random generation creates one Packing, not one Packing per radius or geometry variant.
4. Set the separate placement **Random seed**, cuboid minimum/size, and placement mode in the same window. Set **Activation step** afterward in Packing Properties. Existing random Packings reopen Configure through **Reconfigure** in Properties. A legacy Sphere Packing without a saved radius recipe starts with **Keep saved radii**; selecting Arithmetic sequence or Random explicitly replaces those variants. Sphere radii or LS geometries are sorted by descending enclosing radius and assigned cyclically, largest first. If fewer particles are generated than candidates, only the largest candidates are used; otherwise every candidate appears in each full cycle. The placement seed controls positions and LS orientations, not this radius/geometry order. Regeneration preserves the Packing's stable ID. Canceling or failing generation leaves the prior project intact.

Random placement has two modes. **No overlap** defaults to one particle and zero clearance, and uses the requested total particle count, clearance and placement-attempt limit, with conservative enclosing-sphere separation against both new and existing rigid particles. It can leave loose arrangements for elongated shapes; an overfilled box fails without adding a partial sample. **Porosity (overlap allowed)** derives a whole-particle count from cyclically assigned candidate volumes and `(1 - porosity) * box volume`, using sphere volumes or centroid-corrected LS volumes. Integer counts approximate the target; the Console reports the achieved nominal porosity. For each particle it first tries non-overlapping positions with the specified clearance and attempt limit. Only if those attempts fail does that particle accept an overlapping in-box position; other particles still get their own separation attempts. Overlapping particle volumes are counted separately, so nominal porosity is not a geometric-union void fraction. Both modes keep enclosing spheres inside the requested cuboid. Walls, reversed-SDF containers and SPH sources are not obstacles: check physical boundaries separately. Neither mode establishes mechanical equilibrium; use suitable initial overlaps and settling parameters.

Batch-created geometry and types are **not permanently read-only**. Old project files' geometry/type `generatedByRandomSample` flags are ignored and omitted when resaved. The particle types remain serialized even though they have no separate tree category. A referenced type or geometry must still be reassigned or deleted together with its dependents; existing saved-frame prefixes and post-run locks remain protected. Reconfiguring a size or shape does not imply that previous random separation remains valid, so review overlaps afterward.

Each random packing stores actual per-particle type IDs, positions relative to its editable Origin and LS orientations. It can be moved, scheduled, hidden, recolored and deleted normally. Its stored positions do not offer lattice-count/spacing controls; changing the Origin translates it without regeneration. Reconfigure through its dialog to change the Sphere radius range, variant count, method, and shared Sphere material, or the LS geometries, shared LS material, and placement settings. Irregular geometry stores its generated triangles as the authoritative mesh and, when created by the current workflow, also stores the procedural source used by **Reconfigure Geometry...** for subsequent surface/grid changes.

Each generation is one undoable transaction. Project saves preserve explicit mesh/placement data; loading never rerolls random seeds or rewrites existing type IDs. Schema 8 still carries per-placement references. The legacy packing-only `generatedByRandomSample` flag is provenance, not a category selector or editing lock. Older samples appear under Sphere Packings or LS Particle Packings without changing their order, IDs, Bonds or schedules.

## 9. SPH Packings

SPHDEM uses one global SPH property set, but the project may contain multiple Blocks or Jets.

Choose **SPH Packings > +**, then **SPH Block** or **SPH Jet**, to open the corresponding **Configure SPH Block** or **Configure SPH Jet** window before the Packing is added. The add menu fixes the kind; Configure has no **Source kind** field. Set the name, Block minimum and particle counts or Jet inlet center/radius/length, and initial velocity. The Packing is added only after confirmation. Its Properties page starts with **Particle Packing** and places **Activation step** in a separate **Activation** section. Properties shows neither an internal **ID** nor a **Particle count** row; Block counts remain in Configure, which also hides the internal ID. Select an existing Packing and use **Reconfigure SPH Packing...** in Properties to reopen the generation values while keeping its stable ID; canceling leaves the previous Packing unchanged. The global SPH material/discretization settings remain under **Analysis > Loop Parameters** rather than in each Packing.

In a project exported from a recorded frame, the global SPH properties are read-only whenever the saved model contains an SPH Block or Jet, including a source scheduled for later activation. Particle spacing, smoothing length, reference density, dynamic viscosity, design velocity, and artificial sound speed remain fixed to the saved model. Newly appended sources use that same property set. Other solver/output settings remain editable; use a new initial project to change the fluid discretization or material properties. Older frame projects without a saved property baseline adopt their imported global values once; resaving records that baseline.

### 9.1 Block

A Block creates a regular fluid volume from its minimum position, 3-D particle count, and global spacing. Activation step supports staged fluid insertion.

### 9.2 Jet

A Jet uses inlet position, inlet velocity, radius, and length to create a cylindrical particle source. Velocity must be nonzero because it also defines the jet axis.

While a generated jet particle remains inside its active virtual inlet pipe, FunDEMBeta marks it as constrained and restores the prescribed inlet velocity. A constrained particle keeps its current density: density reinitialization, density-rate evaluation, and density integration are skipped for that particle. It still participates normally as a neighbor in the pressure, viscosity, and density calculations of unconstrained particles. The constraint clears automatically when the particle leaves the pipe or the finite jet duration ends.

### 9.3 SPH particle display

SPH Block and Jet Packings use depth-correct particle impostors in preview, calculation, recorded playback, and animation export. Use the Packing's controls under **Post-processing > Packings** to select visibility, color, opacity, and velocity-magnitude coloring. **Clip Plane** selects particles by their center positions.

The application does not expose liquid-surface reconstruction. Recorded SPH frames can be inspected, played, or exported directly; no surface preparation or mesh cache is required. These display choices do not alter the SPH state or scientific output. The default SPH VTU fields remain position, velocity, mass, and density.

## 10. Bond Packings

The editable project does not maintain a top-level collection of individual Bonds. A Bond Packing generates Bonds in bulk between two Particle Packings. The compiler stores the generated values in the corresponding FunDEMBeta interaction container.

The **Configure Bond Packing** window lists **Name**, **Particle Packing 1**, **Particle Packing 2**, and **Rule**, followed by **Maximum particle position distance** for the distance rule or the saved-contact source for the Contacts rule. It does not set Bond length. After creation, **Properties** provides an editable **Name** and shows the selected source Packing names and the Bond Geometry, Stiffness, and BK Damage values; the internal ID is not shown. **Reconfigure Bond Packing** retains the ID, any custom Bond length, stiffness, and damage values while changing the generation settings; an automatic length source follows the selected endpoint types. Automatic Bond area sources still follow the generation rule.

### 10.1 Particle position distance rule

A candidate pair is selected when the Euclidean distance between its stored particle position/reference points (the center for a sphere) does not exceed **Maximum particle position distance**. An LS particle's stored position is not necessarily its centroid when it uses fixed geometry. The threshold is not a surface-to-surface gap or a Bond length. With **Custom** Bond length, the pair can be bonded even without physical contact. The saved rule enum remains `"Distance"` and its threshold field remains `maximumCenterDistance`.

- Bond point is the midpoint between the two facing bounding-sphere surface points.
- New sphere-only Packings use particle-center distance for Bond length; Packings with an LS endpoint use actual LS contact overlap. In the latter mode, pairs without positive overlap are skipped even if they pass the particle-position-distance threshold.
- **Properties > Bond Geometry > Bond length (m)** accepts a positive manual value and switches the Packing to **Custom**. This allows Distance-rule pairs without physical contact to be bonded.
- **Bond area source** offers **Custom** or **Minimum bounding-sphere circle** (`pi * minimumBoundingRadius²`). With **Custom**, enter **Bond area (m²)**.

### 10.2 Contacts rule

This rule is valid for a project exported from a playback frame because a normal initial project has no saved Contacts.

- Bond point uses the saved physical Contact point.
- Normal uses the saved Contact normal.
- New sphere-only Packings use particle-center distance; Packings with an LS endpoint use the saved physical Contact overlap. Enter a positive **Bond length (m)** in Properties to override either source.
- **Bond area source** offers **Custom** or **Contact area**. The latter uses the saved Contact area.

Changing a source Packing updates an automatic length mode to match the endpoint type. Entering a manual Bond length switches the Packing to **Custom**. Legacy LS Bond Packings using center-distance length load as **Overlap**. Existing checkpoint Bonds retain their already-computed lengths.

### 10.3 Stiffness and BK damage

A Bond Packing sets normal, shear, bending, and torsional stiffness. In **Properties > BK Damage**, set **Bond area source** and, for a custom source, **Bond area (m²)** before the Mode-I and Mode-II critical energies, mode-mixity exponent, and damage-initiation ratio. The available area sources depend on the generation rule as described above.

A new Bond Packing defaults to **Custom** Bond area source and **Bond area = 0 m²**. A zero area disables fracture evaluation while allowing an elastic Bond response. In C++ and saved JSON this field is named `crossSectionArea`; older JSON documents containing `fractureArea` remain readable, including their Bond VTU field selections. New documents and VTU files use `crossSectionArea`.

The viewport renders each Bond as a cylinder:

- center: Bond point;
- axis: Bond normal;
- length: equivalent length;
- effective area: `crossSectionArea * (1 - damageFactor)`;
- physical radius: `sqrt(effectiveArea / pi)`.

### 10.4 Selecting Bond Packings for display

Each Bond Packing appears by name directly under **Post-processing > Packings**. Use its Eye button or select that Packing and change **Visible** and **Opacity**. The live frame, preview, and playback frames use those same per-Packing settings.

Bond Packing visibility is independent of the Force Chain selection. It affects only the viewport and never deletes Bonds, changes mechanics, or changes VTU output.

### 10.5 Bond elastic-energy coloring

Select a Bond Packing under **Post-processing > Packings** and set **Coloring** to **Uniform**, **Total elastic energy**, **Normal elastic energy**, **Shear elastic energy**, **Bending elastic energy**, or **Torsional elastic energy**. Each choice applies to individual Bonds in that Packing, so two Bond Packings can display different energy components at the same time. Uniform uses the Packing's configured color. The five energy choices map each Bond's selected spring energy, in joules, to the shared scalar palette. Total elastic energy is:

`E = E_normal + E_shear + E_bending + E_torsional` in joules.

This is the total for one Bond, not the sum over its Packing. Normal, shear, bending, and torsional choices show the corresponding term alone. The values follow the solver's energy calculation; the display does not multiply them by `1 - damageFactor` again. The cylinder's effective area still follows the damage-based rule in Section 10.3, independently of its color.

Under **Post-processing > Legends > Bond Elastic Energy**, choose **Range > Historical maximum** or **Custom**. Historical maximum uses one common scale for the selected energy components of visible Bond Packings. Changing the visible Packing scope or its component choice uses the relevant maxima already accumulated during the current session, including state captured before energy coloring was enabled. The legend title is generic because visible Packings may use different components. Custom exposes **Minimum (J)** and **Maximum (J)** and overrides the automatic range.

An older or imported playback frame may contain total Bond energy but lack the four component values. If the selected component is unavailable, its Bond is drawn with its uniform Packing color instead of showing a fabricated zero-energy value. Newly calculated frames contain the component values.

Bond Packing visibility, opacity, and uniform-color settings remain available. Coloring and legend-range changes affect only Post-processing; they do not change Bond stiffness, damage, forces, or scientific output.

## 11. Force Chain display

The viewport displays sphere-sphere, sphere-LSParticle, and LSParticle-LSParticle Contacts. Each actual Contact produces a branch from its contact point to each finite-mass owner's center of mass. An infinite-mass side is never drawn: a particle contacting a fixed wall therefore has only the finite particle's branch. A Contact between two infinite-mass owners has no visible branch. Contacts are not merged into a center-to-center pair chain; distinct surface Contacts retain their actual locations.

Select **Post-processing > Contacts > Force component** to display **Normal force** (the default), **Tangential force**, or **Resultant force**. These are magnitudes in newtons; they control both cylinder width and color, and the legend uses the selected quantity's name. The control remains available during calculation and playback and does not change contact mechanics.

For a contact with unit normal `n` and evaluated force `F`, the tangential component is `F - dot(F, n) * n`. Resultant force is the magnitude of the full evaluated vector, not the sum of normal and tangential magnitudes. Rolling and torsional moments are not forces and are not included. Both branches of the same Contact use that Contact's selected force magnitude; multiple Contacts between the same owners are displayed independently. Width uses square-root scaling, clamping the normalized magnitude to `[0, 1]`:

\[
r=r_{min}+(r_{max}-r_{min})\sqrt{\operatorname{clamp}\!\left(\frac{F-F_{min}}{F_{max}-F_{min}},0,1\right)}
\]

Color and width use the same selected force magnitude. **Post-processing > Legends > Force Chain Magnitude** provides the existing historical or custom minimum/maximum range. Histories for normal, tangential, and resultant forces are independent, so switching components never reuses another component's maximum. Changing the rigid-particle Packing selection uses the accumulated history of the newly selected Packings. Custom bounds remain explicit user settings when switching components.

New playback frames and exported frame projects retain all three force components. Older frame-project JSON files stored only normal-force magnitudes; they load with normal display selected unless explicitly configured otherwise, and their unavailable tangential component is treated as zero until the solver evaluates new contact forces.

In the project tree, select **Post-processing > Contacts**, then use **Select Force-chain Packings...**. The shared selector groups Sphere Packings and LS Particle Packings; SPH fluid Packings are not included. A Contact is included when either owner belongs to a selected Packing, and all its finite-mass branches are shown. Selecting a fixed wall's Packing can therefore show the finite particles' wall-contact branches without drawing a branch to the wall center. Selecting all rigid Packings also includes future rigid Packings; an explicit empty selection shows no force chains.

## 12. Post-processing and camera controls

### 12.1 Navigation structure

There is no View menu or View item in the project tree. Display controls are organized as follows:

- **Post-processing > Packings** in the project tree contains every rigid-particle Packing, SPH Block/Jet, and Bond Packing display page, with an Eye button for each Packing.
- **Post-processing > Contacts** contains Force Chain visibility and rigid-particle Packing scope.
- **Post-processing > Filters** contains the global coordinate axes and Clip Plane.
- **Post-processing > Legends** contains the three shared scalar-range controls: **Velocity Magnitude**, **Force Chain Magnitude**, and **Bond Elastic Energy**. Packing-bounds controls belong to each Packing's display page, not to Legends.
- The top **Settings** menu contains only **Display Storage** and **Workspace**. **Settings > Display Storage** contains separate **Viewport** and **Playback Cache** budgets; **Settings > Workspace** shows or hides workspace panels. Bond and Force Chain visibility controls remain in the Post-processing tree.

Camera controls do not appear in the Post-processing tree or Settings menu. The camera-icon button immediately after **Reset** on the Simulation toolbar is the only Camera menu.

### 12.2 Default state

Global coordinate axes, Force Chains, Clip Plane filtering, and **Show plane** all start disabled in a new project. Enable the axes manually under **Post-processing > Filters** when needed. Each Particle Packing, SPH Block, Jet, and Bond Packing owns its own Eye and opacity controls, initially visible and fully opaque. Particle Packings additionally have **Packing Bounds > Visible** and **Bounding-box dimensions** switches, both off by default; selecting a tree item does not change them.

### 12.3 Camera

- Left drag orbits without an artificial vertical-angle limit.
- Clicking inside the viewport does not select a Packing; use the tree to choose the target for preprocessing or Post-processing controls.
- Right or middle drag pans.
- The mouse wheel zooms.
- Open the camera-icon menu immediately after **Reset** to use **Fit Scene** or a preset.
- **Fit Scene** frames every Packing and the complete current particle state.
- The seven presets are Isometric, Front, Back, Left, Right, Top, and Bottom.

Camera changes affect only viewport and projection matrices. They do not alter particle state, regenerate LS display meshes, or upload an unchanged opaque-particle instance buffer.

### 12.4 Scene bounds

Scene bounds are the union of the true bounds of every Particle Packing, SPH Block, and Jet, independent of visibility and Activation step. During calculation and playback, the full unsampled current-particle AABB is included so moving particles remain inside the fitted scene. Force Chains, Bonds, axes, and the Clip Plane do not enlarge the scene. These bounds drive camera fitting, projection, axis length, and clip ranges; no global grid or scene-bounds box is drawn. Explicitly enabled per-Packing boxes are separate display aids. The global coordinate axes always begin at world origin `(0, 0, 0)`, not at the minimum corner of a Packing or the scene. Only their length scales with scene size. Axis lines and X/Y/Z labels share this origin in the viewport; an origin outside the camera view is not moved into the frame. Both lines and labels are omitted from recorded animations and their exported PNG keyframes, without changing the viewport visibility setting.

Each Sphere, LSParticle, and SPH Packing has its own **Packing Bounds** section under **Post-processing > Packings**. **Visible** controls its box, and **Bounding-box dimensions** controls the X/Y/Z length labels in metres. The stored fields are `boundsVisible` and `boundsDimensionsVisible`, both defaulting to `false`. Dimensions require both switches, and their editor is disabled while Visible is off. These values persist independently of tree selection and do not change scene fitting. Eye-off and zero-opacity Packings suppress both box and labels. The only automatic box is the temporary placement aid required by preprocessing **Move in View**; Move does not enable dimension labels, and disabling it returns to the Packing's saved Post-processing bounds settings.

### 12.5 Transparency and depth

Every physical object uses one perspective and depth convention:

1. Opaque surfaces render first and write depth.
2. Transparent surfaces blend from back to front without writing depth.
3. A closed transparent boundary is split into far and near surface layers so internal particles are rendered between them.
4. Edges, Packing bounding boxes, Bond cylinders, and Force Chains obey world-space depth.
5. Text, numeric annotations, and manipulation handles use the final screen overlay; Packing-box edges are not an always-on-top overlay.

This policy prevents a boundary edge from appearing permanently in front of particles and prevents a transparent wall from applying inconsistent colors to members of one Packing.

### 12.6 Clip Plane

- **Enabled** applies the positional filter.
- **Show plane** controls only the orange plane guide.
- **Normal axis** selects X, Y, or Z.
- **Retained side** chooses coordinates less than/equal to or greater than/equal to the position.
- **Section position** is measured in metres.

Particles are retained or hidden as complete objects according to their center positions; triangles are not physically cut. Both branches of a Force Chain are classified using their actual Contact point. A Bond is classified using its Bond point. Drag the orange center handle to move the plane along its normal.

### 12.7 Controls available during calculation and background work

After the solver starts, every control that changes mechanics, references, or solver parameters remains locked until Reset. The following viewport-only controls remain available:

- camera orbit, pan, zoom, Fit Scene, and standard views;
- Packing Eye, opacity, and color mode;
- axes, Force Chains, and Bonds visibility;
- Sphere and LS Packing scope for Force Chains;
- each Bond Packing's Eye, visibility, opacity, and coloring;
- Clip Plane settings and manipulation;
- each Packing's bounding-box visibility and dimension annotations.

These controls modify camera state or separate presentation descriptions and masks. They do not regenerate, reorder, or resize solver particle arrays, Contact/Bond arrays, or recorded playback-history arrays. Background preparation, project file operations, import, and export temporarily lock the workspace until they finish; the controls above become available again during normal calculation. Packing placement is a model change, not a display setting, so it remains locked. Playback navigation is available only when calculation and background work are idle. Under **Settings > Display Storage**, **Viewport** changes particle presentation sampling, while **Playback Cache** changes how much recorded-frame data is retained in memory for reuse. Both budgets can be changed during normal calculation.

## 13. Particle Force Modules

Both formulations can load external rigid-particle force laws from dynamic libraries. A module declares whether it supports spheres, LS particles, or both. SphereDEM dispatches its compatible sphere and LS arrays; SPHDEM dispatches only LS boundary particles because this ABI does not expose SPH fluid state. Module metadata declares identity, supported families, parameter types, units, bounds, and defaults. Properties uses the shared templates to generate the required rows.

The reference hydrodynamics module declares:

- Enable;
- Water velocity;
- Fluid density;
- Free-surface height;
- Drag coefficient.

The bundled hydrodynamics module supports spheres only, so it belongs in SphereDEM projects. The bundled Particle Damping module supports finite-mass spheres and LS particles. Modules run after Contact, Bond, and other internal forces have been assembled. The CPU host packs one state array, invokes each compatible module, validates the accumulated result, and adds it to particles. The reference hydrodynamics callback itself applies its formula in parallel. Load only trusted native libraries built for the host operating system, architecture, and ABI. In the public portable release, read `force-modules/README.md`, `force-modules/SphereHydrodynamics.md`, and `force-modules/ParticleDamping.md` for loading and physical-model details. Source-build instructions in the model guides require a separate development checkout.

## 14. Output

### 14.1 Output events

Output interval is expressed in DEM steps. One output event:

1. writes scientific VTU;
2. updates `energy.dat`;
3. stores one compact View playback frame;
4. stores one full restart checkpoint used by frame export.

These products use the same frame number, solver step, and simulation time. Complete bundles, the committed run manifest, and timestamped PVD collections are described in [Run directories and complete frames](WORKFLOW_IMPROVEMENTS.md#run-directories-and-complete-frames).

### 14.2 Mandatory and optional fields

The application always writes fields required to reconstruct basic geometry and lets the user select other FunDEMBeta fields in **Output**. VTU uses binary encoding by default.

- Sphere: position, radius, and velocity are mandatory.
- LSParticle: surface-node position, node velocity, and required mass data are mandatory; finite- and infinite-mass particles are written separately.
- SPH: position, velocity, mass, and density are mandatory.
- Contact: point and normal are mandatory; force, spring, and energy fields are optional.
- Bond: point and normal are mandatory; stiffness, damage, endpoint, torque, and energy fields are optional.

The render frame contains only data required for interactive display, while its paired restart checkpoint preserves every particle state plus retained Contacts and Bonds. Both are written to a private temporary disk history. Frame metadata stays resident, and a configurable playback cache retains recently used render frames within a 256 MiB default budget. Shared immutable LS geometry is reused across retained frames and counted once. **Settings > Display Storage > Playback Cache** controls this cache independently of **Viewport** presentation sampling; see Section 20.1 for the memory tradeoffs. Frames larger than the budget are loaded on demand without cache retention, and scientific checkpoints are not cached. Eviction or turning the cache off does not remove recorded frames from disk or reduce their particle data.

The budget does not cap total application RAM or GPU memory: live solver data, the current frame, active readers, and other application allocations still need memory. Long recordings require sufficient free space on the system temporary drive. Reset or project replacement releases the old temporary history after active readers finish; normal shutdown also removes it. Use **File > Save Run...** to retain complete reopenable results before resetting or closing. An abnormal termination can leave temporary files behind. Opened saved archives remain on disk; see [Save a run and reopen it](WORKFLOW_IMPROVEMENTS.md#save-a-run-and-reopen-it).

SPH VTU, playback frames, and restart checkpoints are captured from one synchronized current-time observation, even when an output step falls between SPH acoustic updates. Merely viewing or exporting that state does not change the simulation's acoustic schedule. A disk-write failure is reported and does not publish a partially recorded frame.

### 14.3 `energy.dat`

`energy.dat` records the solid DEM system and does not mix SPH fluid energy into the same total. Inspect:

- kinetic energy;
- gravitational potential energy;
- contact elastic energy;
- Bond elastic energy;
- total mechanical energy.

Even a nominally nondissipative model can show integration error from finite time steps, stiff contacts, or initial overlap. Reduce the time step and inspect initial geometry before changing display settings.

## 15. Run, Pause, Step, and Reset

The Simulation toolbar uses neutral icon-and-text buttons: a lightning symbol for **Run**, two vertical bars for **Pause**, an arrow ending at a bar for **Step**, and a return arrow for **Reset**. The shared command template is 32 logical pixels high with 18-pixel icons. A divider separates Reset from the first three commands; the main camera menu remains separate. These commands perform calculation, unlike the icon-only playback controls below the viewport. During calculation, only Pause is enabled among these four commands; preparation prevents repeat commands until its safe completion.

### 15.1 First Run

The first Run performs its expensive preparation in the background and reports each available stage in **Output > Console**:

1. increases geometry padding when the project contains both sphere and LS particle types;
2. validates every stable ID and reference;
3. expands Packings;
4. creates material, geometry, particle, and interaction containers;
5. generates Bond Packings;
6. initializes the CPU solver;
7. reserves a unique calculation child under the configured result root, with private core staging;
8. advances by **Steps this run**.

For a mixed sphere/LS project, every geometry must satisfy `padding cells × grid spacing >= maximum sphere radius`. The maximum includes all sphere types, including infinite-mass types and types used by future-activation Packings. The software increases insufficient padding to the smallest suitable integer; it never decreases padding or changes grid spacing or the physical shape. This is a system preparation rule, including for generated resources and read-only imported-frame geometry. The corrected padding is used for both preview and solver geometry, then reflected in Properties and saved project data; the Console lists each change. A sphere-only or LS-only project is unchanged. Preparation stops with an explanation if satisfying the condition would exceed the supported padding limit; it does not run with silently truncated padding. This check runs when constructing the model, not on each DEM step.

A paused Run is labeled **Resume** and resumes its existing target. A Run after completion requests another round from the current state. Time, global step, output numbering, playback history, and the calculation's child directory remain continuous; previous run directories are retained.

The window does not wait through a nested UI event loop while geometry, containers, initial Contacts, or the first output frame are being prepared. A preparation failure is reported without starting the requested continuous calculation.

### 15.2 Pause

Pause stops the worker at a safe step boundary. It does not unlock the physical model. Continue with Resume or Step.

Live monitoring targets a 500 ms refresh interval to reduce snapshot-copy and rendering overhead. Every configured output boundary still publishes its frame and requests a display update, so throttling never delays data publication beyond the next output interval. Pausing, single stepping, and completion also publish the current state. This wall-clock target does not change the solver time step or the output-step interval; a busy interface can still present fewer frames than are recorded.

### 15.3 Step

The first Step performs the same project compilation and then advances one DEM step. Use it to inspect initial Contacts, Force Chains, and Bonds.

### 15.4 Reset

Once the solver has started, physical parameters remain locked even after the current Run has finished. Select Reset to return to editable state. When runtime state or playback history exists, Reset asks for confirmation before discarding that session. It does not delete project definitions or saved output files.

### 15.5 Checks, archives, and quantitative results

**Simulation > Check Model...** checks references, resources, estimates, material rules, and activation/motion schedules before preparation. **File > Save Run...** preserves the committed run in a new archive directory; **Open Results...** reopens it read-only. **Results > Result Query...** and **Export Solid Energy...** provide full-state tabular/CSV data. Reset returns an opened archive to its saved initial project; use **Export Playback Frame as Project...** to restart from a selected recorded state. See [Workflow improvements](WORKFLOW_IMPROVEMENTS.md) for the exact behavior, CLI commands, and current limitations.

## 16. Playback

Every playback frame corresponds to one output event. The playback tools can:

- scrub the timeline;
- select the previous or next frame;
- play at 1:1 simulation-time speed;
- pause and inspect from any camera;
- change Post-processing settings while playing.

Playback never reruns the solver. A large Output interval causes visible jumps between frames. Reduce the interval when smoother animation is required.

### 16.1 SPH particle playback

Pause or finish the calculation, select the visible SPH Packings under **Post-processing > Packings**, then scrub the recorded frames or press **Play**. SPH particles use the same recorded-time playback path as rigid particles. No reconstruction, preparation pass, or surface cache is required.

Visibility, opacity, color, velocity-magnitude coloring, and Clip Plane settings affect presentation only. Playback smoothness depends on the recorded Output interval and the rendering capacity of the machine.

## 17. Saving an animation

**File > Save Animation...** exports recorded output frames as Animated PNG, Motion-JPEG AVI, or H.264 MP4. The command is available only when the compiled session is synchronized, the solver is not running, and at least two recorded frames exist. Pause an active calculation or wait for the current Run to finish before exporting. Animation export reads recorded presentation snapshots; it does not rerun the solver or modify particle, Contact, Bond, or checkpoint data.

### 17.1 Procedure

1. Set the viewport camera and every required Post-processing option before starting the export. Packing visibility, colors, opacity and velocity coloring, Force Chains, Bonds, their Packing scopes, legends, the Clip Plane, and explicitly enabled per-Packing bounds and dimensions are captured as currently configured. Global coordinate-axis lines and X/Y/Z labels are always omitted from recordings, while their normal View setting remains unchanged. Transient editing aids, including Move-only Packing boxes, alignment guides, and manipulation handles, are also omitted.
2. Choose **File > Save Animation...**.
3. Select **Format**, then set **First frame**, **Last frame**, **Frame stride**, and **Playback rate**. **Loop playback** is available only for Animated PNG.
4. Check the live **Output summary**.
5. Select **Save Animation**, then choose the destination. The file dialog uses the extension of the selected encoder: `*.png`/`*.apng`, `*.avi`, or `*.mp4`.
6. Follow frame progress in **Output > Console**. The workspace is temporarily locked until export finishes; no progress window opens.

The source canvas is the current viewport size in physical display pixels, including operating-system display scaling. Resize the viewport before opening the dialog when a different output resolution is required. Every frame uses the same canvas; export never scales or crops a frame. APNG and AVI preserve the source dimensions exactly. H.264 requires even dimensions, so an odd width or height is increased by one pixel by extending the outermost source edge; scene content is not rescaled.

SPH Packings are exported as particles using the current Post-processing settings. No liquid-surface preparation is needed.

### 17.2 Frame selection and timing

- **First frame** and **Last frame** are inclusive recorded-frame indices.
- **Frame stride** exports the first frame, every requested interval after it, and always the selected last frame even when the stride does not land on it exactly.
- **Playback rate** scales recorded simulation time. `1.0` preserves real recorded timing, `2.0` plays twice as fast, and `0.5` plays at half speed.
- **Loop playback** has a precise, format-specific meaning. It starts enabled for APNG; enabling it writes the APNG play count as zero, which instructs compatible viewers to restart at the first frame indefinitely, while disabling it writes one play. It does not duplicate source frames or alter any duration in the summary. AVI and MP4 do not carry this application-level loop instruction, so the checkbox is disabled for those formats and repeat playback is controlled by the video player.

One common presentation timeline is calculated from the original simulation times before an encoder is selected. Increasing the stride therefore removes intermediate images without accelerating the represented motion. The final frame uses a positive neighboring recorded interval so it remains visible at the end of a play or before an APNG loop restarts. Each format represents that timeline as follows:

- **Animated PNG (`*.png`, `*.apng`)** stores the calculated duration with each lossless, full-color frame. Both extensions contain identical APNG data. The embedded play count is the only format-level Loop behavior provided by the application.
- **Motion-JPEG AVI (`*.avi`)** JPEG-compresses every image independently at quality 90. AVI uses a fixed time base, so the writer repeats frames as needed to approximate the calculated per-frame holds; this can introduce small timing quantization and can increase the encoded frame count. The Qt JPEG image-format plugin must be available. AVI 1.0 output is limited to 4 GiB, and exceptionally long or highly irregular timelines can exceed its internal expanded-frame limit.
- **H.264 MP4 (`*.mp4`)** stores lossy H.264 video with the calculated presentation timestamps and durations. It usually produces the smallest delivery file. The encoder uses Media Foundation on Windows and AVFoundation on macOS; neither requires FFmpeg.

The summary reports:

- **Exported frames**: the number of selected source images after range and stride selection; an AVI may contain additional repeated encoded frames to represent holds;
- **Simulation span**: selected last-frame time minus selected first-frame time;
- **Playback span**: simulation span divided by Playback rate;
- **Animation duration**: encoded duration including the final-frame hold;
- **Output size**: encoded canvas width and height in physical pixels, including the possible one-pixel H.264 even-dimension extension.

### 17.3 Output safety and state restoration

APNG is the lossless archival choice, Motion-JPEG AVI favors simple independently decodable frames, and H.264 MP4 is the compact delivery choice. High-resolution APNG and AVI output can be substantially larger than MP4. Use a narrower frame range, a larger stride, or a smaller viewport when file size matters. Every format contains presentation pixels only; use VTU, `energy.dat`, or an exported frame project for quantitative data and restart state.

The destination is written atomically. Cancellation or an encoding error discards the temporary output; no partial animation replaces the requested file. While export is active, the workspace is temporarily locked, preserving the selected camera and display settings. Closing the main window requests cancellation at a safe frame boundary and leaves the application open until export cleanup completes. When export finishes, fails, or is canceled, the application restores the previously selected live or playback frame, resumes the snapshot polling state, restores the normal controls, and resumes recorded playback when it had been active before export.

### 17.4 Running a project directly to MP4

The Windows application can run a complete project and export its recorded history without navigating the editor:

```powershell
& 'C:\FunDEM\FunDEM.exe' --run-project-video 'C:\FunDEM\examples\gombocSelfRighting.fundem.json' 'C:\Videos\gomboc.mp4'
& 'C:\FunDEM\FunDEM.exe' --run-project-video 'C:\FunDEM\examples\damBreakSquareColumn.fundem.json' 'C:\Videos\damBreak.mp4'
```

Both commands use a real `1920 x 1080` render window, export every recorded frame with stride 1, and retain the original simulation timeline at playback rate 1. SPH projects use particle visualization, just like interactive playback. This is not a headless solver: Qt/OpenGL rendering and the platform's native H.264 support are required. Closing its render window cancels the job.

For `gomboc.mp4`, the runner also creates `gomboc.mp4.json` with progress, timings, frame counts, and completion/error status, plus `gomboc.mp4.first.png`, `.middle.png`, and `.last.png`. It refuses to replace an existing video, report, or keyframe. Check the report's `completed` value and process exit code: `0` means success, `2` means failure. A retained failure report is diagnostic data, not a completed video.

To make videos for six of the seven bundled examples, invoke the reusable script from the source checkout. It does not include the irregular-particle cylindrical-mold project:

```powershell
.\scripts\run-example-videos.ps1 -PackageDirectory 'C:\FunDEM' -DestinationDirectory 'C:\Videos\FunDEM-run1'
```

The destination must not exist. The script archives exact project files and their assets under `projects`, records executable/project hashes in `run-manifest.json`, and saves per-case logs. It defaults to two concurrent runs with eight OpenMP threads each; use `-ConcurrentRuns 1 -ThreadsPerRun 4` to reduce resource usage. SPH projects use the same particle-rendering export path.

Scientific output uses a new unique calculation child under the project's configured result root, normally beside the executable—not inside the video destination. Core cleanup is confined to that child's staging directory, and previous runs are retained. Physical simulation duration is not wall-clock execution time: calculation and video encoding can take substantially longer.

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

### 20.1 Display Storage

**Settings > Display Storage** contains two independent budgets:

| Setting | Default | Purpose |
| --- | --- | --- |
| **Viewport** | 512 MiB | Controls the number of particle instances retained for presentation. |
| **Playback Cache** | 256 MiB | Retains recently used complete render frames and their shared geometry for playback reuse. |

**Viewport** offers 64 MiB to 4 GiB. When demand exceeds its budget, sampling is allocated fairly across all Packings, including hidden ones: each non-empty Packing receives representation when capacity permits, then remaining capacity is distributed in proportion to its remaining particle count. Hidden instances remain in the budgeted presentation cache, so reopening a Packing's Eye reveals its retained samples immediately without regenerating the Packing. Calculation, Contacts, Bonds, VTU output, and playback-history particle arrays still retain every particle.

**Playback Cache** offers **Off**, 64, 128, 256, 512, 1024, 2048, and 4096 MiB. It uses least-recently-used eviction, retaining multiple frames when they fit. Shared immutable LS geometry is counted once across the retained frames. Lowering the budget immediately evicts cache entries as needed; **Off** clears retained entries and makes subsequent requests load from disk. A frame larger than the budget can still be viewed and exported without being retained. Neither eviction nor **Off** deletes the full disk history or changes scientific data.

The 256 MiB default is an engineering starting point, not a measured optimum. A larger cache can avoid repeated disk reads when revisiting frames that fit, at the cost of more retained memory; a smaller cache leaves more memory available for the solver and other work. First visits, cache misses, oversized frames, frame copying, and rendering can still take time and may block playback. The cache does not preload the whole recording or guarantee a particular playback speed. Neither budget is a limit on total process RAM or GPU memory, and the two values do not add up to a complete memory estimate.

Both choices are saved as application preferences. They apply to the current session and future sessions, survive Reset and New/Open project, and are independent of project files. Changing the playback budget leaves the temporary-history disk lifecycle unchanged.

### 20.2 Sphere and SPH display

Sphere and SPH particles use depth-correct two-triangle GPU impostors during preview, calculation, recorded playback, and animation export. They do not construct a traditional sphere mesh per particle and are therefore suitable for high instance counts.

**Settings > Display Storage > Viewport** limits particle-display buffers rather than the number of particles in the simulation. Display sampling affects presentation only; it does not change the solver state or VTU output. Hide unneeded Packings or lower the Viewport setting when inspecting large cases on a memory-constrained machine.

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

A particle definition, Packing, Bond Packing, or Module references an invalid stable ID. Inspect the affected Packing's configuration and choose an existing Material or LS Geometry; inspect Bond Packing and Module references in their own Properties. The serialized particle definitions remain part of the project even though there is no separate Particles tree branch.

### 21.3 LSParticle is not visible

Check, in order:

1. the LS Particle Packing's configuration names a valid LS Material and Geometry;
2. the Packing contains at least one LS particle;
3. Packing Eye and opacity;
4. Activation step;
5. whether the Clip Plane hides its center;
6. whether display-storage sampling is active.

### 21.4 Force Chains are not visible

The viewport displays rigid-particle Contact branches. Confirm:

1. a sphere-sphere, sphere-LSParticle, or LSParticle-LSParticle Contact exists;
2. **Post-processing > Contacts > Visible** is enabled in the project tree;
3. at least one owner Packing is selected;
4. the selected force component has positive magnitude;
5. at least one owner has finite mass and a nonzero-length branch;
6. the Clip Plane retains the actual Contact point.

### 21.5 Bonds are not visible

Confirm:

1. the Bond Packing's Eye or **Visible** control is enabled under **Post-processing > Packings**;
2. that Bond Packing's **Opacity** is greater than zero;
3. the Bond point lies on the retained side of the Clip Plane;
4. equivalent length is positive;
5. normal is valid;
6. the Distance or Contacts rule generated at least one Bond.

As damage reaches one, effective area approaches zero and the displayed cylinder contracts accordingly.

### 21.6 Colors differ inside a transparent box

Run the `FunDEM.exe` distributed with this manual. Closed transparent boundaries use far/near surface layers and real surface-depth sorting. Reverse SDF does not participate in main-View transparency sorting.

### 21.7 Locating a new result directory

Each new calculation creates a unique `run-*` child under the configured result root. Check **Run Statistics...**, the Console, or CLI stderr for its actual path. Continuing with Run uses that same child; prior runs are retained. Use **Save Run...** to create an archive that **Open Results...** can reopen. Raw interrupted-run recovery is not an automatic Open Results operation.

## 22. First guided run: Gomboc self-righting

1. Choose **File > Examples > Gomboc self-righting**. See [the example guide](EXAMPLE_GUIDE.md#gomboc-self-righting) and keep its bundled `examples/assets/Gomboc.obj` mesh available.
2. Inspect **Analysis > Formulation**, **Analysis > Loop Parameters**, Materials, LS Geometries, and the Gomboc and fixed-floor Packings. Each Packing shows its particle definition; there is no separate Particles tree branch. The project already defines the starting position and orientation; no placement edits are needed.
3. Check the supplied settings: time step `1.0e-4 s`, new-round length `1200000` steps, and output interval `500` steps. One fresh Run covers `120 s`, with recorded frames spaced by `0.05 s`.
4. Optionally use **Step** to inspect the initial View and result path. Use **Reset** before the full run if you want that run to start again from the initial state.
5. Click **Run**, wait for the completion message, and inspect rocking and self-righting in playback. Running the same round again continues the existing state; it does not restart the drop.
6. Plot the energy columns in `gombocSelfRighting_files/energy.dat`, resolved from the executable directory. Restitution is `0.4` and sliding friction is `0.1`, so this is a dissipative case: total mechanical energy should decay overall as motion settles, not remain constant. Numerical traces need not decrease at every individual output sample.
7. Open the VTU files in ParaView to inspect positions, shapes, and velocity. Optional scientific fields can be selected in **Analysis > Output** before starting a fresh calculation.
8. Export one playback frame as a project, reload it, and Run to inspect restart behavior. Use a distinct output directory when retaining both runs.

The other packaged projects are the interlocked chain, the bonded cloth falling onto a box, the dam break around a square column, Brazil-nut segregation, the superellipsoid drum, and irregular particles in a cylindrical mold. Open them from **File > Examples**. The chain uses physical interlocking rather than Bonds; the cloth uses triangular unbreakable Bonds and the packaged Particle Damping module; the dam break demonstrates SPH/LS interaction; Brazil-nut segregation and the drum demonstrate staged activation followed by prescribed boundary motion. The cylindrical-mold project uses irregular LS particles generated from superellipsoids. See [the example guide](EXAMPLE_GUIDE.md) for the complete project settings and required assets.

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

The public Windows ZIP includes these runtime documents:

- `README.md`: installation and quick start.
- `docs/USER_MANUAL.md`: this manual.
- `docs/WORKFLOW_IMPROVEMENTS.md`: model checks, complete run directories, saved archives, quantitative CSV exports, and the headless CLI.
- `force-modules/README.md`, `SphereHydrodynamics.md`, and `ParticleDamping.md`: module loading and physical-model guides, all under `force-modules/`.
- `examples/README.md` and `examples/randomShapeColumn.md`: bundled project descriptions and the irregular-particle guide.
- `THIRD_PARTY-NOTICES.md`, `FUNDEM_BETA_CORE.txt`, and `licenses/`: notices and license texts.

The Apple Silicon bundle also includes `docs/MACOS_GUIDE.md` under `FunDEM.app/Contents/Resources`. A full development install may additionally contain `docs/ARCHITECTURE.md`, `docs/CODE_REFERENCE.md`, `studio/` documentation, `force-modules/sdk/`, and module implementation examples; these are not in the public runtime packages.
