# IsaacLab-Arena-Tutorial-with-Docker

# Pre-Requisites
- Have Ubuntu 22.04 installed. check ``wsl --list --verbose``
- Have Docker Desktop installed and running (open it and let it running on the background): check ``docker info | findstr "Operating System"``. You should see something like: Operating System: Docker Desktop
- Ubuntu has GPU access: test open Ubuntu and run ``docker run --rm --gpus all nvidia/cuda:12.2.0-base-ubuntu22.04 nvidia-smi``. You should see your GPU info if everything is configured correctly.

# Clone Arena
-> Open an Ubuntu terminal and inside it, do:

- Create a projects folder:
```
cd ~
mkdir projects
cd projects
```

- Clone repo
```
git clone https://github.com/isaac-sim/IsaacLab-Arena.git
```

- navigate to folder ``cd IsaacLab-Arena``

- initialize submodules
```
git config submodule.submodules/IsaacLab.url https://github.com/isaac-sim/IsaacLab.git
git config submodule.submodules/Isaac-GR00T.url https://github.com/NVIDIA/Isaac-GR00T.git

git submodule update --init --recursive
```
check ``ls -la docker/``. you should see the file ``run_docker.sh``

- install the Nvidia Docker container ``./docker/run_docker.sh``
This will open the docker config .yaml file that will fetch the right docker image to install a docker container with all necessary dependencies to run Arena

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

  
