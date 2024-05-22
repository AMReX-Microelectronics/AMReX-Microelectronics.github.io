.. _install-ferrox:

MagneX
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

MagneX is a massively parallel, 3D micromagnetics solver for modeling magnetic materials. MagneX solves the Landau-Lifshitz-Gilbert (LLG) equations, including exchange, anisotropy, demagnetization, and Dzyaloshinskii-Moriya interaction (DMI) coupling. The algorithm is implemented using Exascale Computing Project software framework, AMReX, which provides effective scalability on manycore and GPU-based supercomputing architectures.
   
Our community is here to help. Please report installation problems or general questions about the code in the `github Issues <https://github.com/AMReX-Microelectronics/MagneX/issues>`_ tab.

Installation
------------

We begin with instructions for a basic, pure-MPI (no GPU) installation. More detailed instructions for GPU systems are below.

Download AMReX and MagneX Repositories
--------------------------------------

Make sure that AMReX and MagneX are cloned at the same root location. 

.. code-block:: bash
   
   git clone https://github.com:AMReX-Codes/amrex.git
   git clone https://github.com:AMReX-Microelectronics/MagneX.git

Dependencies
------------

Beyond a standard Ubuntu22 installation, the Ubuntu packages libfftw3-dev, libfftw3-mpi-dev, and cmake are required.
SUNDIALS is optional and enabled Runge-Kutta, implicit, and multirate integrators (more detailed instructions in the full documentation).
heFFTe is a required dependancy. At the same level that AMReX and MagneX are cloned, run: 

.. code-block:: bash
                
   git clone https://github.com/icl-utk-edu/heffte.git
   cd heffte
   mkdir build
   cd build
   cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_STANDARD=17 -DBUILD_SHARED_LIBS=OFF -DCMAKE_INSTALL_PREFIX=. -DHeffte_ENABLE_FFTW=ON -DHeffte_ENABLE_CUDA=OFF ..
   make -j4
   make install

Build
-----

Navigate to MagneX/Exec/ and run:

.. code-block:: bash

   make -j4
