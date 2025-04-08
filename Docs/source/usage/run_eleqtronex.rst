.. _usage_run:

Run ELEQTRONeX
=========

Run
---

Run locally
^^^^^^^^^^^
Upon compilation, executable is generated in ``Exec/`` folder in the ELEQTRONeX folder.
To run the executable with an input file from the `input/` folder in the `ELEQTRONeX` folder,
we can do the following:

Assuming we are in the ``Exec/`` folder, to run with a single processor, execute:

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

- ``use_electrostatic = 1`` Electrostatics module enabled.
- ``use_transport = 1`` Transport module enabled.
- ``transport.use_negf = 1`` NEGF solver enabled.
- ``use_diagnostics = 1`` Diagnostics are enabled.
- ``domain.embedded_boundary = 1`` Set embedded boundaries in the domain.

Field data output parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- ``plot.folder_name = <output_folder>``  Replace ``<output_folder>`` with the foldername.
- ``plot.fields_to_plot = <MF_name>.<ID> <MF_name> vecField`` vector of ``multiFabs`` to be outputted. Replace <MF_name> with the name of multifab defined in ``macroscopic.fields_to_define``. If ``<ID>`` is not specified, then data is outputted only in the output folder. ``vecField`` outputs the gradient of electrostatic potential in all three directions.
  Additionally, there is an option to create raw_fields folder as ``<output_folder>/raw_fields``, where raw data may be outputted. Replace ``<ID>`` with 1 to output the multifab in the output folder as well as separately in the ``raw_fields`` folder along with the ghost cells. This may be useful while debugging. Replace ``<ID>`` with 2 to output data only in the ``raw_fields`` folder. 
- ``plot.write_after_init = 1``  Data can be written out after initialization and before the first iteration. This may be useful for debugging before running the simulation.
- ``plot.write_interval = <int value>``  If the previous parameter is enabled, then this parameter can be used to specify the interval with which the data is written out.
- ``plot.rawfield_write_interval = <int value>``  This interval can be different from ``plot.write_interval``.

Boundary Conditions
^^^^^^^^^^^^^^^^^^^
- ``domain.is_periodic = 0 1 0`` sets whether domain is periodic in the X, Y, Z directions, respectively. In this example, `Y` direction is periodic.
- ``boundary.hi = <X_boundary_type>(<dirichlet_value_or_string_name>) <Y_boundary_type>(<string_name>) <Z_boundary_type>(<neumann_value_or_string_name>)`` This parameter sets the maximum domain boundaries in the `X`, `Y`, and `Z` directions. Replace ``<*_boundary_type>`` by ``dir`` (Dirichlet), ``rob`` (Robin), ``neu`` (Neumann), or ``per`` (Periodic). 
  We can specify floating point values on Dirichlet and Neumann boundaries for electrostatic potential in paranthesis. If no value is specified then ``0.0`` is assumed. 
  We may choose to specify a string parameter in the paranthesis such as ``dir(Zmax)`` to specify a time and/or spatially varying function. In this case, we need to specify another parameter,  ``boundary.<string_name>_function``, for parsing the function, such as ``boundary.Zmax_function = "10*cos(2*pi*x/(2*Lx))"``. See Parser in the AMReX documentation.
  For Robin boundaries, we need to set a string parameter, for example ``rob(Ymax)`` and set three more Robin boundary specific parameters as ``boundary.<string_name>_a_function``, ``boundary.<string_name>_b_function``, ``boundary.<string_name>_f_function``. If ``domain.is_periodic`` is specified to be periodic, then it overrides the ``boundary.hi`` and ``boundary.lo`` parameters. 
- ``boundary.lo = neu(-0.5) neu dir(5)`` Similarly, set the minimum domain boundaries in the `X`, `Y`, and `Z` directions. In this example, minimum `X`, `Y` boundaries are Neumann with values of -0.5 and 0., while minimum `Z` boundary is Dirichlet with a potential value of 5~V.


Embedded boundary parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
For these parameters to work, we enable ``domain.embedded_boundary=1``.

- ``domain.specify_using_eb2=<boolean>`` If this is true, then we load EBs provided by AMReX using its inbult interface. Typically set to 0.
- ``ebgeom.objects=<object_label1> ...`` Here we can set EB object labels. If we want to set more than 1 labels, we can do that by listing space-separated labels. For example, we can define labels as Source, Drain. Gate can be specified using either EB or the boundary. If we are setting gate using EB, we should use the label ``Gate``.
- ``ebgeom.specify_inhomo_dir=1`` This means we specify inhomogeneous Dirichlet boundaries on the EB. We can set different Dirichlet boundaries on each EB.
- ``<object_label>.geom_type = <options>`` <options> can be ``box`` to set a box-shaped EB. Or it can be ``cntfet_contact_cyl`` to define a cylindrical EB shape with a cylindrical cavity (in which carbon nanotube goes). Or it can be ``cntfet_contact_rect`` to define a rectangular EB shape with a rectangular cavity. For all supported shape, see function ``ReadObjectInfo(...)`` in the file ``Source/Input/EmbeddedBoundaries.cpp``.
  
Below we provide example of using ``cntfet_contact_cyl``. We assume that we are defining it for an object named Drain.

- ``Drain.geom_type = cntfet_contact_cyl`` Here we define the geometry type for the Drain.

- ``Drain.inner_radius=<float value>`` Here we specify the inner radius of the cavity.

- ``Drain.thickness=<float value>`` Here we set the thickness of the cylinder.

- ``Drain.center=<X-center> Y-center> <Z-center>`` These are all float values that define the center of the cylinder in a domain.

- ``Drain.height = <float value>`` This defines the height of the cylinder in the axial direction. This height protrudes the cylinder symmetrically around its center in the axial direction such that the height is as specified here.

- ``Drain.direction=<value>`` ``<value>`` can be 0, 1, or 2 for defining the axial direction as X, Y, or Z.

- ``Drain.surf_soln=<float value>`` This parameter can be used to set the potential on the surface. Optionally we can set a parser for setting the potential as follows.

- ``Drain.surf_soln_parser=<boolean>`` Optionally we can use a parser for setting the potential, by setting this parameter 1. In this case, we do not define the previous parameter. This parameter is useful in case if the source potential varies with time. Note that in the case of steady state NEGF, we do not actually vary time but we use the time parameter ``t`` in the parser equation to vary the steps, e.g. to compute I-Vds characteristics by varying drain-source voltage (Vds) value.

- ``Drain.surf_soln_function=<parser expression>`` Here we can write a string enclused parser expression as supported by AMReX. For example to vary drain-source voltage, we can write ``<parser expression>`` as ``"SV + Vds_max - (Vds_max-Vds_min) * t"``, where ``SV`` is a constant voltage on the source (typically 0), ``Vds_max`` is the maximum drain-source voltage, ``Vds_min`` is the minimum drain-source voltage, and ``t`` is a `time` varying parameter from 0 to 1 to span  the entire range from ``Vds_max`` to ``Vds_min``.


Restart parameters
^^^^^^^^^^^^^^^^^^
- ``restart = 1`` set while restarting the code.
- ``restart_step = <step_number>`` Replace ``<step_number>`` with step to be restarted.

Electrostatics module parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
See AMReX documentation for MLMG parameters such as ``mlmg.set_verbose``, ``mlmg.max_order``, ``mlmg.absolute_tolerence``, and ``mlmg.relative_tolerance``.

For debugging, we typically need:

- ``mlmg.set_verbose = <integer number>`` which sets the verbosity level for output of the MLMG multigrid solver. Useful for debugging. Usually set to 0.

  
Diagnostic parameters
^^^^^^^^^^^^^^^^^^^^^
For the following parameters to work, set ``use_diagnostics=1``.

- ``diag.specify_using_eb = <boolean>`` With this flag, we can specify diagonstic regions using embedded boundaries. It is typically 1.
- ``diag.objects = <label1> ... `` Here we can define multiple labels for different diagnostic objects. This label can be any string, e.g ``Z_rho``, which we will use next to output charge density on a plane.
- ``Z_rho.geom_type = plane`` Here we are defining geometry type as a plane.
- ``Z_rho.direction = <value>`` <value> can be 0, 1, or 2, to set X, Y, or a Z-plane.
- ``Z_rho.location = <float value>`` This can be used to set the location of the Z-plane.
- ``Z_rho.fields_to_plot = charge_density`` This plots the charge density on the plane. This field label should be defined in ``macroscopic.fields_to_define``.


NEGF and Broyden module parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- ``transport.NS_names = <NS1> <NS2>`` Optional parameter to define vector of string names for nanostructures. Folders with these names are created in the ``<plot.folder_name>/negf/`` folder.
- ``transport.NS_num = <number>`` If ``transport.NS_names`` is not specified then we need to set this parameter defining the number of nanostructures.
- ``transport.NS_type_default = CNT`` The default type of nanostructures. Here it is defined as CNT, referring to carbon nanotube.
- ``transport.gather_field = phi`` Here ``phi`` refers to the name of the multifab defined to hold potential.
- ``transport.deposit_field = charge_density`` Here ``charge_density`` refers to the named of the multifab defined to hold charge density values.
- ``transport.NS_initial_deposit_value = <value>`` This is the initial charge deposited on the surface of the material while starting the simulation.
- ``transport.reset_with_previous_charge_distribution = <boolean>`` Value of 1 means when the gate-source or drain-source voltages change (i.e. for step > 0) during sweeping while characterizing a FET, we initialize the charge on the material based on the converged charge at previous conditions. If set to 0, then the charge is always initialized with a value set for ``NS_initial_deposit_value``. At step 0, the charge is initialized with a value set for ``NS_initial_deposit_value`` regardless of what value we set for this parameter.
- ``transport.selfconsistency_algorithm = broyden_second`` Currently only Broyden's modified second algorithm is supported in parallel. See the first ELEQTRONeX paper for details.
- ``transport.Broyden_fraction = <float value>`` Value of the Broyden fraction is defined between 0 to 1. Typically 0.1.
- ``transport.Broyden_norm_type = <type>`` ``<type>`` can be relative or absolute. Typically we have used relative norm.
- ``transport.Broyden_max_norm = <float value>`` Broyden iterations stop if the relative norm at all charge density sites is below this value. Typically set to 1.e-5.
- ``transport.Broyden_threshold_maxstep = <int value>`` Broyden iterations stop if the number of iterations exceed the value defined here. Typically 100.

The following parameters can be defined via ``NS_default`` prefix, with an option to override then by redefining it with a prefix defined by a nanostructure name, e.g. ``CNT`` for the example defined above. This is useful when we want to change the parameters only for specific nanostructures, while retaining the default behavior for the result of nanostructures.
For example, if we want to define 10 nanotubes, all having most parameters common, but one or two parameters varying across nanotubes, then the common parameters can be defined with prefix ``NS_default``, while the nanostructure specific parameters can be defined with prefix, ``NS_1``, ``NS_2``, etc. Here we assume that we have defined ``transport.NS_names = NS_1 NS_2`` for these names to be valid.
Below we use prefix ``NS_default``.

- ``NS_default.num_unitcells = <int value>`` Number of unitcells of material. Note that for all examples with carbon nanotube, we have defined the CNT length in terms of number of unitcells.
- ``NS_default.rotation_order = <type1> <type2> ...`` where ``<type>`` can be ``X``, ``Y``, or ``Z``. Material is initialized with its center at (0,0,0) coordinates. Rotation is applied to this material using Euler angles in the order specified here. ``NS_default.rotation_order = Z Y X`` would mean rotation is first applied about the Z axis, then Y, then X.
- ``NS_default.rotation_angle_type = <type>`` ``<type>`` can be ``D`` for degrees or ``R`` for radians.
- ``NS_default.rotation_angles = <alpha> <beta> <gamma>`` where these values correspond to Euler angles.
  - ``NS_default.offset = <X-offset> <Y-offset> <Z-offset>`` The material geometry is centered around zero. This parameter lets up translate it. These values are floats. In multiple nanotube simulations, we have different offsets for each nanotube.
- ``NS_default.contact_Fermi_level = <float value>`` This is the Fermi level defined as input in the time independent Green's function formalism.
- ``NS_default.contact_mu_specified = <boolean>`` 1 means that we provide the value of Fermi levels at the contacts. O means that it is computed based on the specified voltage on the embedded boundaries designated as source and drain. This parameter is set to 1, when we vary gate-source voltage, while keeping drain-source voltage fixed. It is set to 0, when we vary drain-source voltage, while keeping gate-source voltage fixed.
- ``NS_default.contact_mu = <value1> <value2>`` If the above parameter is set to 1, then we define the value of contact Fermi levels here. E.g. ``Ef (Ef-Vds)`` where ``Ef`` is a constant defining the Fermi level of the source and while ``Ef-Vds`` is the Fermi level of the drain with ``Vds`` is a constant defining the drain-source bias.
- ``NS_default.contact_T = <value1> <value2>`` These two values correspond to temperatures of source and drain contacts, respectively (in Kelvin).
- ``NS_default.gate_terminal_type = <type>`` where ``<type>`` can be an embedded boundary, in which case we need to define an embedded boundary for the gate, e.g. in the case of a gate-all-around geometry. Other option for ``<type>`` is ``Boundary``, in which case we need to define a Dirichlet with the name as ``Gate``, e.g. for a planar CNTFET geometry, we define ``boundary.low = neu(0.) neu(0.) dir(Gate)`` and ``boundary.Gate_function = "SV + Vgs_max - (Vgs_max-Vgs_min) * t"`` which defines a gate at the Zmin boundary.
- ``NS_default.Fermi_tail_factors = <value1> <value2>`` These values are multipliers that define minimum and maximum tails for the Fermi function. Typically we define 14 for both values, meaning that Fermi function has tails ``-14kT`` and ``+14kT``. This parameter is used in the code while defining the integration region.
- ``NS_default.eq_integration_pts = <value1> <value2> <value3>`` All 3 values are integers and they correspond to number of quadrature points used for integration using Gauss-Legendre polynomials. The three paths are used for integration the equilibrium region in the complex plane (see supplementary Fig.2 in the first ELEQTRONeX paper). Instead of integrating over a real line from Z_a to Z_b, we integrate along path 1: from Z_b to Z_c, path 2: from Z_c to Z_d, and path 3: from Z_d to Z_a. Note that the third path is curved.
- ``NS_default.num_noneq_paths=<value>`` In nonequilibrium, we have a second term to integrate, as defined in the Eq. (7) of the supplementary note 3 of the first ELEQTRONeX paper. This integration occurs over a line parallel to the real line (shifted by ``NS_default.E_zPlus_imag`` on the imaginary line). This path is by default assumed to be a single path of integration (``<value>`` is set to ``1``). However, it can be divided into ``3`` parts for the adaptive scheme.
- ``NS_default.noneq_integration_pts=<value>`` Here we define the number of integration points for each nonequilibrium path. If there is only a single path, then the value correspond to that single path. If there are 3 paths, then we need to provide 3 values.
- ``NS_default.NS_default.num_recursive_parts=<int value>`` This number has to do with the recursive (serial) part of the calculation for calculating Green's function, in block tri-diagonal inversion algorithm (see explanation of supplementary Fig. 1(b) in the first ELEQTRONeX paper.)The integer value chosen for this parameter divides the recursive array into as many parts and overlaps recursive computation of each part with its copy from CPU to GPU. Ideally, we want to divide the array small enough that the time for the two tasks are equal.

- ``NS_default.E_valence_min = <float value in eV>`` This is the minimum value of the valence bandedge for integration (see supplementary Fig. 2).
- ``NS_default.E_pole_max = <float value in eV>`` This value is the parameter Delta in supplementary Fig. 2, which determines how many Fermi function poles we include on the imaginary plane in the contour integration. 
- ``NS_default.E_zPlus_imag = <float>`` This is the small number eta in Eq.(2) of the first ELEQTRONeX paper.

NEGF data output parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- ``NS_default.write_at_iter=<boolean>`` 1 means we write out data (induced charge, potential, and relative/absolute norm) at each field site at each iteration.
- ``NS_default.flag_compute_DOS=<boolean>`` 1 means we compute and write the density of states of the material based on contact Fermi levels. 
- ``NS_default.flag_write_LDOS = <boolean>`` This flag write out local density of states at each step (step refers to different Vgs or Vds values depending what we are varying). For this parameter to work, ``NS_default.flag_compute_DOS`` must be 1.
- ``NS_default.flag_write_LDOS_iter = <boolean>`` This flag writes out local density of states at each iteration at a given Vgs, Vds conditions. Useful for debugging.
- ``NS_default.write_LDOS_iter_period=<int value>`` If the previous parameter is enabled, then this parameter can be used to set the interval with which the LDOS output is written out.
- ``NS_default.flag_compute_flatband_dos=<boolean>`` 1 means we compute the density of states of the material when the bands are flat, i.e. assuming that the contact Fermi levels are at 0.
- ``NS_default.flatband_dos_integration_points=<int value>`` Number of integration points for  the path defined by limits in the next parameter.
- ``NS_default.flatband_dos_integration_limits= <value1> <value2>`` e.g. ``-1. 1.`` would mean we  integrate from E=-1 eV to 1 eV.
- ``NS_default.flag_write_integrand=<boolean>`` 1 means we write out the integrands at the center of the channel. For example, in Fig. 3d of the first ELEQTRONeX paper, we plot integrands.
- ``NS_default.write_integrand_interval=<int value>`` Without setting this parameter integrand is written out only at convergence. If we want to see integrand at specific Broyden iterations, we can specify the interval here.
- ``NS_default.flag_write_charge_components=<boolean>`` 1 means, we write out the individual parts of charge density that make up the induced charge density, e.g. the contribution from equilibrium path (part 1 of Eq. (7), computed using Residue theorem), part 2 of Eq. (7), computed by integrating over a real line, neutral charge density as defined in the paper. This is useful for debugging.


Carbon nanotube parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^

- ``CNT_default.type_id = m_index n_index`` These are integer values. Carbon nanotube is defined as (m_index, n_index) nanotube.
- ``CNT_default.acc = bond_length`` bond_length is carbon-carbon bond length (0.142 nm).
- ``CNT_default.gamma = 2.5 `` gamma is the overlap parameter in eV (see the first ELEQTRONeX paper).

Point charge parameters
^^^^^^^^^^^^^^^^^^^^^^^^

- ``charge_density_source.type = point_charge`` This parameter chooses the external charge density source as point charges.
- ``pc.default_charge_unit = <value>`` This means default charge for each trap is ``<value>e``.
- ``pc.default_occupation = <value>`` This means the default occupation for each trap is ``<value>``. 0 means completely deactivated, 1 means fully active. 
- ``pc.charge_density = <float value>`` This is the charge density in SI units. For 3D and 2D random distribution of charges the density is in units of e/nm^3 and e/nm^2, respectively.
- ``pc.mixing_factor = <float value>`` This value refers to the simple mixing factor used for updating Vi in Eq.(5) of the second ELEQTRONeX paper.
- ``pc.flag_vary_occupation = <boolean>`` This flag is used to set traps with variable occupation according to Eq.(4) of the second ELEQTRONeX paper.
- ``pc.Et = <float value>`` If the ``flag_vary_occupation`` flag is 1, then we can use this parameter to set Vt in the denominator in Eq.(4) of the second ELEQTRONeX paper on traps. Note that ``Et`` should have been defined as ``Vt``.
- ``pc.V0 = <float value>`` This is the parameter Vo in Eq.(4).
- ``pc.flag_random_positions = <boolean>`` This flag determines whether we are going to initialize traps with a random distribution or specify them one by one.
- ``pc.random_seed = <int value>`` This value corresponds to random seed used for generating trap distribution.
- ``pc.offset = <value1> <value2> <value3>`` The trap locations are translated by this much amount in the X, Y, and Z directions when they are initialized with a random distribution (between 0 and 1). We can use this parameter to fix the minimum bounds of the cuboid region in which we want to initialize the traps. For example, we typically set this as ``pc.offset =  (-GO_width/2. + 10*dx) (-G_length/2.) (ZOffset)`` where ``GO_width, G_length, Zoffset, dx`` are the constants used to set the gate-oxide width, gate length, Z value along the gate-oxide just above the vacuum/gate-oxide interface, and cell-size in the X direction, respectively.
- ``pc.scaling = <value1> <value2> <value3>`` Similarly, the trap locations are scaled by these values when the traps are initialized with a random distribution (between 0 and 1). For example, we may apply scaling as ``pc.scaling = (GO_width - 20*dx) (G_length) (0)`` in addition to the offset defined above, which means that the traps will be introduced in the cuboid defined by the gate-oxide width 10dx short of the domain boundaries, entire gate length, and Z-plane marked by Zoffset.
- ``pc.num = <int value>`` If we know the specific trap locations where we want to introduce traps, we can initialize this number. For this to work, we need to set ``pc.flag_random_positions=0``.
- ``pc_<i>.location = <Xi> <Yi> <Zi>`` if ``pc.num`` is set then we can set the X, Y, Z locations of each ith trap with this parameter, where <i> is a number from 1 to value set by pc.num. 
- ``pc.flag_write_individual_charge_files= <boolean>`` As the name suggestions, with this parameter, we can output locations and other parameters corresponding to each trap at each step when their occupation values are converged.
