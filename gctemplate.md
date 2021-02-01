## An Environmental Variables Setting Templates for GEOS-Chem in Pace-Cluster
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/01
```
#==============================================================================
# %%%%% clear existing environment variables %%%%%
#==============================================================================
unset PERL_HOME
unset CC
unset CXX
unset FC
unset F77
unset F90
unset NETCDF_BIN
unset NETCDF_HOME
unset NETCDF_INCLUDE
unset NETCDF_LIB
unset NETCDF_FORTRAN_BIN
unset NETCDF_FORTRAN_HOME
unset NETCDF_FORTRAN_INCLUDE
unset NETCDF_FORTRAN_LIB
unset GC_BIN
unset GC_INCLUDE
unset GC_LIB
unset GC_F_BIN
unset GC_F_INCLUDE
unset GC_F_LIB
unset OMP_NUM_THREADS
unset OMP_STACKSIZE

##=============================================================================
# Load spack
export SPACK_ROOT=/storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack
source $SPACK_ROOT/share/spack/setup-env.sh

##==============================================================================
module purge
module load intel/19.0.5
echo "Spack load modules for compiler intel@19.0.5.281..."
spack load cgdb%intel@19.0.5.281
spack load gdbm%intel@19.0.5.281
spack load gmake%intel@19.0.5.281
spack load hdf5%intel@19.0.5.281
spack load perl%intel@19.0.5.281
spack load flex%intel@19.0.5.281
spack load openmpi%intel@19.0.5.281
spack load netcdf-c%intel@19.0.5.281
spack load netcdf-fortran%intel@19.0.5.281
spack load ncurses%intel@19.0.5.281
spack load ncview%intel@19.0.5.281
spack load cmake%intel@19.0.5.281

##=============================================================================
# define environment variables for compilers
##=============================================================================
export FC=ifort
export CC=icc
export CXX=icpc
export F77=ifort
export F90=ifort

# Environment variables for the netCDF C-language interface
export NETCDF_HOME=$(spack location -i netcdf-c@4.7.4%intel@19.0.5.281)
export NETCDF_BIN=$NETCDF_HOME/bin
export NETCDF_INCLUDE=$NETCDF_HOME/include
export NETCDF_LIB=$NETCDF_HOME/lib
export GC_BIN=$NETCDF_BIN
export GC_INCLUDE=$NETCDF_INCLUDE
export GC_LIB=$NETCDF_LIB

# Environment variables for the netCDF Fortran-languge interface
export NETCDF_FORTRAN_HOME=$(spack location -i netcdf-fortran@4.5.3%intel@19.0.5.281)
export NETCDF_FORTRAN_HOME_BIN=$NETCDF_FORTRAN_HOME/bin
export NETCDF_FORTRAN_HOME_INCLUDE=$NETCDF_FORTRAN_HOME/include
export NETCDF_FORTRAN_HOME_LIB=$NETCDF_FORTRAN_HOME/lib
export GC_F_BIN=$NETCDF_FORTRAN_BIN
export GC_F_INCLUDE=$NETCDF_FORTRAN_INCLUDE
export GC_F_LIB=$NETCDF_FORTRAN_LIB

```
