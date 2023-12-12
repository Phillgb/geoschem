Outputs
=======

This section will address and describe the main outputs of GEOS-Chem TOMAS simulations 
and try to guide the user on how to interpret those outputs. 

Where to find them
------------------

The outputs of the GEOS-Chem simulations are all written in the run directory of
there respective simulations. If the simulation was configured to output midrun
checkpoint restart files, those are created in the subdirectory 
:file:`$SIM_RUNDIR$/Restarts`. All diagnosis files from the collections enabled 
in the :file:`HISTORY.rc` configuration file are created in the subdirectory 
:file:`$SIM_RUNDIR$/OutputDir`.

Log Files
---------

Multiples log files are created and saved in the home run directory of each 
simulations when the model is executed. The main file of interest for most user
is the :file:`./runlog.txt` file containing the general console outputs of the 
model, but for a more complete description of all log files available after each
simulations please refer yourself to the :ref:`original Output description page`.

Simulations grid Data
---------------------

How to visualize NetCDF Files
-----------------------------

Data Analysis scripts
---------------------

.. _original Output description page: https://gchp.readthedocs.io/en/stable/user-guide/output_files.html