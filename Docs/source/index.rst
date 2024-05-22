:orphan:

MicroEleX
---------

Exascale-Enabled Models and Algorithms for Microelectronics Applications

The MicroEleX package contains a variety of models and algorithms for physical modeling of microelectronic circuitry, including electrostatics, electrodynamics, superconducting physics, micromagnetics, multi-ferroic systems, and quantum transport. MicroEleX leverages the AMReX software framework to provide scalability on GPU-based supercomputing architectures. The code is open source and designed to be algorithmically flexible so developers can incorporate enhanced or customized physics. It contains the following code packages:


ARTEMIS
-------

`ARTEMIS <https://github.com/AMReX-Microelectronics/artemis>`__ is an advanced electrodynamics code based on `WarpX <https://ecp-warpx.github.io>`__.
It couples Maxwell's equations with classical models describing quantum behavior of materials used in microelectronics.

It supports many features including:

    - Perfectly-Matched Layers (PML)
    - Heterogeneous materials
    - User-defined excitations
    - Landau-Lifshitz-Gilbert equations for micromagenetics


ARTEMIS is a *highly-parallel and highly-optimized code*, which can run on GPUs and multi-core CPUs, and includes load balancing capabilities.
In addition, ARTEMIS is also a *multi-platform code* and runs on Linux, macOS and Windows. ARTEMIS has leveraged the ECP code WarpX, and is built on the ECP framework AMReX.

FerroX
-------

`FerroX <https://github.com/AMReX-Microelectronics/FerroX>`__ is a massively parallel, 3D phase-field simulation framework for modeling ferroelectric materials based scalable logic devices. We self-consistently solve the time-dependent Ginzburg Landau (TDGL) equation for ferroelectric polarization, Poisson's equation for electric potential, and semiconductor charge equation for carrier densities in semiconductor regions. The algorithm is implemented using Exascale Computing Project software framework, AMReX, which provides effective scalability on manycore and GPU-based supercomputing architectures. The code can be used for simulations of ferroelectric domain-wall induced negative capacitance (NC) effect in Metal-Ferroelectric-Insulator-Metal (MFIM) and Metal-Ferroelectric-Insulator-Semiconductor-Metal (MFISM) devices.

ELEQTRONeX
----------

`ELEQTRONeX <https://github.com/AMReX-Microelectronics/eXstatic>`__ is a framework for electrostatic-quantum transport modeling of nanomaterials at exascale, built using the AMReX library, currently supporting the modeling carbon nanotube field-effect transistors (CNTFETs) with multiple nanotubes. It is developed as part of a DOE-funded project called 'Codesign and Integration of Nanosensors on CMOS'. It builds upon the AMReX library, developed as part of DOE's exascale computing projects (ECP).

The framework comprises three major components: the electrostatic module, the quantum transport module, and the part that self-consistently couples the two modules. The electrostatic module computes the electrostatic potential induced by charges on the surface of carbon nanotubes, as well as by source, drain, and gate terminals, which can be modeled as embedded boundaries with intricate shapes. The quantum transport module uses the nonequilibrium Green's function (NEGF) method to model induced charge. Currently, it supports coherent (ballistic) transport, contacts modeled as semi-infinite leads, and Hamiltonian representation using the tight-binding approximation. The self-consistency between the two modules is achieved using Broyden's modified second algorithm, which is parallelized on both CPUs and GPUs. Preliminary studies have demonstrated that the electrostatic and quantum transport modules can compute the potential on billions of grid cells and compute the Green's function for a material with millions of site locations within a couple of seconds, respectively.

MagneX
------

For classes of micromagnetic problems where the electromagnetic fields are slowly varying, the magnetostatic approximation offers huge computational savings. We are developing a new micromagnetics code, MagneX for modeling ferromagnetic materials using the magnetostatic approximation. MagneX incorporates many different physical phenomena, such as exchange coupling, anisotropy, Dzyaloshinskii Moriya interaction (DMI), and demagnetization. The GPU-capabilities provided by the AMReX library makes MagneX a powerful tool for simulating a wide range of micromagnetic applications, including magnetic storage and memory devices.

.. raw:: html

   <style>
   /* front page: hide chapter titles
    * needed for consistent HTML-PDF-EPUB chapters
    */
   section#installation,
   section#usage,
   section#theory,
   section#data-analysis,
   section#epilogue {
       display:none;
   }
   </style>


Installation
------------
.. toctree::
   :caption: INSTALLATION
   :maxdepth: 1
   :hidden:

   install/ferrox
   install/artemis

Usage
-----
.. toctree::
   :caption: USAGE
   :maxdepth: 1
   :hidden:

   usage/how_to_run
   usage/domain_decomposition
   usage/parameters
   usage/python
   usage/examples

Data Analysis
-------------
.. toctree::
   :caption: DATA ANALYSIS
   :maxdepth: 1
   :hidden:

   dataanalysis/formats
   dataanalysis/yt
   dataanalysis/openpmdviewer
   dataanalysis/openpmdapi
   dataanalysis/paraview
   dataanalysis/visit
   dataanalysis/visualpic
   dataanalysis/picviewer
   dataanalysis/reduced_diags
   dataanalysis/workflows


Epilogue
--------
.. toctree::
   :caption: EPILOGUE
   :maxdepth: 1
   :hidden:

   acknowledgements
