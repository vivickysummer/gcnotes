## Run GCClassic.13.0.0 in Pace-Cluster
> By Bai Bin(1600013520@pku.edu.cn)
> Last updated: 2021/02/01
More Info at http://wiki.seas.harvard.edu/geos-chem/index.php/Getting_Started_with_GEOS-Chem

\##==================================================================
\# if you are in Pengfei Liu’s group at Gatech
\# skip step 1-3 and start from step 4
\# spack is already installed!!!
\##==================================================================

### 1. download SPACK in home directory
git clone https://github.com/spack/spack.git

### 2. load SPACK
> export SPACK_ROOT=/storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack
> source $SPACK_ROOT/share/spack/setup-env.sh
\# or just
\# source /storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack/share/spack/setup-env.sh

### 3. download Packages Using 'spack install'
> module load intel/19.0.5
> time spack install netcdf-fortran %intel@19.0.5.281
> time spack install flex %intel@19.0.5.281
> time spack install cmake %intel@19.0.5.281
> time spack install gmake %intel@19.0.5.281
> time spack install ncview %intel@19.0.5.281
> spack load texinfo
> time spack install cgbd %intel@19.0.5.281
> time spack install ncl %intel@19.0.5.281
\# see also https://github.com/geoschem/geos-chem-cloud/issues/35

##==================================================================
\# if you are in Pengfei Liu’s group at Gatech
\# just start from here (spack is already installed)!!
##==================================================================

