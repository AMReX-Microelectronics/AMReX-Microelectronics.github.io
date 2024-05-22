.. _usage_run:

Run FerroX
=========

In order to run a new simulation:

#. create a **new directory**, where the simulation will be run
#. make sure the FerroX **executable** is copied into this directory 
#. add an **inputs file** to the directory
#. run

1. Run Directory
----------------

On Linux/macOS, this is as easy as this

.. code-block:: bash

   mkdir -p <run_directory>

Where ``<run_directory>`` by the actual path to the run directory.

2. Executable
-------------

If you have already compiled the code, the FerroX executable is stored in the Exec folder.
Copy the **executable** to this directory:

.. code-block:: bash

   cp FerroX/Exec/<ferrox_executable> <run_directory>/

where ``<ferrox_executable>`` should be replaced by the actual name of the executable and ``<run_directory>`` by the actual path to the run directory.

3. Inputs
---------

Add an **input file** in the directory.
This file contains the numerical and physical parameters that define the situation to be simulated.

On HPC systems, also copy and adjust a submission script that allocated computing nodes for you.

4. Run
------

**Run** the executable, e.g. with MPI:

.. code-block:: bash

   cd <run_directory>

   # run with an inputs file:
   mpirun -np <n_ranks> ./<ferrox_executable> <input_file>

Here, ``<n_ranks>`` is the number of MPI ranks used, and ``<input_file>`` is the name of the input file.
Note that the actual executable might have a longer name, depending on build options.


On an HPC system, you would instead submit the **job script** at this point, e.g. ``sbatch <submission_script>`` (SLURM on Cori/NERSC) or ``bsub <submission_script>`` (LSF on Summit/OLCF).

5. Outputs
----------

By default, FerroX will write a status update to the terminal (``stdout``).
On HPC systems, we usually store a copy of this in a file called ``outputs.txt``.

By default, output file are written in plotfile format and you can use various :ref:`data analysis and visualization tools <dataanalysis-formats>`.

5. Input parameters
--------------------

