# FunDEM computation core

FunDEM 0.4.3 uses [FunDEMBeta](https://github.com/kaiqideng/FunDEMBeta) as its
in-process numerical computation core. The desktop application maps accepted
project definitions to native materials, LS geometries, particles, contact
pairs, Bonds and solver configuration. FunDEMBeta provides geometry generation,
signed-distance grids, applicable centroid/mass integration, contact detection,
constitutive response, integration and coupled SPH–DEM dynamics.

The core is compiled into the application; users of a complete binary package
do not need its source tree or a separate FunDEM core DLL. The desktop source
remains a separate private project. The core's open-source license does not
license all desktop source or third-party components under the same terms.

## Core attribution and license

FunDEMBeta

Copyright 2026 Kaiqi Deng

This product includes software developed by Kaiqi Deng and FunDEM contributors.

FunDEMBeta is supplied under **Apache License 2.0**. An unmodified copy of its license and
NOTICE is provided in [licenses/FunDEMBeta-LICENSE.txt](licenses/FunDEMBeta-LICENSE.txt)
and [licenses/FunDEMBeta-NOTICE.txt](licenses/FunDEMBeta-NOTICE.txt).
Preserve those files with distributions. The upstream license and notices,
not this explanatory summary, govern the core.

## Backend and output boundaries

CPU is the default. A CUDA-enabled application can also select GPU and Hybrid;
Hybrid places Sphere/SPH dynamics on GPU and LS dynamics on CPU. Running the
deployed CUDA application requires a compatible NVIDIA device and driver, not
a locally installed compilation toolkit. The packaged runtime profile and
tested hardware, rather than this explanation, establish a binary's actual
device coverage.

The desktop currently records native result archives without VTU or energy DAT
output. Results plots read those saved records; they do not enable additional
core file outputs or recalculate interactions. Exact solver restart from a
rendered/recorded frame is not provided by the desktop DAT interchange workflow.

The external event-local periodic-boundary service supplements Sphere/LS seam
contacts and supplies SPH minimum-image neighbor displacements. Native contact
and SPH laws remain authoritative; combined solid pairs are evaluated once and
no ghost mass is introduced. SPH internal positions stay continuous while saved
and displayed observations wrap into the configured domain. The service supports
CPU, GPU and Hybrid with strict single-image support limits; periodic Bonds are
not supported. It is not a retired benchmark contact-force override or a promise
of arbitrary periodic shapes, walls or multiple-image interactions.
