# Tutorial: Using Visual Studio Code with Slurm

**Note:** This tutorial is compiled and adjusted from the tutorial on the NGS101 blog post:
[No More Command-Line Only: Run Jupyter Lab, RStudio, and VS Code Interactively in Your Browser on Any HPC Cluster with Pixi](https://ngs101.com/no-more-command-line-only-run-jupyter-lab-rstudio-and-vs-code-interactively-in-your-browser-on-any-hpc-cluster-with-pixi/) by Giang Nguyen.

## 1. Connect to HPC-01

To connect to HPC-01, use the following command in your terminal:

```shell
ssh <username>@trongchinh.zapto.org -p 6820
```

## 2. Activate base mamba environment

Once you are connected to HPC-01, activate the base mamba environment by:

```shell
source /opt/miniforge/bin/activate
```

This action allows you to access the base environment on HPC-01, which includes essential tools and libraries for remote connection.

In case you have your own conda/mamba that was installed locally, you must install some essential packages in your environment on HPC-01 to ensure that the remote connection works properly. You can do this by running:

```shell
mamba install -c conda-forge jupyterlab         ### Install JupyterLab
mamba install -c conda-forge code-server        ### Install code-server
mamba install screen                            ### Install screen for session management
```

## 3. Start new screen session and submit code-server or jupyterlab job

To start a new screen session, run the following command:

```shell
screen -S code-server
```

After starting the screen session, you can submit a job to run code-server or JupyterLab using the `srun` command. For example:

```shell
srun --mem=8GB --cpus-per-task=4 --time=4:00:00 --pty bash
```

**Note:**

1. 'code-server' is the name of the screen session. You can choose any name you like.
2. You can adjust the memory, CPU, and time parameters according to your needs. The maximum runtime for jobs on HPC-01 is normally limited to 2 days, with some exceptions.

Once you are in the new screen session, you can start code-server or JupyterLab by running:

```shell
code-server --bind-addr 0.0.0.0:8888        ### Start code-server on port 8888
```

or

```shell
### Start JupyterLab on port 8888
jupyter-lab --no-browser --ip=0.0.0.0 --port=8888 --ServerApp.allow_remote_access=True
# Prints: http://worker-01:8888/lab?token=abc123...
```

**Note:** 

1. This session will keep running even if you disconnect from HPC-01, allowing you to reconnect later and continue working.
Therefore, you can detach from the screen session by pressing `Ctrl + A` followed by `D`. To reattach to the screen session later, use:

```shell
screen -r code-server
```

2. Tmux is another terminal multiplexer that you can use instead of screen. It offers more features and better session management.

## 4. Access code-server or JupyterLab from your local machine

To access code-server or JupyterLab from your local machine, you need to set up an SSH tunnel. Run the following command in your local terminal:

```shell
ssh -N -L 8888:worker-01:8888 <username>@trongchinh.zapto.org -p 6820
```

### 4.1. Access code-server

After setting up the SSH tunnel, you can access code-server by opening your web browser and navigating to:

```
http://localhost:8888
```

After accessing the URL, you will be prompted to enter a password. The password is in a code-server configuration file located at `~/.config/code-server/config.yaml` on HPC-01. You can view the password by running:

```shell
cat ~/.config/code-server/config.yaml
### Example output:
### bind-addr: ...
### auth: password
### password: ...
### cert: false
```

### 4.2. Access JupyterLab

After setting up the SSH tunnel, you can access JupyterLab by opening your web browser and navigating to:

```
http://localhost:8888/lab?token=abc123...
```

Please note that the URL is provided in the output when you start JupyterLab.