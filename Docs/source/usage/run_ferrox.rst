.. _usage_run:

Run FerroX
=========

In order to run a new simulation:

#. Create a **new directory**, where the simulation will be run
#. Make sure the FerroX **executable** is copied into this directory 
#. Add an **inputs file** to the directory
#. Run

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
Sample input files can be found in the `Examples <https://github.com/AMReX-Microelectronics/FerroX/tree/development/Exec/Examples>`__ folder.

**Simple Testcase Example**

You can run the following to simulate a MFIM heterostructure with a 5 nm HZO as the ferroelectric layer and 4 nm alumina as the dielectric layer under zero applied voltage:

.. code-block:: bash

   mpirun -n 4 ./main3d.gnu.TPROF.MPI.CUDA.ex Exec/Examples/inputs_mfim_Noeb

On an HPC system, you would instead submit the **job script** at this point, e.g. ``sbatch <submission_script>`` (SLURM on Cori/NERSC) or ``bsub <submission_script>`` (LSF on Summit/OLCF).

5. Outputs
----------

By default, FerroX will write a status update to the terminal (``stdout``).
On HPC systems, we usually store a copy of this in a file called ``outputs.txt``.

By default, output file are written in plotfile format and you can use various :ref:`data analysis and visualization tools <dataanalysis-formats>`.

**Specific Example: Data Analysis with yt**

You can extract simulation data in numpy array format using yt:

.. code-block:: python

   import yt
   ds = yt.load('./plt00001000/')  # for data at time step 1000
   ad0 = ds.covering_grid(level=0, left_edge=ds.domain_left_edge, dims=ds.domain_dimensions)
   P_array = ad0['Pz'].to_ndarray()  # z-component of polarization

For more details on data extraction with yt, see the `covering grid documentation <https://yt-project.org/doc/examining/low_level_inspection.html#examining-grid-data-in-a-fixed-resolution-array>`_.

6. Input parameters
--------------------
.. note::

   FerroX input options are read via AMReX `ParmParse <https://amrex-codes.github.io/amrex/docs_html/Basics.html#parmparse>`__.

.. note::

   The AMReX parser is used for the right-hand-side of all input parameters that consist of one or more integers or floats, so expressions like ``<species_name>.density_max = "2.+1."`` and/or using user-defined constants are accepted.

Specific Run Examples
---------------------

The following examples show how to run FerroX with different build configurations:

**GNU Make builds (from Exec directory):**

**MPI+OMP build:**

.. code-block:: bash

   mpirun -n 4 ./main3d.gnu.TPROF.MPI.OMP.ex Exec/Examples/inputs_mfim_Noeb

**MPI+CUDA build:**

.. code-block:: bash

   mpirun -n 4 ./main3d.gnu.TPROF.MPI.CUDA.ex Exec/Examples/inputs_mfim_Noeb

**With embedded boundaries:**

.. code-block:: bash

   mpirun -n 4 ./main3d.gnu.TPROF.MPI.OMP.EB.ex Exec/Examples/inputs_mfim_eb

**CMake builds (from project root directory):**

**MPI+OMP build:**

.. code-block:: bash

   mpirun -n 4 ./build/bin/main3d.gnu.TPROF.MPI.OMP.ex Exec/Examples/inputs_mfim_Noeb

**MPI+CUDA build:**

.. code-block:: bash

   mpirun -n 4 ./build/bin/main3d.gnu.TPROF.MPI.CUDA.ex Exec/Examples/inputs_mfim_Noeb

**With embedded boundaries (if built with -DFerroX_EB=ON):**

.. code-block:: bash

   export OMP_NUM_THREADS=1
   mpirun -n 4 ./build/bin/main3d.gnu.TPROF.MTMPI.OMP.EB.ex Exec/Examples/inputs_mfim_eb

**With time-dependent simulations (if built with -DFerroX_TIME_DEPENDENT=ON):**

.. code-block:: bash

   export OMP_NUM_THREADS=1
   mpirun -n 4 ./build/bin/main3d.gnu.TPROF.MTMPI.OMP.TD.ex Exec/Examples/inputs_mfim_Noeb

Overall simulation parameters
-----------------------------

* ``domain.prob_lo`` and ``domain.prob_hi`` (`3 floats`; in meters)
    The extent of the full simulation box. This box is rectangular, and thus its
    extent is given here by the coordinates of the lower corner (``domain.prob_lo``) and
    upper corner (``domain.prob_hi``). The first axis of the coordinates is x, second is y, and the last is z.

* ``domain.n_cell`` (`3 integers`)
    The number of grid points along each direction.

* ``domain.max_grid_size`` (`3 integers`; optional)
    Maximum allowable size of each **subdomain**
    (expressed in number of grid points, in each direction).
    Each subdomain has its own ghost cells, and can be handled by a
    different MPI rank ; several OpenMP threads can work simultaneously on the
    same subdomain.

    If ``max_grid_size`` is such that the total number of subdomains is
    **larger** that the number of MPI ranks used, than some MPI ranks
    will handle several subdomains, thereby providing additional flexibility
    for **load balancing**.

    Further information on **max_grid_size and blocking_factor** can be found `here <https://amrex-codes.github.io/amrex/docs_html/GridCreation.html#sec-grid-creation>`__.

* ``nsteps`` (`integer`)
    The number of time steps to run.

* ``prob_type`` (`integer`, 1, 2 or 3)
     prob_type = 1 for 2D problems

     prob_type = 2 for 3D problems

     prob_type = 3 for convergence tests.

* ``TimeIntegratorOrder`` (`integer`, 1 or 2)
     TimeIntegratorOrder = 1 for first-order forward Euler time integrator for the TDGL equation

     TimeIntegratorOrder = 2 for second-order Predictor-Corrector time integrator for the TDGL equation

* ``use_sundials`` (`integer`, 0 or 1)
      This integer parameter enables the user to decide whether or not they 
      want to run FerroX with the Sundials library. Note that 
      this parameter takes on a default value of 0 (disable Sundials).

* ``plot_int`` (`integer`)
    This string defines the timesteps at which data is dumped.
    Use a negative number or 0 to disable data dumping.

* ``dt`` (`float`)
    Time step size.

Polarization Boundary Conditions
^^^^^^^^^^^^^^^
* ``P_BC_flag_lo`` and ``P_BC_flag_hi``(`3 integers`, 0, 1, 2, or 3)
    Polarization boundary condition at lo and hi ferroelectric material boundaries.
    The first axis of the coordinates is x, second is y, and the last is z.

    0 : P = 0

    1 : dP/dz = -P/lambda

    2 : dP/dz = 0

    3 : No BC (extend outside FE)

    3 : No BC (1st-order one-sided)

* ``lambda`` (`float`)
    Value of lambda for polarization boundary condition.

Electrical Boundary Conditions
^^^^^^^^^^^^^^^

* ``domain.is_periodic`` (`3 integers`, 0 or 1)
    Whether or not to use a periodic boundary condition along coordinate directions.
    For example, domain.is_periodic = 1 1 0 means Poisson's equation will be solved with periodic boundary conditions in x and y directions.

* ``boundary.lo`` and ``boundary.hi`` (`3 strings`, per, neu, or dir)
    per: periodic

    neu: Neumann

    dir(`float`): Dirichlet with the value inside the parenthesis 

    For example, 

    boundary.hi = per per dir(0.0)

    boundary.lo = per per dir(0.0)

    will use periodic boundary condition for Poisson's equation in x and y directions and Dirichlet boundary condition in z direction
    with :math:`\Phi = 0.0~V` at both hi_z and lo_z boundaries.

Stack Geometry
^^^^^^^^^^^^^^^
* ``FE_lo`` and ``FE_hi`` (`3 floats`)
    The high and low extent of the ferroelectric material region along x, y, and z.

* ``DE_lo`` and ``DE_hi`` (`3 floats`)
    The high and low extent of the dielectric material region along x, y, and z.

* ``SC_lo`` and ``SC_hi`` (`3 floats`)
    The high and low extent of the semiconductor material region along x, y, and z.

Material Properties (`float`)
^^^^^^^^^^^^^^^
epsilon_0 = vacuum permittivity

epsilonX_fe = relative permittivity of the ferroelectric material in x direction

epsilonZ_fe = relative permittivity of the ferroelectric material in z direction

epsilon_de = relative permittivity of the dielectric material

epsilon_si = relative permittivity of the semiconductor material

**Landau Free energy coefficients:**

alpha 

beta 

gamma 

alpha_12 

alpha_112

alpha_123

**Kinetic Coefficient in the TDGL equation**

BigGamma 

**Gradient energy coefficients**

g11 

g44 

g44_p 

g12 

**Semiconductor material is by-default Silicon**

Please refer to the `source code <https://github.com/AMReX-Microelectronics/FerroX/blob/development/Source/FerroX.cpp>`__ for full list of parameters and default values for 
semiconductor bandgap, affinity etc..





