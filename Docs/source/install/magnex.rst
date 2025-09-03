.. _install-ferrox:

MagneX
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

MagneX is a massively parallel, 3D micromagnetics solver for modeling magnetic materials. MagneX solves the Landau-Lifshitz-Gilbert (LLG) equations, including exchange, anisotropy, demagnetization, and Dzyaloshinskii-Moriya interaction (DMI) coupling. The algorithm is implemented using Exascale Computing Project software framework, AMReX, which provides effective scalability on manycore and GPU-based supercomputing architectures.
   
Our community is here to help. Please report installation problems or general questions about the code in the `github Issues <https://github.com/AMReX-Microelectronics/MagneX/issues>`_ tab.

Installation
------------

Quick Start
~~~~~~~~~~~

**Prerequisites:** Ubuntu22 + libfftw3-dev, libfftw3-mpi-dev

**Build Commands:**

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/MagneX.git
   cd MagneX/Exec
   make -j4

Detailed Installation Process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prerequisites and Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

MagneX requires a modern C++ compiler, MPI implementation, and several key dependencies:

- **Base system:** Ubuntu22 (or equivalent Linux distribution)
- **Required packages:** libfftw3-dev, libfftw3-mpi-dev
- **AMReX:** Exascale computing framework (can be built internally or externally)
- **SUNDIALS:** Optional for advanced time integration methods
- **cmake:** Required only for CMake builds (optional for GNU Make builds)

Obtaining the Source Code
^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone AMReX and MagneX repositories at the same root location to enable seamless integration:

.. code-block:: bash
   
   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/MagneX.git

Understanding the Build System
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

MagneX supports two build systems:

- **GNU Make (default):** Traditional build system for standard configurations
- **CMake:** Modern build system with automatic dependency management for advanced use cases

The GNU Make system provides the simplest build process for most users, while CMake offers additional flexibility for complex configurations.

Standard Build Process
^^^^^^^^^^^^^^^^^^^^^^^

For a standard CPU-only build using GNU Make, navigate to MagneX/Exec/ and run:

.. code-block:: bash

   make -j4

For CMake builds (alternative approach):

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_src=../amrex
   cmake --build build -j 4

Build Verification
^^^^^^^^^^^^^^^^^^

After successful compilation, verify the installation by running the test suite or example problems included in the MagneX distribution.

Advanced Build Options
-----------------------

Alternative Build Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~

**CMake Build System:**

For users requiring advanced configuration options or automatic dependency management:

Performance Optimizations
~~~~~~~~~~~~~~~~~~~~~~~~~~

**OpenMP build for shared-memory parallelism:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_COMPUTE=OMP -DMagneX_amrex_src=../amrex
   cmake --build build -j 4

**CUDA build for GPU acceleration:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_COMPUTE=CUDA -DMagneX_amrex_src=../amrex
   cmake --build build -j 4

Physics Module Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Core CMake Configuration Options:**

- ``-DMagneX_COMPUTE=NOACC/OMP/CUDA/HIP`` - Computing backend (default: NOACC)
- ``-DMagneX_MPI=ON/OFF`` - Multi-node support (default: ON)
- ``-DMagneX_FFT=ON/OFF`` - FFT support (default: ON)
- ``-DMagneX_SUNDIALS=ON/OFF`` - SUNDIALS ODE solver support (default: OFF)

**SUNDIALS Integration for Advanced Time Integration:**

External SUNDIALS installation:

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_SUNDIALS=ON \
     -DMagneX_sundials_internal=OFF \
     -DSUNDIALS_DIR=/path/to/sundials/lib/cmake/sundials

Local SUNDIALS source:

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_SUNDIALS=ON \
     -DMagneX_sundials_src=/path/to/sundials/source

Debug and Development Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Development build with local AMReX source:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_src=../amrex
   cmake --build build -j 4

**Testing with specific AMReX pull request:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_pr=1234

Platform-Specific Configurations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**External AMReX installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

**Custom AMReX repository/branch:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_amrex_repo=https://github.com/user/amrex.git \
     -DMagneX_amrex_branch=my_branch

**Multi-GPU CUDA build with external AMReX:**

.. code-block:: bash

   export CMAKE_PREFIX_PATH=/path/to/amrex/install:$CMAKE_PREFIX_PATH
   cmake -S . -B build \
     -DMagneX_COMPUTE=CUDA \
     -DMagneX_amrex_internal=OFF
   cmake --build build -j 4
