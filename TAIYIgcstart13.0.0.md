## Run GCClassic.13.0.0 in Pace-Cluster
> By Bai Bin(*1600013520@pku.edu.cn*)\
> Last updated: 2021/02/2

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
```
copy it to your own home dir from others
```
source ~/.GC 
```
copy it to your own home dir from others
```
cmake . -DOMP=n -DRUNDIR=../ ../CodeDir
make -j
make install
```
see more details at http://wiki.seas.harvard.edu/geos-chem/index.php/Compiling_with_CMake

### 6. submit task
now you will find '**gcclassic**' in your dir
```
qsub task.job
```
see template from others
copy it to your own run directory and modify
### 7. check your task state
```
bjobs
```
