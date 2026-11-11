# Fluent System Icons attribution

The following monochrome SVG assets are original Microsoft Fluent System Icons,
Regular artwork. Their stock paths, view boxes, and native optical weights have
not been redrawn or restroked.

| Local asset | Original glyph |
| --- | --- |
| materials.svg | Layer 20 Regular |
| material-pairs.svg | Shape Intersect 20 Regular |
| particle-instances.svg | Cube 20 Regular |
| events.svg | Folder Open 20 Regular |
| geometry.svg | Shapes 20 Regular |
| templates.svg | Grid Circles 24 Regular |
| ls-particle-templates.svg | Cube Multiple 20 Regular |
| fluid.svg | Fluid 20 Regular |
| particle-external-constraints.svg | Link 20 Regular |
| add.svg | Add 16 Regular |
| camera.svg | Camera 20 Regular |
| play.svg | Play 20 Regular |
| playback-first.svg | Previous 20 Regular |
| playback-last.svg | Next 20 Regular |
| playback-pause.svg | Pause 20 Regular |
| workspace-setup.svg | Box Edit 20 Regular |
| workspace-simulation.svg | Calculator 20 Regular |
| results.svg | Data Histogram 20 Regular |
| checkmark.svg | Checkmark 16 Regular |
| chevron-up.svg | Chevron Up 16 Regular |
| chevron-down.svg | Chevron Down 16 Regular |
| chevron-left.svg | Chevron Left 16 Regular |
| chevron-right.svg | Chevron Right 16 Regular |
| help.svg | Question Circle 16 Regular |

Source: https://github.com/microsoft/fluentui-system-icons

The shared control glyphs from Add through Question Circle were imported from
upstream commit `83b26348c811f072429427ff48362238656524fe`. Original files are
under `assets/<Glyph name>/SVG/ic_fluent_<glyph_name>_<size>_regular.svg`.
Most application-owned action glyphs use this family, including menu ticks,
expansion and submenu chevrons, and explicitly enabled parameter help.
Standard checkbox controls use Qt Fusion's original 16 px indicators; no
duplicate Checkbox SVG assets are bundled or used to redraw checkboxes.
The Camera action asset and the active Box Edit, Calculator, and
Data Histogram workspace assets are stock SVGs from the same commit.
The two Fit actions use Lucide `maximize-2` and `focus` instead; see the bundled
`licenses/Lucide-Icons-NOTICE.md` for their ISC notice and adaptations.
The Shape Intersect asset also preserves its raw stock SVG from that commit.
Solid Particle Instances reuse the stock Cube 20 Regular previously used for
Geometry, retaining `particle-instances.svg` and `UiIcon::ParticleInstances`.
SPH Parameters and SPH Block/Jet leaves share the stock Fluid 20 Regular.
Events, Geometry, Sphere Templates, LS Particle Templates and Particle External
Constraints also use untouched stock artwork through their existing registry
entries. Grid Circles is supplied upstream at 24 and 28 units, not 20; the
24-unit original is rendered in the same shared 20 logical-pixel tree slot.
Work Tree object glyphs draw at the common 20 logical px size; they are decorative,
not additional actions or focus targets.
Workspace artwork uses the shared 20 logical px size inside 40 px buttons,
without changing the original paths. Play is shared by simulation and Results
transport controls, not the workspace rail.
OS-owned title-bar controls retain their system appearance. FunDEM's
application logo is not a UI control glyph.

These symbols are not the FunDEM application logo. The latter retains its own
artwork and provenance described in README.md.

## Original FunDEM control artwork

The following SVGs are original FunDEM UI artwork, not Microsoft Fluent
System Icons. They are explicit user-approved exceptions to the shared
Fluent Regular icon family, retaining the shared monochrome palette and
toolbar control dimensions.

| Local asset | Artwork |
| --- | --- |
| axes.svg | Three plain coordinate-axis strokes sharing one origin, without arrowheads or labels |
| grid.svg | Four straight strokes forming a simple hash grid, without an outer frame |
| step.svg | Straight right arrow and vertical stop bar from FunDEMSoftware |
| reset.svg | Cubic reset loop and return arrow from FunDEMSoftware |
| bonds.svg | User-defined three-cell honeycomb: three hollow hexagons sharing edges, two above and one below; 24-unit view box and neutral #212121 strokes |

Axes and Grid use a 20-unit view box and a 1.5-unit stroke to match the displayed
optical weight of the adjacent action glyphs. They retain scalable SVG paths and
share the common 16 x 16 logical-pixel toolbar icon size, including normal
high-DPI scaling. Their checked states use the common toolbar-toggle
background, not alternate glyphs.

Step and Reset preserve the exact vector geometry of
the historical `FunDEMSoftware/studio/studioControls.cpp`, function `drawInterfaceSymbol`,
cases `singleStep` and `reset`. Their 24-unit view boxes keep
the original coordinates and round strokes: 2 units for Step and
1.8 units for Reset. They are scalable SVG ports, not screenshots or redrawn
Fluent assets. They use the same shared 16 x 16 logical-pixel registry icons
and shared 28 px-high solver buttons with caption-measured widths. Their selection does
not change the separate workspace-navigation icons.
The historical path records artwork provenance; it is not a current source dependency.

## Pinned domain-asset sources

The six assets below are byte-for-byte copies from upstream commit
`83b26348c811f072429427ff48362238656524fe`. Paths are relative to the official
[Microsoft Fluent System Icons repository](https://github.com/microsoft/fluentui-system-icons/tree/83b26348c811f072429427ff48362238656524fe).
Their original view boxes, paths and optical weights are unchanged; only local
filenames select their established FunDEM registry entries.

| Local asset | Upstream path | SHA-256 of original SVG bytes |
| --- | --- | --- |
| events.svg | `assets/Folder Open/SVG/ic_fluent_folder_open_20_regular.svg` | `c5f8b2db9af20a454c1f05ce6eda4b21c99f1b8677b14b6d7d07d5817140a487` |
| geometry.svg | `assets/Shapes/SVG/ic_fluent_shapes_20_regular.svg` | `2d244aa8e470790e351518ada676508e0278242880563d235e69720cee951c93` |
| templates.svg | `assets/Grid Circles/SVG/ic_fluent_grid_circles_24_regular.svg` | `57eb842ec75bd32de3825c1eb114a653de0956cd0d70129f4b71daf960676d7f` |
| ls-particle-templates.svg | `assets/Cube Multiple/SVG/ic_fluent_cube_multiple_20_regular.svg` | `1bd7e44f7b8a2ab13488c234933ae9cec153f44ff942763fff50f66febfb7849` |
| fluid.svg | `assets/Fluid/SVG/ic_fluent_fluid_20_regular.svg` | `bca56b461f57c4d34dfe13b48b26d8a8df388390fee1d32d76b7e98b1aad9b4c` |
| particle-external-constraints.svg | `assets/Link/SVG/ic_fluent_link_20_regular.svg` | `82b1fa623cc2d9c7a757b747ff706e62899ba160c575093ddfb1f38481541bc3` |

The pinned upstream [LICENSE](https://github.com/microsoft/fluentui-system-icons/blob/83b26348c811f072429427ff48362238656524fe/LICENSE)
is reproduced below. Its original file-byte SHA-256 is
`69bc45dc42b9acb96a69823adbc6ae538374e3c0bde169b855b32c48eaaef52f`.
The MIT notice covers the listed Microsoft assets, not original FunDEM control
artwork, Lucide-derived actions or the application logo.

The shared DPI-aware renderer retains the common 20 logical-pixel object slot,
87.5% ink envelope and neutral enabled/disabled colors. Render and transport
glyphs remain 16 px, Add remains 14 px in a 24 px target, workspace glyphs remain
20 px in 40 px targets, and tree rows remain 28 px. No source path is restroked
to change those presentation metrics. Existing iconless Results nodes remain
iconless.

## MIT License

Copyright (c) 2020 Microsoft Corporation

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
