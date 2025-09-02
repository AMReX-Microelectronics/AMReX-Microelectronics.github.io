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

1. **Clone AMReX** (dependency):

   .. code-block:: bash

      git clone git@github.com:AMReX-Codes/amrex.git

2. **Clone ARTEMIS** in the same directory as AMReX:

   .. code-block:: bash

      git clone git@github.com:AMReX-Microelectronics/artemis.git

   Make sure ``amrex/`` and ``artemis/`` are placed alongside each other in your filesystem.

3. **Build ARTEMIS**:

   ARTEMIS supports both GNU Make and CMake build systems with various configuration options.

   **Option 1: Build with GNU Make**

   Navigate to the ``Exec/`` folder inside ``artemis/`` and use one of the following commands:

   .. code-block:: bash

      cd artemis/Exec/

      # Basic build
      make -j 4

      # Build without LLG
      make -j 4 USE_LLG=FALSE

      # Build with LLG (default)
      make -j 4 USE_LLG=TRUE

      # GPU build with CUDA
      make -j 4 USE_LLG=TRUE USE_GPU=TRUE

   **Option 2: Build with CMake**

   Create a build directory and configure:

   .. code-block:: bash

      cd artemis
      mkdir build && cd build

      # Basic CPU Build
      cmake .. -DCMAKE_BUILD_TYPE=Release
      cmake --build . -j 4

   **Advanced CMake Configurations:**

   .. code-block:: bash

      # MPI + OpenMP Build
      cmake -S . -B build \
        -DCMAKE_BUILD_TYPE=Release \
        -DWarpX_MPI=ON \
        -DWarpX_COMPUTE=OMP \
        -DWarpX_MAG_LLG=ON
      cmake --build build -j 4

      # GPU Build with CUDA
      cmake -S . -B build \
        -DCMAKE_BUILD_TYPE=Release \
        -DWarpX_COMPUTE=CUDA \
        -DWarpX_MPI=ON \
        -DWarpX_MAG_LLG=ON \
        -DAMReX_CUDA_ARCH=8.0  # Adjust for your GPU architecture
      cmake --build build -j 4

      # Build without LLG
      cmake -S . -B build \
        -DCMAKE_BUILD_TYPE=Release \
        -DWarpX_MAG_LLG=OFF
      cmake --build build -j 4

   **Common CMake Configuration Options:**

   - ``-DWarpX_MAG_LLG=ON/OFF`` - Enable/disable LLG equation (default: ON)
   - ``-DWarpX_MPI=ON/OFF`` - Enable/disable MPI (default: ON)
   - ``-DWarpX_COMPUTE=NOACC/OMP/CUDA/SYCL`` - Set compute backend
   - ``-DWarpX_PRECISION=SINGLE/DOUBLE`` - Set floating point precision
   - ``-DWarpX_EB=ON/OFF`` - Enable/disable embedded boundaries
   - ``-DWarpX_OPENPMD=ON/OFF`` - Enable/disable openPMD I/O
   - ``-DCMAKE_BUILD_TYPE=Debug/Release`` - Set build type

   **AMReX Configuration Options:**

   .. code-block:: bash

      # Use external AMReX installation
      cmake -S . -B build \
        -DWarpX_amrex_internal=OFF \
        -DAMReX_DIR=/path/to/amrex/lib/cmake/AMReX

      # Use local AMReX source directory
      cmake -S . -B build -DWarpX_amrex_src=/path/to/amrex/source

      # Use custom AMReX repository/branch
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
