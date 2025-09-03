.. _install-ferrox:

ELEQTRONeX
=====

.. raw:: html

   <style>
   .rst-content section>img {
       width: 30px;
       margin-bottom: 0;
       margin-top: 0;
       margin-right: 15px;
       margin-left: 15px;
       float: left;
   }
   </style>

Our community is here to help.
Please `report installation problems <https://github.com/AMReX-Microelectronics/ELEQTRONeX/issues/new>`_ in case you are stuck.

Installation
------------

Quick Start
~~~~~~~~~~~

AMReX and ELEQTRONeX must be cloned in the same directory. Configure preprocessor flags in ``Source/Code_Definitions.H`` before building.

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/ELEQTRONeX.git
   cd ELEQTRONeX/Exec/
   make -j4

Detailed Installation Process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prerequisites and Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ELEQTRONeX requires AMReX as its core dependency for adaptive mesh refinement capabilities. The software also requires appropriate preprocessor flag configuration in ``Source/Code_Definitions.H`` before compilation:

- ``#define NUM_MODES 1`` - Sets matrix block size for NEGF (1 for single mode, higher for multi-mode systems)
- ``#define NUM_CONTACTS 2`` - Sets number of metal leads (currently verified for 2 contacts: source and drain)

For GPU builds, CUDA support is needed, and for parallel execution, MPI libraries (MPICH or OpenMPI) are required.

Obtaining the Source Code
^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone both AMReX and ELEQTRONeX from their respective GitHub repositories:

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/ELEQTRONeX.git

Ensure both repositories are placed at the same directory level for the build system to locate dependencies correctly.

Understanding the Build System
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ELEQTRONeX supports both GNU Make and CMake build systems:

- **GNU Make**: Uses GNUmakefile in the Exec/ directory with various USE_* flags
- **CMake**: Provides automatic dependency management and modern build configuration

Key configuration areas include:
- **Compute Backend**: CPU (NOACC), OpenMP (OMP), CUDA, or HIP
- **Physics Modules**: Embedded boundaries, transport solver (NEGF), time-dependent simulations
- **Performance Options**: MPI support, HYPRE integration, Broyden parallelization

Standard Build Process
^^^^^^^^^^^^^^^^^^^^^^^

For GNU Make builds:

.. code-block:: bash

   cd ELEQTRONeX/Exec/
   make -j4

For CMake builds:

.. code-block:: bash

   cd ELEQTRONeX
   cmake -S . -B build
   cmake --build build -j 4

Build Verification
^^^^^^^^^^^^^^^^^^

After successful compilation, verify the build by checking that the executable was created and can display help information. Also ensure any required preprocessor flags in ``Source/Code_Definitions.H`` are properly configured for your simulation needs.

Advanced Build Options
-----------------------

Alternative Build Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~

**GNU Make with Custom Flags:**

To build with MPI and CUDA, ensure that either MPICH or OpenMPI, along with the appropriate CUDA modules, are installed and loaded. Configure in the GNUmakefile located in the Exec/ directory by setting the appropriate USE_* flags as described in the Compile-Time Configuration Options section.

**CMake with Specific Backends:**

OpenMP build:

.. code-block:: bash

   cmake -S . -B build -DELEQTRONeX_COMPUTE=OMP
   cmake --build build -j 4

CUDA build:

.. code-block:: bash

   cmake -S . -B build -DELEQTRONeX_COMPUTE=CUDA
   cmake --build build -j 4

Performance Optimizations
~~~~~~~~~~~~~~~~~~~~~~~~~~

**CPU build with embedded boundaries and transport:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_COMPUTE=OMP \
     -DELEQTRONeX_EB=ON \
     -DELEQTRONeX_TRANSPORT=ON

**GPU build with HYPRE support:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_COMPUTE=CUDA \
     -DELEQTRONeX_HYPRE=ON

**Debug build with printing enabled:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Debug \
     -DELEQTRONeX_PRINT_HIGH=ON

Compile-Time Configuration Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**GNU Make Configuration Flags:**

Configure in GNUmakefile with the following options and their defaults:

- ``AMREX_HOME ?= ../../amrex`` - Specifies the location of the AMReX library
- ``DEBUG=FALSE`` - Sets debug mode
- ``USE_HYPRE=FALSE`` - Enable hypre for multigrid bottom solver
- ``COMP=gnu`` - Sets GNU compiler
- ``DIM=3`` - Builds code for 3D domain
- ``CXXSTD=c++17`` - Sets C++17 for compilation
- ``TINY_PROFILE=FALSE`` - Enable AMReX profiler
- ``USE_MPI=TRUE`` - Enable MPI support
- ``USE_OMP=FALSE`` - Disable OpenMP
- ``USE_CUDA=TRUE`` - Activate CUDA support for GPU utilization
- ``USE_EB=TRUE`` - Enable embedded boundaries
- ``USE_TRANSPORT=TRUE`` - Enable transport solver (NEGF method)
- ``COMPUTE_GREENS_FUNCTION_OFFDIAG_ELEMS=FALSE`` - Switch off computations and storage of off-diagonal elements of Green's functions in NEGF solver
- ``COMPUTE_SPECTRAL_FUNCTION_OFFDIAG_ELEMS=FALSE`` - Switch off computations and storage of off-diagonal elements of spectral functions in NEGF solver
- ``BROYDEN_PARALLEL=TRUE`` - Use efficient parallel version of Broyden's algorithm for self-consistency between electrostatics and NEGF modules
- ``TIME_DEPENDENT=TRUE`` - Build code for accepting voltages on embedded boundaries with varying values

**Common CMake Options:**

- ``-DELEQTRONeX_COMPUTE=NOACC/OMP/CUDA/HIP`` - Computing backend (default: NOACC)
- ``-DELEQTRONeX_MPI=ON/OFF`` - Multi-node support (default: ON)
- ``-DELEQTRONeX_EB=ON/OFF`` - Embedded boundary support (default: ON)
- ``-DELEQTRONeX_TRANSPORT=ON/OFF`` - Transport support (default: ON)
- ``-DELEQTRONeX_TIME_DEPENDENT=ON/OFF`` - Time-dependent support (default: ON)
- ``-DELEQTRONeX_BROYDEN_PARALLEL=ON/OFF`` - Broyden parallel support (default: ON)
- ``-DELEQTRONeX_HYPRE=ON/OFF`` - HYPRE support (default: OFF)

**Print Debug Options:**

- ``-DELEQTRONeX_PRINT_HIGH=ON/OFF`` - High level debug printing (default: OFF)
- ``-DELEQTRONeX_PRINT_MEDIUM=ON/OFF`` - Medium level debug printing (default: OFF)
- ``-DELEQTRONeX_PRINT_LOW=ON/OFF`` - Low level debug printing (default: OFF)
- ``-DELEQTRONeX_PRINT_NAME=ON/OFF`` - Function name debug printing (default: OFF)

**Preprocessor Configuration:**

Set flags in ``Source/Code_Definitions.H`` before compilation:

- ``#define NUM_MODES 1`` - Sets matrix block size for NEGF (1 for single mode, higher for multi-mode systems)
- ``#define NUM_CONTACTS 2`` - Sets number of metal leads (currently verified for 2 contacts: source and drain)

Advanced Build Examples
~~~~~~~~~~~~~~~~~~~~~~~

**CPU build with embedded boundaries and transport:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_COMPUTE=OMP \
     -DELEQTRONeX_EB=ON \
     -DELEQTRONeX_TRANSPORT=ON

**GPU build with HYPRE support:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_COMPUTE=CUDA \
     -DELEQTRONeX_HYPRE=ON

**Debug build with all print options enabled:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Debug \
     -DELEQTRONeX_PRINT_HIGH=ON

**Build with local AMReX source (recommended for development):**

.. code-block:: bash

   cmake -S . -B build -DELEQTRONeX_amrex_src=../amrex
   cmake --build build -j 4

**Build with external AMReX using CMAKE_PREFIX_PATH:**

.. code-block:: bash

   export CMAKE_PREFIX_PATH=/path/to/amrex/install:$CMAKE_PREFIX_PATH
   cmake -S . -B build -DELEQTRONeX_amrex_internal=OFF

Platform-Specific Configurations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**External AMReX Installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

**Local AMReX Source Directory:**

.. code-block:: bash

   cmake -S . -B build -DELEQTRONeX_amrex_src=/path/to/amrex/source

**Custom AMReX Repository/Branch:**

.. code-block:: bash

   cmake -S . -B build \
     -DELEQTRONeX_amrex_repo=https://github.com/user/amrex.git \
     -DELEQTRONeX_amrex_branch=my_branch

**Test with Specific AMReX Pull Request:**

.. code-block:: bash

   cmake -S . -B build -DELEQTRONeX_amrex_pr=1234

