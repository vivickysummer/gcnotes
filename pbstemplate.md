## A PBS template in Pace-Cluster
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
