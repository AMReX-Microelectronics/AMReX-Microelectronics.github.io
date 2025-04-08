.. _usage_run:

ELEQTRONeX Output
=================

Field and Diagnostic Data Output
--------------------------------

ELEQTRONeX writes field data using AMReX's native plotfile format <https://amrex-codes.github.io/amrex/docs_html/IO.html>_, consistent with other AMReX-based codes.

For tools and methods to visualize AMReX plotfiles, refer to the AMReX Visualization documentation <https://amrex-codes.github.io/amrex/docs_html/Visualization_Chapter.html>.

Field Data Output
^^^^^^^^^^^^^^^^^
Field data is written to the main output directory specified by the plot.folder_name parameter. Within this directory, time step-specific subdirectories are created, each named using the format plt<step_id>.

The specific field variables to output, along with additional control options, can be configured under ``Field data output parameters`` in the ``Run ELEQTRONeX`` section.

Diagnostic Data Output
^^^^^^^^^^^^^^^^^^^^^^

If the ``use_diagnostics`` parameter is enabled, ELEQTRONeX also generates diagnostic output data.

- A ``diag/`` subdirectory is created inside the main output folder.

- Within ``diag/``, a separate folder is generated for each diagnostics object defined in the ``diag.objects`` parameter.

- Each diagnostics object folder contains corresponding output files in AMReX’s native plotfile format.

Configuration details for diagnostic outputs can be found under ``Diagnostic parameters`` in the ``Run ELEQTRONeX`` section.

NEGF Data Output
-----------------

The NEGF related data is written out in a folder ``negf/`` created inside a main folder specified by parameter ``plot.folder_name``.

Step-wise data 
^^^^^^^^^^^^^^

NEGF module writes out the following files containing converged values at each step (i.e. at a fixed Vgs, Vds conditions). There may be more than 1 steps if we run simulation where one of these parameters vary, e.g. to obtain I-Vgs or I-Vds characteristics.

- ``step<step_id>_Qout.dat`` This file contains induced charge at each field site. Field sites are locations on a material at which potential values are fed to the NEGF module from the electrostatic module. For carbon nanotube, these locations are the rings on each nanotube. The first column contains an id from 0 onward, the second column contains the ring locations in the domain, and the third column corresponds to the induced charge in electron.

- ``step<step_id>_U.dat`` Similarly, this file contains the potential values at each field site in Volts.

- ``step<step_id>_norm.dat`` This file contains the absolute or relative norm calculated for each field site. The type of norm is set by the parameter ``transport.Broyden_norm_type``.

In the above files, ``<step_id>`` is a 4 digit identifier for each step starting from step 0 to the number of steps. For example, the identifier for step 7 is ``0007``. The biases at each step can be seen in file ``I.dat`` explained next. 

Current/conductance and more
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

File ``I.dat`` written in ``negf/`` folder contains some essential data calculated at convergence at each step.

In this file we write out the following data in columner manner:

- ``step`` Step id.

- ``Vds`` Drain-source voltage at a given step.

- ``Vgs`` Gate-source voltage at a given step.

- ``I at contact_1`` This is steady state current in Amperes passing through the first contact (typically source).

- ``I at contact_2`` This is current through passing through the second contact (typically drain). At steady state and at convergence, the magnitude of current through both contacts should be the same but their sign must be opposite (Kirchhoff's law).

- ``Avg_intg_pts`` This is the average number of integration points at a given step. Average is over all Broyden iterations. Integration points may vary if we are using adaptive integration approach for the integration over a real line (part 2 of the integral at nonequilibirum).

- ``Total_Iter`` This is the total number of Broyden iterations in that step.

- ``Conductance / (2q^2/h)`` This is zero-bias conductance normalized by the quantum of conductance. This is meaningful when Vds=0.

Note that if you rerun the simulation such that it outputs the data in the same folder, then this file is not replaced. In stead, the new simulation output is appended to what was written out before. It is up to the user to cleanup lines corresponding to old runs or store this file before rerunning the simulation. 

Iteration-wise data
^^^^^^^^^^^^^^^^^^^
Optionally, we can write out data (induced charge, potential, norm) at each Broyden iteration in each step if we enable flag ``NS_default.write_at_iter``. In this case, a folder is created in ``negf/`` folder corresponding to each step, e.g. ``step<step_id>/``. In this folder, files for each iterations are written out which have same format as step-wise files. 

Density of States - flatband
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can write out flatband DOS by enabling parameter ``NS_default.flag_compute_flatband_dos``. See explanation where corresponding input parameters are described. 
In this case, ``DOS_flatband/`` folder is created in the ``negf/`` folder.
In this folder, a file is written out called ``transport_char.dat``.
This file contains the following data in columner manner.

- ``E_r`` This is the real part of the energy value in eV. While calculating flat-band DOS, we integrate over a real axis within limits defined by parameter ``NS_default.flatband_dos_integration_limits``, so the imaginary part of the energy is a constant small number defined by ``NS_default.E_zPlus_imag`` parameter.

- ``Fermi_Contact1`` This is the Fermi function value of contact 1 (typically, source) at a given energy point.

- ``Fermi_Contact2`` This is the Fermi function value of contact 2 (typically, drain) at a given energy point.

- ``DOS_r`` This is the real part of the density of states at a given energy value.

- ``Transmission_r`` This is the transmission function at a given energy value.

- ``Conductance_r`` This is the conductance function normalized by the quantum of conductance at a given energy value. The zero bias conductance reported in ``I.dat`` file is the total conductance obtained by integrating the conductance function.

Density of States - step-wise
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can also write out DOS at each step by enabling parameter ``NS_default.flag_compute_DOS``. In this case, a step-wise folder is created entitled, ``DOS_step<step_id>``, in which we write out a file ``trasport_char.dat`` with similar contents explained above.


Local Density of States 
^^^^^^^^^^^^^^^^^^^^^^^

We can output local DOS at convergence in a given step by enabling parameter ``NS_default.flag_write_LDOS``. 
In this case, in each ``DOS_step<step_id>`` folder, additional files are created each corresponding to an energy point ``Ept_<energy_point_id>.dat``.
These energy points correspond to energy points used for nonequilibrium integration path defined by ``NS_default.noneq_integration_pts``.
Each file contains two columns, field site locations in the primary transport direction ``PTD``, e.g. each nanotube ring, and the local density of states at that location ``LDOS``.

Optionally, we can also write out LDOS at each Broyden iteration by setting parameter ``NS_default.flag_write_LDOS_iter``. In this case, LDOS data is written out in the step-specific folder created for writing iteration-wise data. So for this to work, we should enable ``NS_default.write_at_iter`` parameter. In each step-wise folder, an iteration specific folder is created ``DOS_iter<iteration_id>``. In this folder, LDOS data is written out in the format similar to that described above. Note that this parameter should only be used for debugging, since large amounts of data will be written out.

Integrand in density matrix 
^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can also write out step-wise integrand as plotted in Fig. 3d of the first ELEQTRONeX paper by enabling parameter ``NS_default.flag_write_integrand``.
This is outputted in file, ``step<step_id>_integrand.dat``.

The file contains columner data listing the following contents.

- ``(E_r-mu_0)/kT`` Normalized energy. Energy (real part) minus the Fermi level of the first contact divide by kT.

- ``Intg_Channel`` Integrand at a field site at the center of the channel.

- ``Intg_Source`` Integrand at a field site at the center of the source.

- ``Intg_Drain`` Integrand at a field site at the center of the drain.

- ``|F1-F2|`` Difference in the Fermi levels.

- ``E_r`` This is the real part of the energy value in eV. 

Optionally, we can output integrand at specific Broyden iterations by specifying parameter ``NS_default.write_integrand_interval``.

Charge components
^^^^^^^^^^^^^^^^^

By enabling parameter ``NS_default.flag_write_charge_components``, we can output the individual parts of charge density that make up the induced charge density, e.g. the contribution from equilibrium path (part 1 of Eq. (7), computed using Residue theorem), part 2 of Eq. (7), computed by integrating over a real line, neutral charge density as defined in the paper. This is useful for debugging.

This data is written out at each iteration in a file ``iter<iteration_id>_chargeComp.dat`` in each step folder. For this parameter to work ``NS_default.write_at_iter`` flag must be 1.


Point Charge Data Output
------------------------

By enabling parameter ``pc.flag_write_individual_charge_files``, we can output locations and other parameters corresponding to each trap at each step when their occupation values are converged.

This data is written out in a folder named ``point_charge/`` in the main folder (at the same level as the ``negf/`` folder).
In this folder, a step-wise file is created, named ``step<step_id>.dat``, containing the following data:

- ``ID`` point charge ID.

- ``Charge`` charge of the trap. Typically 1, meaning 1e.

- ``Occupation`` occupation of the trap.

- ``Potential`` Potential at the trap location.

- ``RelDiff`` Relative difference in the potential at the trap location.

- ``X`` X-location in the domain.
 
- ``Y`` Y-location in the domain.

- ``Z`` Z-location in the domain. (This is optional and worked when AMREX_SPACEDIM=3).

Regardless of the parameter ``pc.flag_write_individual_charge_files``, file ``total_charge_vs_step.dat`` is written out whenever point charges are used in the simulation.
This file writes out the total charge and total occupation from all traps at each step at each iteration, among other things. This is useful to see how the total charge converges. The total charge typically converges in the last few Broyden iterations at each step.
