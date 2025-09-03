.. _install-ferrox:

FerroX
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

FerroX is a massively parallel, 3D phase-field simulation framework for modeling ferroelectric materials based scalable logic devices. We self-consistently solve the time-dependent Ginzburg Landau (TDGL) equation for ferroelectric polarization, Poisson's equation for electric potential, and semiconductor charge equation for carrier densities in semiconductor regions. The algorithm is implemented using Exascale Computing Project software framework, AMReX, which provides effective scalability on manycore and GPU-based supercomputing architectures. The code can be used for simulations of ferroelectric domain-wall induced negative capacitance (NC) effect in Metal-Ferroelectric-Insulator-Metal (MFIM) and Metal-Ferroelectric-Insulator-Semiconductor-Metal (MFISM) devices.

Our community is here to help.
Please `report installation problems <https://github.com/AMReX-Microelectronics/FerroX/issues/new>`_ in case you should get stuck.

Installation
------------

Quick Start
~~~~~~~~~~~

**Prerequisites:** Git, C++ compiler, CUDA toolkit (for GPU builds)

**Build Commands:**

.. code-block:: bash

   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/FerroX.git
   cd FerroX/Exec && make -j 4

Detailed Installation Process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prerequisites and Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FerroX requires a modern C++ compiler, Git version control, and optional dependencies like CUDA toolkit for GPU acceleration and SUNDIALS for advanced time integration. The AMReX library is required and can be obtained automatically through CMake or manually cloned.

Obtaining the Source Code
^^^^^^^^^^^^^^^^^^^^^^^^^^

Download the AMReX repository at the same directory level as where you plan to install FerroX:

.. code-block:: bash
   
   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/FerroX.git

Understanding the Build System
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FerroX supports two build systems: GNU Make for quick builds and CMake for advanced configuration. GNU Make is simpler but offers fewer options, while CMake provides comprehensive dependency management and cross-platform support.

Standard Build Process
^^^^^^^^^^^^^^^^^^^^^^^

Navigate to the FerroX/Exec directory and build using your preferred method:

**GNU Make (GPU default):**

.. code-block:: bash

   cd FerroX/Exec
   make -j 4

**CMake (basic):**

.. code-block:: bash

   cd FerroX
   cmake -S . -B build
   cmake --build build -j 4

Build Verification
^^^^^^^^^^^^^^^^^^

After successful compilation, verify the installation by running the test cases provided in the Examples directory. Check that the executable runs without errors and produces expected output files.

Advanced Build Options
----------------------

Alternative Build Systems
~~~~~~~~~~~~~~~~~~~~~~~~~~

**GNU Make Options:**

.. code-block:: bash

   # CPU build
   make -j 4 USE_CUDA=FALSE
   
   # CPU build with SUNDIALS
   make -j 4 USE_CUDA=FALSE USE_SUNDIALS=TRUE

For SUNDIALS support with GNU Make, first follow the SUNDIALS installation steps as described `here <https://github.com/AMReX-Microelectronics/MagneX/blob/development/Exec/README_sundials>`_.

**CMake with External Dependencies:**

.. code-block:: bash

   # Use external AMReX installation
   cmake -S . -B build \
     -DFerroX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

Performance Optimizations
~~~~~~~~~~~~~~~~~~~~~~~~~~

**GPU Acceleration:**

.. code-block:: bash

   cmake -S . -B build -DFerroX_COMPUTE=CUDA
   cmake --build build -j 4

**CPU Optimizations:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_COMPUTE=OMP \
     -DFerroX_SIMD=ON

Physics Module Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Embedded Boundaries and Time-Dependent Features:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_EB=ON \
     -DFerroX_TIME_DEPENDENT=ON

**SUNDIALS Integration:**

For SUNDIALS library integration, first follow the SUNDIALS installation steps as described `here <https://github.com/AMReX-Microelectronics/MagneX/blob/development/Exec/README_sundials>`_.

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_SUNDIALS=ON \
     -DFerroX_sundials_src=/path/to/sundials/source

Debug and Development Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Debug Build with Verbose Output:**

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Debug \
     -DFerroX_PRINT_HIGH=ON \
     -DFerroX_PRINT_MEDIUM=ON

**Core Configuration Options:**

- ``-DFerroX_COMPUTE=NOACC/OMP/CUDA/SYCL/HIP`` - Computing backend (default: OMP)
- ``-DFerroX_PRECISION=SINGLE/DOUBLE`` - Floating point precision (default: DOUBLE)
- ``-DFerroX_EB=ON/OFF`` - Embedded boundary support (default: OFF)
- ``-DFerroX_TIME_DEPENDENT=ON/OFF`` - Time-dependent simulations (default: OFF)
- ``-DFerroX_SUNDIALS=ON/OFF`` - SUNDIALS ODE solver support (default: OFF)
- ``-DFerroX_MPI=ON/OFF`` - Multi-node support (default: ON)
- ``-DFerroX_SIMD=ON/OFF`` - CPU SIMD acceleration (default: OFF)

**Debug Print Options:**

- ``-DFerroX_PRINT_HIGH=ON/OFF`` - High level debug printing (default: OFF)
- ``-DFerroX_PRINT_MEDIUM=ON/OFF`` - Medium level debug printing (default: OFF)
- ``-DFerroX_PRINT_LOW=ON/OFF`` - Low level debug printing (default: OFF)
- ``-DFerroX_PRINT_NAME=ON/OFF`` - Function name debug printing (default: OFF)

Platform-Specific Configurations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**AMReX Source Management:**

.. code-block:: bash

   # Use local AMReX source
   cmake -S . -B build -DFerroX_amrex_src=/path/to/amrex/source
   
   # Use custom AMReX repository/branch
   cmake -S . -B build \
     -DFerroX_amrex_repo=https://github.com/user/amrex.git \
     -DFerroX_amrex_branch=my_branch
   
   # Test with specific AMReX pull request
   cmake -S . -B build -DFerroX_amrex_pr=1234

**SUNDIALS External Integration:**

.. code-block:: bash

   # Use external SUNDIALS installation
   cmake -S . -B build \
     -DFerroX_SUNDIALS=ON \
     -DFerroX_sundials_internal=OFF \
     -DSUNDIALS_DIR=/path/to/sundials/lib/cmake/sundials

**HPC System Configuration:**

For high-performance computing systems, ensure appropriate modules are loaded. For example, on Perlmutter:

.. code-block:: bash

   module load cudatoolkit


