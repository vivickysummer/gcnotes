## Run GCClassic.13.0.0 in Taiyi-Cluster
> By Bai Bin(*baib@mail.sustech.edu.cn*)\
> Last updated: 2021/02/22

More Info at http://wiki.seas.harvard.edu/geos-chem/index.php/Getting_Started_with_GEOS-Chem

### 1. download Source Code in your GEOS-Chem directory ('GC' for me)
```
cd GC
git clone https://github.com/geoschem/GCClassic.git GCClassic.13.0.0
```
### 2. get GEOS-Chem and HEMCO as sub-modules
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
### 3. copy run directory
```
cd run/
./createRunDir.sh
```
need to type ExtData directory for first time:
```
/data/ese-shul/data/ExtData
```
finish your options and set directory to be copied into\
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/Creating_run_directories_for_GEOS-Chem_13.0.0_and_later

### 4. set GEOS-Chem task
```
cd /your/run/directory/
vi input.geos          # change run time and others
vi HEMCO_Config.rc     # shut-down several offline emissions and use online extensions
vi HISTORY.rc          # choose preferred output
```
additionally, you need to change you MET FIELD in **HEMCO_Config.rc** here:
```
ROOT:                        /data/ese-shul/data/ExtData/HEMCO
METDIR:                      /your/met/file/data/ExtData/GEOS_4x5/MERRA2
```
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/GEOS-Chem_configuration_files
### 5. build run environment
do below in your run directory
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
#
##==============================================================================
# Load spack
export SPACK_ROOT=/work/ese-baib/spack
source $SPACK_ROOT/share/spack/setup-env.sh

module purge
echo "Loading modules for compiler intel@18.0.3..."
spack load perl%intel@18.0.3
spack load flex%intel@18.0.3
spack load openmpi%intel@18.0.3
spack load netcdf-c%intel@18.0.3
spack load netcdf-fortran%intel@18.0.3
#spack load nco%intel@18.0.3
spack load ncview%intel@18.0.3
spack load cmake%intel@18.0.3

##=============================================================================
# define environment variables for compilers
##=============================================================================
export FC=ifort
export CC=icc
export CXX=icpc
export F77=ifort
export F90=ifort

# Environment variables for the netCDF C-language interface
export NETCDF_HOME=$(spack location -i netcdf-c@4.7.3%intel@18.0.3)
export NETCDF_BIN=$NETCDF_HOME/bin
export NETCDF_INCLUDE=$NETCDF_HOME/include
export NETCDF_LIB=$NETCDF_HOME/lib
export GC_BIN=$NETCDF_BIN
export GC_INCLUDE=$NETCDF_INCLUDE
export GC_LIB=$NETCDF_LIB

# Environment variables for the netCDF Fortran-languge interface
export NETCDF_FORTRAN_HOME=$(spack location -i netcdf-fortran@4.5.2%intel@18.0.3)
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

### 6. submit task
now you will find '**gcclassic**' in your run directory, you can submit your task by
```
bsub < task.job
```
a template is here, copy it to your own run directory and modify your path and other settings if needed
```
#!/bin/bash
#BSUB -J TORERO
#BSUB -q short
#BSUB -n 40
#BSUB -R "span[ptile=40]"
#BSUB -W 360:00
#BSUB -o run_%J.out
#BSUB -e run_%J.err

source ~/.GC
source ~/.bashrc

export OMP_NUM_THREADS=40
export OMP_STACKSIZE=2gb

cd /your/run/directory/

time ./gcclassic >> GC.log

exit 0
```
### 7. check your task state
```
bjobs
```
