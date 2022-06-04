# wandb
Note: `taro` is my local machine. Replace all instances of `taro` with yours, and all instances of `awilf` with your andrewid. Atlas is the name of our cluster; it can be any slurm based cluster.

This is just a simple example to get you started.

## Motivation

## Installing dependencies
`torch, torchvision, wandb`

## Development on Local

## Deploying Grid Searches on Atlas
### Installing
On Atlas `clone` this repo into `/work/awilf/`
Create a conda env with `wandb` in it. I called mine `sweep`.

### To Run
1. On Atlas initialize the sweep
```
cd /work/awilf/wandb_example
conda activate sweep
wandb sweep config.yaml
```

2. Edit the `.sbatch` file describing what your worker nodes will do
The above command will print the full sweep name last.  Make this the last line of your `sweep.sbatch` script, e.g.
```
#!/bin/bash
#SBATCH...
...
singularity exec...
<entity/project/sweepid> # e.g. socialiq/wandb_example/yb5sssqu
```

You should also edit the other inputs to `sbatch`, e.g. `--output`...etc. 

If you edited this script on your local machine, make sure to pull from your local to update your script on Atlas.
```
rsync -av awilf@taro:/work/awilf/wandb_example /work/awilf/
```

3. Initiate the sweep
Paste this command as many times as nodes as you'd like to parallelize across.  Good practice for Atlas is to cap it at 8 so we don't flood the cluster.
```
sbatch sweep.sbatch
sbatch sweep.sbatch
sbatch sweep.sbatch
...
```

Check out the link on your wandb page to watch the sweep in action. 
**TODO: email thing

### To Debug
Do (1) and (2) but then provision an interactive node via `srun`
```
srun -p gpu_low --gres=gpu:1 --mem=10GB --pty bash
```

Once inside, make it a `wandb agent` child process.
```
singularity exec -B /work/awilf/ --nv /results/awilf/imgs/tvqa_graph.sif \
wandb agent <str/from/above/here>
```

### To Set Up

