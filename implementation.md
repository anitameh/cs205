### Hardware & Running the Code

Final CPU calculations were run on the Harvard Faculty of Arts and Sciences Odyssey2 cluster. Final GPU calculations were run on the GPGPU cluster on Odyssey2, which is owned by the Aspuru-Guzik group and contains 32 NVIDIA K20s, two per node. Access was provided by Sam Blau, a member of the Aspuru-Guzik group, and he ran all of the GPU calculations. Local installs of PyCUDA and SciKits, containing CUDABLAS, were performed, necessitating the following modification of Sam’s .bashrc file to run the calculations:

```
export PYTHONPATH=$HOME/pycuda/lib/python:$PYTHONPATH

Submission files for running the code on 16 CPUs and 16 GPUs are shown below:

#!/bin/sh
#SBATCH -n 16 #Number of CPUs
#SBATCH –ntasks-per-node=16 # Use cores on the same node
#SBATCH -t 120 #Runtime in minutes
#SBATCH -p aspuru-guzik #Partition to submit to
#SBATCH –mem=200000 #Memory per node in MB
#SBATCH –exclusive # Run on a node without any other jobs

module load hpc/mpi4py-1.3_python-2.7_openmpi-1.6_gcc-4.7.0thread
module load hpc/python-2.7.1_full_stack

mpiexec -n 16 python svd_approx.py > svd_approx.out

#!/bin/sh
#SBATCH -n 16 #Number of GPUs
#SBATCH –ntasks-per-node=2  # Use both GPUs on a node
#SBATCH -t 120 #Runtime in minutes
#SBATCH -p gpgpu #Partition to submit to
#SBATCH –mem=30000 #Memory per node in MB
##SBATCH –exclusive  # Run on nodes without any other jobs
##SBATCH –mem-per-cpu #Memory per cpu in MB

module load hpc/mpi4py-1.3_python-2.7_openmpi-1.6_gcc-4.7.0thread
module load hpc/python-2.7.1_full_stack
module unload hpc/gcc-4.7.0
module load hpc/gcc-4.6.1
module load hpc/cuda-5.0.35

mpiexec -n 16 python svd_approx.py > svd_approx.out
```

PyCUDA would only compile and run successfully in the presence of GCC-4.6, hence the unloading of GCC4.7 seen above. We recognize that since access to gpgpu is restricted to the Aspuru-Guzik group, repeating these calculations exactly will be almost impossible for the instructors; however, we hope that with these submission file examples the code could be run on Resonance GPUs, though memory might be a bit of an issue.

### Performance & Scaling

In order to understand the performance of our implementation CPUs versus GPUs we ran our code on one CPU and one GPU for random matrices of variable side length (1000, 5000, 8000, 10000, 15000, 20000). As can be seen below, the GPU outperforms the CPU by order of magnitude and exhibits superior scaling.

[Fig 1]

However, we could not go beyond 20,000 x 20,000 matrices on a single GPU because of the memory limitations, which would not be an issue on the CPU since Odyssey2 nodes have 256GB of memory each.

We then wanted to probe the scaling of our algorithms on CPUs and GPUs. All of these calculations were done on a total 20,000 x 20,000 random matrix, but in order to minimize communication and memory constraints, the matrix was generated piecewise on each CPU or GPU rather than being generated on a single location and distributed. This is in part why we see such excellent pure MPI speedup below:

[Fig 2]

Unfortunately, we see very little speedup from parallel GPUs. The only benefit we really gain is the additional memory available when using multiple GPUs. This could either indicate that generating the random matrices or the remaining communication are of similar speed as the matrix-vector product itself.
