GEOS-Chem TOMAS Installation
============================

The installation of the GEOS-Chem model as already been very well documented in 
the official `GEOS-Chem wiki`_. This guide aim at explaining and highlithing the 
specific considerations that need to be taken during the installation process for 
someone who plans on using the model GEOS-Chem with the TOMAS microphysics module.

1. Get source code
------------------

The source code of the GEOS-Chem model is publically available on `Github`_ as a 
superproject, containing both the code for GCClassic and HEMCO. While the current 
guide is aimed at showcassing the use of GEOS-Chem classic, know that other version
of GEOS-Chem, like GCHP or GEOS-Chem using WRF inputs data, are available in the 
same superproject. 

To obtain the source code of the GEOS-Chem classic model latest stable version, 
you can use the following command in a command line:

.. code-block:: console 
    
    git clone --recurse-submodules https://github.com/geoschem/GCClassic.git

This command will download the GCClassic repository in your current working directory.
To verify that the previous command worked navigate to the :file:`GCClassic` folder 
and get a listing of what that the :file:`src` directory contains.

.. code-block:: console

    cd GCClassic
    ls -CF src/*

You should obtain an output similar to this one:

.. code-block:: text

    src/CMakeLists.txt

    src/GEOS-Chem:
    APM/          CMakeLists.txt  GeosRad/   Headers/     ISORROPIA/   NcdfUtil/  README.md
    AUTHORS.txt   CMakeScripts/   GeosUtil/  History/     KPP/         ObsPack/   run/
    CHANGELOG.md  GeosCore/       GTMM/      Interfaces/  LICENSE.txt  PKUCPL/    test/

    src/HEMCO:
    AUTHORS.txt   CMakeLists.txt  CONTRIBUTING.md  LICENSE.txt  run/  SUPPORT.md
    CHANGELOG.md  CMakeScripts/   docs/            README.md    src/

2. Creation of running directory
--------------------------------

The first thing to do after you downloaded the source code of GEOS-Chem is to create 
a run directory in which you will store your configuration files, output data files 
from the model and other files specific to a single simulation. Luckily, the source
code of GEOS-Chem is shipped with a script guiding and automating the process of
run directory creation.

 #. First, navigate to your GCClassic source code folder and get a listing of its content.

    .. code-block:: console

        cd path/to/GCClassic
        ls -CF

    You should a similar output to this one:

    .. code-block:: text

        AUTHORS.txt   CMakeLists.txt  CONTRIBUTING.md  LICENSE.txt  run@  SUPPORT.md
        CHANGELOG.md  CMakeScripts/   docs/            README.md    src/  test@

    The :file:`run@` symbolic link is pointing to the :file:`src/GEOS-Chem/run/GCClassic`
    folder in which the :file:`createRunDir.sh` file is located. 

 #. The next step is then to navigate to the run folder and get a directory listing:

    .. code-block:: console

        cd run
        ls -CF

    You should see this ouptut.

    .. code-block:: text

        archiveRun.sh*                  getRunInfo*                 HEMCO_Diagn.rc.templates/  README.md
        createRunDir.sh*                gitignore                   HISTORY.rc.templates/      runScriptSamples/
        geoschem_config.yml.templates/  HEMCO_Config.rc.templates/  init_rd.sh*

    We want to verify that that the :file:`createRunDir.sh*` executable is present.

 #. Run the :file:`createRunDir.sh` by typing:

    .. code-block:: console

        $ ./createRunDir.sh

 #. You will then be prompted with multiples messages that will guide you throughout
    the creation process of run directory by supplying some information about the 
    simulation to be created. If you are creating a run directory for the first time,
    you will be asked to fill out the first time user registration before being 
    presented the following prompt. You have to fill out this registration only once 
    and simply have to answer all the questions before being redirected to the normal 
    run directory creation process.

    .. code-block:: text

        ===========================================================
        GEOS-CHEM RUN DIRECTORY CREATION
        ===========================================================

        -----------------------------------------------------------
        Choose simulation type:
        -----------------------------------------------------------
        1. Full chemistry
        2. Aerosols only
        3. CH4
        4. CO2
        5. Hg
        6. POPs
        7. Tagged CH4
        8. Tagged CO
        9. Tagged O3
        10. TransportTracers
        11. Trace metals
        12. Carbon

    For most simulations, you will want to create a full chemistry simulation by 
    typing :command:`1` and then pressing :command:`ENTER`. 

 #. You will then be asked to specify what additionnal options of GEOS-Chem you would
   want to add to the simulations. 

    .. code-block:: text

        -----------------------------------------------------------
        Choose additional simulation option:
        -----------------------------------------------------------
        1. Standard
        2. Benchmark
        3. Complex SOA
        4. Marine POA
        5. Acid uptake on dust
        6. TOMAS
        7. APM
        8. RRTMG

    To use TOMAS microphysics in your simulations, you will have to select the options
    :command:`6` and press :command:`ENTER`.

 #. You will then be asked to specify the number of bins you want TOMAS to use to 
    form the particle distribution of the aerosols species it considers.

    .. code-block:: text

        -----------------------------------------------------------
        Choose TOMAS option:
        -----------------------------------------------------------
        1. TOMAS with 15 bins
        2. TOMAS with 40 bins

    At first it is recommended to try your simulations with **15 bins** (press :command:`1`)
    to limit the computationnal time needed to execute a simulation. Once you are 
    familliar with the model and its simulations output, you can use the **40 bins**
    option (press :command:`2`) to obtain a more precise definition of the particle 
    size distribution.

 #. Next, you will have to specify what meteorological product you want to use as 
    intput for GEOS-Chem. 

    .. code-block:: text

        -----------------------------------------------------------
        Choose meteorology source:
        -----------------------------------------------------------
        1. MERRA-2 (Recommended)
        2. GEOS-FP
        3. GISS ModelE2.1 (GCAP 2.0)

    It is recommended to use the **MERRA-2** product if possible (press :command:`1`).
    Most of the input files already downloaded on the Stetson cluster for GEOS-Chem 
    usage are **MERA-2** products.

 #. The next prompt will ask you to provide the horizontal grid resolution you want 
    to use in your simulation of GEOS-Chem. 

    .. code-block:: text

        -----------------------------------------------------------
        Choose horizontal resolution:
        -----------------------------------------------------------
        1. 4.0  x 5.0
        2. 2.0  x 2.5
        3. 0.5  x 0.625

    For shorter computation intensive global simulations at the beginning, it is 
    recommended to use the **4.0° x 5.0°** (press :command:`1`) to start. If you 
    choose the option :command:`3`, you will create a nested-grid simulation and will
    be prompted to provide a grid domain as a follow up. 

 #. After specifying your horizontal grid dimension, you will be asked to specify 
    the vertical resolution the simulation will use under the form of the number of 
    vertical pressure levels to consider in the atmosphere.

    .. code-block:: text

        -----------------------------------------------------------
        Choose number of levels:
        -----------------------------------------------------------
        1. 72 (native)
        2. 47 (reduced)

    For memory conservation purposes, you can use the **47** levels option (press 
    :command:`2`), otherwise the **72** levels (press :command:`1`) are recommended
    for more precission.

 #. You will then be prompted for the folder in which you want to create your simulation
    run directory. As you will likely have to create new run directories each time 
    you want to simulate a different time period or region, in case of nested-grid 
    simulations, we recommend that you store all your run directories inside a common 
    folder under the parent directory where you cloned the GEOS-Chem model source code.

    .. code-block:: text

        -----------------------------------------------------------
        Enter path where the run directory will be created:
        -----------------------------------------------------------

    The path you provide can be either absolute or relative. 

 #. You will then be prompted to name the run directory to be created.

    .. code-block:: text

        -----------------------------------------------------------
        Enter run directory name, or press return to use default:

        NOTE: This will be a subfolder of the path you entered above.
        -----------------------------------------------------------

    If you simply press :command:`ENTER`, the script will use your answer given to 
    the previous prompts to create a default name similar to this one 
    :file:`gc_05x0625_NA_47L_merra2_fullchem_TOMAS15`.

 #. Finally, you will be asked whether or not you want to initiate a git repository
    in the newly created run directory. 

    .. code-block:: text

        -----------------------------------------------------------
        Do you want to track run directory changes with git? (y/n)
        -----------------------------------------------------------

    Type :command:`y` and then :command:`ENTER` to track changes made to the GEOS-Chem
    configuration using Git.

 #. Once this is done, the full path to your new run directory should be displayed 
    in the command line and you will be able to navigate to that specific path to 
    modify the :ref:`GEOS-Chem configuration files` as you like.

3. Compilation of the code
--------------------------

After creating your run directory, you will have to compile the source code of GEOS-Chem 
using the relevant options for your simulation.

 #. First step is to navigate to the run directory of your simulation

    .. code-block:: console

        $ cd /data12/pgbourdon/GCC_v14.1.1/rundirs/gc_4x5_47L_merra2_fullchem_TOMAS15/

    If demanding a listing of the file in that directory you should have an output 
    similar to this one:

    .. code-block:: text

        archiveRun.sh   CreateRunDirLogs     geoschem_run.sh                 HISTORY.rc  runScriptSamples
        build           download_data.py     getRunInfo                      metrics.py  species_database.yml
        build_info      download_data.yml    HEMCO_Config.rc                 OutputDir
        cleanRunDir.sh  gcclassic            HEMCO_Config.rc.gmao_metfields  README.md
        CodeDir         geoschem_config.yml  HEMCO_Diagn.rc                  Restarts

 #. The next step is to navigate to the :file:`build` of your run directory

    .. code-block:: console

        $ cd build
    
    This directory is where CMake and your compiler will put the files they generate.

 #. Once you are in the :file:`build` folder of your run directory you will then 
    have to initialize your build directory using CMake.

    .. code-block:: console

        $ cmake ../CodeDir -DRUNDIR=.. -DTOMAS=y -DTOMAS_BINS=15 -DBPCH_DIAG=y

    The :file:`../CodeDir` symbolic link used here is refering to the GEOS-Chem 
    source code directory and is automatically included in all run directory created 
    via the :file:`./createRunDir.sh` script. The following elements of this command
    are options of the builds that can be changed. The :ref:`options choices relatives to 
    TOMAS` usage will be described in the next section.

 #. After the initialization of the build directory as been completed, you will 
    be able to build your GEOS-Chem executable using the following command:

    .. code-block:: console

        $ make -j 

    Console outputs will be returned at each steps of the installation and should 
    end with similarly to this after a successful build.

    .. code-block:: text

        [ 98%] Built target GeosCore
        Scanning dependencies of target gcclassic
        [ 98%] Building Fortran object src/CMakeFiles/gcclassic.dir/GEOS-Chem/Interfaces/GCClassic/main.F90.o
        [100%] Linking Fortran executable ../bin/gcclassic
        [100%] Built target gcclassic

 #. Once the :file:`gcclassic` executable as been built, you will be able to installation
    it using :

    .. code-block:: console

        $ make install

    You should see output similar to this one:

    .. code-block:: text

        [  1%] Built target KPP_FirstPass
        [ 11%] Built target Headers
        [ 13%] Built target JulDay
        [ 19%] Built target NcdfUtil
        [ 25%] Built target GeosUtil
        [ 27%] Built target Transport
        [ 29%] Built target HeadersHco
        [ 29%] Built target JulDayHco
        [ 33%] Built target NcdfUtilHco
        [ 35%] Built target GeosUtilHco
        [ 48%] Built target HCO
        [ 57%] Built target HCOX
        [ 58%] Built target HCOI_Shared
        [ 67%] Built target KPP
        [ 72%] Built target History
        [ 73%] Built target ObsPack
        [ 73%] Built target Isorropia
        [ 98%] Built target GeosCore
        [100%] Built target gcclassic
        Install the project...
        -- Install configuration: "Release"
        -- Installing: /data12/pgbourdon/GCC_v14.1.1_KPPtest/rundirs/gc_05x0625_NA_47L_merra2_fullchem_TOMAS15/build_info/CMakeCache.txt
        -- Installing: /data12/pgbourdon/GCC_v14.1.1_KPPtest/rundirs/gc_05x0625_NA_47L_merra2_fullchem_TOMAS15/build_info/summarize_build
        -- Installing: /data12/pgbourdon/GCC_v14.1.1_KPPtest/rundirs/gc_05x0625_NA_47L_merra2_fullchem_TOMAS15/gcclassic
        -- Set runtime path of "/data12/pgbourdon/GCC_v14.1.1_KPPtest/rundirs/gc_05x0625_NA_47L_merra2_fullchem_TOMAS15/gcclassic" to ""

    Going back one folder to your run directory main folder and looking at the file
    it contain you should now see a :file:`gcclassic` executable and a :file:`build_info`
    folder.

    .. code-block:: console

        $ cd ..
        $ ls
        archiveRun.sh   CreateRunDirLogs     getRunInfo                      metrics.py        species_database.yml
        build           download_data.py     HEMCO_Config.rc                 OutputDir
        build_info      download_data.yml    HEMCO_Config.rc.gmao_metfields  README.md
        cleanRunDir.sh  gcclassic            HEMCO_Diagn.rc                  Restarts
        CodeDir         geoschem_config.yml  HISTORY.rc                      runScriptSamples

    If this is the case, you are now ready to configure your simulations as you like 
    and run a GEOS-Chem simulation!

.. _options choices relatives to TOMAS:

4. Considerations specific to TOMAS
-----------------------------------

When compiling your GEOS-Chem simulations there are specific options that need to 
be passed to CMake in order for the simulations to work properly. In the following 
section, we will list these options and present the accepted values for those options.

.. option:: RUNDIR

    Define the path to the run directory. 

    In most case, you will use relative path :file:`-DRUNDIR=..`, since you will 
    be building from the subfolder :file:`build` of your run directory. You will 
    need to adapt the path provided here if that is not the case.

.. option:: TOMAS

    Configure GEOS-Chem with the `TOMAS aerosol microphysics package`_.

    .. option:: y

        Will activate the TOMAS package.

    .. option:: n

        Will deactivate the TOMAS package **(Default option)**

    Since TOMAS microphysics are deactivated by default when compiling GEOS-Chem, 
    it is very important to not forget to add this option when compiling the model 
    for your aerosol project.

.. option:: TOMAS_BINS

    Specifies the number of bins TOMAS will use to generate the particle size distribution. 

    .. option:: 15

        Use 15 bins 

    .. option:: 40

        Use 40 bins

    This parameter needs to be included every time you plan to use TOMAS microphysics
    and is required for compilation if :file:`-DTOMAS=y`.

.. _GEOS-Chem wiki: https://geos-chem.readthedocs.io/en/stable/index.html
.. _Github: https://github.com/geoschem
.. _TOMAS aerosol microphysics package: http://wiki.seas.harvard.edu/geos-chem/index.php/TOMAS_aerosol_microphysics