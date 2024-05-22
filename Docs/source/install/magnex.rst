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

Our community is here to help.
Please `report installation problems <https://github.com/AMReX-Microelectronics/FerroX/issues/new>`_ in case you should get stuck.


Download AMReX Repository

.. code-block:: bash
   
   git clone git@github.com:AMReX-Codes/amrex.git

Download FerroX Repository

.. code-block:: bash

   git@github.com:AMReX-Microelectronics/FerroX.git

Build
-----
Make sure that the AMReX and FerroX are cloned in the same location in their filesystem. Navogate to the Exec folder of FerroX and execute 

.. code-block:: bash

   make -j 4

.. only:: html

   .. image:: hpc.svg

HPC Systems
-----------

If want to use FerroX on a specific high-performance computing (HPC) system, follow the same steps as above. For MPI+CUDA build, make sure that appropriate CUDA modules are loaded. For instance, on Perlmutter you will need to do:

.. code-block:: bash

   module load toolkit




