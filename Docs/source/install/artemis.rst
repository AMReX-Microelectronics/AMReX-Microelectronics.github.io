.. _install-artemis:

ARTEMIS (Adaptive mesh Refinement Time-domain ElectrodynaMIcs Solver)
=====================================================================

ARTEMIS is a high-performance coupled electrodynamics–micromagnetics solver for fully physical modeling of signals in microelectronic circuitry. Its primary features include:

- **Finite-Difference Time-Domain (FDTD)** approach for Maxwell’s equations.
- **Landau–Lifshitz–Gilbert (LLG)** equation modeling for micromagnetics.
- **Adaptive Mesh Refinement** implemented via the `AMReX <https://github.com/AMReX-Codes/amrex>`_ framework.
- **GPU acceleration** and **scalable parallel performance** on modern manycore architectures.

The code couples magnetization physics with electromagnetic fields in a temporally second-order accurate manner, using a trapezoidal scheme in time for the LLG equation and straightforward explicit FDTD updates for the electromagnetic fields. In practice, ARTEMIS has shown *excellent scaling* results on NERSC multicore and GPU systems, delivering up to a 59× speedup on GPU relative to a single CPU node.

Installation
------------

Quick Start
~~~~~~~~~~~

AMReX and ARTEMIS must be cloned in the same directory.

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/artemis.git
   cd artemis/
   make -j 4

Detailed Installation Process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prerequisites and Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ARTEMIS requires AMReX as its core dependency. The AMReX library provides the adaptive mesh refinement framework that enables ARTEMIS's high-performance capabilities. Both repositories must be placed alongside each other in your filesystem for the build system to locate the dependencies correctly.

Obtaining the Source Code
^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone both AMReX and ARTEMIS from their respective GitHub repositories:

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/artemis.git

Ensure the directory structure appears as:

.. code-block:: text

   parent_directory/
   ├── amrex/
   └── artemis/

Understanding the Build System
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ARTEMIS supports both GNU Make and CMake build systems. The choice depends on your preference and platform requirements:

- **GNU Make**: Simpler configuration, suitable for development and testing
- **CMake**: More flexible, better for complex builds and integration with other tools

Key build flags control physics models and performance optimizations:

- **Physics Flags**: ``USE_LLG`` (GNU Make) or ``WarpX_MAG_LLG`` (CMake) enable the Landau-Lifshitz-Gilbert equation for ferromagnetic dynamics
- **Performance Flags**: ``USE_GPU`` (GNU Make) or ``WarpX_COMPUTE`` (CMake) control hardware acceleration

Standard Build Process
^^^^^^^^^^^^^^^^^^^^^^^

For GNU Make builds, navigate to the execution directory and compile:

.. code-block:: bash

   cd artemis/
   make -j 4

For CMake builds, create a separate build directory:

.. code-block:: bash

   cd artemis
   mkdir build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   cmake --build . -j 4

Both methods produce an executable ready for simulation. By default, the Landau-Lifshitz-Gilbert equation is enabled, allowing for magnon-photon coupling simulations.

Build Verification
^^^^^^^^^^^^^^^^^^

After successful compilation, verify the build by running a test simulation. The executable will be located in the build directory or top directory depending on your build method.

For detailed instructions on setting up and running ARTEMIS simulations, see :ref:`usage_run_artemis`.

Advanced Build Options
-----------------------

Alternative Build Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~

**GNU Make with Custom Flags:**

Disable LLG physics:

.. code-block:: bash

   make -j 4 USE_LLG=FALSE

Enable GPU acceleration:

.. code-block:: bash

   make -j 4 USE_GPU=TRUE

**CMake with Explicit Control:**

Disable LLG equation:

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Release \
     -DWarpX_MAG_LLG=OFF
   cmake --build build -j 4

Performance Optimizations
~~~~~~~~~~~~~~~~~~~~~~~~~~

**MPI + OpenMP Build:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Release \
     -DWarpX_MPI=ON \
     -DWarpX_COMPUTE=OMP \
     -DWarpX_MAG_LLG=ON
   cmake --build build -j 4

**GPU Build with CUDA:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Release \
     -DWarpX_COMPUTE=CUDA \
     -DWarpX_MPI=ON \
     -DWarpX_MAG_LLG=ON \
     -DAMReX_CUDA_ARCH=8.0  # Adjust for your GPU architecture
   cmake --build build -j 4

Compile-Time Configuration Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Common CMake Options:**

- ``-DWarpX_MAG_LLG=ON/OFF`` - Enable/disable LLG equation (default: ON)
- ``-DWarpX_EB=ON/OFF`` - Enable/disable embedded boundaries
- ``-DWarpX_OPENPMD=ON/OFF`` - Enable/disable openPMD I/O
- ``-DWarpX_PRECISION=SINGLE/DOUBLE`` - Set floating point precision
- ``-DCMAKE_BUILD_TYPE=Debug/Release`` - Set build type
- ``-DWarpX_MPI=ON/OFF`` - Enable/disable MPI (default: ON)
- ``-DWarpX_COMPUTE=NOACC/OMP/CUDA/SYCL`` - Set compute backend

**Debug Build Example:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Debug \
     -DWarpX_MAG_LLG=ON
   cmake --build build -j 4

Platform-Specific Configurations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**External AMReX Installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DWarpX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

**Local AMReX Source Directory:**

.. code-block:: bash

   cmake -S . -B build -DWarpX_amrex_src=/path/to/amrex/source

*Note: For GNU Make builds, set* ``AMREX_HOME`` *in the GNUmakefile.*

**Custom AMReX Repository/Branch:**

.. code-block:: bash

   cmake -S . -B build \
     -DWarpX_amrex_repo=https://github.com/user/amrex.git \
     -DWarpX_amrex_branch=my_branch

Visualization and Data Analysis
-------------------------------

ARTEMIS uses the AMReX I/O format for storing simulation results. You can use tools such as `VisIt <https://wci.llnl.gov/simulation/computer-codes/visit>`_, `ParaView <https://www.paraview.org/>`_, or other readers compatible with AMReX plotfiles.

Additionally, `yt <https://yt-project.org/>`_ can be used in Python to load the data for advanced post-processing:

.. code-block:: python

   import yt
   ds = yt.load('./plt00001000/')  # load plotfile at time step 1000
   ad0 = ds.covering_grid(level=0, left_edge=ds.domain_left_edge, dims=ds.domain_dimensions)
   E_array = ad0['Ex'].to_ndarray()  # Retrieve Ex (x-component of E-field)

Publications
------------

- **Z. Yao, R. Jambunathan, Y. Zeng, and A. Nonaka**,
  A massively parallel time-domain coupled electrodynamics–micromagnetics solver.
  *The International Journal of High Performance Computing Applications*, 2022;36(2):167-181.
  `doi:10.1177/10943420211057906 <https://doi.org/10.1177/10943420211057906>`_

- **S. S. Sawant, Z. Yao, R. Jambunathan, and A. Nonaka**,
  Characterization of transmission lines in microelectronic circuits using the ARTEMIS solver,
  *IEEE Journal on Multiscale and Multiphysics Computational Techniques*, vol. 8, pp. 31-39, 2023,
  `doi:10.1109/JMMCT.2022.3228281 <https://doi.org/10.1109/JMMCT.2022.3228281>`_

- **R. Jambunathan, Z. Yao, R. Lombardini, A. Rodriguez, and A. Nonaka**,
  Two-fluid physical modeling of superconducting resonators in the ARTEMIS framework,
  *Computer Physics Communications*, 291, p.108836, 2023.
  `doi:10.1016/j.cpc.2023.108836 <https://doi.org/10.1016/j.cpc.2023.108836>`_
