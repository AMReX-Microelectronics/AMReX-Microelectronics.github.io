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

We begin with instructions for a basic, pure-MPI (no GPU) installation. More detailed instructions for GPU systems are below.

Download AMReX and MagneX Repositories
--------------------------------------

Make sure that AMReX and MagneX are cloned at the same root location. 

.. code-block:: bash
   
   git clone https://github.com/AMReX-Codes/amrex.git
   git clone https://github.com/AMReX-Microelectronics/MagneX.git

Dependencies
------------

Beyond a standard Ubuntu22 installation, the Ubuntu packages libfftw3-dev, libfftw3-mpi-dev, and cmake are required.
SUNDIALS is optional and enabled Runge-Kutta, implicit, and multirate integrators (more detailed instructions in the full documentation).
heFFTe is a required dependancy. At the same level that AMReX and MagneX are cloned, run: 

.. code-block:: bash
                
   git clone https://github.com/icl-utk-edu/heffte.git
   cd heffte
   mkdir build
   cd build
   cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_STANDARD=17 -DBUILD_SHARED_LIBS=OFF -DCMAKE_INSTALL_PREFIX=. -DHeffte_ENABLE_FFTW=ON -DHeffte_ENABLE_CUDA=OFF ..
   make -j4
   make install

Build
-----

MagneX supports both GNU Make and CMake build systems with various configuration options.

Option 1: Build with GNU Make
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Navigate to MagneX/Exec/ and run:

.. code-block:: bash

   make -j4

Option 2: Build with CMake
~~~~~~~~~~~~~~~~~~~~~~~~~~~

MagneX also supports building with CMake, which can automatically download and build dependencies.

**Basic CPU build (NOACC backend):**

.. code-block:: bash

   cmake -S . -B build
   cmake --build build -j 4

**OpenMP build:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_COMPUTE=OMP
   cmake --build build -j 4

**CUDA build:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_COMPUTE=CUDA
   cmake --build build -j 4

**Core CMake Configuration Options:**

- ``-DMagneX_COMPUTE=NOACC/OMP/CUDA/HIP`` - Computing backend (default: NOACC)
- ``-DMagneX_MPI=ON/OFF`` - Multi-node support (default: ON)
- ``-DMagneX_FFT=ON/OFF`` - FFT support (default: ON)
- ``-DMagneX_SUNDIALS=ON/OFF`` - SUNDIALS ODE solver support (default: OFF)

**AMReX Configuration Options:**

**Use external AMReX installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_amrex_internal=OFF \
     -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

**Use local AMReX source directory:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_src=/path/to/amrex/source

**Use custom AMReX repository/branch:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_amrex_repo=https://github.com/user/amrex.git \
     -DMagneX_amrex_branch=my_branch

**Test with specific AMReX pull request:**

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_pr=1234

**SUNDIALS Configuration Options:**

**Use external SUNDIALS installation:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_SUNDIALS=ON \
     -DMagneX_sundials_internal=OFF \
     -DSUNDIALS_DIR=/path/to/sundials/lib/cmake/sundials

**Use local SUNDIALS source directory:**

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_SUNDIALS=ON \
     -DMagneX_sundials_src=/path/to/sundials/source

**Example Build Commands:**

Build with local AMReX source (recommended for development):

.. code-block:: bash

   cmake -S . -B build -DMagneX_amrex_src=../amrex
   cmake --build build -j 4

OpenMP build with SUNDIALS support:

.. code-block:: bash

   cmake -S . -B build \
     -DMagneX_COMPUTE=OMP \
     -DMagneX_SUNDIALS=ON
   cmake --build build -j 4

CUDA build with external AMReX:

.. code-block:: bash

   export CMAKE_PREFIX_PATH=/path/to/amrex/install:$CMAKE_PREFIX_PATH
   cmake -S . -B build \
     -DMagneX_COMPUTE=CUDA \
     -DMagneX_amrex_internal=OFF
   cmake --build build -j 4
