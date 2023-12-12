.. _GEOS-Chem configuration files:

Simulations configuration
=========================

This section will present how to modify the setup files of GEOS-Chem to configure 
the simulations to your liking. The main files and parameters that are most commonly 
changed by users will be described in this section also.

Verify the compilation options
------------------------------

To verify your build configuration before you start changing the configuration Files
of your simulation, you can execute the following command in the :file:`~/build_info`
directory: 

.. code-block:: bash

    ./summarize_build.sh

After making sure that all your compilation configuration, are the one you expect 
and want you can proceed to the next steps of the simulation configuration.

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

This section will describe the most common changes made by user to the respective 
configuration files of each simulations with GEOS-Chem TOMAS.

.. option:: GEOSChem_config.yml

    In the :file:`GEOSChem_config.yml` file, you will find the general configuration 
    of your GEOS-Chem simulation. Mainly, all the parameters related to the spatial 
    grid configuration and simulation period and the time steps are all defined in 
    that file. The most common changes made to that file are changes in the definition 
    of the simulation period. You can verify that all the grid parameters have the  
    expected values from the run directory configuration. 

    .. option:: start_date

        Change this option to make it correspond with the starting date and time of 
        your simulation. Use the following format YYYYmmDD, HHMMSS. 

    .. option:: end_date

        Change this option to make it correspond with the ending date and time of 
        your simulation. Use the following format YYYYmmDD, HHMMSS.

    .. option::debug_printout

        Change this option to :code:`True` to enable more detailled printout when 
        debugging the model.

    For a detailled description of the other options in the :file:`GEOSChem_config.yml`
    file see the `GEOSChem_config page`_ in the original documentation of the model. 

.. option:: HEMCO_config.rc

    The :file:`HEMCO_Config.rc` file is containing all parameters related to the 
    configuration of the Harmonized Emissions Component (aka HEMCO). A detailled  
    description of each options in the :file:`HEMCO_Config.rc` file is given in the 
    original HEMCO configuration and in the original page `HEMCO_Config page`_ of The
    original GEOS-Chem wiki. 

    Here we will focus on presenting the principales modifications made to this file 
    for the purpose of using the TOMAS module on the stetson server and explain some 
    of the verifications that need to be done before launching a simulation. 

    .. option:: ROOT 

        Make sure that this directory is :file:`/data10/ctm/HEMCO`

    .. option:: METDIR

        Make sure that this directory is :file:`/data10/ctm/GEOS_4x5.d/MERRA2`

    .. option:: Verbose

        Option indicating how detailled the model printouts will be. The value of that 
        option can vary between :literal:`0` (no additional output) and :literal:`3`
        (Maximal amount of outputs). By default this option has the value :literal:`0`. 
        It can be changed to :literal:`3` to get the maximum amount of printout possible, 
        when trying to debug the model.

    .. option:: Warning 

        Option indicating how detailled the warnings returned by the model will be. 
        Value can vary between :literal:`0` and :literal:`3`. By default this option 
        has the value :literal:`1`. Can be changed to :literal:`3`, when trying to 
        debug the model to get the most amount of information possible. 

    In the :literal:`EXTENSION SWITCHES`, make sure that the following options are toggled 
    on and off. 

    .. code-block:: console

        --> OFFLINE_DUST           :       false    # 1980-2019
        --> OFFLINE_BIOGENICVOC    :       true     # 1980-2020
        --> OFFLINE_SEASALT        :       false    # 1980-2019
        -->  CalcBrSeasalt         :       false
        --> OFFLINE_SOILNOX        :       false    # 1980-2020

    In the :literal:`BASE EMISSIONS` section, make sure that the name Restart files 
    for GEOS-Chem have the right name. The general structure of those file name is 
    :literal:`GEOSChem.Restart.TOMAS{#Bins}.$YYYY$MM$DD_$HH$MNz.nc4`. Also make sure 
    that the option parameter for the :literal:`sourceTime` in the first line of the GEOSChem
    restart file is set to :literal:`CYS`, instead of :literal:`EFYO`. This allow a 
    more relaxed selection of the initial concentrations for each species in Restart 
    file and to skip missing species in case the time steps of your simulation don't 
    perfectly match the one in the restart file.

    Here is an example of the :literal:`BASE EMISSIONS` section configured for the 
    use of TOMAS with 40 bins.

    .. code-block:: console

        #==============================================================================
        # --- GEOS-Chem restart file ---
        #==============================================================================
        (((GC_RESTART
        * SPC_           ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 SpeciesRst_?ALL?    $YYYY/$MM/$DD/$HH CYS xyz 1 * - 1 1
        * DELPDRY        ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Met_DELPDRY         $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1        
        * KPP_HVALUE     ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_KPPHvalue      $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1        
        * WETDEP_N       ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_WetDepNitrogen $YYYY/$MM/$DD/$HH EY   xy  1 * - 1 1
        * DRYDEP_N       ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_DryDepNitrogen $YYYY/$MM/$DD/$HH EY   xy  1 * - 1 1
        * SO2_AFTERCHEM  ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_SO2AfterChem   $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1
        * H2O2_AFTERCHEM ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_H2O2AfterChem  $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1
        * AEROH2O_SNA    ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_AeroH2OSNA     $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1
        * ORVCSESQ       ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_ORVCSESQ       $YYYY/$MM/$DD/$HH EY   xyz 1 * - 1 1
        * JOH            ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_JOH            $YYYY/$MM/$DD/$HH EY   xy  1 * - 1 1
        * JNO2           ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_JNO2           $YYYY/$MM/$DD/$HH EY   xy  1 * - 1 1
        * STATE_PSC      ./Restarts/GEOSChem.Restart.TOMAS40.$YYYY$MM$DD_$HH$MNz.nc4 Chem_StatePSC       $YYYY/$MM/$DD/$HH EY   xyz count * - 1 1
        )))GC_RESTART

.. option:: HISTORY.rc

    This file is specifying the outputs of the GEOS-Chem simulation. Unless you want 
    to change the default outputs from GEOS-Chem, you should not have to modify this 
    file. Even in the case where you want to modify the amount of information returned 
    by the model, you will probably mainly change the first section 
    :literal:`COLLECTION NAME DECLARATIONS`. In this section, you can enable/disable 
    the different diagnosis collections for GEOS-Chem by uncommenting/commenting them 
    with the :code:`#` symbol. For more granular control of the outputs, you can change 
    the individual temporality of save and the variables they contain in the following 
    subsections of the :file:`HISTORY.rc` file. See the original `HISTORY.rc documentation`_
    for more details. 

.. _GEOSChem_config page: https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/geoschem-config.html#cfg-gc-yml
.. _HEMCO_Config page: https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/hemco-config.html
.. _HISTORY.rc documentation: https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/history.html