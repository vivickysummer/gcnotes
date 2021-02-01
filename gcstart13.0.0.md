## Run GCClassic.13.0.0 in Pace-Cluster
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/01\

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
```
cd GC
git clone https://github.com/geoschem/GCClassic.git GCClassic.13.0.0
```
### 5. get GEOS-Chem and HEMCO as sub-modules
```
cd GCClassic
git submodule update --init --recursive
```
you may add '**alias gsu="git submodule update --init --recursive"**' in **~/.bashrc** for convenience
### 6. copy run directory
```
cd run/
./createRunDir.sh
```
need to type ExtData dir for first time:
```
/storage/coda1/p-pliu40/0/shared/GEOS-Chem/data/ExtData
```
and finish your options and set dir to be copied into\
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/Creating_run_directories_for_GEOS-Chem_13.0.0_and_later

### 7. set GEOS-Chem task
come to the directory you just created, here I show my setting:
```
cd ~/p-pliu40-0/GC/rundirs/gc_4x5_fullchem/
vi input.geos          # change run time and others
vi HEMCO.Config.rc     # shut-down several offline emissions and use online extensions
vi HISTORY.rc          # choose preferred output
```
see more at http://wiki.seas.harvard.edu/geos-chem/index.php/GEOS-Chem_configuration_files
### 8. build run environment
do below in your run directory, here i.e. **~/p-pliu40-0/GC/rundirs/gc_4x5_fullchem/**
```
cd build
source /storage/coda1/p-pliu40/0/shared/GEOS-Chem/.bashrc 
```
copy it to your own home dir so you can just **source ~/.bashrc**\
you can find a template as **gcbashtemplate.md** in this git dir
```
source /storage/coda1/p-pliu40/0/shared/GEOS-Chem/.GC 
```
copy it to your own home dir so you can just **source ~/.GC**\
you can find a template as **gctemplate.md** in this git dir
```
cmake . -DOMP=n -DRUNDIR=../ ../CodeDir
make -j
make install
```
see more details at http://wiki.seas.harvard.edu/geos-chem/index.php/Compiling_with_CMake

### 9. submit task
now you will find '**gcclassic**' in your dir
```
qsub task.pbs
```
see template in **/storage/coda1/p-pliu40/0/shared/GEOS-Chem/.templategc13.pbs**\
or you can find it as **pbstemplate.md** in this git dir\
copy it to your own run dir and modify
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
checkjob <job id>            # check job status
```
