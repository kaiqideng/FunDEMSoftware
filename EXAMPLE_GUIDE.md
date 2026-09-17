# Example projects

The seven projects below are complete FunDEMSoftware models. Open one with **File > Open Project** to inspect its initial model, then choose **Run** to simulate. Each file contains its materials, geometries, particle types, Packings, solver settings, and output selection. Generated random-sample geometries and particle types are read-only; their packing placement and display settings remain editable before calculation. Referenced OBJ meshes are included in `assets`; keep that directory with the project files. The cloth example also uses the bundled Particle Damping library in `force-modules`. The level-5 irregular-particle column starts with global coordinate axes visible; the other projects start with them hidden. Change this setting under **Post-processing > Filters** if needed. The Brazil-nut project also starts with Clip Plane filtering and its plane display disabled.

| Project | File | Calculation time | Output interval |
| --- | --- | --- | --- |
| Gomboc self-righting | [`gombocSelfRighting.fundem.json`](gombocSelfRighting.fundem.json) | 120 s | 0.05 s |
| Physically interlocked anchor chain | [`interlockedChain.fundem.json`](interlockedChain.fundem.json) | 3 s | 0.05 s |
| Damped fine-grain cloth on a box | [`clothBoxDrop.fundem.json`](clothBoxDrop.fundem.json) | 3 s | 0.05 s |
| Dam break around a square column | [`damBreakSquareColumn.fundem.json`](damBreakSquareColumn.fundem.json) | 3 s | 0.05 s |
| Brazil nut segregation | [`brazilNut.fundem.json`](brazilNut.fundem.json) | 8 s | 0.05 s |
| Layered superellipsoids in a rotating drum | [`superellipsoidDrum.fundem.json`](superellipsoidDrum.fundem.json) | 8 s | 0.05 s |
| Level-5 irregular particles in a cylindrical mold | [`randomShapeColumnLevel5.fundem.json`](randomShapeColumnLevel5.fundem.json) | 2 s per Run | 0.05 s |

Calculation times are physical simulation times, not estimates of wall-clock execution time. Output directories are created beside the executable. Existing results are not part of the bundled example projects. See the [user manual](../docs/USER_MANUAL.md) for execution, playback, restart export, and energy output.

## Gomboc self-righting

`gombocSelfRighting.fundem.json` uses the unchanged `assets/Gomboc.obj` mesh, the original Euler-derived orientation, a `0.0023125 m` LS grid spacing, and a `0.1 m` initial center height. Ceramic and floor LS materials retain their existing stiffness, density, restitution, and friction settings. The Gomboc and the infinite-mass floor are separate single-particle LS Packings.

The CPU SphereDEM solver advances 1,200,000 steps at `1e-4 s` and records one frame every 500 steps. Results are written to `gombocSelfRighting_files`. This is a dissipative self-righting example: restitution and friction allow mechanical energy to decrease as the particle comes to rest, so it is not an energy-conservation test.

```powershell
FunDEM.exe examples\gombocSelfRighting.fundem.json
```

## Physically interlocked anchor chain

`interlockedChain.fundem.json` contains eleven links at `0.07 m` pitch and `0.3 m` initial height. Alternating links use identity and 90-degree X-axis orientations. The two endpoint links have infinite mass; the nine interior links can fall under gravity. There are no Bonds: the links are connected by their physical interlocking.

Three Packings reproduce this arrangement. The shared `assets/anchorChainLink.obj` mesh contains 4,480 vertices and 8,960 triangles, generated with 30 straight, 40 arc, and 32 cross-section segments. The source utility is `scripts/generateAnchorChainLink.cpp`.

The CPU solver advances 150,000 steps at `2e-5 s`, for a total of 3 s, and records one frame every 2,500 steps (`0.05 s`). Results are written to `interlockedChain_files`.

```powershell
FunDEM.exe examples\interlockedChain.fundem.json
```

## Damped fine-grain cloth on a box

`clothBoxDrop.fundem.json` matches the physical parameters of FunDEMBeta's `tutorial2.cpp`: a bonded triangular cloth falls onto the outside of a fixed solid box. The box dimensions remain exactly `0.24 x 0.24 x 0.24 m`, centered at `(0.30, 0.30, 0.12) m`. Its SDF is not reversed: this is an obstacle under the cloth, not an enclosing container. The sphere centers start at `z = 0.38 m`, leaving approximately `0.13679 m` between the sphere undersides and the box top.

One `93 x 107 x 1` hexagonal Packing provides 9,951 spheres, below the requested 10,000-particle ceiling. Their diameter is `0.006417112299 m`; the cloth spans `0.60 x 0.59550 m`, centered in X/Y over the original box. This preserves an equilateral triangular neighborhood instead of changing the cloth into a square spring grid. A single Distance Bond Packing connects only nearest neighbors at a threshold of `1.01` diameters, generating 29,454 undirected bonds. Reference lengths come from particle-center separation. Zero cross-section area retains the tutorial's unbreakable bonds; these zero-area bonds do not produce visible cylinders, although their force and energy fields are written to VTU.

The beam coefficients are recomputed for the finer diameter using the tutorial's `E = 1e6 Pa`, `nu = 0.3`, circular section `A = pi r^2`, `I = pi r^4 / 4`, and `J = 2I`:

| Coefficient | Formula | Value |
| --- | --- | --- |
| Normal stiffness | `E A / d` | `5039.988214 N/m` |
| Shear stiffness | `12 E I / d^3` | `3779.991161 N/m` |
| Bending stiffness | `0.01 E I / d` | `0.000129714587 N m` |
| Torsional stiffness | `0.01 G J / d`, `G = E / (2 (1 + nu))` | `0.000099780452 N m` |

Density remains `1500 kg/m3`. Each sphere has mass approximately `0.000207543339 kg`, and total cloth mass is approximately `2.06526 kg`. Reducing sphere and beam thickness at unchanged density reduces the mass from the former 4,106-sphere tutorial's approximately `3.22484 kg`; the current tutorial and software sample both use the fine discretization. This is a finer-grain demonstration, not a fixed-thickness continuum mesh-convergence test.

Contact restitution is reduced to `0.05` and sliding friction increased to `0.6` for stronger collision dissipation. The separately loaded **Particle Damping** module also damps free cloth motion, which restitution alone cannot do: `F = -20 m v` and `torque = -3.418598284e-8 omega` in SI units. For these uniform spheres, the torque coefficient corresponds to an angular decay rate of `40 /s`. The infinite-mass box is excluded. This deliberately dissipative demonstration changes both the settling trajectory and energy history; it is not an energy-conservation test or a calibrated textile material model.

The CPU solver runs 300,000 steps at `1e-5 s`, for exactly 3 s, with output every 5,000 steps (`0.05 s`) to `clothBoxDrop_files`. The tutorial's single-bond time-step estimate at this diameter is approximately `1.275e-5 s`; the selected smaller step also divides the requested duration and output interval exactly. This estimate is not a proof of nonlinear stability for every configuration. Results and DEM energy histories use the ordinary software output path.

```powershell
FunDEM.exe examples\clothBoxDrop.fundem.json
```

Keep `force-modules/fundemParticleDamping.dll` with the executable. The case intentionally fails to compile its solver if that enabled module is missing, rather than silently omitting the requested damping. On a non-Windows source build, replace the module filename with the corresponding built `.so` or `.dylib` library before loading the project.

## Dam break around a square column

`damBreakSquareColumn.fundem.json` uses five SPH Blocks to create a `0.40 x 0.61 x 0.30 m` reservoir and a one-particle-deep wet bed around a `0.12 x 0.12 x 0.75 m` square column. There are 80,376 fluid particles inside a closed `1.60 x 0.61 x 0.75 m` tank. The tank and column are fixed LS Packings.

The CPU SPHDEM solver uses `0.01 m` spacing, `0.013 m` smoothing length, `1,000 kg/m3` reference density, and `0.001 Pa s` dynamic viscosity. It advances 150,000 base steps at `2e-5 s` and records one frame every 2,500 steps. Results are written to `damBreakSquareColumn_files`. No column-force recording hook is installed, so there is no separate `columnForce.dat` or filtered column-load history.

The fluid is displayed as SPH particles during calculation, recorded playback, and animation export. After pausing or finishing, inspect any recorded frame or press **Play** directly; no liquid-surface preparation is required. Packing visibility, opacity, color, velocity-magnitude coloring, and Clip Plane settings remain available in Post-processing. See [Playback](../docs/USER_MANUAL.md#16-playback).

```powershell
FunDEM.exe examples\damBreakSquareColumn.fundem.json
```

## Brazil nut segregation

`brazilNut.fundem.json` models both grain species with level sets. The lower bed retains six layers; the upper bed now has thirty layers, six more than the previous twenty-four-layer configuration:

| Event | Activation step | Time | Objects |
| --- | --- | --- | --- |
| Lower bed | 0 | 0 s | `5 x 5 x 6` = 150 small ellipsoids |
| Large grain | 30,000 | 0.3 s | One LS sphere of radius 22 mm |
| Upper bed | 60,000 | 0.6 s | `5 x 5 x 30` = 750 small ellipsoids |
| Start box vibration | 120,000 | 1.2 s | Infinite-mass box |

There are 901 finite-mass grains and one box. Small grains have semi-axes of 7, 5, and 5 mm and reproducibly randomized orientations. Both species use the same `2,500 kg/m3` density. The large grain and upper bed retain their existing origins. The box is `0.583 m` high, centered at `z = 0.1715 m`: its bottom remains at `z = -0.12 m`, and its top is at `z = 0.463 m`. Increasing its height by six layer spacings (`0.087 m`) preserves about 50 mm of headspace above the enlarged upper bed, using a conservative all-orientation particle extent. Activation, vibration, material, and output settings are unchanged.

The small ellipsoid retains subdivision level 3 (642 surface nodes), and the large sphere retains subdivision level 5 (10,242 surface nodes). These settings provide similar mean surface-node tributary areas without changing the existing contact resolution.

After the filling schedule, the box begins 20 Hz harmonic Z translation with 4 mm amplitude. The dimensionless peak acceleration is `Gamma = A (2 pi f)^2 / g = 6.44`. The calculation uses 800,000 steps at `1e-5 s` (8 s), with output every 5,000 steps to `brazilNut_files`. Activation and vibration use fixed times, not a measured settling criterion. This is a qualitative segregation example; the schedule alone does not guarantee a particular rise time or complete rest before vibration.

```powershell
FunDEM.exe examples\brazilNut.fundem.json
```

## Layered superellipsoids in a rotating drum

`superellipsoidDrum.fundem.json` uses three superellipsoid species in bottom-to-top layers inside a narrow, closed cylindrical LS drum. The species are introduced in sequence while the drum is stationary. All loading layers are placed inside the drum without overlapping the other species' initial loading regions.

The drum has a `0.62 m` radius and a `0.30 m` axial length, reduced from `1.4 m`. There are exactly three granular Packings, one per species, plus the drum Packing. The lower Packing has `15 x 6 x 8 = 720` boxy grains; the middle has `20 x 5 x 6 = 600` prolate grains; and the upper has `17 x 7 x 9 = 1,071` oblate grains, for 2,391 movable grains. Each Packing contains multiple close-packed horizontal layers. Species-specific spacing and origins keep the three initial loading bands separate and inside the closed drum for every generated random orientation. The three reusable particle types are unchanged.

Analytic grain volume is approximately 18.15% of the drum volume. Assuming a settled-bed solid fraction of 0.60 gives a bulk bed occupying about 30.26% of the drum; a looser fraction of 0.55 gives about 33.01%. This is approximately one-third loading rather than an exact fill requirement. It is a design estimate, not a measured settled volume; the actual bed depends on packing density, particle shape, and motion. Initial loose layers occupy a larger height before falling and settling. Conservative shape envelopes leave at least 3.04 mm between initial species bands, 7.53 mm from the cylindrical wall, and 4.49 mm from the end walls.

The first species is active at 0 s, the second at 1 s, and the third at 2 s. The infinite-mass drum begins prescribed rotation at 3 s, at `2 rad/s` about its longitudinal Y axis. These are fixed settling intervals, not an automatic test for zero particle velocity.

The CPU solver uses a `2e-5 s` step for 400,000 steps (8 s total) and records every 2,500 steps (0.05 s) to `superellipsoidDrum_files`. Material properties, grain shapes, surface resolutions, and deterministic random orientations retain their existing settings. The translucent drum permits inspection of the loading and mixing process.

```powershell
FunDEM.exe examples\superellipsoidDrum.fundem.json
```

## Level-5 irregular particles in a cylindrical mold

`randomShapeColumnLevel5.fundem.json` contains 240 finite-mass irregular LS particles in one Random particle packing. Twelve generated shapes are reused 20 times each, with random orientations. Before volume-preserving reshaping into elongated, flat, and blocky families, base radii span `8–12 mm` and radial surface offsets are bounded by `−4 to +4 mm`. These initial bounds are not the final reshaped particle extents. Each shape has an actual level-5 surface mesh: 10,242 vertices and 20,480 triangles. A second packing contains the infinite-mass, closed cylindrical mold, with a `0.075 m` radius, `0.9 m` height, reversed SDF, and opacity `0.10`.

The starting arrangement is a conservative enclosing-sphere deposition, not a settled DEM state. Gravity, friction, and restitution subsequently allow the irregular surfaces to find their contacts. The prepared project has been checked for solver-corrected surface containment and save/reload consistency, but no settling simulation was run when authoring it. The configured `2 s` is an initial calculation round, not a verified convergence time. Monitor kinetic energy and particle velocities, and continue with another Run if needed. There are no bonds, and the mold remains present; this is not a self-supporting column demonstration.

The CPU SphereDEM solver uses `1e-5 s` for 200,000 steps and writes every 5,000 steps (`0.05 s`) to `randomShapeColumnLevel5_files`. Although it uses the SphereDEM formulation, every solid in this project is an LS particle; no ordinary sphere particles are present. High-resolution surface output can be large with 41 scheduled frames including the initial frame. See the [dedicated example guide](randomShapeColumnLevel5.md) for geometry, material values, initialization, execution, and result interpretation.

```powershell
FunDEM.exe examples\randomShapeColumnLevel5.fundem.json
```
