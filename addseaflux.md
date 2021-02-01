## Add emissin of new species with sea-air flux in GEOS-Chem-Use CH<sub>3</sub>I as example
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/01

For sake of convenience, 
below I use **$HEMCO** as the root directory of HEMCO files,
**$GCClassic** as the root directory of GEOS-Chem 13.0.0 source code.
### 1. check your new HEMCO nc file input
Assume my new CH3I ocean concentration map is stored as : **$HEMCO/CH3I/v2021-01/MONTHLY.OCEAN.CH3I.$YYYY.nc** \
firstly I need to determining if your netCDF file is **COARDS-compliant**, i.e. GEOS-Chem-accepted\
A script **isCoards** in this path: **$GCClassic/src/GEOS-Chem/NcdfUtil/perl/** can be used to examine \
```
ls ~/p-pliu40-0/GC/GCClassic.13.0.0/src/GEOS-Chem/NcdfUtil/perl/isCoards
```
To use this script, you need to load package **ncl** to activate **ncdump** command firstly by:
```
source /storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack/share/spack/setup-env.sh
spack load ncl
```
After that, you can check your nc file with this command:
```
$GCClassic/src/GEOS-Chem/NcdfUtil/perl/isCoards $HEMCO/CH3I/v2021-01/MONTHLY.OCEAN.CH3I.$YYYY.nc
```
the output is like this:
```
The following items adhere to the COARDS standard:
---------------------------------------------------------------------------
-> Dimension "lon" adheres to standard usage
-> Dimension "lat" adheres to standard usage
-> Dimension "time" adheres to standard usage
-> lon(lon)
-> lon is monotonically increasing
-> lon:units = "degrees_east"
-> lat(lat)
-> lat is monotonically increasing
-> lat:units = "degrees_north"
-> time(time)
-> time is monotonically increasing
-> time:calendar = "proleptic_gregorian"
-> time:units = "days since 2003-01-01 00:00:00"
-> CH3I_OCEAN(time,lat,lon)
-> CH3I_OCEAN:long_name = NaNf

The following items DO NOT ADHERE to the COARDS standard:
---------------------------------------------------------------------------
-> lon:long_name (or lon:standard_name) is missing
-> lat:long_name (or lat:standard_name) is missing
-> time:long_name (or time:standard_name) is missing
-> CH3I_OCEAN:long_name (or CH3I_OCEAN:standard_name) is missing
-> CH3I_OCEAN:units is missing
-> The "conventions" global attribute is missing
-> The "history" global attribute is missing
-> The "title" global attribute is missing

The following optional items are RECOMMENDED:
---------------------------------------------------------------------------
-> Consider adding lon:axis ="X"
-> Consider adding lat:axis = "Y"
-> Consider adding time:axis = "T"
-> Consider adding CH3I_OCEAN:missing_value
-> Consider adding CH3I_OCEAN:add_offset
-> Consider adding CH3I_OCEAN:scale_factor
-> Consider adding the "format" global attribute
-> Consider adding the "references" global attribute

For more information how to fix non COARDS-compliant items, see:
http://wiki.geos-chem.org/Preparing_data_files_for_use_with_HEMCO
```
according to this, you can change the attribute which *DO NOT ADHERE to the COARDS standard* of your nc file using Python **xaary**, here is an example of python script:
```
import xarray as xr
CH3I = xr.open_mfdataset('$HEMCO/CH3I/monthly/v202101/MONTHLY.OCEAN.CH3I.2010.nc')
CH3I.lat.attrs['units'] = 'degrees_north'
CH3I.lat.attrs['standard_name'] = 'latitude'
CH3I.lat.attrs['long_name'] = 'Latitude'
CH3I.lat.attrs['axis'] = 'X'
CH3I.lon.attrs['units'] = 'degrees_east'
CH3I.lon.attrs['standard_name'] = 'longitude'
CH3I.lon.attrs['long_name'] = 'Longitude'
CH3I.lon.attrs['axis'] = 'Y'
CH3I.time.attrs['standard_name'] = 'time'
CH3I.time.attrs['long_name'] = 'Time'
CH3I.time.attrs['calender'] = 'standard'
CH3I.time.attrs['axis'] = 'T'
CH3I.CH3I_OCEAN.attrs['units'] = 'kg/m3'
CH3I.CH3I_OCEAN.attrs['long_name'] = 'global ocean methyl iodide concentration'
CH3I.attrs['Conventions'] = "COARDS"
CH3I.attrs['history'] = "Mon Jan 01 00:00:00 2021 UTC"
CH3I.attrs['Title'] = "COARDS/netCDF file containing ocean methyl iodide concentration data"
CH3I.to_netcdf('$HEMCO/CH3I/monthly/v202101/MONTHLY.OCEAN.CH3I.2010.Examined.nc')
```
make sure your nc file is adhere to GEOS-Chem requirement, and go on to next step.\
for more information, see http://wiki.seas.harvard.edu/geos-chem/index.php/The_COARDS_netCDF_conventions_for_earth_science_data.

### 2. check species information in GEOS-Chem
there is a file **species_database.yml** in path **$GCClassic/src/GEOS-Chem/run/GEOS**, which is used to store species information used in GEOS-Chem.
```
cd $GCClassic/src/GEOS-Chem/run/GEOS
vi species_database.yml
```
you can find the detailed explanation for this file in http://wiki.seas.harvard.edu/geos-chem/index.php/Guide_to_species_in_GEOS-Chem \
since **henry law constant** is need for seaflux computation, if it's not prepared for your species, you need to add by your own. \
Once the **henry law constant** for your species is deployed, go on to next step.
### 3. change fortran source code
```
cd $GCClassic/src/HEMCO/src/Extensions
ls
```
this directory is for the code of **Extensions** controlling emission other than those in **base** Extensions.\
you can find **hcox_seaflux_mod.F90** which is in charge of the seaflux emission of several species now.
```
vi hcox_seaflux_mod.F90
```
the basic description of seaflux computation is as below:
> The oceanic flux is parameterized according to Liss and Slater, 1974:

> **F = K<sub>g</sub> x ( C<sub>air</sub> - H·C<sub>water</sub> )**

> where F is the net flux, \
> K<sub>g</sub> is the exchange velocity, \
> C<sub>air</sub> and C<sub>water</sub> are the air and aqueous concentrations, respectively, \
> and H is the dimensionless air over water Henry constant. \
> This module calculates the source and sink terms separately. The source is given as flux, the sink as deposition rate:

> source = K<sub>g</sub> x H x C<sub>water</sub>     [kg m<sup>-2</sup> s<sup>-1</sup>] \
> sink   = K<sub>g</sub> / DEPHEIGHT      [s<sup>-1</sup>] 

> The deposition rate is obtained by dividing the exchange velocity by the deposition height DEPHEIGHT, e.g. the height over which deposition occurs. \
> This can be either the first grid box only, or the entire planetary boundary layer. \
> The HEMCO option 'PBL_DRYDEP' determines which option is being used. \
> K<sub>g</sub> is calculated following Johnson, 2010, which is largely based on the work of Nightingale et al., 2000a/b. \
>  The salinity and seawater pH are currently set to constant global values  of 35 ppt and 8.0, respectively. 

go to **LINE 690** of **hcox_seaflux_mod.F90**, the code here is:
```
    ! ----------------------------------------------------------------------
    ! Get species IDs and settings
    ! ----------------------------------------------------------------------

    ! # of species for which air-sea exchange will be calculated
    Inst%nOcSpc = 7  ! updated to include MENO3, ETNO3, MOH

    ! Initialize vector w/ species information
    ALLOCATE ( Inst%OcSpecs(Inst%nOcSpc) )
    DO I = 1, Inst%nOcSpc
       Inst%OcSpecs(I)%HcoID      = -1
       Inst%OcSpecs(I)%OcSpcName  = ''
       Inst%OcSpecs(I)%OcDataName = ''
       Inst%OcSpecs(I)%LiqVol     = 0d0
       Inst%OcSpecs(I)%SCWPAR     = 1
    ENDDO

    ! Counter
    I = 0

    ! ----------------------------------------------------------------------
    ! CH3I:
    ! ----------------------------------------------------------------------

    I = I + 1
    IF ( I > Inst%nOcSpc ) THEN
       CALL HCO_ERROR ( HcoState%Config%Err, ERR, RC )
       RETURN
    ENDIF

    Inst%OcSpecs(I)%OcSpcName  = 'CH3I'
    Inst%OcSpecs(I)%OcDataName = 'CH3I_SEAWATER'
    Inst%OcSpecs(I)%LiqVol     = 1d0*7d0 + 3d0*7d0 + 1d0*38.5d0 ! Johnson, 2010
    Inst%OcSpecs(I)%SCWPAR     = 1 ! Schmidt number following Johnson, 2010

```
Copy a loop and change the **OcSpcName** and **OcDataName** and conresponding **LiqVol** and **SCWPAR** and add **Inst%nOcSpc** by 1. \
Here, **OcSpcName** should exactly be the species name defined in GEOS-Chem, \
**OcDataName** is the array used to pass data, no restriction yet better to be self-explained, \
refer to the description in source code file about **LiqVol** and **SCWPAR**. 

### 4.compile 
About how to run newest GCClassic.13.0.0, refer to https://github.com/baib-EAS/gcnotes/blob/main/gcstart13.0.0.md
go to run directory:
```
cd ~/p-pliu40-0/GC/rundirs/gc_4x5_fullchem/
cd build
source ~/.bashrc
source ~/.GC
cmake . -DOMP=n -DRUNDIR=../ ../CodeDir
make -j
make install
cd ..
```
it would be quite quick if you have compile and build before, since only changed part need to be re-compiled.
### 5. change HEMCO Configuration file
```
vi HEMCO_Config.rc
```
firstly, add your species in **LINE 129**, here I add CH3I
```
101     SeaFlux                : on    DMS/ACET/ALD2/MENO3/ETNO3/MOH/CH3I
```
then you need to change the extension setting below around **LINE 2396**
```
#==============================================================================
# --- Seawater concentrations for oceanic emissions (Extension 101) ---
#==============================================================================
(((SeaFlux
101 CH3I_SEAWATER  $ROOT/CH3I/v2021-01/MONTHLY.CH3I.$YYYYT00:00:00.000000000.nc   CH3I_OCEAN 2003-2018/1-12/1/0 C xy kg/m3  CH3I  -  1 1
101 DMS_SEAWATER    $ROOT/DMS/v2015-07/DMS_lana.geos.1x1.nc                       DMS_OCEAN  1985/1-12/1/0 C xy kg/m3  DMS   -  1 1
...
)))SeaFlux

```
**101** is the unique number of seaflux extension;\
**CH3I_SEAWATER** is the array name you defined in **hcox_seaflux_mod.F90**;\
**$ROOT/CH3I/v2021-01/MONTHLY.CH3I.$YYYYT00:00:00.000000000.nc** is your nc file path and token is allowed;\
**CH3I_OCEAN** is the variable name in you nc file;\
**CH3I_OCEAN 2003-2018/1-12/1/0** means the time span and frequency of your nc file;\
**CH3I** is the exact species name in GEOS-Chem;\
for more detailed information, see http://wiki.seas.harvard.edu/geos-chem/index.php/The_HEMCO_User%27s_Guide#Extension_switches.

Once you've set your configuration file properly, you can start to use your new emission!!!
