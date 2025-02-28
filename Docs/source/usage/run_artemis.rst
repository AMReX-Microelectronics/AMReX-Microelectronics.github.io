.. _usage_run_artemis:

Run ARTEMIS
===========

This page describes how to set up and run a simulation using ARTEMIS (Adaptive mesh Refinement Time-domain ElectrodynaMIcs Solver). With ARTEMIS, you can model electromagnetic fields using the finite-difference time-domain (FDTD) method as well as couple them to magnetization dynamics through the Landau–Lifshitz–Gilbert (LLG) equation.

Steps to Run a New Simulation
-----------------------------

1. **Set up a Run Directory**

   On Linux/macOS, create a directory for your simulation:

   .. code-block:: bash

      mkdir -p <run_directory>

   Replace ``<run_directory>`` with the path where you want to store the run outputs, input files, and executables.

2. **Copy the ARTEMIS Executable**

   If you have already compiled ARTEMIS (see :ref:`install-artemis`), the executable (e.g., ``main3d.*.ex``) will reside in the ``Exec/`` folder under ``artemis``. Copy it to your run directory:

   .. code-block:: bash

      cp /path/to/artemis/Exec/<artemis_executable> <run_directory>/

   Here, ``<artemis_executable>`` should be replaced by the name of your compiled executable (for example, ``main3d.gnu.TPROF.MTMPI.OMP.GPUCLOCK.ex``).

3. **Prepare an Input File**

   You will need an input file that specifies the numerical and physical parameters of your simulation. ARTEMIS reads input parameters via AMReX’s `ParmParse <https://amrex-codes.github.io/amrex/docs_html/Basics.html#parmparse>`_. These parameters include:

   - **Domain geometry**: domain length, number of cells, etc.
   - **Electromagnetic solver settings**: time-step size, total time steps, output frequency, boundary conditions, source definitions (e.g. waveguide modes, plane-wave excitations).
   - **PML configuration**: absorbing boundary thickness, PML parameters.
   - **LLG options** (if using micromagnetics): magnetization field initialization, material parameters for exchange, damping, anisotropy, etc.

   Sample input files are available in ``artemis/Examples/``. For instance:

   .. code-block:: bash

      cd <run_directory>
      cp /path/to/artemis/Examples/Waveguide/inputs_3d_empty_X_band ./my_inputs_file

   You may then edit ``my_inputs_file`` to customize your simulation parameters.

4. **Run the ARTEMIS Executable**

   To launch the simulation, navigate to your run directory and execute the ARTEMIS binary with the input file. For an MPI run, for example:

   .. code-block:: bash

      cd <run_directory>
      mpirun -np <n_ranks> ./<artemis_executable> my_inputs_file

   - ``<n_ranks>``: number of MPI processes to use.
   - ``<artemis_executable>``: your ARTEMIS compiled binary (e.g. ``main3d.gnu.TPROF.MTMPI.CUDA.GPUCLOCK.ex``).

   If you are using GPU acceleration, ensure your environment is correctly set up to use CUDA (or HIP). For instance, on some HPC systems you may need:

   .. code-block:: bash

      module load cuda
      mpirun -n <n_ranks> ./<artemis_executable> my_inputs_file

   where CUDA driver/toolkit is loaded beforehand.

5. **Monitor the Simulation**

   As the simulation runs, ARTEMIS will output status updates (e.g., current time step, maximum field values, or any warnings) to your terminal (stdout). On HPC systems, you often redirect this output to a file (for example, ``outputs.txt``) by including something like:

   .. code-block:: bash

      mpirun -n <n_ranks> ./<artemis_executable> my_inputs_file > outputs.txt

   or using a job submission script if running on a batch scheduler (e.g., SLURM, LSF).

6. **Outputs and Plotfiles**

   ARTEMIS typically writes data in **AMReX plotfile** format at specified intervals set in your input file (e.g., ``plot_int`` parameter). These plotfiles will be named something like ``plt0000100``, where the number refers to the time step. You can visualize these outputs with:

   - `ParaView <https://www.paraview.org/>`_
   - `VisIt <https://wci.llnl.gov/simulation/computer-codes/visit>`_
   - `yt <https://yt-project.org/>`_ (using ``yt.load('plt0000100')``)

   For instance, to visualize using ParaView, copy or link the ``plt0000XXX`` directory to your local machine (if running on HPC), open ParaView, and choose ``Open -> (your plotfile)``. ParaView recognizes the AMReX format and allows you to slice, colorize, or animate your fields in 3D.

7. **Common Parameters in ARTEMIS Input Files**

   Although ARTEMIS shares many input parameters with other AMReX-based codes, some are specific to electromagnetic or micromagnetics simulations. Key options include:

   - **Maxwell solver options**: time step size, Courant condition, update intervals.
   - **Boundary conditions**: `domain.is_periodic` or the specification of PML or conducting walls.
   - **Micromagnetics**: If ``USE_LLG=TRUE`` was enabled, the input file should include magnetization initialization and LLG-related coefficients (Gilbert damping, exchange constant, etc.).
   - **Output frequency**: e.g., ``plot_int = 100`` to write fields every 100 steps.

   See examples in ``artemis/Examples/`` for detailed input parameters.

8. **Finalizing the Run**

   Once the simulation reaches the final time step (or meets a stopping criterion), ARTEMIS writes the last plotfile and exits. If you are on an HPC system, you can check your job queue or the output file (e.g., ``outputs.txt``) to confirm the run completed successfully. The final plotfile often stores the full fields at the last time step for post-processing.


Advanced Usage
--------------

- **MPI + OpenMP**: If you built ARTEMIS with both MPI and OpenMP (`make -j 4 USE_OMP=TRUE`), you can control the number of OpenMP threads by setting ``OMP_NUM_THREADS``. 
- **Adaptive Mesh Refinement (AMR)**: If you use a multi-level input file (e.g., specifying `amr.n_level`), ARTEMIS will refine the mesh around regions of interest. Plotfiles will then show multiple levels of data.
- **Micromagnetics**: When ``USE_LLG=TRUE``, additional array fields for magnetization components will be allocated, and the FDTD step will couple to LLG updates. 
- **Superconductivity**: Some versions of ARTEMIS allow modeling superconductors with a two-fluid model. Ensure your input file is set up properly, referencing the parameters introduced in the relevant publication or example.

Common Pitfalls
---------------

1. **MPI Rank Mismatch / Library Errors**:
   - Ensure that your environment is loading the correct MPI library before running, e.g.:
     ::
       module load openmpi
       mpirun -n 4 ./main3d...
   - Mixing different MPI implementations at compile/run time can cause errors.

2. **CUDA or HIP Errors**:
   - If you compiled with ``USE_GPU=TRUE``, verify that your CUDA or HIP drivers match the version you used during compilation. 
   - On HPC systems:
     ::
       module load cuda
       nvidia-smi
     to confirm GPU visibility.

3. **Out-of-Memory**:
   - Very fine meshes or many levels of AMR can exceed GPU or CPU memory. Try reducing resolution or domain size.
   - Check if your domain decomposition is balanced across MPI ranks.

4. **Missing Output Files**:
   - Check that the parameter ``plot_int`` in your input file is set appropriately (e.g., 100 steps) to generate plotfiles.
   - If no plotfiles are produced, you might be running fewer steps than ``plot_int``.

For further assistance, please open an issue on our GitHub or consult the AMReX documentation.


Further Documentation
---------------------

- :ref:`install-artemis` for building instructions and dependencies.
- `ARTEMIS GitHub <https://github.com/AMReX-Microelectronics/artemis>`_ for source code, issues, and updates.
- `Examples/ <https://github.com/AMReX-Microelectronics/artemis/tree/main/Examples>`_ directory for multiple sample scenarios (waveguide tests, magnetically tunable filters, superconducting resonators, etc.).

We welcome user contributions and feedback. If you encounter any problems or have feature requests, please open an issue on our `GitHub repository <https://github.com/AMReX-Microelectronics/artemis/issues>`_.
