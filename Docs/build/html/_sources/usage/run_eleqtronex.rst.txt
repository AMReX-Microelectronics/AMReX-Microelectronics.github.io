.. _usage_run:

Run ELEQTRONeX
=========

Upon compilation, executable is generated in Exec/ folder in the ELEQTRONeX folder.
To run the executable with an input file from the `input/` folder in the `ELEQTRONeX` folder,
we can do the following:

1. Run with a single processor
------------------------------
Assuming we are in the `Exec/` folder, do:

.. code-block:: bash

   ./<executable> ../input/<specific input folder>/<input file> <other arguments>

Ensure the `plot.folder_name` parameter in the input file specifies the output directory.
Alternatively, you can specify this parameter on the command line in <other arguments>, which will override what is specified in the input file.

2. Run with a multiple processors
---------------------------------
.. code-block:: bash

   mpiexec -np <mpi ranks> ./<executable> ../input/<specific input folder>/<input file> <other arguments>

2. Run on Perlmutter
--------------------
To run the code on Perlmutter, we can submit a jobscript as shown below for 2 nodes and 8 GPUs.

.. code-block:: bash

   #!/bin/bash
   #SBATCH -N 2
   #SBATCH -C gpu
   #SBATCH -G 8
   #SBATCH -J <job name>
   #SBATCH -q regular
   #SBATCH --mail-user=saurabhsawant@lbl.gov
   #SBATCH --mail-type=ALL
   #SBATCH -t 00:30:00
   #SBATCH -A m4540_g
   #SBATCH -o <job name>.o%j
   #SBATCH -e <job name>.e%j
   
   #OpenMP settings:
   export OMP_NUM_THREADS=1
   export OMP_PLACES=threads
   export OMP_PROC_BIND=spread
   
   #run the application:
   srun -n 8 -c 32 --cpu_bind=cores -G 8 --gpu-bind=single:1  <path to Exec folder>/<executable>  <path to input folder>/<path to input file>

For more information on, see ``https://docs.nersc.gov/systems/perlmutter/running-jobs/``.
NERSC also provides a script generator, ``https://my.nersc.gov/script_generator.php``.

2. Inputs
---------

Add an **input file** in the directory (see :ref:`examples <usage-examples>` and :ref:`parameters <running-cpp-parameters>`).
This file contains the numerical and physical parameters that define the situation to be simulated.

On :ref:`HPC systems <install-hpc>`, also copy and adjust a submission script that allocated computing nodes for you.
Please :ref:`reach out to us <contact>` if you need help setting up a template that runs with ideal performance.

4. Run
------

**Run** the executable, e.g. with MPI:

.. code-block:: bash

   cd <run_directory>

   # run with an inputs file:
   mpirun -np <n_ranks> ./warpx <input_file>

or

.. code-block:: bash

   # run with a PICMI input script:
   mpirun -np <n_ranks> python <python_script>

Here, ``<n_ranks>`` is the number of MPI ranks used, and ``<input_file>`` is the name of the input file (``<python_script>`` is the name of the :ref:`PICMI <usage-picmi>` script).
Note that the actual executable might have a longer name, depending on build options.

We used the copied executable in the current directory (``./``); if you installed with a package manager, skip the ``./`` because WarpX is in your ``PATH``.

On an :ref:`HPC system <install-hpc>`, you would instead submit the :ref:`job script <install-hpc>` at this point, e.g. ``sbatch <submission_script>`` (SLURM on Cori/NERSC) or ``bsub <submission_script>`` (LSF on Summit/OLCF).

.. tip::

   In the :ref:`next sections <running-cpp-parameters>`, we will explain parameters of the ``<input_file>``.
   You can overwrite all parameters inside this file also from the command line, e.g.:

   .. code-block:: bash

      mpirun -np 4 ./warpx <input_file> max_step=10 warpx.numprocs=1 2 2

5. Outputs
----------

By default, WarpX will write a status update to the terminal (``stdout``).
On :ref:`HPC systems <install-hpc>`, we usually store a copy of this in a file called ``outputs.txt``.

We also store by default an exact copy of all explicitly and implicitly used inputs parameters in a file called ``warpx_used_inputs`` (this file name can be changed).
This is important for reproducibility, since as we wrote in the previous paragraph, the options in the input file can be extended and overwritten from the command line.

:ref:`Further configured diagnostics <running-cpp-parameters-diagnostics>` are explained in the next sections.
By default, they are written to a subdirectory in ``diags/`` and can use various :ref:`output formats <dataanalysis-formats>`.
