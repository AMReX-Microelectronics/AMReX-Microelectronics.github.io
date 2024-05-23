.. _usage_run:

Run ELEQTRONeX
=========

Run
---

Run locally
^^^^^^^^^^^
Upon compilation, executable is generated in Exec/ folder in the ELEQTRONeX folder.
To run the executable with an input file from the `input/` folder in the `ELEQTRONeX` folder,
we can do the following:

Assuming we are in the `Exec/` folder, to run with a single processor, execute:

.. code-block:: bash

   ./<executable> ../input/<specific input folder>/<input file> <other arguments>

Ensure the `plot.folder_name` parameter in the input file specifies the output directory.
Alternatively, you can specify this parameter on the command line in <other arguments>, which will override what is specified in the input file.

For multiple processors, execute:

.. code-block:: bash

   mpiexec -np <np> ./<executable> ../input/<specific input folder>/<input file> <other arguments>

Run on Perlmutter
^^^^^^^^^^^^^^^^^^   
To run the code on `Perlmutter <https://docs.nersc.gov/systems/perlmutter/>`_, we can submit a jobscript as shown below for 2 nodes and 8 GPUs.

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

Inputs
------

Module enabling parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^
The following flags enable particular modules.

- ``use_electrostatic = 1`` uses the electrostatic module.
- ``use_transport = 1`` uses the transport module.
- ``transport.use_negf = 1`` sets the transport module to use the NEGF solver.
- ``use_diagnostics = 1`` can be used to set the diagnostics.
- ``amrex.the_arena_is_managed=1`` uses managed memory for CUDA. At present, the code is tested for managed memory enabled.

Data output parameters
^^^^^^^^^^^^^^^^^^^^^^

Restart parameters
^^^^^^^^^^^^^^^^^^

NEGF parameters
^^^^^^^^^^^^^^^

Electrostatics parameters
^^^^^^^^^^^^^^^^^^^^^^^^^
Diagnostics
^^^^^^^^^^^

Output
------
------
