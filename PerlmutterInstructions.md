
## Instructions to request training accounts on NERSC

1. Please create a training account by signing up at the following link:
https://iris.nersc.gov/train

2. Make sure to remember your username and password generated at the end of the sign up process.

3. Ensure the login works by opening a terminal and logging on to Perlmutter, using
`ssh <username>@perlmutter.nersc.gov`.

4. Review the following instructions to use jupyter@nersc:
https://docs.nersc.gov/services/jupyter/

5. Review the instructions to set-up Jupyter kernel with custom docker image:
https://docs.nersc.gov/services/jupyter/


## Steps to run with the CUDA Quantum container on Perlmutter.

### Step 1: Pull the image
Use this image:  `ghcr.io/1tnguyen/cuda-quantum:mpich-231710`

Pull in the image using:
```
shifterimg -v pull <image>
```
Note that this image has been configured to utilize the cuda-aware CRAY MPICH on Perlmutter.  
<br>
To see the image:
```
shifterimg images | grep -i "cuda-quantum"
```

### Step 2: Spin-up the container

#### Interactive jobs

To ask for an interactive allocation using commandline (request 1 node with 4 gpus, spawn off 1 task per gpu with each gpu being visible to each task):

```
salloc -N 1 --gpus-per-task=1 --ntasks-per-node=4 --gpu-bind=none -t 120 --qos=interactive -A <project_name> -C gpu --module=cuda-mpich --image=<image>
```

<br>
Once the allocation goes through, you should see the directory where you launched the job from.  
<br>
To run with a single gpu:
<br>

```
shifter python <filename> --target nvidia
```

<br>
To run with the `nvidia\-mgpu` (for increasing the number of qubits) backend on 1 node and 4 gpus:

```
srun -N 1 -n 4 shifter python <filename> --target nvidia-mgpu
```

#### Batch jobs
Use [this script](https://github.com/poojarao8/nersc-quantum-day/blob/master/multinode.script) to run a multi-node multi-gpu simulation.
<br>
To run with a single gpu, replace the `srun` line with 

```
shifter python ghz.py --target nvidia
```

Finally, to submit your job to the queue, use `sbatch <multinode_script>`.