# HPC-01 Manual

## 1. Accessing HPC-01

Use SSH to connect to the HPC-01 server.

**Command:**

``` shell
ssh <username>@trongchinh.zapto.org
```

**Note:** Replace `<username>` with your assigned username.

------------------------------------------------------------------------

## 2. Folder Structure in HPC-01

### Home Directory

Your home directory is located at:

    /home/<username>

This directory should **only be used for**:

-   Installing tools
-   Storing programs or scripts
-   Managing software environments (e.g., Conda)

⚠️ Do **not store large datasets** in your home directory.

------------------------------------------------------------------------

### Working Directories

HPC-01 provides four storage locations for working data:

    /mnt/10T2/
    /mnt/12T/
    /mnt/18T/
    /mnt/22T/

These directories are intended for storing:

-   Project data
-   Intermediate files
-   Analysis results

Each user should create a personal working directory inside one of these
locations.

Example:

``` shell
# Go to the storage location
cd /mnt/18T

# Create your working directory
mkdir <your_work_dir_name>

# Set permissions for your directory
chmod 700 <your_work_dir_name>
```

`chmod 700` ensures that:

-   The **owner** has full access (read, write, execute)
-   **Other users cannot access** the directory

------------------------------------------------------------------------

### Checking Available Storage

You can check the available disk space using:

``` shell
df -h
```

------------------------------------------------------------------------

## 3. Submitting Jobs with SLURM

HPC-01 uses **SLURM** as the job scheduler.

There are two common ways to run jobs:

-   **Interactive jobs**
-   **Batch jobs**

------------------------------------------------------------------------

### 3.1 Interactive Jobs

Interactive jobs allow you to run commands directly on a compute node.

Example:

``` shell
srun --nodes=1 --ntasks=1 --cpus-per-task=4 --mem=16G --time=4:00:00 --pty /bin/bash
```

**Important parameters:**

-   `--cpus-per-task` : Number of CPU cores required for the job
-   `--mem` : Amount of RAM required
-   `--time` : Maximum runtime for the job

⚠️ On HPC-01, jobs are **normally limited to 2 days**, with some
exceptions.

More information about `srun` can be found here:

https://slurm.schedmd.com/srun.html

------------------------------------------------------------------------

### 3.2 Batch Jobs

Batch jobs are recommended for **long-running tasks or scripts that do
not require user interaction**.

Example SLURM batch script:

``` shell
#!/bin/bash
#SBATCH --job-name=test
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=4:00:00
#SBATCH --output=output.txt
#SBATCH --error=error.txt

echo "Running on $(hostname)"
sleep 60
```

Submit the job using:

``` shell
sbatch <your_file.sh>
```

------------------------------------------------------------------------

### 3.3 Monitoring and Cancelling Jobs

To view your running or pending jobs:

``` shell
squeue
```

Example output:

``` shell
(base) trongchinh@worker-01:~$ squeue
JOBID PARTITION NAME USER ST TIME NODES NODELIST(REASON)
11500 main kraken_f hoangson PD 0:00 1 (Resources)
11501 main spades_l hoangson PD 0:00 1 (Priority)
11442 main nf-NFCOR chibao R 1-16:41:41 1 worker-01
```

To cancel a job:

``` shell
scancel <your_job_ID>
```

Example:

``` shell
scancel 11500
```

------------------------------------------------------------------------

## 4. Setup a _mamba_ environment

You must initialize a mamba for your account by:

```shell
source /opt/miniforge/bin/activate
```
