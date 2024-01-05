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
simulations please refer yourself to the `original outputs description page`_.

Simulations grid Data
---------------------

The gridded outputs of the GEOS-Chem model prescribed in the :file:`HISTORY.rc` are saved
in the subdirectory :file:`$SIM_RUNDIR$/OutputDir`. These outputs are saved in 
NetCDF4 files (.nc4) and can be viewed with software like Panoply or programming 
language like Python. 

It is also possible to examine the content of the resulting netCDF4 files with the 
package **ncdump**. This package is especially usefull to explore the content of 
the netCDF4 files in a quick and digestible maner directly in the terminal. A frequent
use of that package is to be able to explore the name of the variables in the NetCDF4
file before creating a program that will represent the data in human legible figures
and maps. In order to have all the variables names of the netCDF4 files printed out 
in your command terminal use the following command:

.. code-block:: bash

    ncdump -ct GEOSChem.SpeciesConc.20190701_0000z.nc4

For a full description of the capabilities of the **ncdump** package see: 
https://www.unidata.ucar.edu/software/netcdf/workshops/2011/utilities/Ncdump.html

Data Analysis scripts
---------------------

The analysis of the output data of the GEOS-Chem is best done through programming 
scripts. The use of **Python** is generally recommended, since it is easily accessible 
and there exist a good amount of exemple scripts available to new user in that 
language. The following section will provide one such exemple of script aiming to 
represent the surface concentrations of the TOMAS aerosols species on a map. 

The main packages required for the analysis of the GEOS-Chem gridded outputs with
**Python** are :

    - xarray
    - cartopy
    - matplotlib
    - numpy
    - pandas

At the beggining of the analysis script it is generally good to have a list containing
the name of the **GEOS-Chem** or **TOMAS** species that you want to include in your 
analysis. 

.. code-block:: Python

    sdhead = ['NH4', 'NIT', 'SF', 'SS', 'ECIL', 'ECOB', 'OCIL', 'OCOB', 'DUST',
          'AW', 'NK']

Here we use the *NH4* and *NIT* species from the base **GEOS-Chem** model and all 
the **TOMAS** species. 

Other variables, such as the molar weight and density of the different species 
represented are also usefull to define when wanting to represent the particle size 
distribution in term of number or mass. Some dimmensions proper to your configuration 
of **GEOS-Chem** and **TOMAS**  (e.g. # TOMAS BINS and # Pressure levels) are 
also usefull to define early in your analysis script to generalize the loops 
over those dimenssions later on. 

.. code-block:: Python

    nbins = 15  # Number of TOMAS bins
    nlevs = 72  # Number of pressure levels

    # Molecular weights (NH4, NIT, SF, SS, ECIL, ECOB, OCIL, OCOB, DUST)
    molwgt = [18., 62.01, 96., 58.5, 12., 12., 12., 12., 100., 18.]      # [g/mol]

    # Dry densities of the aerosol species
    dens = [1770.,2000.,2000.,1400.,1500.]      #kg m-3 [so4, ss, bc, oa, dust]

**Python** can easily read netCDF4 files through the use to the package *xarray*.
The following is an exemple of how it is possible to browse through a folder to 
find the :file:`.nc4` files outputed by **GEOS-Chem** and extract only the species
included in our previous list :code:Python`sdhead` at the surface. Since the 
**TOMAS** species have an extra dimension compared to the base **GEOS-Chem** 
ones for their bin number, it is necessary to treat their importation separatly. 
Starting with version 14.2.0 of **GEOS-Chem** it is also necessary to treat the 
**TOMAS** bins ranging from 1 to 9 differently then the 10 to 15 bins, since 
they now have a padding 0. 

.. code-block:: Python

    # dist = xr.open_mfdataset(data_files, combine='nested', concat_dim='Time', parallel=True)
    dist = []
    for i, file in enumerate(data_files):
    fname = os.path.join(indir, file)
    nc_file = xr.open_dataset(fname)
    data = []
    for ic, c in enumerate(sdhead):
        sizedist = []
        for k in range(0, nbins):
            if c == 'NH4' or c == 'NIT':
                sizedist.append(nc_file['SpeciesConc_'+c].isel(lev=0))
            else:
                if k < 9:
                sizedist.append(nc_file['SpeciesConc_'+c+'0'+str(k+1)].isel(lev=0))
                else:
                sizedist.append(nc_file['SpeciesConc_'+c+str(k+1)].isel(lev=0))
        sizedist = xr.concat(sizedist, dim='Size bins')
        data.append(sizedist)
    data = xr.concat(data, dim='Species')
    dist.append(data)

Once this is done, it is possible to consolidate all the species in a single array 
along a chosen dimension using the following statement:

.. code-block:: Python

    dist = xr.concat(dist, dim='time')

Since the **GEOS-Chem** model is reporting the concentration of each species in
term of mixing ratio it is often necessary to convert those mixing ratios to mass 
and number concentrations when comparing those results to in-situ instrumentation.
The assumption that the standard temperature and pressure conditions are present
in all cell at the surface is close enough to reality to be used here.

.. code-block:: Python

    # Convert GC particle mixing ratio to concentrations [cm^-3] STP
    dist[-1, :, :] = dist[-1, :, :] / 22.4E6 * 273 / 293

    # Calculate the total concentration
    total_conc = dist.sum('Size bins')

    # Convert mass species to µg m-3
    for ic, c in enumerate(molwgt):
        mass_dist[ic] = dist[ic] * c / 22.4E6 * 1E6 * 1E9

**TOMAS** define the particles sizes bins in function of the mass limits of the 
particles and not their diameters. It is then necessary to convert the mass limits 
of the particle size bins to limits in term of diameters. The below code is looping 
through all the mass size limits for either 15 or 40 bins configuration of **TOMAS**.
The mass limits are converted to volume and then to diameters afterward. The 40 
bins configuration of **TOMAS** is using a mass doubling scheme to define the bins
limits, while the 15 bins configuration of **TOMAS** is using a quadrupling scheme.
The only exception is the last bins limits of the 15 bins configuration which is  
32x larger than the previous limit.

.. code-block:: Python

    xk = np.zeros(nbins+1)  # particle mass cutoffs [kg] ... 15 bins makes 16 bins edges
    if nbins == 15:
        xk[0] = 1.6E-23         # avg mass per particle in lowest bin edge
        for i in range(1, nbins+1):
            if i < nbins-1:
                xk[i] = xk[i-1]*4
            else:
                xk[i] = xk[i-1]*32
    elif nbins == 40:
        xk[0] = 7.33E-25        # avg mass per particle in lowest bin edge
        for i in range(1, nbins+1):
            xk[i] = xk[i-1]*2

    rho_assumed = 1400.     # [kg m^-3] assumed density

    vol = xk/rho_assumed
    Dpk1 = (6.*vol/np.pi)**(1./3.)*1E6      # particle diameter cutoffs [µm]
    Dp_lower = Dpk1[:-1]                    # Extract lower limits
    Dp_upper = Dpk1[1:]                     # Extract upper limits
    
The median mass of the particles in each of the bins can than be calculated by 
using the median diameter of the particles in these respective bins with the 
following expression.

.. code-block:: Python

    pmass = xr.DataArray([(4/3 * np.pi * (Dp1/2)**3) * mw for mw in molwgt],
                     dims= ['Species', 'Size bins'])



.. _original outputs description page: https://gchp.readthedocs.io/en/stable/user-guide/output_files.html