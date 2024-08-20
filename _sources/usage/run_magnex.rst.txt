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
