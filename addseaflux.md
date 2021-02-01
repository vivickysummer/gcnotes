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
