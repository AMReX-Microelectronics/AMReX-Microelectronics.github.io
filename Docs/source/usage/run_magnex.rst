.. _usage_run:

Run MagneX
=========

In order to run a new simulation:

#. Create a **new directory**, where the simulation will be run
#. Make sure the MagneX **executable** is copied into this directory
#. Add an **inputs file** to the directory
#. Run

To run the executable, e.g. with MPI:

.. code-block:: bash

   cd <run_directory>

   # run with an inputs file:
   mpiexec -n <num_mpi_ranks> ./<MagneX_executable> <input_file>

**Simple Testcase Example**

You can run the following to simulate muMAG Standard Problem 4 dynamics:

.. code-block:: bash

   ./main3d.gnu.MPI.ex standard_problem_inputs/inputs_std4

Inputs
------

An **input file** contains the numerical and physical parameters that define the situation to be simulated.

Many sample inputs files can be found in ``MagneX/Exec``, ``MagneX/Exec/regression_inputs``, and ``MagneX/Exec/standard_problem_inputs``.

A list of parameters, with descriptions, can be found in ``MagneX/Source/MagneX.cpp``.
Also, MagneX uses a python-style parser for material properties and external bias field; see ``MagneX/Source/Initialization.cpp``.

Outputs
-------

By default, MagneX will write a status update to the terminal (``stdout``).
Based on ``plot_int`` the code will also write out plotfiles.
Refer to the `following link <https://amrex-codes.github.io/amrex/docs_html/Visualization_Chapter.html>`_ for several visualization tools that can be used for AMReX plotfiles.

**Specific Example: Data Analysis with yt**

You can extract simulation data in numpy array format using yt:

.. code-block:: python

   import yt
   ds = yt.load('./plt00010000/')  # for data at time step 10000
   ad0 = ds.covering_grid(level=0, left_edge=ds.domain_left_edge, dims=ds.domain_dimensions)
   Mx_array = ad0['Mx'].to_ndarray()  # x-component of magnetization

For more details on data extraction with yt, see the `covering grid documentation <https://yt-project.org/doc/examining/low_level_inspection.html#examining-grid-data-in-a-fixed-resolution-array>`_.

Specific Run Examples
---------------------

The following examples show how to run MagneX with different build configurations:

**GNU Make builds (from Exec directory):**

.. code-block:: bash

   ./main3d.gnu.MPI.ex standard_problem_inputs/inputs_std4

**CMake builds (from project root directory):**

**CPU build (NOACC backend):**

.. code-block:: bash

   ./build/main3d.gnu.MPI.ex Exec/standard_problem_inputs/inputs_std4

**OpenMP build:**

.. code-block:: bash

   ./build/main3d.gnu.MPI.OMP.ex Exec/standard_problem_inputs/inputs_std4

**CUDA build:**

.. code-block:: bash

   ./build/main3d.gnu.MPI.CUDA.ex Exec/standard_problem_inputs/inputs_std4

**Using the convenience symlink:**

.. code-block:: bash

   ./build/magnex Exec/standard_problem_inputs/inputs_std4 
