## Run GCClassic.13.0.0 in Pace-Cluster
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/01

More Info at http://wiki.seas.harvard.edu/geos-chem/index.php/Getting_Started_with_GEOS-Chem
********************************************************************
if you are in Pengfei Liu’s group at Gatech\
skip step 1-3 and start from step 4\
spack is already installed!!!
********************************************************************
### 1. download SPACK in home directory
```
git clone https://github.com/spack/spack.git
```
### 2. load SPACK
```
export SPACK_ROOT=/storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack
source $SPACK_ROOT/share/spack/setup-env.sh
```
or just
```
source /storage/coda1/p-pliu40/0/shared/GEOS-Chem/spack/share/spack/setup-env.sh
```
### 3. download Packages Using 'spack install'
```
module load intel/19.0.5
time spack install netcdf-fortran %intel@19.0.5.281
time spack install flex %intel@19.0.5.281
time spack install cmake %intel@19.0.5.281
time spack install gmake %intel@19.0.5.281
time spack install ncview %intel@19.0.5.281
spack load texinfo
time spack install cgbd %intel@19.0.5.281
time spack install ncl %intel@19.0.5.281
```
see more at  https://github.com/geoschem/geos-chem-cloud/issues/35\
********************************************************************
if you are in Pengfei Liu’s group at Gatech\
just start from here (spack is already installed)!!
********************************************************************
### 4. download Source Code in your GEOS-Chem directory ('GC' for me)
remember to **source ~/.bashrc** firstly if you haven't after this log-in
```
cd GC
git clone https://github.com/geoschem/GCClassic.git GCClassic.13.0.0
```
### 5. get GEOS-Chem and HEMCO as sub-modules
```
cd GCClassic.13.0.0
```
examine your folder by
```
ls -CF src/*
```
and fetch GEOS-Chem and HEMCO file by
```
git submodule update --init --recursive
```
you may add '**alias gsu="git submodule update --init --recursive"**' in **~/.bashrc** for convenience
### 6. copy run directory
```
cd run/
./createRunDir.sh
```
need to type ExtData directory for first time:
```
/storage/coda1/p-pliu40/0/shared/GEOS-Chem/data/ExtData
```
finish your options and set directory to be copied into\
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/Creating_run_directories_for_GEOS-Chem_13.0.0_and_later

### 7. set GEOS-Chem task
come to the directory you just created, here I show my setting:
```
cd ~/p-pliu40-0/GC/rundirs/gc_4x5_fullchem/
vi input.geos          # change run time and others
vi HEMCO_Config.rc     # shut-down several offline emissions and use online extensions
vi HISTORY.rc          # choose preferred output
```
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/GEOS-Chem_configuration_files
### 8. build run environment
do below in your run directory, here i.e. **~/p-pliu40-0/GC/rundirs/gc_4x5_fullchem/**
```
cd build
source ~/.bashrc 
source ~/.GC
```
a bashrc template is here, copy it to your own home dir
```
# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

alias la='ls -a'
alias ll='ls -lhp'
alias cp='cp -pr'
alias rm='rm -i'

# Max out machine limits
ulimit -t unlimited              # cputime
ulimit -f unlimited              # filesize
ulimit -d unlimited              # datasize
ulimit -s unlimited              # stacksize
ulimit -c unlimited              # coredumpsize
ulimit -m unlimited              # memoryuse
ulimit -v unlimited              # vmemoryuse
ulimit -l unlimited              # memorylocked
```
a gc template is here, copy it to your own home dir
```
# .bashrc
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
after that
```
cmake . -DOMP=n -DRUNDIR=../ ../CodeDir
make -j
make install
cd ..
ls
```
see more details at http://wiki.seas.harvard.edu/geos-chem/index.php/Compiling_with_CMake

### 9. submit task
now you will find '**gcclassic**' in your directory, and you can submit your task by
```
qsub task.pbs
```
a template is here, copy it to your own run directory and modify
```
#PBS -A GT-pliu40
#PBS -N GC
#PBS -l nodes=1:ppn=16
#PBS -l mem=128gb
#PBS -l walltime=50:00:00
#PBS -q inferno
#PBS -j oe
#PBS -o gettingStarted.out

source ~/.bashrc
source ~/.GC

export OMP_NUM_THREADS=16
export OMP_STACKSIZE=128gb

cd $PBS_O_WORKDIR

./gcclassic > GC.log

exit 0
```
### 10. check your task state
```
qstat -u <username>
```
to check your name, use
```
whoami
```
### 11. other useful commands
```
qdel <job id>                # kill task
pace-check-queue -c inferno  # check nodes state
pace-check-queue -s inferno  # check nodes property
checkjob <job id>            # check job problem
```
