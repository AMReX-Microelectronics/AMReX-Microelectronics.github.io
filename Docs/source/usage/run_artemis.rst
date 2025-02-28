.. _running-artemis:

Running ARTEMIS
===============

This document demonstrates a few examples of how to **run ARTEMIS** after installation and compilation. Example input scripts reside in the ``Examples/`` directory.

1. Simple Testcase without LLG
------------------------------

Below is an air-filled X-band rectangular waveguide simulation.

- **MPI + OpenMP Build**:

  .. code-block:: bash

     make -j 4 USE_LLG=FALSE
     mpirun -n 4 ./main3d.gnu.TPROF.MTMPI.OMP.GPUCLOCK.ex Examples/Waveguide/inputs_3d_empty_X_band

- **MPI + CUDA Build**:

  .. code-block:: bash

     make -j 4 USE_LLG=FALSE USE_GPU=TRUE
     mpirun -n 4 ./main3d.gnu.TPROF.MTMPI.CUDA.GPUCLOCK.ex Examples/Waveguide/inputs_3d_empty_X_band

2. Simple Testcase with LLG
---------------------------

Below is a magnetically tunable X-band filter simulation.

- **MPI + OpenMP Build**:

  .. code-block:: bash

     make -j 4 USE_LLG=TRUE
     mpirun -n 8 ./main3d.gnu.TPROF.MTMPI.OMP.GPUCLOCK.ex Examples/Waveguide/inputs_3d_LLG_filter

- **MPI + CUDA Build**:

  .. code-block:: bash

     make -j 4 USE_LLG=TRUE USE_GPU=TRUE
     mpirun -n 8 ./main3d.gnu.TPROF.MTMPI.CUDA.GPUCLOCK.ex Examples/Waveguide/inputs_3d_LLG_filter
