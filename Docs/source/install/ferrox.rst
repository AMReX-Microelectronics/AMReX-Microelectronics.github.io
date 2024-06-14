.. _install-ferrox:

FerroX
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

FerroX is a massively parallel, 3D phase-field simulation framework for modeling ferroelectric materials based scalable logic devices. We self-consistently solve the time-dependent Ginzburg Landau (TDGL) equation for ferroelectric polarization, Poisson's equation for electric potential, and semiconductor charge equation for carrier densities in semiconductor regions. The algorithm is implemented using Exascale Computing Project software framework, AMReX, which provides effective scalability on manycore and GPU-based supercomputing architectures. The code can be used for simulations of ferroelectric domain-wall induced negative capacitance (NC) effect in Metal-Ferroelectric-Insulator-Metal (MFIM) and Metal-Ferroelectric-Insulator-Semiconductor-Metal (MFISM) devices.

Our community is here to help.
Please `report installation problems <https://github.com/AMReX-Microelectronics/FerroX/issues/new>`_ in case you should get stuck.

Installation
------------

First, download the AMReX repository:

.. code-block:: bash
   
   git clone https://github.com:AMReX-Codes/amrex.git

At the same directory level as AMReX, download the FerroX Repository:

.. code-block:: bash

   git clone https://github.com/AMReX-Microelectronics/FerroX.git 

Build
-----

Make sure that the AMReX and FerroX are cloned in the same location in your filesystem before 
continuing. Note that the commands to build FerroX depend on your computer's hardware configuration. Navigate to the Exec folder within the FerroX directory and execute the following commands based on your setup:

If running on a computer with a GPU:

.. code-block:: bash

   make -j 4

If running on a computer with a CPU:

.. code-block:: bash

   make -j 4 USE_CUDA=FALSE

Enabling sundials support while building (computer with CPU):

.. code-block:: bash
        
   make -j 4 USE_CUDA=FALSE USE_SUNDIALS=TRUE


If you want to use FerroX on a specific high-performance computing (HPC) system, follow the same steps as above. For MPI+CUDA build, make sure that appropriate CUDA modules are loaded. For instance, on Perlmutter you will need to do:

.. code-block:: bash

   module load cudatoolkit

Incorporating SUNDIALS
----------------------

If you want to incorporate the SUNDIALS library into your FerroX code, first follow the SUNDIALS installation steps as described `here <https://github.com/AMReX-Microelectronics/MagneX/blob/development/Exec/README_sundials>`_. To build the code with SUNDIALS support enabled, you can include the USE_SUNDIALS=TRUE option with the appropriate build command (refer to the example under the 'Build' heading).


