# IsaacLab-Arena-Tutorial-with-Docker

# Official Documentation 
https://developer.nvidia.com/blog/simplify-generalist-robot-policy-evaluation-in-simulation-with-nvidia-isaac-lab-arena
https://isaac-sim.github.io/IsaacLab-Arena/main/index.html
https://isaac-sim.github.io/IsaacLab-Arena/main/pages/quickstart/installation.html
https://github.com/isaac-sim/IsaacLab-Arena

# Pre-Requisites
- Have Ubuntu 22.04 installed. check ``wsl --list --verbose``
- Have Docker Desktop installed and running (open it and let it running on the background): check ``docker info | findstr "Operating System"``. You should see something like: Operating System: Docker Desktop
- Ubuntu has GPU access: test open Ubuntu and run ``docker run --rm --gpus all nvidia/cuda:12.2.0-base-ubuntu22.04 nvidia-smi``. You should see your GPU info if everything is configured correctly.

# Clone Arena
-> Open Docker Desktop and let it run on the background
-> Open an Ubuntu terminal and inside it, do:


- Clone repo
```
git clone https://github.com/isaac-sim/IsaacLab-Arena.git
```

- navigate to folder: ``cd IsaacLab-Arena``
  
- initialize submodules
```
git config submodule.submodules/IsaacLab.url https://github.com/isaac-sim/IsaacLab.git
git config submodule.submodules/Isaac-GR00T.url https://github.com/NVIDIA/Isaac-GR00T.git

git submodule update --init --recursive
```

- check ``ls -la docker/``. you should see the file ``run_docker.sh``

- install the Nvidia Docker container ``./docker/run_docker.sh``

This will open the docker config .yaml file that will fetch the right docker image to install a docker container with all necessary dependencies to run Arena

```
  When you run ./docker/run_docker.sh, it:
  
  Reads docker-compose.yaml — This file specifies:
  
  Which NVIDIA base image to use (Isaac Sim 5.1.0)
  What to install on top (Isaac Lab, Arena)
  Which folders to share between Windows and the container
  GPU access settings
  
  Downloads the base image (~15-20 GB, first time only)
  
  Image: nvcr.io/nvidia/isaac-sim:5.1.0
  This is NVIDIA's official Isaac Sim container from NGC (NVIDIA GPU Cloud)
  
  Builds the Arena layer (~5 GB)
  
  Installs Isaac Lab
  Installs Arena
  Configures everything
  
  Starts the container and gives you a terminal inside it
```

- Start a fresh container: ``docker run -it --gpus all --entrypoint bash isaaclab_arena:latest``

- Inside the container — install ONLY the missing isaaclab core package. IMPORTANT: Use /isaac-sim/python.sh for all pip operations: ``/isaac-sim/python.sh -m pip install --no-deps -e /workspaces/isaaclab_arena/submodules/IsaacLab/source/isaaclab``

- Verify the fix
Input these tests (with the double quotes in the end of each)
```
/isaac-sim/python.sh -c "import isaaclab; print('isaaclab OK')"
/isaac-sim/python.sh -c "import torch; print(f'torch {torch.__version__} OK')"
/isaac-sim/python.sh -c "import isaaclab_arena; print('arena OK')"
```

- navigate to folder ``cd /workspaces/isaaclab_arena``





  
