## Step 1

Create a directory, let’s call it `test-cudaq`.  
<br>
Put this file in the directory. Let’s call it `ghz.py`.
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

n = 30
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
Note that this image has been configured to utilize the CRAY MPICH on nersc  
<br>
To see the image:
```
shifterimg images | grep -i "cuda-quantum"
```


### Step 3

To ask for an interactive allocation using commandline (request 1 node with 4 gpus, spawn off 1 task per gpu with each gpu being visible to each task):

```
salloc -N 1 --gpus-per-task=1 --ntasks-per-node=4 --gpu-bind=none -t 120 --qos=interactive -A m4390_g -C gpu --image=ghcr.io/1tnguyen/cuda-quantum:mpich-231023 --module=cuda-mpich
```

Replace the `m4390_g` with your own project number.  
<br>
You should be able to see your home directory in here.  
<br>
To start the container:
```
shifter /bin/bash
```

### Step 4:

To run the file with a single gpu acceleration:
```
python ghz.py --target nvidia
```

You should see the prepared GHZ state with 30 qubits.  
<br>
To submit a batch job, please copy-paste these lines and let’s call that file `multimode.script`.

```
#!/bin/bash
#SBATCH -A m4390_g
#SBATCH -C gpu
#SBATCH -q regular
#SBATCH -t 00:02:00
#SBATCH -N 256
#SBATCH --ntasks-per-node=4
#SBATCH --gpus-per-task=1
#SBATCH --gpu-bind=none
#SBATCH --image=ghcr.io/1tnguyen/cuda-quantum:mpich-231023
#SBATCH --module=cuda-mpich
 
srun -N 256 -n 1024 shifter ghz.py –target nvidia-mgpu
```

Replace `m4390_g` with your project name and adjust the run time (right now it’s set at 2 mins). In this script, I am running with 256 nodes (256x4 =1024 gpus), please adjust that number as per your requirements.If you want to run with a single gpu, remove the srun command altogether and just use the following in the above file and change `-N` to 1:
```
$shifter python ghz.py --target nvidia
```