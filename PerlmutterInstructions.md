
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

### Step 1

Create a directory, let’s call it `test-cudaq`.  
<br>
Copy-paste the following code block in a file in the directory. Let’s call it `ghz.py`.
``` 
import cudaq

def ghz_state(N):
    kernel = cudaq.make_kernel()
    q = kernel.qalloc(N)
    kernel.h(q[0])
    for i in range(N - 1):
      kernel.cx(q[i], q[i + 1])
 
    kernel.mz(q)
    return kernel

n = 32
print("Preparing GHZ state for", n, "qubits.")
kernel = ghz_state(n)
counts = cudaq.sample(kernel)
counts.dump()
```



### Step 2

Pull in the image using:
```
shifterimg -v pull ghcr.io/1tnguyen/cuda-quantum:mpich-231710
```
Note that this image has been configured to utilize the cuda-aware CRAY MPICH on Perlmutter.  
<br>
To see the image:
```
shifterimg images | grep -i "cuda-quantum"
```


### Interactive jobs

To ask for an interactive allocation using commandline (request 1 node with 4 gpus, spawn off 1 task per gpu with each gpu being visible to each task):

```
salloc -N 1 --gpus-per-task=1 --ntasks-per-node=4 --gpu-bind=none -t 120 --qos=interactive -A mXXXX_g -C gpu --module=cuda-mpich --image=ghcr.io/1tnguyen/cuda-quantum:mpich-231023
```

Replace the `mXXXX_g` with your own project number.  
<br>
Once the allocation goes through, you should see the directory where you launched the job from.  
<br>

To run with a single gpu:
```
shifter python ghz.py --target nvidia
```

You should see the prepared GHZ state with 32 qubits.  
<br>

To run interactively with more qubits, you can use the `nvidia-mgpu` backend (1 node, 4 gpus):
```
srun -N 1 -n 4 shifter python ghz.py --target nvidia-mgpu
```

### Batch jobs
To submit a batch job that will run across multiple nodes and multiple gpus, please copy-paste these lines and let’s call that file `multimode.script`.

```
#!/bin/bash
#SBATCH -A mXXXX_g
#SBATCH -C gpu
#SBATCH -q regular
#SBATCH -t 00:02:00
#SBATCH -N 256
#SBATCH --ntasks-per-node=4
#SBATCH --gpus-per-task=1
#SBATCH --gpu-bind=none
#SBATCH --module=cuda-mpich
#SBATCH --image=ghcr.io/1tnguyen/cuda-quantum:mpich-231023
 
srun -N 256 -n 1024 shifter ghz.py –target nvidia-mgpu
```

Replace `mXXXX_g` with your project name and adjust the wall clock time (set at 2 mins in the snapshot above). In this script, I am running the `ghz.py` file with 40 qubits using 256 nodes (256x4 = 1024 gpus), please change the parameters as per your requirements. If you want to run with a single gpu, adjust the number of nodes, etc. and replace `srun -N 256 -n 1024 shifter ghz.py –target nvidia-mgpu` with:
```
$shifter python ghz.py --target nvidia
```

Finally, to submit your job to the queue, use `sbatch multinode.script`.