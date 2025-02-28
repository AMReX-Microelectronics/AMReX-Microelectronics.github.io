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

   Make sure `amrex/` and `artemis/` are placed alongside each other in your filesystem.

3. **Build ARTEMIS**:

   1. Navigate to the `Exec/` folder inside `artemis/`.
   2. Build with `make -j 4`, for example:

   .. code-block:: bash

      cd artemis/Exec/
      make -j 4

   By default, *LLG* is enabled (`USE_LLG = TRUE`). You can explicitly switch it on/off:

   - **Without LLG**:

     .. code-block:: bash

        make -j 4 USE_LLG=FALSE

   - **With LLG**:

     .. code-block:: bash

        make -j 4 USE_LLG=TRUE

   To enable GPU acceleration (CUDA/HIP, etc.) set `USE_GPU=TRUE` in the make command. Check the `GNUmakefile` or other build scripts for additional optional flags like MPI, OpenMP, etc.

Running ARTEMIS
---------------

**Example Input Scripts** reside in the `Examples/` directory. Below are a couple of quick start demonstrations.

.. _artemis-no-llg:

1. **Simple Testcase without LLG**

   For an air-filled X-band rectangular waveguide simulation:

   - *MPI + OpenMP Build*:

     .. code-block:: bash

        make -j 4 USE_LLG=FALSE
        mpirun -n 4 ./main3d.gnu.TPROF.MTMPI.OMP.GPUCLOCK.ex Examples/Waveguide/inputs_3d_empty_X_band

   - *MPI + CUDA Build*:

     .. code-block:: bash

        make -j 4 USE_LLG=FALSE USE_GPU=TRUE
        mpirun -n 4 ./main3d.gnu.TPROF.MTMPI.CUDA.GPUCLOCK.ex Examples/Waveguide/inputs_3d_empty_X_band

2. **Simple Testcase with LLG**

   For an X-band magnetically tunable filter simulation:

   - *MPI + OpenMP Build*:

     .. code-block:: bash

        make -j 4 USE_LLG=TRUE
        mpirun -n 8 ./main3d.gnu.TPROF.MTMPI.OMP.GPUCLOCK.ex Examples/Waveguide/inputs_3d_LLG_filter

   - *MPI + CUDA Build*:

     .. code-block:: bash

        make -j 4 USE_LLG=TRUE USE_GPU=TRUE
        mpirun -n 8 ./main3d.gnu.TPROF.MTMPI.CUDA.GPUCLOCK.ex Examples/Waveguide/inputs_3d_LLG_filter

Visualization and Data Analysis
-------------------------------

ARTEMIS uses the AMReX I/O format for storing simulation results. You can use tools such as `VisIt`, `ParaView`, or other readers compatible with AMReX plotfiles. 

Additionally, `yt <https://yt-project.org/>`_ can be used in Python to load the data for advanced post-processing:

.. code-block:: python

   import yt
   ds = yt.load('./plt00001000/')  # load plotfile at time step 1000
   ad0 = ds.covering_grid(level=0, left_edge=ds.domain_left_edge, dims=ds.domain_dimensions)
   E_array = ad0['Ex'].to_ndarray()  # Retrieve Ex (x-component of E-field)

Publications
------------

- **Z. Yao, R. Jambunathan, Y. Zeng and A. Nonaka**,
  A massively parallel time-domain coupled electrodynamics–micromagnetics solver.
  *The International Journal of High Performance Computing Applications*, 2022;36(2):167-181.
  `doi:10.1177/10943420211057906 <https://doi.org/10.1177/10943420211057906>`_

- **S. S. Sawant, Z. Yao, R. Jambunathan and A. Nonaka**,
  Characterization of transmission lines in microelectronic circuits using the ARTEMIS solver,
  *IEEE Journal on Multiscale and Multiphysics Computational Techniques*, vol. 8, pp. 31-39, 2023,
  `doi: 10.1109/JMMCT.2022.3228281 <https://doi.org/10.1109/JMMCT.2022.3228281>`_

- **R. Jambunathan, Z. Yao, R. Lombardini, A. Rodriguez, and A. Nonaka**,
  Two-fluid physical modeling of superconducting resonators in the ARTEMIS framework,
  *Computer Physics Communications*, 291, p.108836, 2023.
  `doi:10.1016/j.cpc.2023.108836 <https://doi.org/10.1016/j.cpc.2023.108836>`_
