.. _install-ferrox:

ELEQTRONeX
=====

.. raw:: html

   <style>
   .rst-content section>img {
       width: 30px;
       margin-bottom: 0;
       margin-top: 0;
       margin-right: 15px;
       margin-left: 15px;
       float: left;
   }
   </style>

Our community is here to help.
Please `report installation problems <https://github.com/AMReX-Microelectronics/ELEQTRONeX/issues/new>`_ in case you are stuck.

Follow these instructions for compilation.

1. Download 
-----------

To download the codes in your local linux system or on a specific high-performance computing (HPC) system:

Download AMReX Repository as

.. code-block:: bash
   
   git clone git@github.com:AMReX-Codes/amrex.git

From the ''amrex/'' directory, make the repository:

.. code-block:: bash

   /.configure
   make
   make install

More information on AMReX installation can be found 'here <https://amrex-codes.github.io/amrex/docs_html/BuildingAMReX_Chapter.html>'_.

To run on an HPC system, the Hypre Library is also necessary. Download Hypre, in the same directory level as AMReX, using:

.. code-block:: bash
   
   git clone https://github.com/hypre-space/hypre.git

From the''hypre/src/'' directory, make the library in the same way as AMReX. The following environment variable also needs to be added, pointing to the location of the library:

.. code-block:: bash
   
   export HYPRE_DIR="<path to hypre>/src/hypre"

Full instructions on Hypre installation are in the AMReX documentation, 'here <https://amrex-codes.github.io/amrex/tutorials_html/Hypre_Install.html>'_.

If working locally, not on an HPC, the OpenMPI will also have to be added. This is done with the following sudo command:

.. code-block:: bash
   
   sudo apt-get install openmpi-bin openmpi-doc libopenmpi-dev

If this does not work, alternative methods can be found 'here <https://webpages.charlotte.edu/abw/coit-grid01.uncc.edu/ParallelProgSoftware/Software/OpenMPIInstall.pdf>'_.

Download ELEQTRONeX Repository in the folder hierarchy level as AMReX as

.. code-block:: bash

   git@github.com:AMReX-Microelectronics/ELEQTRONeX.git   

2. Build Parameters
-------------------

Navigate to the ``Exec/`` folder of ELEQTRONeX and execute 

.. code-block:: bash

   make -j <np>
Replace <np> with the number of processes you want to use. 

To build with MPI and CUDA, ensure that either MPICH or OpenMPI, along with the appropriate CUDA modules, are installed and loaded. In the ``GNUmakefile`` located in the ``Exec/`` directory, set ``USE_MPI=TRUE`` to enable MPI support, ``USE_OMP=FALSE`` to disable OpenMP, and ``USE_CUDA=TRUE`` to activate CUDA support for GPU utilization.

Other flags are explained below, with their default values shown:

- ``AMREX_HOME ?= ../../amrex`` specifies the location of the AMReX library.
- ``DEBUG=FALSE`` sets the debug mode.
- ``USE_HYPRE=FALSE`` can be used to set HYPRE for the multigrid bottom solver.
- ``COMP=gnu`` sets the GNU compiler.
- ``DIM=3`` builds the code for 3D domain.
- ``CXXSTD=c++17`` sets  C++17 for compilation.
- ``TINY_PROFILE=FALSE`` is used to enable the AMReX profiler.

Set the following flags based on your configuration needs:

- ``USE_EB=TRUE`` if the input file sets embedded boundaries.
- ``USE_TRANSPORT=TRUE`` for enabling the transport solver, which uses the nonequilibrium Green's function (NEGF) method.
- ``COMPUTE_GREENS_FUNCTION_OFFDIAG_ELEMS=FALSE`` and ``COMPUTE_SPECTRAL_FUNCTION_OFFDIAG_ELEMS=FALSE`` to switch off computations and storage of off-diagonal elements of Green's and spectral functions in the NEGF solver.
- ``BROYDEN_PARALLEL=TRUE`` uses an efficient parallel version of the Broyden's algorithm for self-consistency between electrostatics and NEGF modules.
- ``TIME_DEPENDENT=TRUE`` builds the code for accepting voltages on the embedded boundaries with varying values, for example setting a range of values to obtain full current-voltage characteristics.

3. Preprocessor Flags
---------------------
Set preprocessor flags in the ``../Source/Code_Definitions.H`` file before compiling the code, depending on your configuration needs.

The important ones are: 

- ``#define NUM_MODES 1`` sets the size of block for each matrix element used in NEGF. 
  For example, for modeling carbon nanotubes with single mode using mode-space approximation, NUM_MODES is set to 1, which implies each element of Hamiltonian matrix is a number. If it were 2, it would be an array of size 2. For other materials, this number may set the matrix block as a submatrix of size ``NUM_MODES x NUM_MODES``.

- ``#define NUM_CONTACTS 2`` sets number of metal leads to 2 for source and drain. At present, the code is verified for 2 leads.
