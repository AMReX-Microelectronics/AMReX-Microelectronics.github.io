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

First, download the AMReX repository:

.. code-block:: bash
   
   git clone https://github.com/AMReX-Codes/amrex.git

At the same directory level as AMReX, download the FerroX Repository:

.. code-block:: bash

   git clone https://github.com/AMReX-Microelectronics/FerroX.git 

Build
-----

FerroX supports both GNU Make and CMake build systems with various configuration options.

Option 1: Build with GNU Make
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Make sure that the AMReX and FerroX are cloned in the same location in your filesystem. Navigate to the Exec folder within the FerroX directory and execute the following commands:

**GPU build (default):**

.. code-block:: bash

   make -j 4

**CPU build:**

.. code-block:: bash

   make -j 4 USE_CUDA=FALSE

**CPU build with SUNDIALS support:**

.. code-block:: bash

   make -j 4 USE_CUDA=FALSE USE_SUNDIALS=TRUE

Option 2: Build with CMake
~~~~~~~~~~~~~~~~~~~~~~~~~~~

FerroX also supports building with CMake, which automatically downloads and builds dependencies.

**Basic CPU build:**

.. code-block:: bash

   cmake -S . -B build
   cmake --build build -j 4

**GPU build with CUDA:**

.. code-block:: bash

   cmake -S . -B build -DFerroX_COMPUTE=CUDA
   cmake --build build -j 4

**Advanced CMake Configurations:**

CPU build with embedded boundaries and time-dependent support:

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_COMPUTE=OMP \
     -DFerroX_EB=ON \
     -DFerroX_TIME_DEPENDENT=ON

GPU build with SUNDIALS support:

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_COMPUTE=CUDA \
     -DFerroX_SUNDIALS=ON

Debug build with print options enabled:

.. code-block:: bash

   cmake -S . -B build \
     -DCMAKE_BUILD_TYPE=Debug \
     -DFerroX_PRINT_HIGH=ON

**Core CMake Configuration Options:**

- ``-DFerroX_COMPUTE=NOACC/OMP/CUDA/SYCL/HIP`` - Computing backend (default: OMP)
- ``-DFerroX_PRECISION=SINGLE/DOUBLE`` - Floating point precision (default: DOUBLE)
- ``-DFerroX_EB=ON/OFF`` - Embedded boundary support (default: OFF)
- ``-DFerroX_TIME_DEPENDENT=ON/OFF`` - Time-dependent simulations (default: OFF)
- ``-DFerroX_SUNDIALS=ON/OFF`` - SUNDIALS ODE solver support (default: OFF)
- ``-DFerroX_MPI=ON/OFF`` - Multi-node support (default: ON)
- ``-DFerroX_SIMD=ON/OFF`` - CPU SIMD acceleration (default: OFF)

**Print Debug Options:**

- ``-DFerroX_PRINT_HIGH=ON/OFF`` - High level debug printing (default: OFF)
- ``-DFerroX_PRINT_MEDIUM=ON/OFF`` - Medium level debug printing (default: OFF)
- ``-DFerroX_PRINT_LOW=ON/OFF`` - Low level debug printing (default: OFF)
- ``-DFerroX_PRINT_NAME=ON/OFF`` - Function name debug printing (default: OFF)

**AMReX Configuration Options:**

**Use external AMReX installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

**Use local AMReX source directory:**

.. code-block:: bash

   cmake -S . -B build -DFerroX_amrex_src=/path/to/amrex/source

**Use custom AMReX repository/branch:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_amrex_repo=https://github.com/user/amrex.git \
     -DFerroX_amrex_branch=my_branch

**Test with specific AMReX pull request:**

.. code-block:: bash

   cmake -S . -B build -DFerroX_amrex_pr=1234

**SUNDIALS Configuration Options:**

**Use external SUNDIALS installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_SUNDIALS=ON \
     -DFerroX_sundials_internal=OFF \
     -DSUNDIALS_DIR=/path/to/sundials/lib/cmake/sundials

**Use local SUNDIALS source directory:**

.. code-block:: bash

   cmake -S . -B build \
     -DFerroX_SUNDIALS=ON \
     -DFerroX_sundials_src=/path/to/sundials/source

**HPC System Notes:**

If you want to use FerroX on a specific high-performance computing (HPC) system, follow the same steps as above. For MPI+CUDA build, make sure that appropriate CUDA modules are loaded. For instance, on Perlmutter you will need to do:

.. code-block:: bash

   module load cudatoolkit

Incorporating SUNDIALS
----------------------

If you want to incorporate the SUNDIALS library into your FerroX code, first follow the SUNDIALS installation steps as described `here <https://github.com/AMReX-Microelectronics/MagneX/blob/development/Exec/README_sundials>`_. To build the code with SUNDIALS support enabled, you can include the USE_SUNDIALS=TRUE option with the appropriate build command (refer to the example under the 'Build' heading).


