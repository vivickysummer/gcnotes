## Add emissin of new species with sea-air flux in GEOS-Chem in Pace-Cluster
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/01

For sake of convenience, 
below I use **$HEMCO** as the root directory of HEMCO files,
**$GCClassic** as the root directory of GEOS-Chem 13.0.0 source code.
### 1. change fortran source code
```
cd $GCClassic/src/HEMCO/src/Extensions
ls
```
this directory is for the code of **Extensions** controlling emission other than those in **Core** Extensions.\
you can find **hcox_seaflux_mod.F90** which is in charge of the seaflux emission of several species now.
```
vi hcox_seaflux_mod.F90
```
the basic description of seaflux computation is as below:
> The oceanic flux is parameterized according to Liss and Slater, 1974:\
> F = K<sub>g</sub> * ( C<sub>air</sub> - H·C<sub>water</sub> )\
> where F is the net flux, \
> K<sub>g</sub> is the exchange velocity, \
> C<sub>air</sub> and C<sub>water</sub> are the air and aqueous concentrations, respectively, \
> and H is the dimensionless air over water Henry constant. \
> This module calculates the source and sink terms separately. The source is given as flux, the sink as deposition rate: \
> source = K<sub>g</sub> * H * C<sub>water</sub>     [kg m<sup>-2</sup> s<sup>-1</sup>] \
> sink   = K<sub>g</sub> / DEPHEIGHT      [s<sup>-1</sup>] \
> The deposition rate is obtained by dividing the exchange velocity by the deposition height DEPHEIGHT, e.g. the height over which deposition occurs. \
> This can be either the first grid box only, or the entire planetary boundary layer. \
> The HEMCO option 'PBL_DRYDEP' determines which option is being used. \
> K<sub>g</sub> is calculated following Johnson, 2010, which is largely based on the work of Nightingale et al., 2000a/b. \
>  The salinity and seawater pH are currently set to constant global values  of 35 ppt and 8.0, respectively. \
