# Fluent System Icons attribution

The following monochrome SVG assets are original Microsoft Fluent System Icons,
Regular artwork. Their stock paths, view boxes, and native optical weights have
not been redrawn or restroked.

| Local asset | Original glyph |
| --- | --- |
| materials.svg | Layer 20 Regular |
| material-pairs.svg | Shape Intersect 20 Regular |
| particle-instances.svg | Cube 20 Regular |
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
The two Fit actions use Lucide `maximize-2` and `focus` instead; see
[LUCIDE-NOTICE.md](LUCIDE-NOTICE.md) for their ISC notice and adaptations.
The Shape Intersect asset also preserves its raw stock SVG from that commit.
Solid Particle Instances reuse the stock Cube 20 Regular previously used for
Geometry, retaining `particle-instances.svg` and `UiIcon::ParticleInstances`.
SPH retains its separate fluid-splat reference redraw.
Events, Geometry, Sphere Templates, LS Particle Templates,
SPH and Particle External Constraints use
reference-based FunDEM vector redraws recorded below, through their existing
shared registry entries. They are not Microsoft Fluent artwork.
The two template-family redraws retain `templates.svg` and
`ls-particle-templates.svg` in their existing registry entries, not the former
Grid Circles/Cube Multiple stock artwork. They draw through the shared registry
at the same 20 logical-pixel object size.
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
and 28 x 28 px toolbar controls as other Render tools. Their selection does
not change the separate workspace-navigation icons.
The historical path records artwork provenance; it is not a current source dependency.

## User-selected reference vector redraws

These six assets are FunDEM vector redraws from user-selected visual
references. Preserve each reference's composition rather than replacing it
with an unrelated abstract symbol. They are not stock Microsoft Fluent icons
or a claim of wholly unrelated original artwork.

| Local asset | Reference composition | Private reference provenance supplied by the user |
| --- | --- | --- |
| events.svg | Open folder outline with a rear tab and inset back sheet | User-uploaded VectorStock-watermarked reference; no stock identifier or source URL supplied |
| geometry.svg | Overlapping rear cylinder, left pyramid and right cube | [Flaticon Geometry reference](https://www.flaticon.com/free-icon/geometry_3873950) |
| templates.svg | Nineteen touching outline circles in centered hexagonal rows of 3/4/5/4/3 | User-uploaded Sphere Template reference; no stock identifier or source URL supplied |
| ls-particle-templates.svg | Nine rounded irregular rock outlines arranged as a wide pile | User-uploaded LS Particle Template reference; no stock identifier or source URL supplied |
| fluid.svg | Fluid-splat outline, shared by fixed SPH Parameters and SPH Block/Jet instance leaves | User-uploaded outline reference; no source URL supplied |
| particle-external-constraints.svg | Two elongated hollow rings with an outlined middle link | [Pngtree Link reference](https://pngtree.com/freepng/link-icon_4467887.html) and the uploaded screenshot |

The shared icon renderer centers each SVG's actual ink bounds with one common
87.5%-slot ink envelope and uses neutral enabled/disabled colors at the current
device pixel ratio. This presentation does not reassign the stock paths'
authorship or licenses. Reference redraw compositions remain intact; unusually
thin outlines are adjusted only for their small-size optical weight.

All existing Work Tree domain icons use `UiMetrics::treeIconSize = 20` logical
px, not asset-specific sizes; ordinary Render/transport glyphs remain 16 px,
Add remains 14 px in its 24 px target, workspace icons remain 20 px in 40 px
targets, and tree rows remain 28 px. Raw view boxes and stroke-unit values need
not match between redraws. Use shared DPI scaling and matched optical weight.
Solid instance leaves use the particle redraw. Fixed SPH Parameters and SPH
Block/Jet instance leaves share the same fluid-splat redraw through
`UiIcon::SPHSources`. Preserve the existing iconless Results nodes.

These redraws remain private source/preview assets for this change. Reference
images are not bundled and no source watermark is copied into the paths.
The links above record private provenance, not a license grant or a statement
that any reference permits redistribution of the redraw.
This notice does not establish third-party license clearance; review reference
rights separately before any future redistribution. The Microsoft MIT license
below covers only the listed stock Microsoft assets, not these redraws,
original FunDEM control artwork or the application logo.

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
