.. _GEOS-Chem configuration files:

Simulations configuration
=========================

This section will present how to modify the setup files of GEOS-Chem to configure 
the simulations to your liking. The main files and parameters that are most commonly 
changed by users will be described in this section also.

Verify the compilation options
------------------------------



Copying Restart Files over to run directory
-------------------------------------------

Before starting any new simulation, GEOS-Chem need to be given an initial state 
of its multiples variable and grids. A value need to be attributed to the multiples 
variables used in the model before it can start and this is done through the use
of restart files. Restart files are created at the end of each simulations of the 
model or as checkpoint during the simulation to allow the model to restart at the 
same state as the previous simulation finished. 

We use these restart file to initiate all the necessary variables in the model. 
Generic initials files are available in a common :ref:`Storage directory` on Stetson 
under the subfolder :file:`GEOSCHEM_RESTARTS`. Restart files availables on the Stetson
are reference files and are not available for all possible starting dates for the 
simulations. In case the starting date of the simulation does not match the date 
included in the name of the available restart files names, the user can simply modify 
the name of the restart file by hand (using :code:`mv old_name new_name`) to make 
the name of the restart file match the starting date of the simulation.

Adjusting simulations Config Files
----------------------------------

1. GEOSChem_config.yml

2. HEMCO_config.rc

3. HEMCO_diagn.rc

4. HISTORY.rc
