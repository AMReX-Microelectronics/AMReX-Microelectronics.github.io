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
   #SBATCH --mail-user=<user email>@lbl.gov
   #SBATCH --mail-type=ALL
   #SBATCH -t 00:30:00
   #SBATCH -A <project code>
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

- ``use_electrostatic = 1`` Electrostatics module enabled.
- ``use_transport = 1`` Transport module enabled.
- ``transport.use_negf = 1`` NEGF solver enabled.
- ``use_diagnostics = 1`` Diagnostics are enabled.
- ``amrex.the_arena_is_managed=1`` CUDA managed memory enabled.

Data output parameters
^^^^^^^^^^^^^^^^^^^^^^
- ``plot.folder_name = <output_folder>``  Replace ``<output_folder>`` with the foldername, starting in the directory where the code is being executed.
- ``plot.fields_to_plot = <MF_name>.<ID> <MF_name> vecField`` vector of ``multiFabs`` to be outputted. Replace <MF_name> with the name of multifab defined in ``macroscopic.fields_to_define``. If ``<ID>`` is not specified, then data is outputted only in the output folder. ``vecField`` outputs the gradient of electrostatic potential in all three directions.
  Additionally, there is an option to create raw_fields folder as ``<output_folder>/raw_fields``, where raw data may be outputted. Replace ``<ID>`` with 1 to output the multifab in the output folder as well as separately in the ``raw_fields`` folder along with the ghost cells. This may be useful while debugging. Replace ``<ID>`` with 2 to output data only in the ``raw_fields`` folder. 
- ``plot.write_after_init = 1``  Data can be written out after initialization and before the first iteration. This may be useful for debugging before running the simulation.
- ``plot.write_interval = <interval>``  Replace ``<interval>`` with a number specifying the interval of outputting the data. 
- ``plot.rawfield_write_interval = <interval>``  This interval can be different from ``plot.write_interval``.

Boundary Conditions
^^^^^^^^^^^^^^^^^^^
- ``domain.is_periodic = 0 1 0`` sets whether domain is periodic in the X, Y, Z directions, respectively. In this example, `Y` direction is periodic.
- ``boundary.hi = <X_boundary_type>(<dirichlet_value_or_string_name>) <Y_boundary_type>(<string_name>) <Z_boundary_type>(<neumann_value_or_string_name>)`` This parameter sets the maximum domain boundaries in the `X`, `Y`, and `Z` directions. Replace ``<*_boundary_type>`` by ``dir`` (Dirichlet), ``rob`` (Robin), ``neu`` (Neumann), or ``per`` (Periodic). 
  We can specify floating point values on Dirichlet and Neumann boundaries for electrostatic potential in paranthesis. If no value is specified then ``0.0`` is assumed. 
  We may choose to specify a string parameter in the paranthesis such as ``dir(Zmax)`` to specify a time and/or spatially varying function. In this case, we need to specify another parameter,  ``boundary.<string_name>_function``, for parsing the function, such as ``boundary.Zmax_function = "10*cos(2*pi*x/(2*Lx))"``. See Parser in the AMReX documentation.
  For Robin boundaries, we need to set a string parameter, for example ``rob(Ymax)`` and set three more Robin boundary specific parameters as ``boundary.<string_name>_a_function``, ``boundary.<string_name>_b_function``, ``boundary.<string_name>_f_function``. If ``domain.is_periodic`` is specified to be periodic, then it overrides the ``boundary.hi`` and ``boundary.lo`` parameters. 
- ``boundary.lo = neu(-0.5) neu dir(5)`` Similarly, set the minimum domain boundaries in the `X`, `Y`, and `Z` directions. In this example, minimum `X`, `Y` boundaries are Neumann with values of -0.5 and 0., while minimum `Z` boundary is Dirichlet with a potential value of 5~V.

Restart parameters
^^^^^^^^^^^^^^^^^^
- ``restart = 1`` set while restarting the code.
- ``restart_step = <step_number>`` Replace ``<step_number>`` with step to be restarted.

NEGF parameters
^^^^^^^^^^^^^^^
- ``transport.NS_names = <NS1> <NS2>`` Optional parameter to define vector of string names for nanostructures. Folders with these names are created in the ``<plot.folder_name>/negf/`` folder.
- ``transport.NS_num = <number>`` If ``transport.NS_names`` is not specified then we need to set this parameter defining the number of nanostructures.
- ``transport.NS_type_default = CNT`` Default type of nanostructures. Here it is defined as CNT, referring to carbon nanotube.
- ``transport.NS_initial_deposit_value = <value>`` This is the initial charge deposited on the surface of the material while starting the simulation.
- ``transport.gate_terminal_type = <Type>`` ``<Type>`` can be EB, representing gate terminal defined as an embedded boundary, or ``Boundary``, representing gate terminal defined on the domain boundary.

Electrostatics parameters
^^^^^^^^^^^^^^^^^^^^^^^^^
See AMReX documentation for MLMG parameters such as ``mlmg.set_verbose``, ``mlmg.max_order``, ``mlmg.absolute_tolerence``, and ``mlmg.relative_tolerance``.

For debugging, we typically need:

- ``mlmg.set_verbose = <integer number>`` which sets the verbosity level for output of the MLMG multigrid solver. Useful for debugging. Usually set to 0.

Issue with all_around_metal when running on Hypre
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The all_around_metal input file is a good starting place when using ELEQTRONeX. However, the input file of this case is not configured to work on an HPC using Hypre. This is due to an issue with the embedded boundaries of the system. To correct this, navigate to the ''input/negf/all_around_metal'' input file, and locate the ``Gate.height`` input value. Change this from ``Ly + 2*dy/5`` to ``Ly - 16*dy - dy/5``.
